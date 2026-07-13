# Claude Code ベストプラクティス調査 サマリー

## 2026-07-13
- **`/doctor` コマンド（v2.1.205）**: セットアップの健全性チェックが「読み取り専用」から「診断＋修正提案」に進化。未使用スキル・MCP・プラグインのコンテキストコスト比較、CLAUDE.md 重複検出・トリミング提案、遅いフック検出を実行。`/checkup` がエイリアス。
- **Claude Sonnet 5 が新デフォルト + サブエージェントのバックグラウンド実行デフォルト化**: Sonnet 5 は 1M トークンネイティブ・adaptive thinking ON・API $2/$10 per MTok。サブエージェントは結果を待たずに Claude が作業継続、`background: false` でピン留め可能。
- **Auto mode セキュリティ強化**: トランスクリプト改ざんブロック・未解決変数の `rm -rf` 前確認・バックグラウンドタスク通知に「人間の入力なし」を明記。Desktop に in-app ブラウザ搭載。フックのハイフン識別子が完全一致に変更（`mcp__brave-search__.*` 形式に要更新）。

## 2026-07-06
- **新 settings.json 項目（v2.1.175〜v2.1.200）**: `availableModels`+`enforceAvailableModels`（モデル選択制限）、`askUserQuestionTimeout`（非インタラクティブ向け自動続行）、`autoMode.classifyAllShell`（全シェルを安全分類器に通す）、`enabledMcpjsonServers`（信頼フォルダ外での承認サーバー指定）。
- **マルチスラッシュコマンドスタック（v2.1.199）**: `/code-review /fix-issue` のように連続入力で最大6スキルを同時ロード。「レビュー→修正」を1入力で実行可能。
- **CLAUDE.md の実効命令予算（~100〜150）と削除テスト**: Claude 組み込みプロンプトが ~50 命令消費するため実効予算は 100〜150。各行に「削除しても Claude が間違えないなら削除」テストを適用し 100 行以下を目標に。`CLAUDE.local.md` で個人設定を git 管理外で分離。MCP スターターセットは filesystem・github・context7・playwright・sequential-thinking の5本が推奨。Writer/Reviewer パターンが公式化。

## 2026-06-22
- **managed-settings.d/ ドロップインディレクトリ**: systemd 方式でチーム・ツールごとにポリシー JSON を独立管理。数字プレフィックスでマージ順制御（`10-security.json` → `20-devtools.json`）。単一ファイルへの競合編集が不要になる。
- **新 settings.json 項目（v2.1.136-182）**: `footerLinksRegexes`（チケット ID → クリッカブルバッジ）・`axScreenReader`・`disableClaudeAiConnectors`・`policyHelper`（動的ポリシー計算）。`/config key=value` で単一設定を即時更新可能。
- **Hooks 37イベント確定・async/asyncRewake/terminalSequence 追加**: `async: true` でノンブロッキングバックグラウンド実行、`asyncRewake: true` でバックグラウンド完了後に Claude を再起動、`terminalSequence` でデスクトップ通知（v2.1.141+）、`updatedToolOutput` でツール出力のリダクション。MCP ツール数は 40 が安定動作の目安（50超で誤選択）、Streamable HTTP が新標準トランスポート。CLAUDE.md にコンパクション指示を埋め込み重要情報の保持を制御可能。

## 2026-06-15
- **Claude Fable 5（Mythos クラス）リリース（6/9）**: Opus の上位モデル。エージェントハーネスで数日間自律稼働が可能、1M トークンコンテキスト対応。長時間エージェントタスクに `model: fable-5` を指定。
- **プラグインマーケットプレイス & Code Intelligence**: 公式マーケットプレイスに 101+ プラグイン。`/plugin install pyright-lsp` 等 LSP プラグインでジャンプ・型エラー検出が可能、ファイル読み込みを削減。
- **公式 5 大アンチパターン & 敵対的レビューパターン**: Kitchen Sink / 過剰訂正 / CLAUDE.md 肥大化 / 検証省略 / 無限探索 が公式化。実装後は `/code-review` スキルで別サブエージェントによる差分審査が推奨。`/rewind` の "Summarize from/up to here" 2 モードも明確化。`--permission-mode auto` + `--allowedTools` でファンアウトスケーリング、MCP 2.4 で MFA/監査ログ対応。

## 2026-06-08
- **Skills とカスタムコマンドの統合**: `.claude/commands/` と `.claude/skills/<name>/SKILL.md` は統合・同等。新規は Skills 形式推奨。`disable-model-invocation: true`・`context: fork`・`paths:` など詳細フロントマターを活用。動的コンテキスト注入（`` !`command` ``）でスキル実行時のみシェル出力をインライン展開可能。
- **CLAUDE.md の `.claude/rules/` 分離 & 200行ガイドライン**: `paths:` フロントマターで特定ファイル読み込み時のみロードするルールを設定。HTML コメント（`<!-- -->`）はコンテキストから除去されトークン節約に有効。`claudeMdExcludes` でモノレポ内の不要 CLAUDE.md をスキップ。`@AGENTS.md` インポートで他 AI ツールとの共存も可能。
- **hooks 25+ イベント・5タイプ完全版**: `PermissionRequest`（自動承認）・`PreCompact`/`PostCompact`（コンパクション前後処理）・`SubagentStart`/`SubagentStop`・`WorktreeCreate`/`WorktreeRemove` 等が追加確認。`settings.json` 4スコープ体系（Managed > Local > Project > User）と `$schema` 追加によるエディタ補完が実用的。

