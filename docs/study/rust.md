# Rust

## 変数と可変性

- デフォルトで不変。変更可能にするには`let mut x=5`のようにする。
- 定数
  - `const MAX_POINTS: u32 = 100_000;`
  - 型注釈は必須
  - `mut`は使えない
  - `const`で宣言する
- シャドーイング
  - 前に定義した変数と同じ名前の変数を新しく宣言して上書きすること。`mut`はあくまで同じ型だが、こちらは型を変更できる。

## データ型

- スカラー型
  - 整数
    - 特に理由がなければ`i32`を使うこと。
  - 浮動小数点数
    - 特に理由がなければ`f64`を使うこと。
  - 論理値
  - 文字
    - char 型
    - シングルクオートで表す
    - ユニコードのスカラ値であり、世間一般的な「文字」とは異なるので留意(例えばゼロ幅スペースなど)
- 複合型
  - タプル
  - 配列
    - スタックまたは確実に固定長であるものに最適。それ以外であれば可変長であるベクタ型を使え。
    - 長さは変えられない
    - 配列の終端を超えたアクセスはパニックになる

### タプル型

```rust
let tup: (i32, f64, u8) = (500, 6.4, 1);
// or
let tup = (500, 6.4, 1);

// 分配と呼ばれる代入方法
let (x, y, z) = tup;

// 0番目の要素にアクセス
let five_hundred = x.0;
```

### 配列型

```rs
let a = [1, 2, 3, 4, 5];

// 添字を使ったアクセス
let first = a[0];
let second = a[1];

// 型を指定する方法
let a: [i32; 5] = [1, 2, 3, 4, 5];
let b: &[i32] = &a;
```

### 推論

複数の型が推論される可能性がある場合、型注釈が必須。

```rust
let guess: u32 = "42".parse().expect("Not a number!");
```

## 関数

- rust の関数や変数はスネークケースで書く
- 文 --- Statement, 値を返さない
- 式 --- Expression, 値を返す
  - スカラ値
  - 関数呼び出し（最後にセミコロンはつけない。つけると文になる。）
  - マクロ呼び出し
  - スコープ（最後にセミコロンはつけない。つけると文になる。）

関数の書き方

```rust
fn main() {
    let x = plus_one(5);

    println!("The value of x is: {}", x);
}

fn plus_one(x: i32) -> i32 {
    x + 1
}
```

## フロー制御

- if
  - 式なので代入できる

```rust
let condition = false;
let num = if condition { 5 } else { 6 };
```

- loop

```rust
loop {
    // do something
}
```

- while

```rust
while number != 0 {
    // do something
}
```

- for

```rust
let a = [10, 20, 30, 40, 50];
for element in a.iter() {
    println!("the value is: {}", element);
}
```

- range

```rust
for number in (1..4) {
    // do something
}
```

## 所有権

なぜ所有権が必要なのか？

- ヒープメモリを効率的に管理するため
- メモリの二重開放を防ぐため
- GC を不要にするため

### 基本

- Rust の各値は、所有者と呼ばれる変数と対応している。
- いかなる時も所有者は一つである。
- 所有者がスコープから外れたら、値は破棄される。

### ムーブ

- 代入しても所有権が移動しない(Copy される)型
  - 整数型、論理値型、浮動小数点型、文字列スライス(`&str`)
  - 上記から成るタプル
  - 参照
- 代入すると所有権が移動する(Move される)型
  - String 型その他

ムーブしたくない場合は`.clone()`などする必要がある

### 参照と借用

- 引数に**値**を渡すと、値の所有権は関数に**移転する**。
- 引数に**値への参照**を渡すと、値の所有権は関数に**移転しない**。
  - この、関数の引数に参照を取ることを**借用**と呼ぶ

```rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{}' is {}.", s1, len);
}

// 借用
fn calculate_length(s: &String) -> usize {
    s.len()
    // 参照であるsはここで破棄されるが、もとの値は破棄されない。
    // 一時的に借りただけ、というイメージ。
}
```

- 参照を可変にしたい場合は`&mut 変数名`とする。
- スコープ内では以下のいずれかを使用できる
  - 一つの可変参照
  - 多数の不変参照
- 参照は常に有効でなければならない。必要に応じて参照ではなく実体を使うこと。

### スライス

- スライスとは、文字列や配列の一部分に対する参照
- なお、文字列リテラルは自ずから文字列スライスである

```rust
let my_string = String::from("hello world");
&my_string[..]

let numbers = [1, 2, 3, 4, 5];
&numbers[1..3];
```

