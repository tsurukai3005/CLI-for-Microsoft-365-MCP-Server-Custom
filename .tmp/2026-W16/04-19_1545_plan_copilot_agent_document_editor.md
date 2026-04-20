# Copilot Agent ドキュメントエディター 計画書

> **更新履歴**:
> - v5 (2026-04-19) — 上書きあり方針に変更、フロー仕様更新
> - v4 (2026-04-19) — ファイル管理を手動化、MD優先方針、アーキテクチャ大幅簡素化
> - v2 (2026-04-19) — Word出力を削除、PowerPoint生成を追加、ODSL調査結果を反映

## Goal

ブラウザ上のMicrosoft 365 Copilot（Agent Builder）だけで、
ドキュメントの**新規作成（.md / .pptx）と既存編集・上書き（.md）**ができるエージェントを構築する。
PPTXの移動はユーザーが手動で行う。

## Context

- **環境**: ドコモ社内M365テナント。サーバーホスティングに制限あり
- **利用可能ツール**: Agent Builder, Copilot Studio, Power Automate, SharePoint, OneDrive
- **現状の課題**: ドキュメントの作成・修正作業が手作業で非効率

## 設計方針

| 方針 | 説明 |
|------|------|
| **Markdownファースト** | テキスト文書は .md で管理。編集自由度が最も高い形式 |
| **MDは上書き可** | .mdファイルは同名で上書き保存。SharePointバージョン履歴で復元可能 |
| **最小構成** | Power Automateフローは1本のみ（テキスト保存用） |
| **Agent Builder中心** | Copilot Studioへの昇格は必要になってから |

## ファイル形式ごとの対応

| 形式 | 読み取り | 新規作成 | 既存編集 | 方法 |
|------|---------|---------|---------|------|
| **.md** | Knowledge Source | **Flow経由** | **Flow経由（上書き）** | エージェントが内容生成→Flow1本で新規作成 or 上書き |
| .txt / .csv | Knowledge Source | Flow経由 | Flow経由 | .mdと同じフロー |
| .docx | **Knowledge Source** | 不要 | 不要 | Copilot標準機能で読み取り済み。出力不要 |
| **.pptx** | Knowledge Source | **Generate docsスキル** | 不要 | Agent Builder内蔵スキルで生成→OneDriveに保存 |

## Architecture

```
  ┌──────────────────────────────────────────────────────────┐
  │ ユーザー（ブラウザ / Teams）                               │
  │  「この報告の概要セクションを修正して」                     │
  │  「来週の進捗スライドを作って」                             │
  └─────────────────────┬────────────────────────────────────┘
                        │ チャット
                        ▼
  ┌──────────────────────────────────────────────────────────┐
  │ Agent Builder エージェント                                │
  │                                                          │
  │  Instructions（ルール定義書）                              │
  │   - 文書品質ルール、フォルダ制限、コマンド定義             │
  │                                                          │
  │  Knowledge Sources                                       │
  │   - SharePointフォルダ（.docx/.md 読み取り）              │
  │   - _system/rulebook.md（詳細ルール）                     │
  │                                                          │
  │  Skills                                                  │
  │   - 「Generate documents, charts, and code」→ PPTX生成   │
  │                                                          │
  │  Tools                                                   │
  │   - Flow: テキスト保存（.md/.txt/.csvをSharePointに保存） │
  └───────┬──────────────────────────┬───────────────────────┘
          │                          │
          ▼                          ▼
  ┌───────────────┐         ┌────────────────┐
  │ .md 出力           │         │ .pptx 出力      │
  │                    │         │                 │
  │ Flow経由で         │         │ Generate docs   │
  │ SharePointに       │         │ スキル経由で     │
  │ 新規作成 or 上書き │         │ OneDriveに保存  │
  └───────┬───────────┘         └────────┬───────┘
          │                              │
          │ 自動完了                       ▼
          │                      ユーザーが手動で
          │                      目的フォルダに移動
          ▼
  SharePoint の AgentWorkspace/ に保存済み
  （同名ファイルは上書き。バージョン履歴で復元可能）
```

### 構成要素（全体でたった4つ）

| # | 構成要素 | 何をするか |
|---|---------|-----------|
| 1 | **Agent Builder エージェント** | チャットUI + ルール適用 + 読み取り + PPTX生成 |
| 2 | **Power Automate フロー 1本** | エージェントが生成したテキストを .md としてSharePointに保存 |
| 3 | **SharePoint フォルダ** | ワークスペース（ドキュメント格納 + ルールブック） |
| 4 | **ルールブック (.md)** | Knowledge Sourceとして追加する詳細ルール集 |

### v2からの簡素化ポイント

