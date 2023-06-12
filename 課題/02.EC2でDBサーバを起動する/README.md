# EC2 で DB サーバを起動する

## DB サーバをインストールする EC2 インスタンスを起動する
1. セキュリティグループを作成する
  * VPC サービスに移動
  * 左のメニューから Security groups を選択
  * [Create Security Group] を押す
  * 以下の項目を入力
    * Security group name: web-sercurity
    * Description: for web server
    * VPC: default
  * [Add Rule] を押して Inbound rules を追加 (HTTP)
    * Type: HTTP
    * Source type: Anywhere-IPv4
  * [Add Rule] を押して Inbound rules を追加 (SSH)
    * Type: SSH
    * Source type: Anywhere-IPv4
  * [Create security group] を押す

2. EC2 インスタンスを 2つ作成する
  * EC2 サービスに移動
  * 左のメニューから instances を選択
  * [Launch instances] を押す
  * Name: Web Server
  * AMI: Amazon Linux 2023 を使用する
  * Key pair: vockey を使用する
  * Public IP アドレスを割り当てる
  * Number of instances: 2を入力する
  * Firewall (security groups): 1.で作成したセキュリティグループを選択する

パッケージを最新に更新する
sudo dnf update -y

インストール
sudo dnf install mariadb105-server

確認
dnf info mariadb105

起動
sudo systemctl start mariadb

セキュア設定
sudo mysql_secure_installation

有効化
sudo systemctl enable mariadb
