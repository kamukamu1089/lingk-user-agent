# Codex マーケットプレイス登録レポート

作業日: 2026-09-02

## 目的

「今できたmarketplaceを他のAIにも読み込める形のやつを作ってください」という依頼を受けた。直前のタスクでClaude Code用の `.claude-plugin/marketplace.json` を登録済みだったため、この「他のAI」はこのリポジトリがもともと対象としているもう一方のクライアントであるCodexを指すと判断した。CodexにはClaude Codeとは別のマーケットプレイス規約があるため、既存の `.codex-plugin/plugin.json` をCodex側からもインストール可能にするマーケットプレイスマニフェストを追加した。

## 実施内容

- Codex CLI付属の `plugin-creator` システムスキル(`~/.codex/skills/.system/plugin-creator/`)の仕様を確認し、Codexのmarketplace.jsonが以下の規約であることを確認した。
  - リポ/チーム用マーケットプレイスは `<repo-root>/.agents/plugins/marketplace.json` に置く。
  - `source.path` は「マーケットプレイスroot」(`.agents` の親ディレクトリ、= repo-root)からの相対パスで解決される。scaffoldスクリプトが生成する既定値 `./plugins/<plugin-name>` は、そのスクリプトが常に `<root>/plugins/<name>` へ新規プラグインを作る前提の値であり、パス解決自体はrootからの相対パスという一般規則に従う。
- 本リポジトリはプラグイン本体(`.codex-plugin/plugin.json`)がリポジトリrootに直接あるため、`source.path` を `"./"` としてrepo-root自身を指す形にした(Claude Code側で `source: "./"` を使ったのと同じ考え方)。
- `.agents/plugins/marketplace.json` を新規追加した。
  ```json
  {
    "name": "lingk-user-agent-marketplace",
    "interface": { "displayName": "lingk User Agent" },
    "plugins": [
      {
        "name": "lingk-user-agent",
        "source": { "source": "local", "path": "./" },
        "policy": { "installation": "AVAILABLE", "authentication": "ON_INSTALL" },
        "category": "Developer Tools"
      }
    ]
  }
  ```
- README を更新し、「Installing for Codex」節を新設して `codex plugin marketplace add` → `codex plugin add lingk-user-agent@lingk-user-agent-marketplace` の手順、および今後の更新はハンドエディットではなく `plugin-creator` のcachebuster/reinstallフローを使う旨を記載した。Contents一覧と検証コマンド例(`read_marketplace_name.py` の呼び出し方)も追記した。

## 設計上の判断

- マーケットプレイス名は `lingk-user-agent-marketplace` とし、Claude Code側で使った名前と揃えた(識別子規則 `[A-Za-z0-9_-]+` に適合)。
- 個人用マーケットプレイス(`~/.agents/plugins/marketplace.json`)ではなく、リポジトリに同梱するrepo/team形式を選んだ。これはClaude Code側で `--scope project` を選んだ判断と対称的(このワークスペース固有のプラグインを、個人設定ではなくリポジトリ自体に閉じ込める)。
- `plugin-creator` スキルの既定scaffoldは新規プラグインを `<root>/plugins/<name>/` に作る前提だが、本リポジトリは既存の単一プラグインがrepo root直下にある構成のため、その既定レイアウトには従わずrootから直接指す `"./"` を採用した。

## 検証

| 検証 | 結果 |
| --- | --- |
| `python3 .../plugin-creator/scripts/validate_plugin.py /home/moach/lingk/lingk-user-agent`(既存の `.codex-plugin/plugin.json`) | `Plugin validation passed` |
| `python3 .../plugin-creator/scripts/read_marketplace_name.py --marketplace-path .../.agents/plugins/marketplace.json` | `lingk-user-agent-marketplace` を正しく出力(JSON構文・`name`フィールドの妥当性を確認) |
| `codex plugin marketplace add` / `codex plugin add` の実機確認 | 未実施。このマシンに `codex` CLI本体が導入されていないため(`command not found`)。マニフェスト自体の妥当性はplugin-creator付属スクリプトで検証済みだが、CLIによるインストール・有効化の実地確認はできていない |

## 制限と次の作業

- `codex` CLI未導入のため、実際の `codex plugin marketplace add` / `codex plugin add` / スキル一覧反映は未検証。Codex CLIが使える環境でユーザー側に最終確認してもらう必要がある。
- `.agents/plugins/marketplace.json` はローカルパス参照(`source: "local"`)のため、このチェックアウトを別マシン/別パスへ複製した場合は `codex plugin marketplace add` をそのパスで再実行する必要がある(Claude Code側の `.claude-plugin/marketplace.json` と同じ制約)。
- 今後この既存プラグインをローカルで更新する際は、`plugin-creator` の方針どおり `.agents/plugins/marketplace.json` を手編集せず、`read_marketplace_name.py` → `update_plugin_cachebuster.py` → `codex plugin add <name>@<marketplace-name>` の順で反映すること。
