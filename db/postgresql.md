# PostgreSQL

## psql

### コマンド
* 拡張表示  
\x

## シーケンス

* シーケンスの値を変更する  
`select setval('シーケンス名',10);`
* シーケンスの値を初期化する(値を 1 にし、状態を呼び出し前にする)  
`select setval('シーケンス名',1,false);`
* シーケンスの値を確認する  
`select currval('シーケンス名');`
* シーケンスの状態を確認する  
`\d シーケンス名`  
currval of sequence "xxx" is not yet defined in this session というエラーが出る場合は  
`select last_value from <シーケンス名>;`

## ログ

* 発行した SQL をログ出力する  
postgresqlconf に次の指定をする  
`log_statement='all'`
