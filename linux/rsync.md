# rsync

## rsync で指定するパス
* (正解) /var/log が /opt/log にコピーされます。  
`rsync -auv /var/log/ /opt/log/`
* (間違い) /var/log が /opt/log/log としてコピーされます。  
`rsync -auv /var/log /opt/log/`
* (間違い) /var/log が /opt/log/log としてコピーされます。  
`rsync -auv /var/log /opt/log`
* (正解) /var/log が /opt/log にコピーされます。  
`rsync -auv /var/log /opt`

## Mac で rsync する時のパス文字化け対策
Mac の UTF-8 が他のマシンと異なるところから、濁点、半濁点が正しく処理できない場合

* バージョン3 の rsync をインストール  
  ```
  brew install rsync
  ```
* /usr/local/bin にインストールされたバージョン 3 の rsync を使って同期  
  ```
  /usr/local/bin/rsync -auv --rsync-path=/usr/local/bin/rsync --iconv=UTF-8-MAC,UTF-8-MAC <転送元> <転送先>
  ```
  --rsync-path: 接続先ホストで使用する rsync のパス  
  --iconv: 使用する文字コード
