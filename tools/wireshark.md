- Table of Content  
{:toc}

# Wireshark

## キャプチャ後の表示フィルタ

* 指定したNo以降のパケットのみ表示  
(例) 5番目以降のパケットのみ表示  
`frame.number >= 5`
* 指定した時刻以降のパケットのみ表示  
(例) 2010年10月1日 10:30:20以降のパケットのみ表示  
`frame.time>="Oct 1, 2010 10:30:20"`
* http のみ表示  
http
* SIP と RTP のみ表示  
sip || rtp

## キャプチャしたファイルから条件にマッチするパケットを別ファイルに出力

* キャプチャファイルをフィルターして特定のパケットだけを別ファイルに抽出
  ```
  tshark -r input.pcap -Y "ip.addr==192.168.1.1 && tcp.port=8080" -w output.pcap
  ```
  tcpdump を使う場合は
  ```
  tcpdump -r input.pcap "tcp and host 192.168.1.1 and port 8080" -w output.pcap
  ```
* キャプチャファイルから、TCP リセットのパケットだけをフィルタして別ファイルに抽出
  ```
  tshark -r input.pcap -Y "tcp.flags.reset==1" -w output.pcap
  ```
* 時刻の範囲を指定してフィルタ  
  editcap を使用
  ```
  editcap -A "2025-01-01 09:00:00" -B "2025-01-01 09:10:15" input.pcap output.pcap
  ```
  または tshark を使用
  ```
  tshark -r input.pcap -Y "frame.time >= \"2025-01-10 09:00:00\" && frame.time <= \"2025-01-01 09:10:15\"" -w output.pcap
  ```

## トラブルシューティング

* Mac でキャプチャしようとすると、次のダイアログメッセージが表示される。  
  「The capture session could not be initiated on interface 'en0'」  
  /dev/bpf* の権限を追加する。  
  ```
  sudo chown $(whoami):admin /dev/bpf*
  ```
  (参考) [MacOSのWiresharkが権限エラーでキャプチャできないとき](https://tex2e.github.io/blog/shell/wireshark-permission-err-macos)
