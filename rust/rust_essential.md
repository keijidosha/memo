# Rust Essential


* プロジェクト作成  
`carge new <プロジェクト名> --bin`  
プロジェクト名のディレクトリが作成される  
* ビルド  
`cargo build`  
* 実行  
`cargo run`  
ビルドが必要な場合は自動的にビルドされる  

## Cargo.toml

* 依存関係を記述  
  ```toml
  [dependencies]
  rand="0.3.0"
  ```
  * 互換性のあるバージョンを使う  
  rand="0.3.0"  
  * 完全に同じバージョンを使う  
  rand="=0.3.0"  
  * 最新版を使う  
  rand="*"  

