- Table of Content  
{:toc}

#  AWS

## EC2

### デフォルト OS アカウント

* Amazon Linux AMI: ec2-user
* Centos AMI: centos または ec2-user
* Debian AMI: admin
* Fedora AMI: fedora または ec2-user
* RHEL AMI: ec2-user または root
* SUSE AMI: ec2-user または root
* Ubuntu AMI: ubuntu
* Oracle AMI: ec2-user
* Bitnami AMI: bitnami

https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/managing-users.html より

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
* aws cli がインストールされていない環境から S3 上に置いたファイルをダウンロード
  * まず aws cli がインストールされた環境で一時的な S3 の URL を生成
    ```
    aws s3 presign s3://my-bucket/path/file --expires-in 300
    ```
    300秒間有効な URL を生成
  * 生成された URL を aws cli がインストールされていない環境から curl でダウンロード
    ```
    curl -o <filename> <生成された一時URL>
    ```

### EC2
* インスタンスのリストを取得
  ```
  aws ec2 describe-instances --profile=taro --region us-east-1
  ```
* インスタンスを作成
  ```
  aws ec2 run-instances --dry-run --region us-east-1 \
    --image-id ami-12345678901234567 --count 1 \
    --instance-type t3a.nano --key-name HogeKey \
    --subnet-id subnet-12345678 subnet-abcdefgh \
    --security-group-ids sg-12345678901234567 \
    --disable-api-termination \
    --tag-specifications "ResourceType=instance,Tags=[{Key=Name,Value=hoge},{Key=id,Value=123}]" \
    "ResourceType=volume,Tags=[{Key=Name,Value=hoge},{Key=id,Value=123}]" \
    --credit-specification CpuCredits=standard \
    --ebs-optimized \
    --block-device-mappings '[{"DeviceName":"/dev/sda1","Ebs":{"VolumeSize":10,"VolumeType":"gp3","DeleteOnTermination":true}}]' \
    --private-ip-address 192.168.1.1 \
    --associate-public-ip-address \
    --network-interfaces '[{"DeviceIndex":0,"NetworkInterfaceId":"eni-xxx"}]' \
    --profile=otp-mfa-acc
  ```  
  "VolumeSize":10: ボリュームサイズ 10GB  
  otp-mfa-acc: 後述の認証で取得した情報をセットしたプロファイル名。インスタスン作成に必要な権限を持つアカウントを使って実行する場合は不要。  
  `--network-interfaces` を指定する場合は、次の項目は指定できない模様。  
  => これらの情報が ENI に関連づけられているから?  
  * --security-group-ids
  * --subnet-id
  * --associate-public-ip-address
  * --private-ip-address
  

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
     `--serial-number` には IAM の「セキュリティ認証情報」タブの「多要素認証(MFA)」に表示されている識別子を指定。
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

### その他

* CLI 実行環境で使われる権限を表示  
  ```
  aws sts get-caller-identity
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
