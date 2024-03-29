# 基本からしっかり学ぶ Rust 入門

[Rust](./rust) に記載したものは省略

## Rust とは?

- Mozilla が支援
- Android OS や Linux カーネルでも使われている
- C/C++/Java/C#のいいとこ取り
  - ネイティブコンパイラ言語 (ダイレクトに機械語に変換できる)
  - メモリ安全
  - スレッドセーフ
  - ガベージコレクタが不要
- 自動テスト機能がある
- モジュールシステムが整っている
  - プロジェクトを階層化、分割して管理する仕組み

### インストールと更新

- インストール
  - 公式サイトのスクリプトを実行する
- 更新
  - `rustup update`
- フォルダ構成
  - `$HOME/.rustup`
    - Rust のアップデート用フォルダで、メタデータやツールチェーン（開発に必要なプログラム群）が置かれる
  - `$HOME/.cargo`
    - パッケージマネジャーのフォルダ
    - Rust のコマンド類は bin フォルダに配置される
- Rust のプログラム
  - rustup
    - ツールチェーンのインストールやアップデートを管理
  - rustc
    - コンパイラ
    - Rust の中核
    - 直接使うことは少ない
  - cargo
    - パッケージマネジャーである
      - 外部ライブラリ(crates)のダウンロード、アップデート、バージョン管理
      - crates.io での自作の crate を共有
    - ビルドツールでもある
      - コードのビルド
      - コードのテスト
      - ドキュメントの作成

### Hello World

```sh
# 新規プロジェクトの作成
cargo new --bin hello_world
cd hello_world

# ビルドと実行(デバッグ用)
cargo run

# ビルドと実行(リリース用)
cargo run --release
```

- ビルド成果は `target/(debug|release)` フォルダに出力される
  - ここでは`hello_world`というバイナリが生成されている
