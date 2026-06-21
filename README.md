# HatoHato Lab

評価駆動開発（EDD）で AI エージェントを作っています。
各エージェントは「**出力の正しさをどう機械判定するか（オラクル）**」を 1 本ずつ別の手法で実証する構成です。薄いラッパーの量産ではなく、リポジトリごとに違う"物差し"を持たせています。

## AI エージェント（オラクル種別ごと）

| リポジトリ | タスク | 正しさの測り方（オラクル） |
|---|---|---|
| [js-to-ts-migration-agent](https://github.com/hatohato-lab/js-to-ts-migration-agent) | JavaScript → TypeScript 移行 | 差分テスト（元コードの実行結果を基準にする） |
| [py2to3-agent](https://github.com/hatohato-lab/py2to3-agent) | Python 2 → 3 移植 | 決定的 golden（保存した期待出力と一致） |
| [metamorphic-sort-agent](https://github.com/hatohato-lab/metamorphic-sort-agent) | ソートを手書き実装 | メタモルフィック（正しければ必ず成り立つ関係） |
| [llm-judge-summary-agent](https://github.com/hatohato-lab/llm-judge-summary-agent) | 文章を 1 文に要約 | LLM-as-Judge ＋ 決定的ゲート |
| [monte-carlo-shuffle-agent](https://github.com/hatohato-lab/monte-carlo-shuffle-agent) | リストをランダムに並べ替え | モンテカルロ統計（多数回＋カイ二乗で一様性） |
| [fuzz-robust-parser-agent](https://github.com/hatohato-lab/fuzz-robust-parser-agent) | 任意の文字列から整数を抽出 | ファジング／暗黙オラクル（大量ランダム入力で不変条件） |
| [spec-divisor-finder-agent](https://github.com/hatohato-lab/spec-divisor-finder-agent) | 整数の約数を 1 つ返す | 仕様アサーション（出力が一意でなくても仕様で検証） |
| [roundtrip-rle-agent](https://github.com/hatohato-lab/roundtrip-rle-agent) | 文字列の RLE 符号化・復号 | プロパティ／往復（decode(encode(x))==x） |

各リポジトリは「エージェント定義 ＋ 外部オラクル（`--selftest` 内蔵）＋ 設計メモ ＋ README」で構成。
`python eval/oracle.py --selftest` で、正しい実装は PASS・既知バグ実装は FAIL になることを再現できます（オラクル自身の検証）。

## その他の公開リポジトリ

- [formpilot](https://github.com/hatohato-lab/formpilot) — PDF データから Web フォームへ自動入力する AI エージェント（LangGraph ReAct + Claude Vision + Playwright）
- [VBA_Tools](https://github.com/hatohato-lab/VBA_Tools) — VBA ツール集
