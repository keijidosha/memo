# tc

* パケロス設定  
sudo tc qdisc add dev eth0 root netem loss 1%
* パケロス設定更新  
sudo tc qdisc change dev eth0 root netem loss 3%
* 設定表示  
sudo tc qdisc show dev eth0
* パケロス設定削除  
sudo tc qdisc del dev eth0 root

* 遅延設定  
sudo tc qdisc add dev eth0 root netem delay 100ms
* 遅延設定更新  
sudo tc qdisc change dev eth0 root netem delay 200ms
* 遅延設定削除  
sudo tc qdisc del dev eth0 root

(参考) https://inokara.hateblo.jp/entry/2016/02/14/191853
