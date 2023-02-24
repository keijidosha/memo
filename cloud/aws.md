#  AWS
## CLI
### S3
* 一覧  
`aws s3 ls s3://<バケット>/<パス>/`
* アップロード  
`aws s3 cp <ファイル> s3://<バケット>/<パス>/`
* 一括アップロード  
`aws s3 sync <ディレクトリ> s3://<バケット>/<パス>/`
* 削除  
`aws s3 rm s3://<バケット>/<パス>/<ファイル>`


## EBS
* EBS パーティション拡張
  1. EBSパーティションを選択して、メニュー[アクション] - [ボリュームの変更] を選択。
  1. [サイズ] に変更後の新しいサイズを指定して、[変更] をクリック。
  1. ブロックデバイスを一覧表示。  
    `lsblk`
  1. 次のように表示された場合は、nvme0n1 を拡張。  
      ```
      nvme0n1     259:0    0   5G  0 disk 
      └─nvme0n1p1 259:1    0   3G  0 part /
      ```
  1. nvme0n1 を拡張。  
    `sudo growpart /dev/nvme0n1 1`
  1. パーティション一覧を表示。
    `df -hT`
  1. 次のように xfs パーティションの場合は、xfs_growfs コマンドで拡張。  
      ```
      Filesystem     Type      Size  Used Avail Use% Mounted on
      /dev/nvme0n1p1 xfs       3.0G  2.3G  745M  76% /
      ```
      xfs_growfs がインストールされていない場合は、xfsprogs をインストール。
  1. xfs のルートパーティションを拡張。  
    `sudo xfs_growfs -d /`  
※2回拡張すると、次は 6時間ほど拡張できなくなるようなので注意が必要。

## S3
### 特定のアカウントに対して、特定の PVC と IPアドレスからだけアクセスを許可する設定

S3コンソールの「アクセス許可」- 「バケットポリシー」で次のように設定
```
{
    "Version": "2012-10-17",
    "Id": "Policy1234567890123",
    "Statement": [
        {
            "Sid": "AllowVpc20201015",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::123456789012:user/hoge"
            },
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::bucket-hoge",
                "arn:aws:s3:::bucket-hoge/*"
            ],
            "Condition": {
                "StringEquals": {
                    "aws:sourceVpc": "vpc-12345678"
                }
            }
        },
        {
            "Sid": "AllowIp20201015",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::123456789012:user/hoge"
            },
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::bucket-hoge",
                "arn:aws:s3:::bucket-hoge/*"
            ],
            "Condition": {
                "IpAddress": {
                    "aws:SourceIp": [
                        "1.2.3.4/32",
                        "2.3.4.0/24"
                    ]
                }
            }
        }
    ]
}
```
