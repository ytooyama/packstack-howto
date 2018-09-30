# Packstack 準備編

最終更新日: 2018/9/30


## この文書について

この文書はとりあえず1台に全部入りのOpenStack環境をさくっと構築する場合の手順です。細かいことは省いてしまったので、もう少し細かい手順については次のページの情報などを参考にしてください。

- [Kilo](https://github.com/ytooyama/rdo-kilo)
- [その他](https://github.com/ytooyama?tab=repositories)


## 前提

- 2Core,4GBメモリー,30GBディスク以上の環境を用意します。
- 固定IPアドレスを設定しておきます。
- 本例はNIC eth1がインターネットゲートウェイと接続されているNICであると想定します（違う場合は読み替えてください）。

## Step 1: インストールまでの流れ

### OSのインストールとアップデート

- RHEL 7.xやScientific Linux 7.x、CentOS 7.xを最小インストールして、アップデートを行っておきます。

### 言語設定を行う

標準出力およびエラー出力を英語で出力するために次の設定を行います。

````
# vi /etc/environment
LANG=en_US.utf-8
LC_ALL=en_US.utf-8
````

### ネットワーク設定の変更

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

### リポジトリーパッケージのインストール

Kilo以降、CentOS 7ではCloudSIGプロジェクトがCentOSユーザー向けにパッケージを用意しています。RDOプロジェクトが用意するパッケージも利用できます。

RHEL7およびCentOS 7以外のRHEL7クローンでは、RDOプロジェクトが用意するリポジトリーパッケージをインストールすることでOpenStackのインストールが可能になります。

両者の違いとしては、RDO版のパッケージの方がCloudSIG版よりも少々新しい点です。


---

- Queens をインストールする場合

以下のコマンドでRDOリポジトリーのパッケージを利用できます(Fedoraはサポートされません)。

````
# yum install -y https://repos.fedorapeople.org/repos/openstack/openstack-queens/rdo-release-queens-1.noarch.rpm
````

CentOS 7ではCloudSIGプロジェクトがメンテナンスしているリポジトリーを利用できます。

````
# yum install -y centos-release-openstack-queens
````

---

- Pike をインストールする場合

以下のコマンドでRDOリポジトリーのパッケージを利用できます(Fedoraはサポートされません)。

````
# yum install -y https://repos.fedorapeople.org/repos/openstack/openstack-pike/rdo-release-pike-1.noarch.rpm
````

CentOS 7ではCloudSIGプロジェクトがメンテナンスしているリポジトリーを利用できます。

````
# yum install -y centos-release-openstack-pike
````


---

- その他のバージョン をインストールする場合


* CentOS 7の場合

以下で公開されているバージョンは`centos-release-openstack-*`のインストールで容易にインストール可能です。例えばOcataリリースは`yum install centos-release-openstack-ocata`で可能です。

<http://mirror.centos.org/centos-7/7/cloud/x86_64/>

もっと古いバージョンは例えば以下にありますが、インストールは難しいと思います。

<http://vault.centos.org/7.3.1611/cloud/x86_64/>


* RHEL 7およびその他の場合

以下からrpmパッケージを見つけてインストールします。

<https://repos.fedorapeople.org/repos/openstack/>


---

### システムアップデートとパッケージのインストール

````
# yum update -y && yum install -y openstack-packstack
````

### PackstackによるOpenStackのデプロイ

リポジトリーパッケージのインストール終わったら、[インストールガイド](Packstack2-QuickStart-installations.md)に進みます。
