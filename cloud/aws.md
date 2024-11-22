- Table of Content  
{:toc}

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

### EC2
* インスタンスのリストを取得
  ```
  aws ec2 describe-instances --profile=taro --region us-east-1
  ```

### AMI
* AMI を作成
  ```
  aws ec2 create-image --profile=taro --instance-id i-1234567890abcdefg --name "hoge" --tag-specifications 'ResourceType=image,Tags=[{Key=Name,Value=hoge},{Key=env,Value=prod}]' 'ResourceType=snapshot,Tags=[{Key=Name,Value=hoge},{Key=env,Value=prod}]'
  ```
* hoge で始まる AMI の一覧
  ```
  aws ec2 describe-images --owner-ids self --filters 'Name=tag:Name,Values=hoge*' --profile taro --region us-east-1
  ```
* AMI を別アカウントに共有
  ```
  aws ec2 modify-image-attribute --profile=taro --image-id ami-1234567890abcdefg --launch-permission "Add=[{UserId=123456789012}]"
  ```
* 別アカウントで共有された AMI をコピー
  ```
  aws ec2 copy-image --source-image-id ami-1234567890abcdefg --source-region us-east-1 --region us-east-1 --name "aminame" --profile taro
  ```

### スナップショット
* hoge で始まるスナップショットの一覧
  ```
  aws ec2 describe-snapshots --owner-ids self --filters 'Name=tag:Name,Values=hoge*' --profile taro --region us-east-1
  ```
* スナップショットをアカウント間でコピーするために必要な権限を付与
  ```
  aws ec2 modify-snapshot-attribute --snapshot-id snap-1234xxx --attribute createVolumePermission --operation-type add --user-ids 123456789012 --profile taro --region us-east-1
  ```

### 認証

* MFA(2段階)認証しているアカウントで CLI を使う
  1. AWS CLI の設定ファイルにプロファイルを追加して MFA 認証が必要なアカウント情報を記述  
     ~/.aws/credentials  
     ```
     [mfa-acc]
     aws_access_key_id = ABCxxx
     aws_secret_access_key = abcxxx
     ```
  1. 認証アプリ(Authyなど)でワンタイムパスワード(OTP)を取得
  1. CLI で期限付きの認証情報を取得
     ```
     aws sts get-session-token --profile mfa-acc --serial-number arn:aws:iam::12345678:mfa/username --token-code <OTP>
     ```
     結果例
     ```
     {
       "Credentials": {
         "AccessKeyId": "ABCxxx",
         "SecretAccessKey": "abcxxx",
         "SessionToken": "xxx...",
         "Expiration": "2024-01-10T00:15:30+00:00"
       }
     }
     ```
  1. 表示された期限付きの認証情報を CLI の設定ファイルの MFA 用プロファイルに追記  
     ~/.aws/credentials  
     ```
     [otp-mfa-acc]
     aws_access_key_id = ABCxxx
     aws_secret_access_key = abcxxx
     aws_session_token = xxx...
     ```
  1. 追記したプロファイル名を指定して CLI を実行
     ```
     aws ec2 describe-instances --profile=otp-mfa-acc
     ```

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
    growpart コマンドがインストールされていない場合は、cloud-utils-growpart をインストール。
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
### 特定のアカウントに対して、特定の VPC と IPアドレスからだけアクセスを許可する設定

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
