# CLI for Microsoft 365 MCP Server (個人カスタム版)

> **注意**: このリポジトリは [pnp/cli-microsoft365-mcp-server](https://github.com/pnp/cli-microsoft365-mcp-server) のコピーです。
> オリジナルは **Microsoft 365 & Power Platform Community** によって開発・メンテナンスされています。
> 本リポジトリは**個人利用目的**でのみ使用しており、再配布やサービス提供を目的としたものではありません。

## 概要

CLI for Microsoft 365 の MCP (Model Context Protocol) サーバーです。AI アシスタントが自然言語で Microsoft 365 テナントを管理できるようにします。

対応する Microsoft 365 サービス:

- Entra ID、OneDrive、OneNote、Outlook、Planner
- Power Apps、Power Automate、Power Platform
- SharePoint Embedded、SharePoint Online
- Teams、Viva Engage など

## 技術スタック

| 項目 | 値 |
|------|-----|
| 言語 | TypeScript (strict mode) |
| ランタイム | Node.js >= 20.0.0 |
| モジュール | ESM (`"type": "module"`) |
| 主要依存 | `@modelcontextprotocol/sdk`, `fuse.js` |
| ビルド | `tsc` |

## 前提条件

- Node.js 20.x 以上
- CLI for Microsoft 365 がグローバルインストール済み

```bash
npm i -g @pnp/cli-microsoft365
```

## セットアップ

### 1. CLI for Microsoft 365 の初期設定

```bash
m365 setup
m365 cli config set --key prompt --value false
m365 cli config set --key output --value text
m365 cli config set --key helpMode --value full
```

### 2. Microsoft 365 へのログイン

```bash
m365 login
```

MCP サーバーは認証を行わないため、事前に `m365 login` で認証しておく必要があります。

### 3. 依存パッケージのインストールとビルド

```bash
npm install
npm run build
```

## 使い方

### VS Code で使用する

`.vscode/mcp.json` に以下を設定します:

```json
{
  "servers": {
    "CLI for Microsoft 365 MCP Server": {
      "type": "stdio",
      "command": "node",
      "args": ["dist/index.js"]
    }
  }
}
```

### MCP Inspector でデバッグする

```bash
npm run start
npx @modelcontextprotocol/inspector node dist/index.js
```

## 提供ツール

| ツール | 説明 |
|--------|------|
| `m365SearchCommands` | ファジー検索でコマンドを検索する |
| `m365GetCommandDocs` | 指定コマンドのドキュメントを取得する |
| `m365RunCommand` | コマンドを実行して結果を返す |
| `m365GetBestPractices` | スクリプト作成のベストプラクティスを取得する |

## ライセンス

オリジナルリポジトリの [MIT License](https://github.com/pnp/cli-microsoft365-mcp-server/blob/main/LICENSE) に従います。

## クレジット

- オリジナル: [pnp/cli-microsoft365-mcp-server](https://github.com/pnp/cli-microsoft365-mcp-server)
- 作者: [Adam Wójcik](https://github.com/Adam-it) / Microsoft 365 & Power Platform Community
