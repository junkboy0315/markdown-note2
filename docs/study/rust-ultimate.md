# Ultimate Rust 2

https://www.udemy.com/course/ultimate-rust-2/

## Idiomatic Code

Idiomatic とは、「〔言葉が〕母語話者が自然に使うような、その言語特有の特徴を見せる」という意味の形容詞。

ソフトウェア工学における「Idiomatic」とは、「与えられた文脈における自然な表現様式に従う」という意味。具体的には以下のようなことを指す。「要はバランス」案件なので、原理主義者にならないよう注意すること。

- 言語の機能を最大限に活用して、コードを簡潔で読みやすくする。ただし、書き方に関する議論に明け暮れてはダメ。
- コミュニティの標準的なコーディングスタイルに従う。ただし、流行に過度に影響されてはダメ。
- 公式ドキュメントやライブラリの使用方法に従う。ただし、それが実績あるエンジニアリングのやり方と反する場合には適切に破棄できる程度に尊重する。

### Rust の Idiomatic Code

`cargo fmt`でコードをフォーマットできる。IntelliJ の設定から`rustfmt`で検索し、`Use Rustfmt instead of the built-in formatter`にチェックを入れてたうえで、保存時にフォーマットがかかる設定にしておくと便利。

`cargo clippy`で様々な検査を行える。Style, Correctness(e.g. 消してもいいようなコード), Complexity(e.g. 無駄に複雑), Performance(e.g. 無駄に遠回り)に関する指摘をしてくれる。 警告を無視したい場合は`#[allow(clippy::xxx)]`のようにする。

## Domumentation

`cargo doc --no-deps --open`を使うと良い。`--no-deps`をつけると依存関係のライブラリのドキュメントを生成しない。`--open`をつけるとブラウザで開く。

デフォルトでは`pub`になっているものがドキュメントに含まれる。`///`または`/** **/`でコメントを書くと、そのコメントがドキュメントに含まれる。

基本的な書き方はマークダウン記法だが、一点だけ違いがある。それは、`[]`の一発でリンクを貼ることができることだ。リンクはスコープ内のものであればなんでもよく、もしほしいものがスコープ内になければ、絶対パスを使えばよい。

```rust
/// link to the [`SOME_VALUE_IN_SCOPE`]
/// link to the [SOME_VALUE_IN_SCOPE]
/// [click here!](SOME_VALUE_IN_SCOPE)
```

ドキュメントには Outer Documentation`///`と Inner Documentation`//!`がある。Inner Documentation は、主にモジュール自体やクレートのルートファイルに対して使われる。

