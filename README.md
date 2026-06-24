# HatoHato Lab

評価駆動開発（EDD）で AI エージェントを作っています。
各エージェントは「**出力の正しさをどう機械判定するか（オラクル）**」を 1 本ずつ別の手法で実証する構成です。薄いラッパーの量産ではなく、リポジトリごとに違う"物差し"を持たせています。

## AI エージェント（オラクル種別ごと）

| リポジトリ | タスク（何をする） | 正しさの測り方（どう採点するか） |
|---|---|---|
| [js-to-ts-migration-agent](https://github.com/hatohato-lab/js-to-ts-migration-agent) | JavaScript を TypeScript に書き換える | 元の JS を実行した出力と、変換後の出力が一致するか（差分テスト） |
| [py2to3-agent](https://github.com/hatohato-lab/py2to3-agent) | Python 2 のコードを Python 3 に直す | 保存した正解の出力と、実行結果が一致するか（golden） |
| [metamorphic-sort-agent](https://github.com/hatohato-lab/metamorphic-sort-agent) | ソートを自分で実装する | 正解表を持たず「ソートなら必ず成り立つ性質」で確かめる（メタモルフィック） |
| [llm-judge-summary-agent](https://github.com/hatohato-lab/llm-judge-summary-agent) | 文章を 1 文に要約する | AI の審査員が基準表で採点し、その点数を固定ルールで合否にする（LLM 審査） |
| [monte-carlo-shuffle-agent](https://github.com/hatohato-lab/monte-carlo-shuffle-agent) | リストをランダムに並べ替える | 毎回変わる出力を多数回実行し、偏りが無いか統計で確かめる（モンテカルロ） |
| [fuzz-robust-parser-agent](https://github.com/hatohato-lab/fuzz-robust-parser-agent) | 文字列から数字だけ取り出す（壊れた入力でも落ちない） | 大量のランダム入力で「落ちない・型が崩れない」を確かめる（ファジング） |
| [spec-divisor-finder-agent](https://github.com/hatohato-lab/spec-divisor-finder-agent) | 整数の約数を 1 つ見つける | 答えが一通りでないので「本当に割り切るか／素数か」を独立に検算（仕様確認） |
| [roundtrip-rle-agent](https://github.com/hatohato-lab/roundtrip-rle-agent) | 文字列を圧縮して元に戻す | 圧縮してから戻すと元に一致するか（往復・round-trip） |

各リポジトリは「エージェント定義 ＋ 外部オラクル（`--selftest` 内蔵）＋ 設計メモ ＋ README」で構成。
`python eval/oracle.py --selftest` で、正しい実装は PASS・既知バグ実装は FAIL になることを再現できます（オラクル自身の検証）。

## その他の公開リポジトリ

- [formpilot](https://github.com/hatohato-lab/formpilot) — PDF データから Web フォームへ自動入力する AI エージェント（LangGraph ReAct + Claude Vision + Playwright）
- [VBA_Tools](https://github.com/hatohato-lab/VBA_Tools) — VBA ツール集
