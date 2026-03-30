# Claude Code ベストプラクティス調査 サマリー

## 2026-03-30
- **CLAUDE.md**: 階層的読み込み（global→project→subdirectory）と `.claude/rules/` によるモジュール分割、`@file` インポート構文が有効。肥大化を避け「Claudeが誤る場合にのみ記載」を基準に。
- **hooks**: 18種類のイベント × 4タイプ（command/prompt/agent/http）。exit code 2でブロック。PostToolUseで自動フォーマット、PreToolUseで機密ファイル保護、SessionStartでコンパクション後コンテキスト復元が特に実用的。
- **settings.json**: `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE: "75"` でコンテキスト劣化前にコンパクション。`$schema` 追加でVSCode補完。`.claude/settings.local.json` は個人設定（auto-gitignored）。
- **MCP**: `.mcp.json` をgit管理、シークレットは `${ENV_VAR}` 展開。Tool Search機能でコンテキスト最大85%削減。Node.js 18以上が必須。
- **Skills**: `.claude/skills/<name>/SKILL.md` が新推奨形式。`disable-model-invocation: true` で副作用コマンドを保護。`!` プレフィックスでシェル出力を動的インジェクト。
- **メモリ管理**: タスク切り替えで `/clear`、手動コンパクションは `/compact`、コンテキスト非汚染の質問は `/btw`。サブエージェントで大規模調査を分離。
- **サブエージェント**: `.claude/agents/<name>.md` でカスタム定義。`memory: project` でセッション間知識を永続化。`model: haiku` でコスト削減、Writer/Reviewerパターンでバイアス排除。
