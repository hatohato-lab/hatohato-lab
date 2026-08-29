# HatoHato Lab

AIエージェントの評価設計（オラクル）の実証と、Claude Code の長期運用から生まれた道具を公開しています。

| 系統 | 本数 | 概要 |
|---|---|---|
| EDD エージェント | 13 | 1リポジトリ=1オラクルで、13種の採点手法を独立に実証：<br>・差分テスト<br>・決定的 golden<br>・メタモルフィック<br>・プロパティ往復<br>・統計検定（カイ二乗）<br>・ファジング<br>・仕様アサーション<br>・実行結果照合（SQL）<br>・LLM-as-Judge＋決定的ゲート<br>・査読のメタ評価×2<br>・文書構造検査<br>・行動回帰テスト＋統計 |
| Claude Code ツール | 6 | 複数セッション並行の実運用から切り出した道具。全て機械判定 eval 同梱：<br>・チャット引き継ぎ（hikitsugi）<br>・ルール同期（rules-sync）<br>・チャット間の黒板（kokuban）<br>・作業フォルダビューア（hatohatoscope）<br>・ルール退役の実測（rule-retirement-eval）<br>・チャットの容量計（context-meter） |
| その他 | 2 | ・formpilot（LangGraph ReAct＋Vision＋Playwright のフォーム自動入力）<br>・VBA_Tools（Excel マクロ集） |

共通するのは「正しさの判定を機械に、最終判断を人間に」という作り方です。
ツール群も自分の実環境で毎日使ってから公開しています。

## AI エージェント（オラクル種別ごと）