## 構造体

```rust
// 構造体
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}

// タプル構造体
struct Color(i32, i32, i32);

// ユニット構造体
struct UnitBook;
```

構造体をプリントする方法

```rust
// deriveを記述する
#[derive(Debug)]
struct Rectangle {}

let rect = Rectangle {};

// `:?`が大事
println!("rect is {:?}", rect);
```

### メソッド(instance メソッド)と関連関数(≒static メソッド)

```rust
impl Rectangle {
  // メソッド(selfをとる)
  fn can_hold(&self, other: &Rectangle) -> bool {
    self.width > other.width && self.height > other.height
  }

  // 関連関数(selfを取らない)
  fn square(size: isize) -> Rectangle {
    Rectangle {
      width: size,
      height: size,
    }
  }
}
```

## Enum

```rust
// 基本形
enum IpAddrKind {
  V4,
  V6,
}
let four = IpAddrKind::V4;
let six = IpAddrKind::V6;

// 発展形（値を持たせることができる）
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
let m = Message::Move { x: 1, y: 2 };
```

### Option 型

Null になりうる値は Option 型として使う必要がある。Option, Some 及び None は標準ライブラリに定義されているため、接頭子なしで使用できる。

```rust
// Option型の定義
enum Option<T> {
    Some(T),
    None,
}

// 使い方の例
let mut maybe_number: Option<i32>;
maybe_number = None;
maybe_number = Some(5);
```

## match

基本形

```rust
enum Coin {
  Penny,
  Nickel,
  Dime,
  Quarter(String),
}

fn value_in_cents(coin: Coin) -> u32 {
  match coin {
    Coin::Penny => 1,
    Coin::Nickel => 5,
    Coin::Dime => 10,
    Coin::Quarter(state) => { // 値を持つEnumの場合はこのように取り出せる
      println!("{}", state);
      25
    }
  }
}
```

Option との組み合わせ

```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}

let five = Some(5);
let six = plus_one(five);
let none = plus_one(None);
```

### if let

`match`で特定のケースだけ取り扱いたい場合は、`if let`を使うことも検討すると良い。

```rust
let mut count = 0;
match coin {
    // {:?}州のクォーターコイン
    Coin::Quarter(state) => println!("State quarter from {:?}!", state),
    _ => count += 1,
}

// これは下記のようにも書ける

let mut count = 0;
if let Coin::Quarter(state) = coin {
    println!("State quarter from {:?}!", state);
} else {
    count += 1;
}
```

## Packages, Crates, and Modules

### Packages, Crates

- package
  - 一つ以上の crate で構成される。crate の数の要件は以下の通り。
    - 少なくとも１つ以上の crate が必要
    - library crate は 0 または 1 つだけ
    - binary crate はいくつでも
  - なんらかのまとまった機能を提供する
  - `cargo.toml`を含む。ここには crate のビルド方法が書かれている
- crate
  - library crate 又は binary crate のこと
- crate root
  - 下記のいずれか
    - `src/lib.rs`(library crate)
      - パッケージ名が crate 名になる
    - `src/main.rs`(binary crate)
      - パッケージ名が crate 名になる
    - `src/bin/**.rs`(binary crate)
  - その crate の root module になる
  - コンパイラが読み込みをスタートする地点

### Modules

- クレート内でコードをグルーピングするために使う
- 可読性と再利用可能性を向上させるために使う
- プライバシーを管理するために使う
  - Public --- コードの外でも使える
  - Private --- コードの外では使えない

```rs
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}

        fn seat_at_table() {}
    }

    mod serving {
        fn take_order() {}

        fn serve_order() {}

        fn take_payment() {}
    }
}
```

上記のようなコードを crate root に定義した場合、モジュールツリーは以下のようになる

```
crate(暗黙的に命名される)
 └── front_of_house
     ├── hosting
     │   ├── add_to_waitlist
     │   └── seat_at_table
     └── serving
         ├── take_order
         ├── serve_order
         └── take_payment
```

### Path

- パスの種類
  - Absolute path --- crate name 又は`crate`のリテラルから始まる
  - Relative path --- `self`、`super`又は同一レベルにあるモジュール名から始まる
- `::`で区切る
- Private と Public の管理
  - デフォルトでは：
    - 呼び出した関数と同一レベルにあるモジュールには`pub`なしでアクセス可能
    - 子モジュールやその内部は Private。パブリックにするには`pub mod`や`pub fn`などが必要。
    - 親モジュールは Public

