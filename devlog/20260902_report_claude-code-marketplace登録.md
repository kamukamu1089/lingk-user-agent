# Claude Code マーケットプレイス登録レポート

作業日: 2026-09-02

## 目的

ユーザーから「lingk-user-agentのプラグインを有効化してほしい」という依頼を受けた。Claude Codeの `enabledPlugins` 設定は `"<plugin>@<marketplace>"` 形式のキーでのみ管理され、マーケットプレイス登録済みのプラグインにしか使えないため、`claude plugin enable` を成立させるにはマーケットプレイス化が必須だった。README にはこれまで「マーケットプレイスのメタデータは意図的に含めていない」と明記されていたため、方針変更としてユーザーに確認したうえで実施した。

## 実施内容

- `.claude-plugin/marketplace.json` を新規追加した。単一プラグイン `lingk-user-agent`(`source: "./"`)を指すローカルディレクトリ型マーケットプレイスとした。
- `claude plugin marketplace add /home/moach/lingk/lingk-user-agent --scope project` を実行し、`/home/moach/lingk/.claude/settings.json` に `extraKnownMarketplaces` を登録した。
- `claude plugin install lingk-user-agent@lingk-user-agent-marketplace --scope project -y` を実行し、同ファイルに `enabledPlugins: {"lingk-user-agent@lingk-user-agent-marketplace": true}` が書き込まれた。
- README の「Development loading」節を「Installing for Claude Code」節へ書き換え、マーケットプレイス経由のインストール手順・スコープの意味・`--plugin-dir` によるセッション限定読み込みの両方を併記した。Contents一覧と検証コマンド例も更新した。

## 設計上の判断

- スコープは `project`(呼び出し時のプロジェクトの `.claude/settings.json`)を選択した。全プロジェクトで有効化する `user` スコープではなく、`lingk-user-agent` を必要とするこのワークスペース(`/home/moach/lingk`)に限定した。
- `--plugin-dir` によるセッション限定読み込みの説明はREADMEに残した。設定ファイルを変更せずに素早く動作確認したい開発者向けの経路として引き続き有効なため。
- Codex向けのマーケットプレイス化は範囲外とした。CodexとClaude Codeのマニフェストは薄いアダプターとして独立しており、今回の変更はClaude Code側の `.claude-plugin/` のみに閉じている。

## 検証

| 検証 | 結果 |
| --- | --- |
| `claude plugin validate /home/moach/lingk/lingk-user-agent`(marketplace.json) | 合格(初回は説明文欠如の警告1件、`description` 追加後は無警告で合格) |
| `claude plugin validate .claude-plugin/plugin.json`(plugin.json、既存) | 合格。ルート `CLAUDE.md` がplugin context扱いされない旨の警告1件のみ(既知・対応不要) |
| `claude plugin marketplace add ... --scope project` | `Successfully added marketplace: lingk-user-agent-marketplace (declared in project settings)` |
| `claude plugin install ... --scope project` | `Successfully installed plugin: lingk-user-agent@lingk-user-agent-marketplace (scope: project)` |
| `claude plugin list --json` | `enabled: true`、`scope: "project"`、`projectPath: "/home/moach/lingk"` を確認 |
| 別プロセスでの `claude -p` 実行 | `lingk-user-agent:use-test-lingk` スキルが利用可能スキル一覧に現れることを確認。既存の対話セッションは起動時にプラグインを読み込むため、このセッション自体には遡って反映されない(次回起動から有効) |

## 制限と次の作業

- 現在動作中だったこのセッションは `--plugin-dir` なし・マーケットプレイス未登録の状態で起動していたため、当該セッション自身には反映されない。次回、当該プロジェクトディレクトリで新しいセッションを開始すれば自動的に有効化される。
- Codex側のマーケットプレイス相当の仕組みは今回スコープ外。必要になれば別タスクとして計画する。
- `.claude-plugin/marketplace.json` はローカルパス参照(`directory` source)のため、このチェックアウトを別マシン/別パスへ複製した場合は `claude plugin marketplace add` をそのパスで再実行する必要がある。