```
  v2（前回）: フロー5本 + スキル + フォルダ5種 + バックアップ自動化
                        ↓
  v4（今回）: フロー1本 + スキル + フォルダ2種 + ファイル管理は手動

  削除したもの:
  ✕ Flow: ファイル内容取得      → Knowledge Sourceで代替
  ✕ Flow: ファイル上書き更新    → Create file (Replace) で兼用
  ✕ Flow: ファイル一覧取得      → Knowledge Sourceで代替
  ✕ Flow: PPTX保存             → Generate docsがOneDriveに直接保存
  ✕ 自動バックアップ           → SharePointバージョン履歴で代替
  ✕ パスバリデーション         → フロー1本なのでInstructionsでの制限で十分
```

## ドキュメント編集の流れ（コア機能）

### .md ファイルの新規作成

```
  ユーザー: 「議事録を新規作成して。参加者は○○、議題は△△」
       │
       ▼
  エージェント:
   1. ルール（rulebook.md）に基づきMarkdownで文面を生成
   2. チャット上で内容をプレビュー表示
   3. ユーザーが「OK」
   4. Flow を呼び出し → SharePointに .md ファイルとして保存
       │
       ▼
  保存先: AgentWorkspace/ に新規 .md ファイル
```

### 既存 .md ファイルの編集（上書き）

```
  ユーザー: 「報告書.md の"検討中"を"決定済み"に修正して」
       │
       ▼
  エージェント:
   1. Knowledge Sourceから報告書.mdの内容を読み取り
   2. 修正版の全文をMarkdownで生成
   3. チャット上で変更箇所を説明して提示
   4. ユーザーが「OK」
   5. Flow を呼び出し → 同名ファイル「報告書.md」として上書き保存
       │
       ▼
  完了。旧版はSharePointのバージョン履歴から復元可能
```

**ポイント**: ファイル名の変更もバージョン管理も不要。
SharePointが自動でバージョン履歴を保持するため、誤操作時も安全。

### 既存 .docx の表現修正

```
  ユーザー: 「提案書.docx の結論セクションをもっと簡潔にして」
       │
       ▼
  エージェント:
   1. Knowledge Sourceから提案書.docxの内容を読み取り
   2. 修正版をMarkdown形式で生成
   3. チャット上で提示
   4. ユーザーが「OK」
   5. Flow を呼び出し → 修正版を .md として保存
       │
       ▼
  ユーザーが手動で:
   - .md の内容をWordにコピペ
   - または Generate docs スキルで新規 .docx として出力
```

### PowerPoint 新規生成

```
  ユーザー: 「来週の進捗報告スライドを作って」
       │
       ▼
  エージェント:
   1. Knowledge Sourceから関連情報（.docx/.md）を参照
   2. スライド構成案を提示
   3. ユーザーが調整指示
   4. 「Generate documents」スキルで .pptx を生成
       │
       ▼
  OneDrive に .pptx が保存される
  ユーザーが手動で目的のフォルダに移動
```

## ルール定義の設計

### Instructions（8,000文字以内）

```
# ドキュメント管理アシスタント

## あなたの役割
ドキュメントの作成・編集・レビューを支援するエージェントです。
テキスト文書はMarkdown (.md) 形式で出力します。

## 出力ルール
- テキスト文書は必ず Markdown (.md) 形式で出力してください
- PowerPointは「Generate documents」スキルで生成してください
- Word (.docx) の出力は行いません。Wordが必要な場合は .md を出力し、
  ユーザーに手動変換を案内してください

## ファイル保存ルール
- テキストファイルの保存には「テキスト保存フロー」を使用してください
- 新規作成時はファイル名に日付プレフィックス (YYYY-MM-DD_) を付与してください
- 既存ファイルの編集時は同じファイル名で上書き保存してください

## 編集時のルール
- 既存ファイルを編集する場合、修正版を同じファイル名で上書き保存してください
- 上書き前に、修正前後の主な変更点をチャットで必ず説明してください
- ユーザーの承認を得てから保存してください

## 文書品質ルール
- です・ます調を基本とする
- 技術用語は初出時に括弧書きで説明を添える
- 機密情報（個人名、顧客名等）が含まれる場合は警告する
- 詳細は _system/rulebook.md を参照

## コマンド（会話スターター）
- 「新規作成」→ 内容を確認 → .md ファイルとして保存
- 「編集して」→ 対象を読み取り → 修正版 .md を保存
- 「スライド作成」→ 構成案を提示 → .pptx を生成
- 「レビュー」→ 指定文書をルールに基づきチェック
- 「ルール確認」→ 現在適用中のルールを一覧表示

## PowerPoint生成ルール
- 日本語で作成
- 1スライドあたり最大200文字目安
- 表紙スライドと目次スライドを必ず含める
- シンプルなデザインを維持する
```

### ルール補足（Knowledge Source）

SharePointの `_system/` フォルダに配置し、Knowledge Sourceとして追加する。

```
AgentWorkspace/
  ├── _system/
  │    ├── rulebook.md         ← 文体ルール、用語集、禁止表現
  │    └── slide_guide.md      ← スライドデザインガイドライン
  │
  └── （ユーザーのドキュメント群）
       ├── 2026-04-19_議事録.md
       ├── 報告書.md             ← 上書き編集可能
       ├── 提案書.docx          ← 読み取り専用
       └── ...
```