```rs
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // Absolute path
    crate::front_of_house::hosting::add_to_waitlist();

    // Relative path
    front_of_house::hosting::add_to_waitlist();
}
```

`super`を使うと親モジュールにアクセスできる

```rs
fn serve_order() {}

mod back_of_house {
    fn fix_incorrect_order() {
        cook_order();
        super::serve_order();
    }

    fn cook_order() {}
}
```

struct の要素はデフォルトで非公開

```rs
mod back_of_house {
    pub struct Breakfast {
        pub toast: String,
        seasonal_fruit: String,
    }

    impl Breakfast {
        // この関数なしではBreakfastは初期化すらできない
        pub fn summer(toast: &str) -> Breakfast {
            Breakfast {
                toast: String::from(toast),
                seasonal_fruit: String::from("peaches"),
            }
        }
    }
}

pub fn eat_at_restaurant() {
    let mut meal = back_of_house::Breakfast::summer("Rye");
    // 変更可能
    meal.toast = String::from("Wheat");

    // 以下のコードはコンパイル不可
    meal.seasonal_fruit = String::from("blueberries");
}
```

一方、enum の要素はデフォルトで公開

```rs
mod back_of_house {
    pub enum Appetizer {
        Soup,
        Salad,
    }
}

pub fn eat_at_restaurant() {
    let order1 = back_of_house::Appetizer::Soup;
    let order2 = back_of_house::Appetizer::Salad;
}
```

### use

下記のようにすると、他のモジュールを接頭詞無しでつかえる。

```rs
use crate::front_of_house::hosting;

// - 絶対パスでも相対パスでもOK
// - 以降、`hosting::***`のように使える
```

慣例として、関数はひとつ上のモジュールを読み込む。これは、関数がローカルのものではないことを明確にするため。

```rs
use crate::front_of_house::hosting;
hosting::add_to_waitlist();
```

慣例として、Enum の場合はそれ自身を読み込む。特に理由はない。

```rs
use std::collections::HashMap;
let mut map = HashMap::new();
```

例外として、名前が重複する場合はそのひとつ上のモジュールから読み込む。

```rs
use std::fmt;
use std::io;

fn function1() -> fmt::Result {...}
fn function2() -> io::Result {...}
```

もしくは下記のように別名をつける。

```rs
use std::io::Result as IoResult;
```

`pub use`とすると再エクスポートできる。

```rs
// 外部のコードから`hosting`を呼び出せるようになる
pub use crate::front_of_house::hosting;
```

外部ライブラリを使いたいときは、`Cargo.toml`に記載したうえで`use`する。

```toml
[dependencies]
rand = "0.8.3"
```

```rs
// TODO: traitってなに？？ Rng.***ではないのか？
use rand::Rng;
let rng = rand::thread_rng()
```

省略記法

```rs
use std::io;
use std::io::Write;
use std::io::Read;

// 上記は下記の通り書ける
use std::io::{self, Write, Read}
```

glob operator も使えるが、基本的にテストでのみ使用すること。見通しが悪くなるため。

```rs
use std::collections::*;
```

### モジュールをファイルに切り出す

`mod`のあとにファイル名を記載することで、そのファイル内のモジュールを呼び出せる。

```rs
// src/front_of_house.rs
pub mod hosting {
  pub fn add_to_waitlist() {}
}
```

```rs
// src/lib.rs
mod front_of_house;
front_of_house::hosting::add_to_waitlist();
```

## Collections

- 予め用意されている便利なデータ構造のこと
- 複数の値を保持できるのが特徴
- Array や Tuple と異なり、ヒープメモリに保持されるため、コンパイル時にサイズを確定させなくてもいい

### Vector

- 単一型である
- 複数の値を保持できる
- 可変長である
- `Vec<T>`

```rs
// 初期値がない場合
let v: Vec<i32> = Vec::new();

// 初期値がある場合（マクロを使って初期化できる）
let v = vec![1, 2, 3];
```

値の追加

```rs
let mut v = Vec::new();

v.push(5);
v.push(6);
v.push(7);
v.push(8);
```

値の取得には２種類の方法がある。いずれも参照を取得する。

```rs
// 結果を&Tとして受け取る
// 存在しなければパニックになる
let third = &v[2];

// 結果をOption<&T>として受け取る
// 存在しなければNoneを返し、存在すればSome(&T)を返す。
let third = v.get(2);
```

反復処理

```rs
// 参照のみ
let v = vec![1,2,3];
for i in &v {
  println!("{}", i);
}

// 変更あり
let mut v = vec![1,2,3];
for i in &mut v {
  // Dereference operatorを使う
  *i += 50;
}
```