| リポジトリ | タスク | カテゴリ | 詳細説明 | 更新日 |
|---|---|---|---|---|
| [js-to-ts-migration-agent](https://github.com/hatohato-lab/js-to-ts-migration-agent) | JavaScript を TypeScript に書き換える | 差分テスト | 手書きの期待値を持たず、元の JS を node で実行した出力そのものを物差しにする差分テスト。候補 TS を tsc --strict でコンパイルし、実行出力が元と完全一致したときだけ PASS（3ケース、タイムアウト30秒）。selftest は正しい移植を通した上で、出力汚染とコンパイル破壊を全ケースに仕込んだ壊れ版6件が全て FAIL になることまで確認する。 | 2026-07-22 |
| [py2to3-agent](https://github.com/hatohato-lab/py2to3-agent) | Python 2 のコードを Python 3 に直す | 決定的 golden | 保存済みの正解出力（golden）と、移植後コードを Python 3 で実行した stdout の完全一致で判定する決定的オラクル。py_compile 通過も必須で、LLM 採点も乱数も使わない（3ケース、タイムアウト10秒）。ケース0件のときに「0/0 で合格」になる空合格は明示エラーで防ぐ。selftest は正例3件 PASS＋壊れ版6件 FAIL の両立を確認する。 | 2026-07-22 |
| [metamorphic-sort-agent](https://github.com/hatohato-lab/metamorphic-sort-agent) | ソートを自分で実装する | メタモルフィック | 正解表を持たず、ソートなら必ず成り立つ5性質（順序・多重集合保存・長さ保存・冪等・入力を並べ替えても同結果）を固定シード211本の入力で全数検査。組み込み sorted/.sort の使用はトークン解析で検出して失格にする（別名代入のすり抜けも防ぐ）。selftest は降順・重複除去・要素脱落の3種のバグ実装が必ずどれかの性質で落ちることを確認する。 | 2026-07-22 |
| [llm-judge-summary-agent](https://github.com/hatohato-lab/llm-judge-summary-agent) | 文章を 1 文に要約する | LLM-as-Judge | AI 審査員がルーブリック3軸（忠実性・網羅性・簡潔性、各0〜5点）＋捏造有無で採点し、その JSON を決定的ゲート「捏造なし かつ 忠実性≥4・網羅性≥3・簡潔性≥3」に通して合否を出す。LLM 出力の型揺れは検証で弾く（bool でない捏造フラグは不合格扱い）。selftest はゲート自体を6見本で検証し、4条件を1つずつ単独で落とせることを確かめる。 | 2026-07-22 |
| [monte-carlo-shuffle-agent](https://github.com/hatohato-lab/monte-carlo-shuffle-agent) | リストをランダムに並べ替える | モンテカルロ統計 | 毎回変わる出力を固定シードで数千回実行し、3関門で判定する統計オラクル。①妥当性（要素保存・入力非破壊、4形状×50回）②位置ごとの偏り（6要素×3000回のカイ二乗、しきい値40）③順列全体の偏り（4要素24通り×3000回、しきい値50 ≈ 自由度23・α=0.001）。位置だけ均等な巡回シフト型のズルは③で落とす。selftest はバグ実装4本の検出方法まで確認する。 | 2026-07-22 |
| [fuzz-robust-parser-agent](https://github.com/hatohato-lab/fuzz-robust-parser-agent) | 文字列から数字だけ取り出す（壊れた入力でも落ちない） | ファジング／暗黙オラクル | 正解出力を一切与えない暗黙オラクル。境界ケース24本＋固定シードのランダム文字列5000本（絵文字・全角・NUL・改行入り）を流し込み、「例外を投げない・必ず list を返す・要素は int（bool 不可）」の3不変条件が1件でも破れたら即 FAIL。selftest は例外型・非list型・非int型・bool混入の4種のバグ実装が全て落ちることを確認する。 | 2026-07-22 |
| [spec-divisor-finder-agent](https://github.com/hatohato-lab/spec-divisor-finder-agent) | 整数の約数を 1 つ見つける | 仕様アサーション | 答えが一通りでないため正解表を持たない仕様アサーション。n=2..5000 の全数で、返した約数 d が 1 < d < n かつ n を割り切るか、None なら n が本当に素数かを、オラクル自身の素数判定で独立に検算する。bool を int と認めない型検査つき。selftest は「常に2を返す」「n 自身を返す」「平方数を素数と誤判定」の3種のバグ実装を検出できることを確認する。 | 2026-07-22 |
| [roundtrip-rle-agent](https://github.com/hatohato-lab/roundtrip-rle-agent) | 文字列を圧縮して元に戻す | プロパティ／往復 | どんな入力でも decode(encode(x)) == x が成り立つかを、際どい手書き10件＋固定シード乱数4000件の計4010件で全数検査するプロパティオラクル。恒等変換のごまかしを防ぐため「長い反復入力では encode 結果が元より短くなる」圧縮性も4文字×3長さ=12点で要求。selftest は個数ズレ2種＋恒等実装1種のバグが必ず落ちることを確認する。 | 2026-07-22 |
| [text-to-sql-agent](https://github.com/hatohato-lab/text-to-sql-agent) | 日本語の質問を SQL に直す | 実行オラクル | SQLite のインメモリ DB に固定スキーマと5行のデータを毎回作り直し、お手本 SQL と候補 SQL を両方実行して結果集合同士を比較する実行オラクル（行順は不問。書き方の違いは結果で吸収）。5問（件数・抽出・グループ平均・最大・条件付き件数）の全問一致で PASS。selftest は部署の取り違え・WHERE 忘れ・構文エラーの3種を落とせることを確認する。 | 2026-07-22 |
| [agent-spec-reviewer](https://github.com/hatohato-lab/agent-spec-reviewer) | 他のエージェント定義を点検する（メタ） | 査読検出力 | わざと欠陥を仕込んだエージェント定義4種＋正常1種のラベル付き見本を査読させ、検出した問題キーの集合が正解ラベルと完全一致するかで「査読の検出力」を採点。見逃しも過剰報告も語彙外キーも FAIL。深さは quick/standard/deep の3段（deep は公式ドキュメントと照合）。selftest は「全部OKと言う盲目版」「陳腐化を見逃す浅い版」「何でも指摘する過剰版」の3種の駄目査読が落ちることを確認する。 | 2026-07-22 |
| [harness-lens-reviewer](https://github.com/hatohato-lab/harness-lens-reviewer) | 設計・計画を6レンズで点検する（メタ） | 査読検出力（6レンズ） | 設計・計画の文章を6レンズ（複雑性配置・Eval-Driven・Bitter Lesson・分業不能性・劇場労働回避・用語射程）で点検させ、赤にしたレンズ集合がラベル付き見本7件（正常1＋各レンズ専用の欠陥6）の正解と完全一致するかで採点。見逃し（recall）も余計な赤（precision）も FAIL。selftest は盲目版・1レンズ見逃し版・全部赤にする過検出版の3種を落とせることを確認する。 | 2026-07-22 |
| [fractal-spec-agent](https://github.com/hatohato-lab/fractal-spec-agent) | 要件・Issueからフラクタル構造の設計書＋用語集を生成する | 文書構造検査 | 生成された設計書ツリーを構造規則 R1〜R9（1枚45行以内・枠の表2〜9行・全ノード同形式・リンク切れゼロ・孤児ゼロ・監視語の定義義務・PlantUML 図必須と横向き禁止・配置規則・推定の隔離）で機械検査し、違反0件で PASS。しきい値は設定で上書き可。selftest は41項目で、わざと壊した見本12種を「正しい規則で検出し、他の規則を誤検出しない」の2本立てで検証し、HTML 出力・図の描画・入力欠落の検出まで含む。 | 2026-08-15 |
| [rule-retirement-eval](https://github.com/hatohato-lab/rule-retirement-eval) | AIへのルールが今のモデルにまだ必要かを実測でふるい分ける | 行動回帰テスト＋統計 | ルール本文を見せずに失敗場面の再現タスクを3変種×7回以上（最低21試行）実行し、再発をチェッカー（保存先違反・削除コマンド・表の分割・禁止語・長さ超過の5種プラグイン）で機械判定。再発1回でも KEEP、再発ゼロは Clopper–Pearson の95%信頼上限（0/21 ≒ 13.3%）を併記して退役候補に留め、最終判断は人間に残す。selftest 21項目には、チェッカーを盲目版に差し替えると判定に差が出る対照実験を含む。 | 2026-08-09 |

各リポジトリの構成と確かめ方：

- 構成は「エージェント定義 ＋ 外部オラクル（`--selftest` 内蔵）＋ 設計メモ ＋ README」
- `python eval/oracle.py --selftest` を実行すると、正しい実装は PASS・既知バグ実装は FAIL になることを再現できます（オラクル自身の検証）
- 専門用語を使わずに中身を説明した「説明書.md」も各リポジトリに同梱しています
- アカウント全体の説明は本リポジトリの [説明書.md](./説明書.md)

## Claude Code ツール

Claude Code の長期運用で必要に迫られて作った道具群。毎日の実作業で使い続け、実用に耐えると確かめてから公開しています。

| リポジトリ | タスク | カテゴリ | 詳細説明 | 更新日 |
|---|---|---|---|---|
| [claude-code-hikitsugi](https://github.com/hatohato-lab/claude-code-hikitsugi) | 新しいチャットで「◯◯のチャットを継いで」と一言いうだけで、前のチャットの続きから再開できる。無傷の生ログ（.jsonl）から引き継ぎメモを自動再建する。 | セッション間通信（過去→未来） | 完全ローカル・通信なし。秘密情報のマスキング既定ON。機械判定20項目の eval つき。 | 2026-08-11 |
| [rule-retirement-eval](https://github.com/hatohato-lab/rule-retirement-eval) | 溜まった CLAUDE.md・ルールのうち「今のモデルにはもう不要なもの」を実測でふるい分ける。ルール無しで失敗場面を再現させ、再発するかを機械判定。 | ルールの実測評価（行動回帰） | 実施例つき（ルール有り/無しの2群×各105試行でルール2本を退役、「有っても破られるルール」1本を発見）。上のオラクル一覧にも掲載（行動回帰テスト型）。 | 2026-08-09 |
| [hatohatoscope](https://github.com/hatohato-lab/hatohatoscope) | AIとの開発で膨らむ作業フォルダ全体を、ブラウザ1枚で「見る・探す・読む」。ファイル名の一部で即検索、Markdown＋Mermaid描画、新しいファイルは自動でツリーに反映。 | ローカルビューア | 「AIが増やす情報量 × 増えない人間の認知」のギャップを埋める読書用ビューア（README に認知科学の論文参照つき）。localhost 専用設計。毎日自分で使用中。 | 2026-08-23 |
| [claude-code-rules-sync](https://github.com/hatohato-lab/claude-code-rules-sync) | ルール（.claude/rules）を書き換えると、開いている全チャットに「読み直して」が自動で届く。ルールは開始時に一度しか読まれない、という隙間を埋めるフック。 | セッション間通信（1→全・放送） | セッション別に変更を検知し、同じ変更は1回だけ通知。新規チャットには通知しない（開始時に読めているため）。機械判定14項目の eval つき。 | 2026-08-15 |
| [claude-code-kokuban](https://github.com/hatohato-lab/claude-code-kokuban) | 複数チャット（セッション）同士が連絡を書き合う「黒板」。フォルダが宛先、Markdownファイルが書き込み、ファイル名の 新着_/既読_ が未読既読。フックが本人の発言のたびに新着を検知してClaudeに知らせる。 | セッション間通信（双方向） | サーバ・常駐プロセスなし。フェイルオープン設計。機械判定6項目の eval つき。hikitsugi（時間方向）・rules-sync（放送）と同じ仕組みの家族で、こちらはチャット⇔チャットの双方向。 | 2026-08-29 |
| [claude-code-context-meter](https://github.com/hatohato-lab/claude-code-context-meter) | 稼働中の全チャットのコンテキスト使用量・上限比・残り・実メモリ・チャット名を1つの表で出す容量計 | コンテキスト計測 | 使用量は生ログ末尾の usage（input＋cache_creation＋cache_read）から読む（ファイルサイズは使用量ではない）。70%で警告、コンパクティング2回以上で乗り換え検討を提示。実測データ（発動点ほぼ100%・残存約8.7%・停止98〜270秒）を README に収録。Windows/Linux 両対応・標準ライブラリのみ。機械判定8項目の eval つき。 | 2026-08-27 |

## その他の公開リポジトリ

| リポジトリ | タスク | 詳細説明 | 更新日 |
|---|---|---|---|
| [formpilot](https://github.com/hatohato-lab/formpilot) | PDF データから Web フォームへ自動入力する AI エージェント | LangGraph ReAct + Claude Vision + Playwright の構成。PDFの読み取りから画面操作までを一気通貫で自動化。 | 2026-07-22 |
| [VBA_Tools](https://github.com/hatohato-lab/VBA_Tools) | Excel 業務効率化の VBA マクロ集 | 結合・一覧・抽出・監査など、日常の Excel 作業を自動化する実用ツール群。 | 2026-07-22 |