ルールの変更は .md を編集するだけ。エージェントの再作成は不要。

## Power Automate フロー仕様（1本のみ）

### Flow: テキスト保存

| 項目 | 内容 |
|------|------|
| **トリガー** | Copilot Studio Agent からの呼び出し |
| **入力** | `fileName` (string), `content` (string), `folderPath` (string, 任意) |
| **処理** | SharePoint「Create file」アクション（同名ファイル存在時: Replace）で保存 |
| **出力** | 保存されたファイルのURL |
| **デフォルト保存先** | /sites/[サイト名]/Documents/AgentWorkspace/ |

```
  フロー構成（3ステップのみ）:

  1. トリガー: Agent から呼び出し
     入力: fileName, content, folderPath(任意)
       │
       ▼
  2. SharePoint「Create file」アクション
     サイト: [固定]
     フォルダ: folderPath ?? "AgentWorkspace"
     ファイル名: fileName
     ファイルコンテンツ: content
     同名ファイル存在時: Replace（上書き）
       │
       ▼
  3. 応答: 保存されたファイルのURLを返却
```

## Key Decisions

- **MD優先**: テキスト文書はMarkdownで管理。Word出力は不要。編集自由度を最大化
- **上書きあり**: .mdは同名ファイルで上書き保存。SharePointのバージョン履歴が
  自動で旧版を保持するため、バックアップフローは不要
- **フロー最小化**: Power Automateフローは「テキスト保存」の1本のみ（新規/上書き兼用）
- **PPTX生成はAgent Builderスキル**: 「Generate documents」スキル（GA済み）を使用。
  出力先はOneDrive → ユーザーが手動で目的フォルダに移動

## Verification

### Phase 1 完了条件
- [ ] Agent Builderでエージェントが応答する
- [ ] Instructions のルールに従った回答が返される
- [ ] Knowledge Sourceから .docx の内容を読み取れる
- [ ] Knowledge Sourceから .md の内容を読み取れる
- [ ] 「新規作成」→ .md ファイルが SharePointに保存される
- [ ] 「編集して」→ 既存 .md ファイルが修正内容で上書き保存される
- [ ] 「レビュー」→ ルールに基づくフィードバックが返される
- [ ] rulebook.md の内容変更がエージェントの振る舞いに反映される

### Phase 2 完了条件
- [ ] 「スライド作成」→ .pptx ファイルがOneDriveに生成される
- [ ] Knowledge Sourceの情報がスライド内容に反映される
- [ ] slide_guide.md のルールがスライドに反映される

## Tasks

### Phase 1: テキスト編集基盤
- [ ] Task 1: SharePointに AgentWorkspace/ と _system/ フォルダを作成
- [ ] Task 2: rulebook.md を作成（文体ルール、用語集）
- [ ] Task 3: Power Automate「テキスト保存」フローを構築（3ステップ）
- [ ] Task 4: Agent Builderでエージェントを作成、Instructions を設定
- [ ] Task 5: フローをエージェントのToolとして接続
- [ ] Task 6: Knowledge SourceにAgentWorkspaceフォルダを追加
- [ ] Task 7: Phase 1の検証

### Phase 2: PowerPoint生成
- [ ] Task 8: slide_guide.md を作成（スライドデザインガイドライン）
- [ ] Task 9: 「Generate documents, charts, and code」スキルを有効化
- [ ] Task 10: PPTX生成の動作検証
- [ ] Task 11: Phase 2の検証

## 参考: ODSL（PowerPoint Copilot 内部アーキテクチャ）

PowerPoint内のCopilotは **ODSL (Office Domain Specific Language)** を使ってスライドを生成する:

1. ユーザープロンプト → Orchestratorが解析
2. Microsoft Graph + Semantic Index で文脈補強
3. ODSL擬似コードをLLMが生成 → バリデーション
4. ODSL → Office JavaScript API にコンパイル → スライド描画

このパイプラインはPowerPointアプリ内部専用。Agent Builder の「Generate documents」
スキルはこれとは別のメカニズム（Code Interpreter的アプローチ）で動作する。

### Sources
- [Microsoft 365 Copilot Architecture](https://learn.microsoft.com/en-us/microsoft-365/copilot/microsoft-365-copilot-architecture)
- [Inside Microsoft 365 Copilot: A Technical Breakdown](https://labs.zenity.io/p/inside-microsoft-365-copilot-technical-breakdown)
- [Agent Builder in Microsoft 365 Copilot](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/agent-builder)
- [Introducing Word, Excel, and PowerPoint Agents](https://techcommunity.microsoft.com/blog/microsoft365copilotblog/introducing-word-excel-and-powerpoint-agents-in-microsoft-365-copilot/4470604)
- [Generate documents from Agent Builder](https://m365admin.handsontek.net/generate-word-excel-powerpoint-documents-agents-built-agent-builder-microsoft-365-copilot/)
- [Copilot Studio: Generate PowerPoint File From A Template](https://www.matthewdevaney.com/copilot-studio-generate-powerpoint-file-from-a-template/)