異なる型をベクターに保存したい場合は、予め Enum として作成しておくことで対応する。

```rs
enum SpreadsheetCell {
    Int(i32),
    Float(f64),
    Text(String),
}

let row = vec![
    SpreadsheetCell::Int(3),
    SpreadsheetCell::Text(String::from("blue")),
    SpreadsheetCell::Float(10.12),
];
```

### String

String とは？

- String literal(`str`)
  - rust で唯一の組み込みの文字列型
  - string slice(`&str`)として使用される。なぜなら、文字列自体はバイナリに組み込まれる完全に変更不可能なものであり、String literal はそこへの参照としてしか存在できないから。
- String type(`String`)
  - ライブラリにより提供される
  - 拡張、変更、所有が可能

rust の世界で'String'と言った場合、String type 又は String slice を指すことが多い。どちらも UTF-8。

String の作り方

```rs
let s = "aaa".to_string();
let s = String::from("aaa");
```

末尾に文字列を追加する。なお、`push_str()`は参照(string literal)を引数として取るので、所有権の移転は発生しない。

```rs
let mut s = String::from("foo");
s.push_str("bar");
```

文字列の結合(+を使う方法)

- `s1`の所有権は s に移る。再利用されるということ。少し効率的。
- `+`に与えることができるのは`&str`型。なお、`&String`は自動的に変換される。deref coercion という。

```rs
let s1 = String::from("tic");
let s2 = String::from("tac");
let s3 = String::from("toe");

let s = s1 + "-" + &s2 + "-" + &s3;
```

文字列の結合(`format!`を使う方法)

- この場合は所有権の移転は一切発生しない。

```rs
let s = format!("{}-{}-{}", s1, s2, s3);
```

UTF−8 の話

- rust の内部では文字列は byte(`vec<u8>`)でとして保持されている

```rs
// 表現したい文字列
"नमस्ते"

// byteで表す
[224, 164, 168, 224, 164, 174, 224, 164, 184, 224, 165, 141, 224, 164, 164, 224, 165, 135]

// Unicode scalar value(コードポイント)で表す
['न', 'म', 'स', '्', 'त', 'े']

// grapheme clusters(人間が目にする文字)で表す
["न", "म", "स्", "ते"]
```

どうしても必要ならインデックスを使うことも可能ではあるが、あまりいいアイデアではない。なお、中途半端な位置で切るとパニックになるので要注意。

```rs
let s1 = "こんちわ".to_string();
let s = &s1[0..3]; // sは&strになる("こ")
```

繰り返し

```rs
// Unicodeスカラ値として取り出して繰り返す
for c in "नमस्ते".chars() {}

// byteとして取り出して繰り返す
for b in "नमस्ते".bytes() {}

// graphene clustersで取り出して繰り返すには外部ライブラリが必要
```

### Hash Map

作成

```rs
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);
```

複数の vector を zip して作成することもできる

```rs
let teams = vec![
  String::from("Blue"),
  String::from("Yellow")
];
let initial_scores = vec![
  10,
  50,
];
let scores: HashMap<_, _> =
    teams.into_iter().zip(initial_scores.into_iter()).collect();
```

値の取得(Option 型が得られる)

```rs
let score = scores.get("Blue");
```

イテレーション

```rs
for (key, value) in &scores {}
```

値の更新

```rs
let mut scores = HashMap::new();
scores.insert(String::from("Blue"), 10);

// 上書き
scores.insert(String::from("Blue"), 25);

// 既存のデータを使って上書き
let count = scores.entry("Blue").or_insert(0);
*count += 1;

// 値がなければ挿入、あれば何もしない
scores.entry(String::from("Blue")).or_insert(50);
```

## エラー

rust には 2 種類のエラーがある。他の言語ではこれらは区別されないことが多い。

- recoverable なエラー
  - `Result<T, E>`型
  - 例）ファイルが見つからなかった場合
- unrecoverable なエラー
  - `panic!`マクロ
  - 例）Array の範囲外にアクセスした場合

### panic!

