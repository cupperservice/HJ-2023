# 2023 年度 サーバー構築演習
## 前提知識
* Linux の基本的な操作

## 目的
クラウド (AWS) の基本的な内容を理解し簡単なシステムの構築ができるようになる

## 内容
### 1. 座学  
* AWSの基礎  
AWS Academy の教材を使用して クラウド（AWS）の基礎を理解する

  * 学習可能なコース
    * Cloud Foundations <- これを実施
    * Cloud Archtecting
    * Cloud Developing
    * Cloud Operations
    * Cloud Security Foundations
    * Data Analytics
    * Data Center Technician
    * Data Engineering
    * Engineering Operations Technician
    * Machine Learning Foundations
    * Machine Learning for Natural Language Processing

* ネットワークの基礎  
以下の動画を使用してネットワークの基礎を理解する
  * [OSI 参照モデル](https://www.youtube.com/watch?v=5QHXbxZIUDg)
  * [TCP/IP (1) インターネット層](https://www.youtube.com/watch?v=Pp6-YXxL06Y)
  * [TCP/IP (2) トランスポート層](https://www.youtube.com/watch?v=erY_CkAVSYE)
  * [IPv4 アドレッシング](https://www.youtube.com/watch?v=KrrK3FJNigY)

### 2. 演習  
0. [EC2を起動する](./課題/00.EC2を起動する.md)
1. [EC2でWebサーバを起動する](./課題/01.EC2でWebサーバを起動する.md)
2. [EC2でDBサーバを起動する](./課題/02.EC2でDBサーバを起動する.md)
3. [EC2でWordPressを起動する](./課題/03.EC2でWordPressを起動する.md)
4. [セキュアなネットワークの構築](./課題/04.セキュアなネットワークの構築.md)
5. [RDSを触ってみる](./課題/05.RDSを触ってみる.md)
6. [ALBを使用してWebサーバを冗長化する](./課題/06.ALBを使用してWebサーバを冗長化する.md)
7. [Auto Scaling Groupを使用してスケーラブルなWebサーバを構築する](./課題/07.AutoScalingGroupを使用してスケーラブルなWebサーバを構築する.md)
8. [S3で静的コンテンツを配信する](./課題/08.S3で静的コンテンツを配信する.md)
9. [Lambdaでサーバレス](./課題/09.Lambdaでサーバレス.md)
10. [DynamoDBを触ってみる](./課題/10.DynamoDBを触ってみる.md)
11. [メールを送信する](./課題/12.メールを送信する.md)
12. [CloudWatchで監視する](./課題/11.CloudWatchで監視する.md)
13. [CloudFormationでサーバ構築を自動化](./課題/13.CloudFormationでサーバ構築を自動化.md)
14. [Rekognitionで画像解析](./課題/14.Rekognitionで画像解析.md)
15. [Dockerでコンテナ化](./課題/15.Dockerでコンテナ化.md)
16. [スケーラブルなWebサイトを構築する](./課題/16.スケーラブルなWebサイトを構築する.md)

---
## 授業の資料置き場
* [2023/04/11](./0411/README.md)
* [2023/04/25](./0425/README.md)
* [2023/05/09](./0509/README.md)
* [2023/05/16](./0516/README.md)
* [2023/05/23](./0523/README.md)
* [2023/05/30](./0530/README.md)
* [2023/06/06](./0606/README.md)
* [2023/06/13](./0613/README.md)
* [2023/06/20](./0620/README.md)
* [2023/06/27](./0627/README.md)
* [2023/07/04](./0704/README.md)
* [2023/07/11](./0711/README.md)
* [2023/07/25](./0725/README.md)
* [2023/08/01](./0801/README.md)
* [2023/09/12](./0912/README.md)
* [2023/09/19](./0919/README.md)
* [2023/09/26](./0926/README.md)
* [2023/10/03](./1003/README.md)
* [2023/10/17](./1017/README.md)

---
## 課題一覧
本受業には以下の課題があります。  
期限は今年度中（卒業まで）です。

### 提出状況
* [提出状況一覧](https://docs.google.com/spreadsheets/d/1zU60i8eh8YeC0afDrRNGNulez7uWAOq0W8ST-GdJskg/edit?usp=sharing)

### 課題
1. AWS Academy 知識確認テスト
    - [x] モジュール1
    - [ ] モジュール2 (不要)
    - [ ] モジュール3 (不要)
    - [ ] モジュール4 (不要)
    - [x] モジュール5
    - [x] モジュール6
    - [x] モジュール7
    - [x] モジュール8
    - [ ] モジュール9 (不要)
    - [x] モジュール10

2. モジュール6  - AWS Lambda [Activity 1]
3. モジュール7  - ラボ 4 : EBS を使用する
4. モジュール8  - ラボ 5 : データベースサーバーを構築 [追加課題](./0606/README.md)
5. [EC2を起動する](./課題/00.EC2を起動する.md)
6. [EC2でWebサーバを起動する](./課題/01.EC2でWebサーバを起動する.md)
7. [EC2でDBサーバを起動する](./課題/02.EC2でDBサーバを起動する.md)
8. [EC2でWordPressを起動する](./課題/03.EC2でWordPressを起動する.md)
9. [セキュアなネットワークの構築](./課題/04.セキュアなネットワークの構築.md)
10. モジュール10 - ラボ 6 :アーキテクチャのスケールと負荷分散を行う
11. [RDSを触ってみる](./課題/05.RDSを触ってみる.md)
12. [S3で静的コンテンツを配信する](./課題/08.S3で静的コンテンツを配信する.md)
13. [Lambdaでサーバレス](./課題/09.Lambdaでサーバレス.md)
14. [ECSでWordPressを起動する](./課題/17.ECSでWordPressを起動する.md)
15. [スケーラブルなWebサイトを構築する](./課題/16.スケーラブルなWebサイトを構築する.md)
