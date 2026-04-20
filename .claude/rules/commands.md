# Custom Commands

The following custom commands are available for specialized tasks:

- name: `/commit`
  description: "GitHub差分を確認し、細かい粒度でルールに従ったコミットメッセージを自動生成してコミットを実行します。(Analyzes GitHub diffs and automatically creates fine-grained commits with rule-based commit messages.)"
  path: commit.md

- name: `/code-review`
  description: "PRのコードレビューを実施し、品質・セキュリティ・保守性の観点からフィードバックを提供します。(Performs code review on PRs, providing feedback on quality, security, and maintainability.)"
  path: code-review.md

- name: `/search`
  description: "Gemini CLIを使用してWeb検索を実行します。(Use the Gemini CLI to perform web searches.)"
  path: search.md

- name: `/d-search`
  description: "Gemini CLIを使用して詳細なWeb検索とレポート生成を行います。(Use the Gemini CLI for deep web searches and detailed reporting.)"
  path: d-search.md

- name: `/pr-summary`
  description: "GitHub PR週次サマリーを.tmp/に生成します。(Generates a weekly PR activity summary and saves it to .tmp/.)"
  path: pr-summary.md

- name: `/pr-template`
  description: "PRの変更内容を分析し、テンプレートに沿った詳細なPR説明文を生成します。(Analyzes PR changes and generates comprehensive PR descriptions.)"
  path: pr-template.md

- name: `/spec`
  description: "仕様駆動開発ワークフローを開始します。(Start Specification-Driven Development workflow.)"
  path: spec.md

- name: `/design`
  description: "要件に基づいた詳細設計書を作成します。(Create detailed design specification based on requirements.)"
  path: design.md

- name: `/requirements`
  description: "タスクの要件定義書を作成します。(Create requirements specification for the given task.)"
  path: requirements.md

- name: `/tasks`
  description: "設計書をタスクに分解します。(Break down design into implementable tasks.)"
  path: tasks.md

- name: `/textlint`
  description: "textlintを実行し、自動修正と手動修正を繰り返してエラーを解消します。(Execute textlint, apply fixes, and iterate until all errors are resolved.)"
  path: textlint.md
