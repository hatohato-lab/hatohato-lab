# HatoHato Lab

LLM の扱い方の知識の木、Claude Code の道具、AI エージェントを公開しています。
下の表が、公開しているリポジトリの一覧です（29本）。
AI エージェントの小項目は、答えを自動で検証する方法です。1本に1つずつ試しました。

| 大項目 | 小項目 | リポジトリ | 一言の説明 |
|---|---|---|---|
| 知識の木 | LLM の扱い方 | [llm-harness-knowledge-tree](https://github.com/hatohato-lab/llm-harness-knowledge-tree) | LLM の扱い方を、根拠の段階つきで分類した日本語の知識の木（葉326枚） |
| Claude Code の道具 | セッション間の連絡 | [claude-code-hikitsugi](https://github.com/hatohato-lab/claude-code-hikitsugi) | 前のチャットの続きを、一言で新しいチャットに引き継ぐ |
| Claude Code の道具 | セッション間の連絡 | [claude-code-rules-sync](https://github.com/hatohato-lab/claude-code-rules-sync) | ルールを書き換えたら、開いている全チャットに知らせる |
| Claude Code の道具 | セッション間の連絡 | [claude-code-kokuban](https://github.com/hatohato-lab/claude-code-kokuban) | チャット同士が連絡を書き合う黒板 |
| Claude Code の道具 | 画面の部品（MOD） | [claude-code-session-info](https://github.com/hatohato-lab/claude-code-session-info) | 開いている全セッションを、1行ずつの表で見る |
| Claude Code の道具 | 画面の部品（MOD） | [claude-code-session-list](https://github.com/hatohato-lab/claude-code-session-list) | セッション間の連絡を、矢印の図で見る |
| Claude Code の道具 | 画面の部品（MOD） | [claude-code-active-rules](https://github.com/hatohato-lab/claude-code-active-rules) | いま読み込まれているルールを、行数と文字数の表で見る |
| Claude Code の道具 | 容量の計測 | [claude-code-context-meter](https://github.com/hatohato-lab/claude-code-context-meter) | 全チャットの使用量と残りを、1つの表で出す |
| Claude Code の道具 | 容量の計測 | [claude-code-chat-doctor](https://github.com/hatohato-lab/claude-code-chat-doctor) | 続行か新チャットかを、根拠つきの1行で勧める |
| Claude Code の道具 | ルールの実測 | [rule-retirement-eval](https://github.com/hatohato-lab/rule-retirement-eval) | ルールが今のモデルにまだ要るかを、実測で分ける |
| Claude Code の道具 | ビューア | [hatohatoscope](https://github.com/hatohato-lab/hatohatoscope) | 作業フォルダー全体を、ブラウザ1枚で見る、探す、読む |
| Claude Code の道具 | 評価駆動の学習 | [oracle-compass](https://github.com/hatohato-lab/oracle-compass) | 評価駆動開発を、5歩の対話で身につける |
| Claude Code の道具 | 改善の地図 | [kaizen-map](https://github.com/hatohato-lab/kaizen-map) | システムを走査し、地図と改善候補を HTML 1枚にする |
| Claude Code の道具 | バージョン監視 | [claude-code-version-guard](https://github.com/hatohato-lab/claude-code-version-guard) | Claude Code の版の遅れを見張る |
| Claude Code の道具 | 作業の分解（スキル） | [claude-code-checklist](https://github.com/hatohato-lab/claude-code-checklist) | 作業を分け、チェックリストと設計書で進める |
| AI エージェント | 差分テスト（元の出力と比べる） | [js-to-ts-migration-agent](https://github.com/hatohato-lab/js-to-ts-migration-agent) | JavaScript を TypeScript に書き換える |
| AI エージェント | 正解と比べる（golden） | [py2to3-agent](https://github.com/hatohato-lab/py2to3-agent) | Python 2 のコードを Python 3 に書き換える |
| AI エージェント | メタモルフィック（性質で調べる） | [metamorphic-sort-agent](https://github.com/hatohato-lab/metamorphic-sort-agent) | ソートを自分で実装する |
| AI エージェント | 往復の性質（戻せるかで調べる） | [roundtrip-rle-agent](https://github.com/hatohato-lab/roundtrip-rle-agent) | 文字列を圧縮して元に戻す |
| AI エージェント | 仕様で検算する | [spec-divisor-finder-agent](https://github.com/hatohato-lab/spec-divisor-finder-agent) | 整数の約数を1つ見つける |
| AI エージェント | ファジング（壊れた入力を流す） | [fuzz-robust-parser-agent](https://github.com/hatohato-lab/fuzz-robust-parser-agent) | 文字列から数字だけ取り出す |
| AI エージェント | 統計検定（偏りを調べる） | [monte-carlo-shuffle-agent](https://github.com/hatohato-lab/monte-carlo-shuffle-agent) | リストをランダムに並べ替える |
| AI エージェント | 実行して比べる | [text-to-sql-agent](https://github.com/hatohato-lab/text-to-sql-agent) | 日本語の質問を SQL にする |
| AI エージェント | AI が採点し、決まった基準で合否 | [llm-judge-summary-agent](https://github.com/hatohato-lab/llm-judge-summary-agent) | 文章を1文に要約する |
| AI エージェント | 査読の検出力を採点する | [agent-spec-reviewer](https://github.com/hatohato-lab/agent-spec-reviewer) | 他のエージェント定義を点検する |
| AI エージェント | 査読の検出力を採点する（6つの観点） | [harness-lens-reviewer](https://github.com/hatohato-lab/harness-lens-reviewer) | 設計や計画を6つの観点で点検する |
| AI エージェント | 文書の形を検査する | [fractal-spec-agent](https://github.com/hatohato-lab/fractal-spec-agent) | 要件から、設計書と用語集を作る |
| その他 | フォーム自動入力 | [formpilot](https://github.com/hatohato-lab/formpilot) | PDF のデータを Web フォームに自動で入れる |
| その他 | Excel | [VBA_Tools](https://github.com/hatohato-lab/VBA_Tools) | Excel の作業を自動化するマクロ集 |

専門用語を使わない説明は [説明書.md](./説明書.md) にあります。
