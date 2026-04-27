# Claude Code ベストプラクティス調査 サマリー

## 2026-04-27
- **`opusplan` モデルエイリアス**: Plan Mode → Opus / 実行フェーズ → Sonnet の自動切替で高品質推論とコスト効率を両立。`CLAUDE_CODE_SUBAGENT_MODEL: "haiku"` でサブエージェントをさらに低コストモデルへ。
- **エフォートレベル体系化**: Opus 4.7 は `low/medium/high/xhigh/max` の5段階（v2.1.117+ でデフォルト `xhigh`）。スキル・サブエージェントフロントマターで `effort:` を個別指定可能。`max` はセッション限定。
- **`permissions.ask`・新設定項目**: `allow`/`deny` に加え `ask`（確認プロンプト）で3ティア権限管理。`showThinkingSummaries`、`fastModePerSessionOptIn`、`disableAutoMode` 等が追加。配列設定はスコープ間でマージ（置換でなく連結）。MCP は SSE 非推奨・HTTP（OAuth 2.1）が標準に。

## 2026-04-20
- **hooks カデンス設計**: Per-session/Per-turn/Per-tool の3分類が明確化。テストスイートは `PostToolUse` ではなく `Stop` フックへ。PreToolUse ブロック理由は `>&2` で Claude に伝達。PostToolUse には `; exit 0` フォールバック必須。
- **コンテキスト定量モデルと設計原則**: 90% 使用率で深刻劣化。ガイダンスなしの成功率 33%（Anthropic 実測）。2026 年は「プロンプト技巧 < コンテキスト設計（CLAUDE.md・MCP・サブエージェント構造）」が定説化。フィードバックループ（検証コマンド）をプロンプトに埋め込む手法が推奨。
- **Skills 統合・CLAUDE.md 10-section・MCP 最小権限**: `.claude/commands/` と `.claude/skills/` が 2026 年に統合。CLAUDE.md は `/init` で自動生成後、10-section 構造に補強が最短経路。`managed-mcp.json` で組織固定配布。`/status` コマンドで設定競合をデバッグ。
- **[追加] Auto-memory**: v2.1.59+ で Claude が自律的に `~/.claude/projects/<project>/memory/` へ知識蓄積。MEMORY.md が200行/25KB インデックス、トピックファイルはオンデマンド。`autoMemoryEnabled: false` で無効化。
- **[追加] CLAUDE.md HTML コメント・`claudeMdExcludes`・`CLAUDE_CODE_NEW_INIT=1`**: `<!-- -->` コメントはコンテキスト除去でトークン節約。モノレポは `claudeMdExcludes` で不要 CLAUDE.md を除外。インタラクティブ init は `CLAUDE_CODE_NEW_INIT=1` で有効化。
- **[追加] hooks `if` フィールド・`stop_hook_active`・`InstructionsLoaded`**: v2.1.85+ の `if` でツール名＋引数の細粒度フィルタ（例: `Bash(git *)`）。Stop フック無限ループは `stop_hook_active` で防止。`InstructionsLoaded` フックで CLAUDE.md ロードタイミングをデバッグ。`CLAUDE_ENV_FILE` で direnv 等の環境変数を永続化。

