# snmp

## mac
* 起動: sudo launchctl load -w /System/Library/LaunchDaemons/org.net-snmp.snmpd.plist
* 停止: sudo launchctl unload -w /System/Library/LaunchDaemons/org.net-snmp.snmpd.plist
* 確認: snmpwalk -v 2c -c public localhost
## コマンド
* 実メモリ: snmpwalk -v 1 -c private localhost .1.3.6.1.4.1.2021.4.6
* 空きメモリ: snmpwalk -v 1 -c private localhost .1.3.6.1.4.1.2021.4.11
* ディスク容量: snmpwalk -v 1 -c private localhost .1.3.6.1.4.1.2021.9.1.6
* ディスク空容量: snmpwalk -v 1 -c private localhost .1.3.6.1.4.1.2021.9.1.7
* ディスク使用率: snmpwalk -v 1 -c private localhost .1.3.6.1.4.1.2021.4.9
* ディスクパス: snmpwalk -v 1 -c private localhost .1.3.6.1.4.1.2021.9.1.2
* デバイス名: snmpwalk -v 1 -c private localhost .1.3.6.1.4.1.2021.9.1.3