- [コラム] バイナリサイズを可能な限り小さくしたい場合は`Unwinding`を`abort`に変更する([参考](https://doc.rust-lang.org/book/ch09-01-unrecoverable-errors-with-panic.html#unwinding-the-stack-or-aborting-in-response-to-a-panic))

```toml
[profile] # リリース環境ならprofile.release
panic = 'abort'
```

- Backtrace を取得するには以下のようにする。

```sh
RUST_BACKTRACE=1 cargo run
```

### Result

- プログラムを止めるまでもないエラーの場合、`Result`型が使われる。
- `Result`, `Ok`, `Err`は prelude により用意されるので、接頭子をつけずに使える。

```rs
// Result型の定義
enum Reeult<T, E> {
  Ok(T),
  Err(E),
}
```

処理結果を確認して、以降の処理を分岐する方法

```rs
use std::fs::File;
let f = File::open("hello.txt");

let f = match f {
  Ok(file) => file,
  Err(error) => panic!("{:?}", error),
};
```

「成功した場合は値を取得し、失敗した場合はパニックする」という処理は定型的であるため、`unwrap()`や`expect()`という関数を使って短縮できるようになっている。

```rs
let f = File::open("hello.txt").unwrap();

// expectはunwrapと同じだが、わかりやすいメッセージを表示することができる
let f = File::open("hello.txt").expect("Failed to open hello.txt");

```

より複雑な場合分けにはマッチガードを使う

```rs
let f = match f {
  Ok(file) => file,
  // if... の部分がマッチガード
  // refの意味はようわからん
  Err(ref error) if error.kind() == ErrorKind::NotFound => match File::create("hello.txt") {
    Ok(fc) => fc,
    Err(e) => {
      panic!("Tried to create file but there was a problem: {:?}", e)
    }
  },
  Err(error) => {
    panic!("There was a problem opening the file: {:?}", error)
  }
};
```

Tips: 型を調べたいときは、全然違う型に代入して意図的にエラーを起こし、エラーメッセージで確認する

```rs
let f: u32 = File::open("hello.txt");
// メッセージ => found enum `Result<File, std::io::Error>`
```

エラーの処理を関数の呼び出し元にまかせる（**エラーの委譲**）には、関数の返り値の型を Result にする。

```rs
use std::io;
use std::io::Read;
use std::fs::File;

fn read_username_from_file() -> Result<String, io::Error> {
    let f = File::open("hello.txt");

    let mut f = match f {
        Ok(file) => file,
        // エラーを呼び出し元に返す
        Err(e) => return Err(e),
    };

    let mut s = String::new();

    match f.read_to_string(&mut s) {
        Ok(_) => Ok(s),
        // エラーを呼び出し元に返す
        Err(e) => Err(e),
    }
}
```

「成功した場合は値を取得し、失敗したときは呼び出し元にエラー処理を委譲する」ことは定型的な処理であるため、`?`演算子を使って簡潔に記載できる様になっている。

```rs
fn read_username_from_file() -> Result<String, io::Error> {
    let mut s = String::new();

    // ?と書くだけでエラーの委譲を行える
    let mut f = File::open("hello.txt")?;
    f.read_to_string(&mut s)?;

    // 又は連結して一文で書くことも可能
    File::open("hello.txt")?.read_to_string(&mut s)?;

    Ok(s)
}
```

### panic と Result の使い分け方

- ユースケースごとの使い分け
  - サンプルコード、プロトタイプコード、テストコードの場合
    - panic(unwrap, expect) が最適。
    - 意図が明確になるため。テストコードを適切に失敗させるため。
  - 開発者がコンパイラよりも情報を持っており、正しさを確信できる場合
    - 例えば、下記は常に正しいので panic してよい。
      ```rs
      let home: IpAddr = "127.0.0.1".parse().unwrap();
      ```
    - 逆に、IP アドレスがユーザ入力等で与えられる場合は Result を使って処理する。

エラー処理のガイドライン

- パニックが最適
  - 悪い状態(前提、保証、契約、不変性が破られた状態)である、かつ以下のいずれかを満たす場合
    - その悪い状態が絶対に起きてはならないことである
    - その時点以降、良い状態であることを前提にコードが書かれている
    - 型を使って問題の発生を防ぐ方法がない
- Result が最適
  - 失敗が予想されるとき(HTTP リクエストなど)

型を使って値が正しいことを保証するには、下記のような Constructor と Getter を使う。下記では値が 1 から 100 の間であることを保証している。

```rs
pub struct Guess {
    // この値は基本的に非公開。モジュールとして呼び出される場合。
    value: u32,
}

impl Guess {
    pub fn new(value: u32) -> Guess {
        if value < 1 || value > 100 {
            panic!("Value must be between 1 and 100, got {}.", value);
        }

        Guess {
            value
        }
    }

    pub fn value(&self) -> u32 {
        self.value
    }
}
```
