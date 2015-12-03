#Packstack Howto 準備編

最終更新日: 2015/12/03


##この文書について
この文書はとりあえず1台に全部入りのOpenStack環境をさくっと構築する場合の手順です。細かいことは省いてしまったので、もう少し細かい手順については次のページの情報などを参考にしてください。

- [Juno](https://github.com/ytooyama/rdo-juno)
- [Kilo](https://github.com/ytooyama/rdo-kilo)
- Liberty
- [その他](https://github.com/ytooyama?tab=repositories)


##前提

- 2Core,4GBメモリー,30GBディスク以上の環境を用意します。
- 固定IPアドレスを設定しておきます。
- 本例はNIC eth1がインターネットゲートウェイと接続されているNICであると想定します（違う場合は読み替えてください）。

##Step 1: インストールまでの流れ

###OSのインストールとアップデート
- RHEL 7.xやScientidfic Linux 7.x、Fedora 21-23を最小インストールして、アップデートを行っておきます。

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

Kilo以降では[CentOS 7を使う場合はこちらの方法](Packstack1a-QuickStart-arrangements-centos7.md)でも構築可能です。ちなみにRDO版のパッケージの方が少々新しいです。

---
 
- Juno をインストールする場合の参照リポジトリー

````
# yum install -y http://rdo.fedorapeople.org/openstack-juno/rdo-release-juno.rpm
````

---

- Kilo をインストールする場合

RHEL7,Scientific Linux7,Fedora 21及び22ではRDOリポジトリーパッケージを利用できます。

````
# yum install -y http://rdo.fedorapeople.org/openstack-kilo/rdo-release-kilo.rpm
````

Fedora 23では標準リポジトリーパッケージでKiloを構築できるので、上記リポジトリー追加は必要ありません。

---

- Liberty をインストールする場合

RHEL7,Scientific Linux7ではRDOリポジトリーパッケージを利用できます(Fedoraはサポートされません)。

````
# yum install -y https://repos.fedorapeople.org/repos/openstack/openstack-liberty/rdo-release-liberty-2.noarch.rpm
````

---

###システムアップデートとパッケージのインストール

````
# yum update -y && yum install -y openstack-packstack
````

###PackstackによるOpenStackのデプロイ

リポジトリーパッケージのインストール終わったら、[インストールガイド](RDO-QuickStart-installations.md)に進みます。