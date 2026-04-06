# Claude Code ベストプラクティス調査 サマリー

## 2026-04-06
- **パーミッションモード**: `auto` モード（v2.1.83+）が追加。Classifier が危険な操作を自動ブロック、`Shift+Tab` でサイクル切り替え。`defaultMode: "acceptEdits"` で承認疲れを解消。Team/Enterprise/API プランのみ。
- **新規 Hooks**: `CwdChanged`（direnv 連携）、`FileChanged`（ファイル監視）、`ConfigChange`（設定監査）、`PermissionRequest`（自動承認）、`PreCompact`/`PostCompact` など多数追加。Hook タイプも `prompt`（LLM 判断）・`agent`（ツール付き）・`http` が新登場。`if` フィールド（v2.1.85+）でツール名＋引数の細粒度フィルタリング可能。
- **Checkpointing**: `Esc+Esc` / `/rewind` でコード・会話を任意時点に復元。セッション間をまたいで保存（30日）。`--fork-session` でセッション分岐も可能。
- **プラグインシステム**: `.claude-plugin/plugin.json` でスキル・hooks・MCP・LSP サーバーをパッケージ化して共有。`--plugin-dir` でテスト、`/reload-plugins` で即時反映。
- **エージェントチーム（実験的）**: 複数 Claude インスタンスが共有タスクリストで協調。チームメート同士の直接通信が可能。`CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS: "1"` で有効化。推奨 3〜5 人。
- **その他**: `CLAUDE.local.md`（個人専用プロジェクト設定・gitignore）、4 フェーズワークフロー（Explore→Plan→Implement→Commit）、LLM インタビューパターン（SPEC.md 生成）、`claude --continue`/`--resume` セッション再開。

## 2026-03-30
- **CLAUDE.md**: 階層的読み込み（global→project→subdirectory）と `.claude/rules/` によるモジュール分割、`@file` インポート構文が有効。肥大化を避け「Claudeが誤る場合にのみ記載」を基準に。
- **hooks**: 18種類のイベント × 4タイプ（command/prompt/agent/http）。exit code 2でブロック。PostToolUseで自動フォーマット、PreToolUseで機密ファイル保護、SessionStartでコンパクション後コンテキスト復元が特に実用的。
- **settings.json**: `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE: "75"` でコンテキスト劣化前にコンパクション。`$schema` 追加でVSCode補完。`.claude/settings.local.json` は個人設定（auto-gitignored）。
- **MCP**: `.mcp.json` をgit管理、シークレットは `${ENV_VAR}` 展開。Tool Search機能でコンテキスト最大85%削減。Node.js 18以上が必須。
- **Skills**: `.claude/skills/<name>/SKILL.md` が新推奨形式。`disable-model-invocation: true` で副作用コマンドを保護。`!` プレフィックスでシェル出力を動的インジェクト。
- **メモリ管理**: タスク切り替えで `/clear`、手動コンパクションは `/compact`、コンテキスト非汚染の質問は `/btw`。サブエージェントで大規模調査を分離。
- **サブエージェント**: `.claude/agents/<name>.md` でカスタム定義。`memory: project` でセッション間知識を永続化。`model: haiku` でコスト削減、Writer/Reviewerパターンでバイアス排除。
