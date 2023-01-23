# Xcode

* コンパイルして「Incomplete implementation」という警告が表示された場合に、何が未実装かを確認する方法
  1. command + 4 で Issue Navagation を開く。
  1. 警告メッセージの左の三角マークをクリックして詳細を表示。
* Xcode を 4.6.2 にアップデートすると、次のエラーが発生する。  
「error: PCH file built from a different branch ((clang-425.0.27)) than the compiler ((clang-425.0.28))」  
=> プロジェクトをクリーンしてビルドし直す。(異なるバージョンのコンパイラで作成されたプリコンパイルヘッダーが使えない?)
