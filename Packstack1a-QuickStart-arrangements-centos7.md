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
- CentOS 7.xを最小インストールして、アップデートを行っておきます。

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

- [CentOS 7を使う場合](Packstack1a-QuickStart-arrangements-centos7.md)
- [RHEL7/Scientific Linux 7を使う場合](Packstack1b-QuickStart-arrangements-others.md)
- [Fedora 21,22,23を使う場合](Packstack1b-QuickStart-arrangements-others.md)
 
- Juno をインストールする場合の参照リポジトリー

````
# yum install -y http://rdo.fedorapeople.org/openstack-juno/rdo-release-juno.rpm
````

- Kilo をインストールする場合

CentOS 7ではCentOS Cloud SIGで用意しているパッケージを利用できます。

````
# yum install -y centos-release-openstack-kilo
````


- Liberty をインストールする場合

CentOS 7ではCentOS Cloud SIGで用意しているパッケージを利用できます。

````
# yum install -y centos-release-openstack-liberty
````


- システムアップデートとパッケージのインストール

````
# yum update -y && yum install -y openstack-packstack
````

###PackstackによるOpenStackのデプロイ

リポジトリーパッケージのインストール終わったら、[インストールガイド](RDO-QuickStart-installations.md)に進みます。