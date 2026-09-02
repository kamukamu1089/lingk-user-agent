# test_lingk マルチLLMプラグイン実装レポート

作業日: 2026-09-02

## 目的

承認済みの「test_lingk マルチLLM対応ユーザーエージェント：ひな形実装計画」に従い、CodexとClaude Codeから同じSkillを利用できる最小プラグインを実装する。

## 実施内容

- ベンダー中立な `skills/use-test-lingk/SKILL.md` を追加した。
- runtime／compile-timeパラメータの参照資料を追加した。
- テキスト／バイナリ出力の参照資料を追加した。
- Codex用とClaude Code用のmanifestを、それぞれ薄いアダプターとして追加した。
- リポジトリの共通開発規則を `AGENTS.md` に追加した。
- Claude Code開発セッション用の薄い `CLAUDE.md` を追加した。
- READMEを目的、対応範囲、読み込み方法、検証方法、非対応範囲が分かる内容へ更新した。

## 設計上の判断

- 研究知識と安全規則は共通Skillに一元化し、製品別manifestへ複製しない。
- 現行 `test_lingk` に独立CLIがないため、Makefile、namelist、実行ファイル、gnuplotを現在の実行インターフェースとして扱う。
- 既存の `data/` を保護するため、実行前に分離したcaseディレクトリを用意する方針をSkillへ記載した。
- MCP、hooks、独自agents、marketplace設定は実体がないため追加しなかった。
- Claude Code公式仕様では、プラグインルートの `CLAUDE.md` はインストール時の利用者コンテキストとしてロードされない。そのため、利用者向け指示はSkillへ置き、`CLAUDE.md` はこのリポジトリの開発用入口に限定した。

## 検証

以下を実施した。

| 検証 | 結果 |
| --- | --- |
| Skill validator | `Skill is valid!` |
| Codex plugin validator | `Plugin validation passed` |
| Claude Code `claude plugin validate .` | 合格。ルート `CLAUDE.md` はplugin利用時のcontextにならないという警告1件のみ |
| 両manifestのJSON構文 | `python3 -m json.tool` で合格 |
| placeholder、固定ホームパス、credential語の検索 | 問題なし |
| whitespace／patch整合性 | `git diff --check` で問題なし |

### 隔離スモークテスト

`test_lingk` の `src/`、`Makefile`、`param.namelist` だけを `/tmp` の新規caseへコピーし、空の `data/` を作ってテストした。

1. 最初の試行は、作業ディレクトリを `/tmp` にした後も相対元パス `../test_lingk` を使ったため、コピー前に失敗した。リポジトリや既存結果への変更はない。
2. 絶対元パスを使ってcaseを作り直し、`make lingk` でGNU Fortranビルドに成功した。
3. `timeout 150 ./lingk.exe` を実行し、約41秒、436 time stepsでsimulation time limitまで正常終了した。
4. 新規case内で、非空の `data/frq.001`、`data/mominzt.001`、複数の `data/fkinzv_im0007_t*.dat` を確認した。
5. 元の `test_lingk` とその `data/` は変更していない。

このテストはビルド、終了状態、生成ファイルの確認であり、独立した収束判定や物理的妥当性検証ではない。

## 制限と次の作業

- 実計算は既存成果物を保護するcase分離方法を確定してから行う。
- 科学的な収束・妥当性判定は未実装。
- Codex／Claude Code marketplaceへの登録は未実施。
- 将来は `test_lingk` 側へ独立CLIと小さな回帰ケースを追加することが望ましい。