## 2026-06-01
- **AutoDream（`autoDreamEnabled`）**: セッション終了後に起動するバックグラウンドサブエージェントがメモリを自動統合。`/memory` で "Auto-dream: on" を確認。`autoMemoryDirectory` でカスタム保存先も指定可能。
- **`.claudeignore` でコンテキスト 40-70% 削減**: `.gitignore` 同構文で Claude の自動探索対象外ファイルを指定。`node_modules/`・`dist/`・`*.lock` を除外するだけで最大効果。機密ファイルは `permissions.deny` で別管理。
- **`/goal` コマンド（v2.1.139）・`sandbox` 詳細設定・新 `settings.json` 項目**: `/goal` で複数ターン自律完了条件を設定（evaluator サブエージェントが毎ターン条件チェック）。`sandbox.filesystem.denyRead` で機密ファイルを OS レベルでブロック。`worktree.symlinkDirectories`・`attribution`・`alwaysThinkingEnabled` 等の新設定を追加。1M トークンは「コンテキスト量より質」戦略が依然重要。

## 2026-05-25
- **コンテキストコスト全体像 & `disable-model-invocation`**: 公式が機能別コスト表を公開。Skills の説明文はデフォルトで毎セッション読み込まれる。`disable-model-invocation: true` でコストをゼロに（副作用スキルに必須）。`skillOverrides` でファイル編集なしに可視性変更可能。
- **CLAUDE.md 200行ガイドライン & 機能追加トリガー表**: 200行超えたら Skills・`.claude/rules/` へ分離。「2回間違えたら CLAUDE.md」「3回貼り付けたら Skill」「毎回コピーしたら MCP」など公式トリガー表が整備された。
- **Custom Statusline・Code Intelligence・機能レイヤー規則**: `/statusline` コマンドで JSON 駆動のコンテキスト使用率バーを即座に設定可能。LSP 連携の Code Intelligence プラグインがファイル読み込みを削減。CLAUDE.md は加算的・Skills/MCP は名前上書き・Hooks は全マージ という機能レイヤー規則が公式明確化。

## 2026-05-18
- **Hooks 28+イベント・5ハンドラータイプの全容**: `asyncRewake`（バックグラウンドから Claude を再起動）、`terminalSequence`（OSC通知）、`modifiedInput`（PreToolUse でツール入力を書き換え）、`mcp_tool` ハンドラー（MCP サーバー直接呼び出し）、`allowedEnvVars`（HTTP フック環境変数）が新規確認。`additionalContext` は10,000文字上限・指示形式NG。
- **サブエージェント管理の進化**: `/agents` UI でインタラクティブ作成・管理。`--agents` CLI フラグで一時エージェント定義。スコープ優先度: Managed > CLI > Project > User > Plugin。`memory: user` で `~/.claude/agent-memory/` に跨セッション永続化。Explore の thoroughness は quick/medium/very thorough。
- **settings.json 新項目・CLAUDE.md ↔ スキル住み分け・コンテキスト管理詳細**: `advisorModel`（セカンダリ推論モデル）、`cleanupPeriodDays: 0`（履歴無効化）、`disableAllHooks`。CLAUDE.md = 全セッション共通、Skills = オンデマンド。「2回修正ルール」公式化。`/rewind` の5オプション（会話のみ・コードのみ・両方・指定点以降を圧縮・指定点以前を圧縮）。

## 2026-05-11
- **Routines（スケジュール自動実行）**: プロンプト+リポジトリ+コネクタをセットにしてcron/API/GitHubイベントで自動実行。クラウドインフラ上で動くため深夜バッチも可。Pro:5回/日、Max:15回/日、Team/Enterprise:25回/日。
- **`/btw` コマンド（v2.1.72）**: 作業中のClaudeに割り込まず、会話履歴に残らない側聞きを実現。プロンプトキャッシュ再利用でコスト最小。サブエージェントとの対比:「既知情報→/btw / 新規調査→subagent」。
- **テレポーテーション & リモートコントロール**: `claude --remote` でCLIからクラウドセッション起動、`claude --teleport` でクラウド→ローカル転送、`/remote-control` でモバイル/Webからローカルセッション操作。claude.ai/code のAuto-fix PR でCI失敗を自動修正。

## 2026-05-04
- **`.claude/rules/` パススコープルール**: YAML `paths:` フロントマターで対象ファイル読み込み時のみルールをロード。CLAUDE.md 肥大化を構造的に解決。`~/.claude/rules/` でユーザーレベル共通設定も可。シンボリックリンクで複数プロジェクト共有可能。
- **検証ファースト原則 & AskUserQuestion インタビューパターン**: 「検証手段を与えること」が単一最大レバレッジポイント（公式確認）。大型機能前に Claude にインタビューさせて SPEC.md 生成 → 別セッションで実装がベストプラクティス化。
- **CLAUDE.md 組織管理強化**: マネージドポリシー CLAUDE.md（`/etc/claude-code/CLAUDE.md`）、AGENTS.md クロスツール互換 `@import`、`/compact` 後の再ロード動作（ルートは再注入・サブディレクトリは非自動）、`/memory` コマンドによる全メモリファイル管理、`--append-system-prompt` での強制指示注入。

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
