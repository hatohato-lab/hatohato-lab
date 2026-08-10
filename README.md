# HatoHato Lab

AI agents built with evaluation-driven development (EDD) — each repository demonstrates a different test oracle.<br>
The rest of this README is written in Japanese.

評価駆動開発（EDD）で AI エージェントを作っています。
各エージェントは「**出力の正しさをどう機械判定するか（オラクル）**」を 1 本ずつ別の手法で実証する構成です。薄いラッパーの量産ではなく、リポジトリごとに違う「物差し」を持たせています。

## AI エージェント（オラクル種別ごと）

| リポジトリ | タスク（何をする） | カテゴリ | 詳細説明（どう採点するか） |
|---|---|---|---|
| [js-to-ts-migration-agent](https://github.com/hatohato-lab/js-to-ts-migration-agent) | JavaScript を TypeScript に書き換える | 差分テスト | 元の JS を実行した出力と、変換後の出力が一致するかで判定。手書きの正解を使わず、元コードそのものを物差しにする。 |
| [py2to3-agent](https://github.com/hatohato-lab/py2to3-agent) | Python 2 のコードを Python 3 に直す | 決定的 golden | あらかじめ保存した正解の出力（golden）と、移植後の実行結果が一致するかで判定。 |
| [metamorphic-sort-agent](https://github.com/hatohato-lab/metamorphic-sort-agent) | ソートを自分で実装する | メタモルフィック | 正解表を持たず「ソートなら必ず成り立つ性質」（順序・要素保存・冪等・置換不変・長さ保存）で判定。 |
| [llm-judge-summary-agent](https://github.com/hatohato-lab/llm-judge-summary-agent) | 文章を 1 文に要約する | LLM-as-Judge | AI の審査員が基準表（ルーブリック）で採点し、その点数を固定ルールで PASS/FAIL に変換。 |
| [monte-carlo-shuffle-agent](https://github.com/hatohato-lab/monte-carlo-shuffle-agent) | リストをランダムに並べ替える | モンテカルロ統計 | 毎回変わる出力を多数回実行し、各位置に各要素が均等に来るか（カイ二乗）で偏りを判定。 |
| [fuzz-robust-parser-agent](https://github.com/hatohato-lab/fuzz-robust-parser-agent) | 文字列から数字だけ取り出す（壊れた入力でも落ちない） | ファジング／暗黙オラクル | 大量のランダム入力で「例外で落ちない・必ずリストを返す・要素は整数」の不変条件が破れないか判定。 |
| [spec-divisor-finder-agent](https://github.com/hatohato-lab/spec-divisor-finder-agent) | 整数の約数を 1 つ見つける | 仕様アサーション | 答えが一通りでないので、返り値が本当に割り切るか・None なら本当に素数かをオラクルが独立に検算。 |
| [roundtrip-rle-agent](https://github.com/hatohato-lab/roundtrip-rle-agent) | 文字列を圧縮して元に戻す | プロパティ／往復 | どんな入力でも decode(encode(x)) が元に一致するか（往復）＋反復は短くなるか（圧縮性）で判定。 |
| [text-to-sql-agent](https://github.com/hatohato-lab/text-to-sql-agent) | 日本語の質問を SQL に直す | 実行オラクル | SQL を実際に DB で動かし、出てくる結果（答え）が一致するかで判定。書き方の違いは結果で吸収する。 |
| [agent-spec-reviewer](https://github.com/hatohato-lab/agent-spec-reviewer) | 他のエージェント定義を点検する（メタ） | 査読検出力 | わざと欠陥を仕込んだ定義の見本を、査読が全部見つけ正例を通せるかで判定（depth で粘りを調整）。 |
| [harness-lens-reviewer](https://github.com/hatohato-lab/harness-lens-reviewer) | 設計・計画を6レンズで点検する（メタ） | 査読検出力（6レンズ） | わざと特定レンズに引っかかる見本で「正しいレンズを赤に・良い設計は通す」かで判定（劇場労働・複雑性転嫁・eval なし 等）。 |
| [fractal-spec-agent](https://github.com/hatohato-lab/fractal-spec-agent) | 要件・Issueからフラクタル構造の設計書＋用語集を生成する | 文書構造検査 | 「枠1枚→要素→また枠」の構造規則（1枚の分量上限・枠の行数・全ノード同形式・リンク完全性・用語の定義義務）を機械検査。わざと壊した見本6種を1見本1違反で検出できるかで自己検証。 |
| [rule-retirement-eval](https://github.com/hatohato-lab/rule-retirement-eval) | AIへのルールが今のモデルにまだ必要かを実測でふるい分ける | 行動回帰テスト＋統計 | ルール本文を見せずに失敗場面の再現タスクを21試行以上実行し、再発を検出器で機械判定。再発ゼロでも95%信頼上限（Clopper–Pearson）を併記し、退役の最終判断は人間に残す。実測でルール2本を退役・1本の形骸化を発見した実施例つき。 |

各リポジトリは「エージェント定義 ＋ 外部オラクル（`--selftest` 内蔵）＋ 設計メモ ＋ README」で構成。
`python eval/oracle.py --selftest` で、正しい実装は PASS・既知バグ実装は FAIL になることを再現できます（オラクル自身の検証）。
各リポジトリには、専門用語を使わずに中身を説明した「説明書.md」も同梱しています（アカウント全体の説明は本リポジトリの [説明書.md](./説明書.md)）。

## Claude Code ツール

Claude Code を長期運用する中で生まれた実用ツール。自分の実環境でドッグフーディングしてから公開しています。

| リポジトリ | 何をする | 特徴 |
|---|---|---|
| [claude-code-hikitsugi](https://github.com/hatohato-lab/claude-code-hikitsugi) | 新しいチャットで「◯◯のチャットを継いで」と一言いうだけで、前のチャットの続きから再開できる。無傷の生ログ（.jsonl）から引き継ぎメモを自動再建する。 | 完全ローカル・通信なし。秘密情報のマスキング既定ON。機械判定20項目の eval つき。 |
| [rule-retirement-eval](https://github.com/hatohato-lab/rule-retirement-eval) | 溜まった CLAUDE.md・ルールのうち「今のモデルにはもう不要なもの」を実測でふるい分ける。ルール無しで失敗場面を再現させ、再発するかを機械判定。 | 実施例つき（ルール有り/無しの2群×各105試行でルール2本を退役、「有っても破られるルール」1本を発見）。上のオラクル一覧にも掲載（行動回帰テスト型）。 |
| [hatohatoscope](https://github.com/hatohato-lab/hatohatoscope) | AIとの開発で膨らむ作業フォルダ全体を、ブラウザ1枚で「見る・探す・読む」。ファイル名の一部で即検索、Markdown＋Mermaid描画、新しいファイルは自動でツリーに反映。 | 「AIが増やす情報量 × 増えない人間の認知」のギャップを埋める読書用ビューア（README に認知科学の論文参照つき）。localhost 専用設計。毎日ドッグフーディング中。 |

## その他の公開リポジトリ

| リポジトリ | 何をする | 特徴 |
|---|---|---|
| [formpilot](https://github.com/hatohato-lab/formpilot) | PDF データから Web フォームへ自動入力する AI エージェント | LangGraph ReAct + Claude Vision + Playwright の構成。PDFの読み取りから画面操作までを一気通貫で自動化。 |
| [VBA_Tools](https://github.com/hatohato-lab/VBA_Tools) | Excel 業務効率化の VBA マクロ集 | 結合・一覧・抽出・監査など、日常の Excel 作業を自動化する実用ツール群。 |