ドキュメントを書いておくと、[Crate.io](https://crates.io/) に自分のライブラリを公開したときに自動的に Web ページが生成される。

## Iterator

for ループはイテレータを使ったパターンに書き換えられる。

```rust
let v = vec![1, 2, 3];

for i in v {
    println!("{}", i);
}

// これは以下のようにも書ける
v.into_iter().for_each(|i| {
    println!("{}", i);
});
```

このようにするメリットは、Iterator Adaptor を使えることと、わずかに速度が速いこと。

**Iterator Adaptor**は関数型プログラミングの道具の一つ。イテレータを受け取り別のイテレータを返す。その際、値に対して何らかのアクションを行う。例えば、`.map()`や`.filter()`などがある。

**Iterator Consumer**はイテレータを消費する。例えば、`.for_each()`、`.collect()`、`.sum()`などがある。Iterator Adaptor は Lazy であり、Consumer に到達して初めて実行される。

Consumer ではジェネリック型が多用されており、明示的な型指定が必要となる場面が多い。ターボフィッシュ(`::<>`)を使って、`collect::<Vec<_>>()`のようにする。あるいは変数に代入する場合は、`let result: Vec<_> = hogehoge.collect()`のように変数に型注釈を行う。`_`は型推論を行わせるためのもので、特に値が複雑な型の場合は超便利である。

[イテレータのドキュメント](https://doc.rust-lang.org/std/iter/trait.Iterator.html#provided-methods)を見ることで、型などの詳細を確認できる。

コレクションをイテレータに変換するメソッドは複数ある。

- `.into_iter()`
  - コレクションを消費して、もはや使えなくする
  - 所有権のある要素群を返す
  - `for _ in v`の糖衣構文
    - `IntoIterator`トレイトを実装していれば、`for`ループに与えたときに暗黙的に`.into_iter()`が呼ばれ、イテレータに変換される仕組み。
- `.iter()`
  - 参照群を返す
  - コレクションの値を見たいだけのときに使う
  - `for _ in &v`の糖衣構文
- `.iter_mut()`
  - 可変参照群を返す
  - コレクションの値を変更したいときに使う
  - `for _ in &mut v`の糖衣構文

コレクションを消費することなくコレクションを空にしたり、一部を抜き出したい場合には、`.drain()`が便利。コレクション以外にもハッシュマップ等にも使える。

```rust
let mut v = vec![6, 7, 8, 9, 10];
let drained: Vec<_> = v.drain(1..4).collect();

assert_eq!(v, vec![6, 10]);
assert_eq!(drained, vec![7, 8, 9]);
```

## Common Traits

トレイトを実装できるのは、struct, enum, closure, function の 4 つだが、メインは struct と enum である。

### 自動でトレイトを実装する (Derivable Traits)

特定の条件において、開発者がコードを書かずともコンパイラにより自動的に実装されるトレイトのこと。`#[derive(Trait1,Trait2)]`のように書くだけで自動で実装される。Derive ができるかどうかは、そのトレイトのドキュメントに記載されている。

#### Debug

デバッグ用の出力を行うためのトレイト。適用するには対象の Struct や Enum のすべてのフィールドに Debug トレイトが実装されている必要がある。なお、プリミティブ型や多くのライブラリの型には、 Debug トレイトがあらかじめ実装されている。

- `println!("{:?}", hogehoge)` 1 行で出力
- `println!("{:#?}", hogehoge)` Pretty に複数行で出力

#### Clone

値を複製するためのトレイト。適用するには対象の Struct や Enum のすべてのフィールドに Clone トレイトが実装されている必要がある。`.clone()`メソッドを実行することで値が複製される。

#### Copy

Clone に関係する特別なマーカートレイト。このトレイトが適用されていると、所有権の移動が発生する状況において代わりにコピーが行われる。

> マーカートレイトとは、メソッドやフィールドを持たないトレイトのこと。これらは型に関する情報をコンパイラに提供し、型の振る舞いを変更するために使われるが、具体的な実装は持たない。

対象の Struct や Enum のすべてのフィールドがスタックに入りきる小さな値（プリミティブ型）の場合に適用できる。ヒープメモリを使う値には適用できない。

Copy は Clone の Subtrait なので、必ずセットでの適用が必要。

### 手動で Trait を実装する方法

1. use 文によりトレイトをスコープに持ってくる
   - ただし standard prelude に含まれるトレイトは、use 文を書かなくても使える
2. ボイラープレート文を生成する
   - IDE の機能に頼るか、ドキュメントの Examples をコピペする
3. トレイトを実装する

#### Default

derive 可能だが、その場合はゼロ値になってしまうので、多くの場合は手動実装が必要となる。use 文は不要。

```rust
impl Default for MyStruct {
    fn default() -> Self {
        Self {
            field1: 123,
            field2: "hello".to_string(),
        }
    }
}

let my_struct = MyStruct {
    field1: 456,
    ..Default::default()
};
```

`..`の部分を Struct update syntax という。既存の構造体のインスタンスを基にして新しいインスタンスを生成するときに、一部のフィールドだけを更新する便利な方法。Rust ではよく使われる。

new などの関連関数は、パラメータを受け取ったり、複雑な初期化処理を行う場合に使われる。Default はデフォルト値を返すだけという点で役割が異なる。

#### PartialEq / Eq

PartialEq は、2 つの値が等しいかどうかを実際に計算する役割を持つトレイト。比較ロジックは、推移的/transitive, 対照的/symmentric であることが求められる。

Eq は PartialEq の Subtrait かつマーカートレイトである。比較のロジックが反射的/reflexive であることが要件に加えられる。Eq を実装するメリットはあまりないが、ハッシュマップでキーとして使えるようになるなどの利点がある。

> - reflexive とは、`a == a`が常に true であること。当たり前に思えるが、例えば NaN は自分自身と等しくない。
> - transitive とは、`a == b`かつ`b == c`ならば`a == c`であること。
> - symmetric とは、`a == b`ならば`b == a`であること。

```rust
impl PartialEq for MyStruct {
    // 以下は`(self: &Self, other: &Self)`と等価
    fn eq(&self, other: &Self) -> bool {
        self.field1 == other.field1 && self.field2 == other.field2
    }
}

impl Eq for MyStruct {} // マーカーなのでこれだけでOK
```

#### From / Into

ある型を別の型に変換するためのトレイト。From と Into は視点が違うだけで、単方向の変換を行うことに差はない。From を実装すれば Into も自動的に実装されるので、基本的には From だけを実装すればよい。

```rust
From<T> for U
Into<U> for T
// -> いずれにせよ T から U に変換される
```

例えば Struct から String に変換したい場合は、String に From トレイトを実装する。

```rust
// [例1]
// 所有権を持つ値を受け取ると消費してしまうので、
// 呼び出し元でStruct全体のcloneが必要となり非効率
impl From<MyStruct> for String {
    fn from(my_struct: MyStruct) -> Self {
        my_struct.name
    }
}

// [例2]
// 参照で受け取って最小限だけcloneするのが一般的
impl From<&MyStruct> for String {
    fn from(my_struct: &MyStruct) -> Self {
        my_struct.name.clone()
    }
}

let my_struct = MyStruct::default();

// Fromトレイトを使う場合 (関連関数)
let s: String = String::from(my_struct);
// Intoトレイトを使う場合 (メソッド)
let s: String = my_struct.into();
```

Into は以下のような使われ方をすることが多い。このようにすると、文字列に変換できるあらゆる型を引数に取ることができる。

```rust
fn show<T: Into<String>>(arg: T) {
    println!("{}", arg.into());
}
```

## Making Errors

ライブラリを公開する際などにカスタムエラーを定義することがある。その際、以下の原則に従うこと。

原則 1: エラーは Enum であること

Error トレイトを実装してさえいれば、どのような Enum でもエラーとして扱うことができるものの、あくまで Enum のみをエラーとして扱うことが推奨される。

原則 2: エラーをグルーピングすること

似たようなエラーは Enum の列挙子としてグルーピングせよ。たくさんの列挙子が並ぶとしても、それらが関連しているのであれば躊躇する必要はない。

原則 3： あなたのエラーのみを公開せよ

サードパーティーのエラーを「あなたのエラー」として公開してしまうと、無駄に第三者への依存が生じてしまう。結果として、第三者のコード変更によって公開 API が破壊されたり、内部実装を変えただけなのに公開 API を変更する必要性が出てきたりしてしまう。なので、ラップするなどして自前のエラーとして公開することが望ましい。ただし例外として、標準ライブラリのエラーはそのまま公開したほうが理にかなう場合もあるので、それはそのつど判断する。

原則 4: non_exhaustive にする

non_exhaustive 属性を設定すると、利用側のコードで match などを行う際に、デフォルトのアームを追加することが強制される。これにより、単なる列挙子の追加によって既存のコードが壊れることを防ぐことができる。

原則 5: Debug, Display, Error トレイトを（この順で）実装する

Error トレイトは Debug と Display のサブトレイトであるため、この順で実装することが推奨される。Debug は derive が可能。Display と Error は手動実装が必要なものの、Error についてはコードを書くことは不要。

以上をまとめると、以下のようなコードになる。

```rust
use std::error::Error;
use std::fmt::{Display, Formatter};

#[derive(Debug)]   // #5: Debugトレイトを実装する
#[non_exhaustive]  // #4: non_exhaustiveにする
enum PuzzleError { // #1: Enumにする
    WontFit(u16),  // #2: グルーピングする
    MissingPiece,  // #2: グルーピングする
    WrongPiece,    // #2: グルーピングする
    // #3: あなたのエラーのみを公開する
}

// #5: Displayトレイトを実装する
impl Display for PuzzleError {
    fn fmt(&self, f: &mut Formatter) -> std::fmt::Result {
        use PuzzleError::*;
        match self {
            WontFit(size) => {
              write!(f, "Piece {} doesn't fit", size)
            }
            MissingPiece => write!(f, "Missing a piece"),
            WrongPiece => write!(f, "Wrong piece"),
        }
    }
}

// #5: Errorトレイトを実装する
impl Error for PuzzleError {}
```

原則 5b: thiserror を使う

Display トレイトの手動実装が面倒な場合は、`thiserror`クレートを使うと簡素化できる。特に理由がなければこちらを使うのがオススメ。前述のコードは以下と等価である。

```rust
use thiserror::Error;

#[derive(Debug, Error)]
#[non_exhaustive]
enum PuzzleError {
    #[error("Piece {0} doesn't fit")]
    WontFit(u16),
    #[error("Missing a piece")]
    MissingPiece,
    #[error("Wrong piece")]
    WrongPiece,
}
```

## Handling Errors

おさらいとして、失敗する可能性のある処理を行う関数は`Result<T, E>`型を返す必要がある点を思い出そう。`Result`は、成功した場合は`Ok(T)`、失敗した場合は`Err(E)`を返す。

ライブラリ作者は可能な限り panic を避けなければならない。エラーが発生した際は Handle or Return するのが原則。Handle は`if let`や`unrap_or`、`unwrap_or_else`などを使ってエラーを処理すること。Return は`?`演算子を使ってエラーを返すこと。

サードパーティーのエラーの種類が多岐にわたる場合、「あなたのエラーのみを返す」の原則に沿うのは大変だが、どうするか？`Box<dyn Error>`を使う方法もあるものの、デファクトといえる`anyhow`ライブラリを使うほうが良い。メリットは以下の通り。

- 多種多様なエラータイプをまとめることでコードがシンプルになる
- コンテキストの付与によりデバッグが容易になる
- エラーが発生するまでのチェーンを分かりやすくたどれる

```rust
use anyhow::{Result, Context};

// anyhowのResultは、Errorトレイトが実装されている値であれば何でも受け取れるため、
// エラーの型を明示的に書く必要がなく、成功時の結果だけを型引数として指定すれば良い。
fn get_data_from_file(path: &str) -> Result<String> {
    std::fs::read_to_string(path)?

    // Contextトレイトをインポートすることで、必要に応じて追加の情報を付与することも可能
    // std::fs::read_to_string(path).with_context(|| format!("failed to read file: {}", path))
}

fn main() -> Result<()> {
    let path = "data.txt";
    let data = get_data_from_file(path)?;

    println!("File content: {}", data);

    Ok(())
}
```

## Crate という言葉について

Crete には文脈によって 2 つの意味がある。

- 1 番目の意味: パッケージのこと
  - e.g. `rand`クレート
  - e.g. エンジニアが日々作成する、cargo.toml を含むプロジェクト全体
- 2 番目の意味: ライブラリもしくはバイナリのこと
  - e.g. `lib.rs`, `main.rs`, `bin/hoge.rs`といった単位

イメージとしてはこうなる

- package (1 番目の意味のクレート)
  - library crate (2 番目の意味のクレート。数は 0 or 1)
  - binary crate (2 番目の意味のクレート。数は 0 - N)

## Unit Testing

Rust のユニットテストは、テスト対象と同一コード内に書くのが idiomatic な書き方。

```rust
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

#[cfg(test)] // テストコードがライブラリとして発行されないようにする
mod test {
  use super::*; // アスタリスクを使っていい、ごく少ない事例です

  #[test] // テストランナーで実行すべき対象であることをcargoに伝える
  fn test_add() { // テスト関数の名は何でもいい。返値はなしか、もしくはResultである必要がある。
    assert_eq!(add(1, 2), 3);
  }
}
```

アサーションに使えるマクロは以下の通り。

- `assert_eq!()` - 値が等しいことを確認する。PartialEq トレイトの実装が前提。
- `assert_ne!()` - 値が等しくないことを確認する。PartialEq トレイトの実装が前提。
- `assert!()` - bool 値を受け取り、true であることを確認する。
- `panic!()` - テストを失敗させる
  - ただし`#[should_panic]`があれば、逆に失敗することが成功とみなされる

テストを実行するには`cargo test`とする。結果は（前述の 2 番目の意味の）クレートごとにセクションを分けされて出力される。ただしセクションわけのインデントが倒錯していて見づらいので注意。緑の文字があればそこがセクションの始まりであると思えば良い。

テスト関数は Result 型を返すことができる。成功時の型は無視される。失敗時の型には anyhow の Error を指定することもできる。こうすることで、失敗時により多くの情報を得るできる。

`cargo test test:***`で特定のテストだけを実行できる。ここでの`test`は自ら定義したモジュールの名前である。