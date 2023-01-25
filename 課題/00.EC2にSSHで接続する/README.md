# EC2にSSHで接続する
## 事前準備
### Cloud 9
1. AWS Management Console のサービス検索で `Cloud9` を検索して選択
![](./img/cloud9-1.png)

2. [Create environment] をクリック
![](./img/cloud9-2.png)

3. Name を入力
![](./img/cloud9-3.png)

4. Secure Shell (SSH) を選択し、[Create] を押す
![](./img/cloud9-4.png)

5. Open を押す
![](./img/cloud9-5.png)

---
### Cloud Shell
1. AWS Management Console のサービス検索で `CloudShell` を検索して選択
![](./img/cloudshell-1.png)

2. Do not show again をチェックして [Close] を押す
![](./img/cloudshell-2.png)

## EC2を起動する
### AWS Management Console
1. AWS Management Console のサービス検索で `EC2` を検索して選択
![](./img/ec2-1.png)

2. [Launch instance] を押す
![](./img/ec2-2.png)

3. Name を入力
![](./img/ec2-3.png)

4. 使用するイメージ (AMI) を選択  
今回はデフォルト (Amazon Linuxを使用) でよい
![](./img/ec2-4.png)

5. Instance type と Key pair (login) を設定  
    * Instance Type: デフォルト(t2.micro)
    * Key pair name: vockey を選択
![](./img/ec2-5.png)

6. Network settings の設定  
今回はデフォルトでよい  
__Auto-assign public IP が Enable となっていることを確認すること__
![](./img/ec2-6.png)

7. [Launch instance] を押す
![](./img/ec2-7.png)

8. [View all instances] を押す
![](./img/ec2-8.png)

9. Instance state と Instance check を確認する
    * Instance state: Running
    * Instance check: 2/2 checks passed
![](./img/ec2-9.png)
---
### AWS CLI
1. AMI ID を確認する  
EC2 のインスタンス一覧の画面で作成した EC2 を選択、AMI IDをコピー
![](./img/ec2-cli-1.png)

2. Security Group ID を確認する  
EC2 のインスタンス一覧の画面で作成した EC2 を選択、Security タブを選択、Security group IDをコピー
![](./img/ec2-cli-2.png)

3. CloudShell を起動する

4. AWS CLI で EC2 を起動
以下のパラメータを設定

```
AMI_ID=AMI ID
SECURITY_GROUP_ID=Security Group ID
NAME=Instance Name
```

コマンドを実行

```
aws ec2 run-instances \
--image-id "$AMI_ID" \
--count 1 \
--instance-type t2.micro \
--key-name vockey \
--security-group-ids "$SECURITY_GROUP_ID" \
--associate-public-ip-address \
--tag-specifications "ResourceType=instance,Tags=[{Key=Name,Value=$NAME}]"
```

5. EC2 の情報を確認  
以下のパラメータを設定

```
INSTANCE_ID=Instance ID
```

コマンドを実行

```
aws ec2 describe-instances \
--instance-ids "$INSTANCE_ID"
```

実行結果を `jq` で加工する

```
aws ec2 describe-instances \
--instance-ids "$INSTANCE_ID" \
| jq '.Reservations[].Instances'
```

6. EC2 を停止する
以下のパラメータを設定

```
INSTANCE_ID=Instance ID
```

コマンドを実行

```
aws ec2 stop-instances \
--instance-ids "$INSTANCE_ID"
```

EC2 インスタンスの状態を確認  
`stopped` となれば停止完了

```
aws ec2 describe-instances \
--instance-ids "$INSTANCE_ID" \
| jq '.Reservations[].Instances[0].State.Name'
```

7. EC2 を起動する  
以下のパラメータを設定

```
INSTANCE_ID=Instance ID
```

コマンドを実行

```
aws ec2 start-instances \
--instance-ids "$INSTANCE_ID"
```

EC2 インスタンスの状態を確認

```
aws ec2 describe-instances \
--instance-ids "$INSTANCE_ID" \
| jq '.Reservations[].Instances[0].State.Name'
```

8. EC2 を終了する
以下のパラメータを設定

```
INSTANCE_ID=Instance ID
```

コマンドを実行

```
aws ec2 terminate-instances \
--instance-ids "$INSTANCE_ID"
```

EC2 インスタンスの状態を確認  
`terminated` となれば終了

```
aws ec2 describe-instances \
--instance-ids "$INSTANCE_ID" \
| jq '.Reservations[].Instances[0].State.Name'
```
