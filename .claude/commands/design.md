---
allowed-tools: TodoWrite, TodoRead, Read, Write, MultiEdit
description: Create detailed design specification based on requirements (Stage 2 of Spec-Driven Development)
---

## Context

- Requirements document: @.tmp/requirements.md

## Your task

### 1. Verify prerequisites

- Check that `.tmp/requirements.md` exists
- If not, inform user to run `/requirements` first

### 2. Analyze requirements

Read and understand the requirements document thoroughly

### 3. Create Design Document

Create `.tmp/design.md` with the following sections:

````markdown
# 詳細設計書 - [タスク名]

## 1. アーキテクチャ概要

### 1.1 システム構成図

[ASCII図でシステム全体の構成を表現]

### 1.2 技術スタック

- 言語: TypeScript (strict mode)
- ランタイム: Node.js >= 20.0.0
- モジュール: ESM (NodeNext)
- フレームワーク: @modelcontextprotocol/sdk
- ツール: [その他使用ツール]

## 2. コンポーネント設計

### 2.1 コンポーネント一覧

| コンポーネント名 | 責務         | 依存関係                 |
| ---------------- | ------------ | ------------------------ |
| [Component A]    | [責務の説明] | [依存するコンポーネント] |

### 2.2 各コンポーネントの詳細

#### [Component A]

- **目的**: [このコンポーネントの目的]
- **公開インターフェース**:
  ```typescript
  interface ComponentA {
    method1(param: Type): ReturnType;
  }
  ```
````

- **内部実装方針**: [実装のアプローチ]

## 3. データフロー

### 3.1 データフロー図

[データの流れを示す図]

## 4. APIインターフェース

### 4.1 MCP Tool定義

[MCPツールのインターフェース定義]

### 4.2 外部API

[CLI for Microsoft 365との連携インターフェース]

## 5. エラーハンドリング

### 5.1 エラー分類

- [エラータイプ1]: [対処方法]

## 6. セキュリティ設計

[認証・認可、データ保護]

## 7. テスト設計

**詳細なテスト設計については、`/test-design`コマンドを実行してテスト設計書を作成してください。**

## 8. 実装上の注意事項

- [注意点1]
- [注意点2]

```

### 4. Update TODO
Use TodoWrite to add "詳細設計の完了とレビュー" as a task

### 5. Present to user
Show the created design document and ask for:
- Technical feedback
- Architecture approval
- Permission to proceed to task breakdown

## Important Notes
- Design should be implementable and testable
- Consider maintainability and extensibility
- Include concrete interface definitions where possible
- Address all requirements from the requirements document
```

think hard
