# セキュアなネットワークの構築
セキュアなネットワークを構築し、その上に [Growi](https://docs.growi.org/ja/) を構築する。

---
## Growi とは
Markdown でドキュメントを書くことができる Wiki ツールです。

---
## この課題で作成するシステムの構成
![](./img/s2.png)

---
## 環境の初期化
CloudFormation を使用して環境を初期化します。

初期化後の環境は以下のようになります。
![](./img/s1.png)

1. CloudShell を起動する

2. [template.yaml](./cfn/template.yaml) を CloudShell 上にダウンロードする

    以下のコマンドを実行して、[template.yaml](./cfn/template.yaml) を CloudShell 上にダウンロードする
    ```
    curl https://raw.githubusercontent.com/cupperservice/HJ-2023/main/%E8%AA%B2%E9%A1%8C/04.%E3%82%BB%E3%82%AD%E3%83%A5%E3%82%A2%E3%81%AA%E3%83%8D%E3%83%83%E3%83%88%E3%83%AF%E3%83%BC%E3%82%AF%E3%81%AE%E6%A7%8B%E7%AF%89/cfn/template.yaml -o template.yaml
    ```

3. 環境を初期化  
    CloudFormation の Stack を作成

    以下のコマンドを実行して環境を初期化します。
    ```
    aws cloudformation create-stack \
    --stack-name initialize \
    --template-body file://template.yaml
    ```
---
## 環境を構築する
### EC2 インスタンスを用意する
#### __Web サーバを用意する__
1. Web サーバ用のセキュリティグループを作成する
    * Security group name: web security group
    * Description: for web server
    * VPC: MyVPC (環境の初期化で作成した VPC)
    * Inbound Roles: 以下の2つのルールを追加

      |Type  |Port|Source                |
      |:-----|:---|:---------------------|
      |SSH   |22  |bastion security group|
      |HTTP  |80  |AnywhereIPv4          |

2. EC2 インスタンスを作成する
  * Name: web
  * AMI: Amazon Linux 2023 を使用する
  * Key pair: vockey を使用する
  * Network Settings で [Edit] を押す
    * VPC: MyVPC (環境の初期化で作成した VPC) を選択
    * Subnet: Public-subnet1 (環境の初期化で作成した Subnet) を選択
    * Auto-assign public IP: Enable を選択
  * Firewall (security groups): web security group (1.で作成したセキュリティグループ) を選択

#### __Application サーバを用意する__
1. Application サーバ用のセキュリティグループを作成する
    * Security group name: application security group
    * Description: for application server
    * VPC: MyVPC (環境の初期化で作成した VPC)
    * Inbound Roles: 以下の2つのルールを追加

      |Type        |Port| Source               |
      |:-----------|:---|:---------------------|
      |SSH         |22  |bastion security group|
      |Custom TCP  |3000|web security group    |

2. EC2 インスタンスを作成する
  * Name: application
  * AMI: Amazon Linux 2 を使用する (Amazon Linux 2023 では growi は動作しない)  * Key pair: vockey を使用する
  * Network Settings で [Edit] を押す
    * VPC: MyVPC (環境の初期化で作成した VPC) を選択
    * Subnet: Private-subnet1 (環境の初期化で作成した Subnet) を選択
    * Auto-assign public IP: Disable を選択
  * Firewall (security groups): web security group (1.で作成したセキュリティグループ) を選択

#### __MongoDB サーバを用意する__
1. MongoDB サーバ用のセキュリティグループを作成する
    * Security group name: mongodb security group
    * Description: for mongodb server
    * VPC: MyVPC (環境の初期化で作成した VPC)
   * Inbound Roles: 以下の2つのルールを追加

      |Type        |Port | Source                   |
      |:-----------|:----|:-------------------------|
      |SSH         |22   |bastion security group    |
      |Custom TCP  |27017|application security group|

2. EC2 インスタンスを作成する
  * Name: mongodb
  * AMI: Amazon Linux 2 を使用する (Amazon Linux 2023 では MongoDB は動作しない)
  * Key pair: vockey を使用する
  * Network Settings で [Edit] を押す
    * VPC: MyVPC (環境の初期化で作成した VPC) を選択
    * Subnet: Private-subnet1 (環境の初期化で作成した Subnet) を選択
    * Auto-assign public IP: Disable を選択
  * Firewall (security groups): application security group (1.で作成したセキュリティグループ) を選択

---
## MongoDB サーバを構築する
1. CloudShell から mongodb の EC2 インスタンスに SSH で接続する

2. 以下のファイルを作成する
    * ファイル:
      ```
      /etc/yum.repos.d/mongodb-org-6.0.repo
      ```
    * 内容
      ```
      [mongodb-org-6.0]
      name=MongoDB Repository
      baseurl=https://repo.mongodb.org/yum/amazon/2/mongodb-org/6.0/x86_64/
      gpgcheck=1
      enabled=1
      gpgkey=https://www.mongodb.org/static/pgp/server-6.0.asc
      ```

3. MongoDB をインストールする
    ```
    sudo yum install -y mongodb-org
    ```

4. インストール結果を確認する
    ```
    mongod --version
    ```
    以下のように表示されればOK
    ```
    mongod --version
    db version v6.0.7
    Build Info: {
      "version": "6.0.7",
      "gitVersion": "202ad4fda2618c652e35f5981ef2f903d8dd1f1a",
      "openSSLVersion": "OpenSSL 1.0.2k-fips  26 Jan 2017",
      "modules": [],
      "allocator": "tcmalloc",
      "environment": {
        "distmod": "amazon2",
        "distarch": "x86_64",
        "target_arch": "x86_64"
      }
    }
    ```

5. MongoDB を起動する
    ```
    sudo systemctl start mongod
    ```

6. 自動起動を有効にする
    ```
    sudo systemctl enable mongod
    ```

7. MongoDB の設定ファイルを編集する  
    リモートから MongoDB にアクセスできるように設定ファイルを変更する
    ```
    sudo vi /etc/mongod.conf
    ```
    `bindIp` の値を mongodb の EC2 インスタンスの Private IP アドレスに変更する

    * 変更前
      ```
      # network interfaces
      net:
        port: 27017
        bindIp: 127.0.0.1  # Enter 0.0.0.0,:: to bind to all IPv4 and IPv6 addresses or, alternatively, use the net.bindIpAll setting.
      ```
    * 変更後
      ```
      # network interfaces
      net:
        port: 27017
        bindIp: mongodb EC2 インスタンスの Private IP
      ```

8. MongoDB を再起動する
    ```
    sudo systemctl restart mongod
    ```

9. MongoDB に接続できるか確認する
    以下のコマンドを実行して MongoDB に接続できることを確認する
    ```
    mongosh --host mongodb EC2 インスタンスの Private IP アドレス
    ```

    以下のように MongoDB shell が起動すればOK
    ```
    mongosh --host 10.0.30.46
    Current Mongosh Log ID: 64a532a9a3cb3b62677c402e
    Connecting to:          mongodb://10.0.30.46:27017/?directConnection=true&appName=mongosh+1.10.1
    Using MongoDB:          6.0.7
    Using Mongosh:          1.10.1

    For mongosh info see: https://docs.mongodb.com/mongodb-shell/

    ------
      The server generated these startup warnings when booting
      2023-07-05T09:04:13.226+00:00: Access control is not enabled for the database. Read and write access to data and configuration is unrestricted
       2023-07-05T09:04:13.226+00:00: vm.max_map_count is too low
    ------

    test>
    ```
---
## Application サーバを構築する
1. nodejs をインストールする  
    以下のコマンドを実行して nodejs 関連のパッケージをインストールする
    ```
    curl -sL https://deb.nodesource.com/setup_14.x -o nodesource_setup.sh
    ```
    ```
    sudo bash nodesource_setup.sh
    ```
    ```
    sudo apt-get install nodejs
    ```
    ```
    sudo npm install -g yarn
    ```

2. growi をセットアップする
    ```
    sudo mkdir /opt
    ```
    ```
    sudo chown ubuntu /opt
    ```
    ```
    cd /opt
    ```
    ```
    git clone https://github.com/weseek/growi.git
    ```
    ```
    cd growi
    ```
    ```
    git checkout -b v4.5.8 refs/tags/v4.5.8
    ```
    ```
    npm install lerna bootstrap
    ```
    ```
    npx lerna bootstrap
    ```

`/opt/growi/growi.conf`

```
NODE_ENV=production
PASSWORD_SEED="`openssl rand -base64 128 | head -1`"
MONGO_URI="mongodb://Private IP Address of Database Server:27017/growi"
FILE_UPLOAD=local
```

`/etc/systemd/system/growi.service`

```
[Unit]
Description=Growi
After=network.target

[Service]
WorkingDirectory=/opt/growi
EnvironmentFile=/etc/sysconfig/growi
ExecStart=/usr/bin/npm start

[Install]
WantedBy=multi-user.target
```



---
## Web サーバを構築する
### Nginx をインストールする
1. Nginx をインストールする EC2 インスタンスの Name を以下のように変更する  
Nginx Web Server

2. EC2 インスタンスに CloudShell から SSH で接続する
  * [接続方法](https://github.com/cupperservice/HJ-2023/blob/main/%E8%AA%B2%E9%A1%8C/00.EC2%E3%82%92%E8%B5%B7%E5%8B%95%E3%81%99%E3%82%8B/README.md#ec2-%E3%82%A4%E3%83%B3%E3%82%B9%E3%82%BF%E3%83%B3%E3%82%B9%E3%81%ABssh-%E3%81%A7%E6%8E%A5%E7%B6%9A)

3. nginx をインストールする

    `sudo dnf install nginx -y`

4. nginx を起動する

    `sudo systemctl start nginx`

5. nginx の自動起動を有効にする

    `sudo systemctl enable nginx`

### リバースプロキシの設定
1. ファイルをダウンロードする  
以下のコマンドを実行して Growi にリクエストを転送するための定義ファイルをダウンロードする  
    ```
    sudo curl https://raw.githubusercontent.com/cupperservice/HJ-2023/main/%E8%AA%B2%E9%A1%8C/04.%E3%82%BB%E3%82%AD%E3%83%A5%E3%82%A2%E3%81%AA%E3%83%8D%E3%83%83%E3%83%88%E3%83%AF%E3%83%BC%E3%82%AF%E3%81%AE%E6%A7%8B%E7%AF%89/conf/growi.conf -o /etc/nginx/conf.d/growi.conf
    ```

2. 1.のファイルを編集  
    vi で編集する例  
    `sudo vi /etc/nginx/conf.d/growi.conf`

    `<application>`の部分を Application サーバの Private IPv4 address に変更する

    * 編集前
        ```
        upstream growi {
            server <application>:3000;
        }
        ```

    * 編集後の例
        ```
        upstream growi {
            server 10.0.10.200:3000;
        }
        ```

    `server_name`を探して、`<server>` を Web サーバの Public IPv4 address に変更する

    * 編集前
        ```
        server {
            listen 80;
            server_name <server>;
        ```

    * 編集後の例
        ```
        server {
            listen 80;
            server_name 54.175.66.232;
        ```

3. Nginx を再起動する  
以下のコマンドを実行して Nginx を再起動する  
`sudo systemctl restart nginx`
