#Packstack 準備編

最終更新日: 2016/04/14


##この文書について
この文書はとりあえず1台に全部入りのOpenStack環境をさくっと構築する場合の手順です。細かいことは省いてしまったので、もう少し細かい手順については次のページの情報などを参考にしてください。

- [Kilo](https://github.com/ytooyama/rdo-kilo)
- Mitaka (作業中)
- [その他](https://github.com/ytooyama?tab=repositories)


##前提

- 2Core,4GBメモリー,30GBディスク以上の環境を用意します。
- 固定IPアドレスを設定しておきます。
- 本例はNIC eth1がインターネットゲートウェイと接続されているNICであると想定します（違う場合は読み替えてください）。

##Step 1: インストールまでの流れ

###OSのインストールとアップデート
- RHEL 7.xやScientidfic Linux 7.x、CentOS 7.xを最小インストールして、アップデートを行っておきます。

###言語設定を行う
標準出力およびエラー出力を英語で出力するために次の設定を行います。

````
# vi /etc/environment
LANG=en_US.utf-8
LC_ALL=en_US.utf-8
````

###ネットワーク設定の変更
- 固定IPアドレスを設定します。
- ifcfg-"NIC"にDEVICEパラメーターを追記します。

(例)

````
....
NAME="eth1"
DEVICE="eth1" #追加
````

- IPアドレスの設定を適用します。うまく反映されない場合は再起動してください。

````
# ifdown eth1;ifup eth1
````

###リポジトリーパッケージのインストール

Kilo以降、CentOS 7ではCloudSIGプロジェクトがCentOSユーザー向けにパッケージを用意しています。RDOプロジェクトが用意するパッケージも利用できます。

RHEL7およびCentOS 7以外のRHEL7クローンでは、RDOプロジェクトが用意するリポジトリーパッケージをインストールすることでOpenStackのインストールが可能になります。

両者の違いとしては、RDO版のパッケージの方がCloudSIG版よりも少々新しい点です。

---

- Mitaka をインストールする場合

以下のコマンドでRDOリポジトリーのパッケージを利用できます(Fedoraはサポートされません)。

````
# yum install -y https://repos.fedorapeople.org/repos/openstack/openstack-mitaka/rdo-release-mitaka-2.noarch.rpm
````

CentOS 7ではCloudSIGプロジェクトがメンテナンスしているリポジトリーを利用できます。

````
# yum install -y centos-release-openstack-mitaka
````

---

- Liberty をインストールする場合

以下のコマンドでRDOリポジトリーのパッケージを利用できます(Fedoraはサポートされません)。

````
# yum install -y https://repos.fedorapeople.org/repos/openstack/openstack-liberty/rdo-release-liberty-2.noarch.rpm
````

CentOS 7ではCloudSIGプロジェクトがメンテナンスしているリポジトリーを利用できます。

````
# yum install -y centos-release-openstack-liberty
````

---

- Kilo をインストールする場合

RHEL7,Scientific Linux7,Fedora 21及び22ではRDOリポジトリーパッケージを利用できます。

````
# yum install -y http://rdo.fedorapeople.org/openstack-kilo/rdo-release-kilo.rpm
````

Fedora 23では標準リポジトリーパッケージでKiloを構築できるので、上記リポジトリーの追加は必要ありません。

CentOS 7ではCloudSIGプロジェクトがメンテナンスしているリポジトリーを利用できます。

````
# yum install -y centos-release-openstack-kilo
````

---

###システムアップデートとパッケージのインストール

````
# yum update -y && yum install -y openstack-packstack
````

###PackstackによるOpenStackのデプロイ

リポジトリーパッケージのインストール終わったら、[インストールガイド](Packstack2-QuickStart-installations.md)に進みます。