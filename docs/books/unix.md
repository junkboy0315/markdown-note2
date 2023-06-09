# UNIX という考え方

## スモール・イズ・ビューティフル

- 一つの巨大なプログラムにしようとする誘惑に負けない
  - 書いたプログラムの大きさがプログラマの器の大きさを示すわけではない
  - 「あらゆる不測の事態に対応できるように」という誤った考えは捨てろ。未来の予測は諦めろ。
  - そもそも、小さなプログラムならあらゆる変化に直ちに対応・修正できる。
- ほとんどの場合、問題を完全に理解していないから巨大な解決策を実装しようとするのだ

メリット

- 小さなプログラムは分かりやすい
- 小さなプログラムは保守しやすい
- 小さなプログラムはリソースを浪費しない
- 小さなプログラムは他のツールと組み合わせやすい

## 一つのことをうまくやる

- その機能が本当に必要なのか常に懐疑的に考えろ
- 「しのびよる多機能主義」を遠ざけろ
- ひとつのことをうまくやるように作れないのなら、問題を完全には理解していない証拠である

## できるだけ早く試作をつくる

- 最初はうまくいかないと考えて対処するのが賢明
- 試作することで何ががうまくいき何がうまくいかないのかが分かる

最速のアプローチ

1. 短い基本設計書を書く（詳細設計書はまだ書かない）
1. コードを書く
1. テストして書き直す。満足できるまで繰り返す。
1. 詳細設計書を書く（必要なら）

従来の方法との違い

- 伝統的な方法 --- 膨大な文書をユーザに示して完成図を読み取ってもらいながら作る
- 最速の方法 --- 実際に機能するアプリケーションをユーザに示しながら作る
- 目標についてわかっていることをいくつか書きとめ、残った時間はシステムの構築に使う
- ある程度の熟慮は必要だが、取り掛かる前にすべての詳細を文書化しても役には立たない

## 効率より移植性

移植性を保つためのコストは後から十分に報われる。移植可能でないものは単にこの瞬間においてのみ効率が良いにすぎない。

- C 言語はシェルスクリプトよりもハード依存の要素が多く、移植性が低い。僅かなスピードのために移植性を犠牲にするな。
- ハードウェアは年々進化するので、新しいハードに移植さえできれば動作スピードは年々速くなる。
- 高効率の方法はほとんどの場合移植性に欠ける
- 移植性が高ければ移行費用を抑えられ、新機能の開発にリソースを向けられる。ライバルに勝てる。

## データは ASCII フラットファイルに保存する

- もっとも移植性の高いデータ形式である
- もっとも広く使われている
- 簡単に読める、編集できる
- grep や awk などの UNIX ツールが使える
- 移植できないもの（バイナリファイル等）の一生は短い

## ソフトウェアの梃子を有効に活用する

- コードは書くな、借りてこい
- NIH(Not Invented Here)症候群＝独自技術症候群に陥るな
  - 優れた組織で陥りがち

## シェルスクリプトを使うことで梃子の効果と移植性を高める

数行のシェルスクリプト = 数万行の C 言語コード

シェルスクリプトを使えば「簡単なプログラムすら書けない」プログラマも含め、全員が勝者になれる

## 過度の対話的インターフェースを避ける

- 小さなものは人間にあまりよくなじまない
- 小さなものは互いにはよく馴染む

だからといって対話的・拘束的なインターフェースはダメ。なぜなら

- 独自言語を学ばないといけない
- そのコマンドの実行中にほかのことができない
- コマンドパーサーが大きく醜くなる
- 他のプログラムと対話できない
- スケールしない
- 梃子を利用できない

せいぜい不可逆で重大な処理を行うときにプロンプトを表示するくらいにしておけ

## すべてのプログラムをフィルタにする

一次データを作るのは常に人間。コンピュータには作れない。すなわち、すべてのプログラムはおのずからフィルタである。

入出力をハードワイヤするのは、プログラムの使われ方を完全に想定できるという「思い上がり」からくる行為である。

- データ入力には stdin を使用する
- データ出力には stdout を使用する
- 帯域外情報には stderr を使用する（エラーへの対応はユーザごとに異なるため）

## その他

- 好みに応じてカスタマイズできる
- カーネルは軽く保つ
- 小文字を使って短く書く
- 印刷しない
  - データはマシン上においておき、いつでも UNIX の強力なツールで操作できるようにしておく
  - 印刷したデータはデータの死亡宣告書である
- 沈黙は金
  - フィルタとして使えるよう、意味のあるデータだけを出力する
  - コメントをごちゃごちゃ出力しない
- 並行的に考える
  - バックグラウンドタスク
- 部分の総和は全体よりも大きい
  - 小さな部品の組み合わせとして大きなアプリケーションを作れ
  - 特定部分だけを修正できるし、柔軟性も保たれるから
- 90%の解を目指す
  - 重要性が低く難しい部分を故意に無視せよ
  - ソフトウェアに完成はない。あるのはリリースだけ。
- 劣るほうが優れている（＝シンプルさを最優先せよ）
  - 適切な設計は、単純さ、正しさ、一貫性、完全性からなる
  - 単純さを犠牲にして完全性を求めるのが「適切な」設計
  - 完全性を犠牲にして単純さを最重要視するのが「劣った」設計
- 階層的に考える
  - ディレクトリ階層
  - ファイルエクスプローラー
  - X ウィンドウシステム
  - 自然界の木、森