## 2026-04-13
- **Skills フロントマター深化**: `effort`・`paths`・`argument-hint`・`user-invocable`・`context: fork`・`agent` など全フィールドが判明。`$ARGUMENTS[N]`/`$N` による引数インデックスアクセス、`${CLAUDE_SESSION_ID}`/`${CLAUDE_SKILL_DIR}` 変数展開、`!`backtick シェルインジェクション（複数行は ` ```! ` ブロック）も活用可能。`disableSkillShellExecution: true` で組織ポリシーとして無効化できる。
- **コンテキスト管理高度化**: `/compact <instructions>` でターゲットコンパクション、CLAUDE.md 内にコンパクション指示を埋め込み可能（「変更ファイルリストとテストコマンドを保持」等）。コンテキスト劣化は 20〜40% 使用率から開始・60% 超で深刻。Plan Mode 中の `Ctrl+G` でプランをテキストエディタ直接編集可能。`/rename` でセッションに意味のある名前をつけて後から検索。
- **非インタラクティブ・ファンアウト**: `--output-format stream-json` でストリーミング JSON 出力、`xargs -P 5 -I{}` + `claude -p` で大規模ファイルマイグレーションの並列ファンアウト、`--allowedTools` でバッチ権限最小化。5 つの公式アンチパターン（キッチンシンク・過剰修正ループ・CLAUDE.md 肥大化・確認なし信頼・無限探索）が明記。

## 2026-04-06
- **パーミッションモード**: `auto` モード（v2.1.83+）が追加。Classifier が危険な操作を自動ブロック、`Shift+Tab` でサイクル切り替え。`defaultMode: "acceptEdits"` で承認疲れを解消。Team/Enterprise/API プランのみ。
- **新規 Hooks**: `CwdChanged`（direnv 連携）、`FileChanged`（ファイル監視）、`ConfigChange`（設定監査）、`PermissionRequest`（自動承認）、`PreCompact`/`PostCompact` など多数追加。Hook タイプも `prompt`（LLM 判断）・`agent`（ツール付き）・`http` が新登場。`if` フィールド（v2.1.85+）でツール名＋引数の細粒度フィルタリング可能。
- **Checkpointing**: `Esc+Esc` / `/rewind` でコード・会話を任意時点に復元。セッション間をまたいで保存（30日）。`--fork-session` でセッション分岐も可能。
- **プラグインシステム**: `.claude-plugin/plugin.json` でスキル・hooks・MCP・LSP サーバーをパッケージ化して共有。`--plugin-dir` でテスト、`/reload-plugins` で即時反映。
- **エージェントチーム（実験的）**: 複数 Claude インスタンスが共有タスクリストで協調。チームメート同士の直接通信が可能。`CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS: "1"` で有効化。推奨 3〜5 人。
- **その他**: `CLAUDE.local.md`（個人専用プロジェクト設定・gitignore）、4 フェーズワークフロー（Explore→Plan→Implement→Commit）、LLM インタビューパターン（SPEC.md 生成）、`claude --continue`/`--resume` セッション再開。`/loop` 定期実行・Channels（Telegram/Discord）外部連携・`--worktree` 分離・Sandbox 設定・LSP プラグイン（コードインテリジェンス）・`effortLevel`/`ultrathink`/`/fast` 等モデル制御設定も新規追加。

## 2026-03-30
- **CLAUDE.md**: 階層的読み込み（global→project→subdirectory）と `.claude/rules/` によるモジュール分割、`@file` インポート構文が有効。肥大化を避け「Claudeが誤る場合にのみ記載」を基準に。
- **hooks**: 18種類のイベント × 4タイプ（command/prompt/agent/http）。exit code 2でブロック。PostToolUseで自動フォーマット、PreToolUseで機密ファイル保護、SessionStartでコンパクション後コンテキスト復元が特に実用的。
- **settings.json**: `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE: "75"` でコンテキスト劣化前にコンパクション。`$schema` 追加でVSCode補完。`.claude/settings.local.json` は個人設定（auto-gitignored）。
- **MCP**: `.mcp.json` をgit管理、シークレットは `${ENV_VAR}` 展開。Tool Search機能でコンテキスト最大85%削減。Node.js 18以上が必須。
- **Skills**: `.claude/skills/<name>/SKILL.md` が新推奨形式。`disable-model-invocation: true` で副作用コマンドを保護。`!` プレフィックスでシェル出力を動的インジェクト。
- **メモリ管理**: タスク切り替えで `/clear`、手動コンパクションは `/compact`、コンテキスト非汚染の質問は `/btw`。サブエージェントで大規模調査を分離。
- **サブエージェント**: `.claude/agents/<name>.md` でカスタム定義。`memory: project` でセッション間知識を永続化。`model: haiku` でコスト削減、Writer/Reviewerパターンでバイアス排除。
