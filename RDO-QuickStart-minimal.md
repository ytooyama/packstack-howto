#Packstack Howto

最終更新日: 2015/10/28

##この文書について
この文書はとりあえず1台に全部入りのOpenStack環境をさくっと構築する場合の手順です。細かいことは省いてしまったので、もう少し細かい手順については次のページの情報などを参考にしてください。

- [Juno](https://github.com/ytooyama/rdo-juno)
- [Kilo](https://github.com/ytooyama/rdo-kilo)
- Liberty
- [その他](https://github.com/ytooyama?tab=repositories)


##前提

- 2Core,4GBメモリー,30GBディスク以上の環境を用意します。
- CentOS 7 64bit版をインストールし`yum update`して最新の状態にします。
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
 
- Juno をインストールする場合の参照リポジトリー

````
# yum install -y http://rdo.fedorapeople.org/openstack-juno/rdo-release-juno.rpm
````

- Kilo をインストールする場合

CentOS Cloud SIGで用意しているパッケージを使うか、RDOのリポジトリーのパッケージを使うかの2つの方法があります。

````
# yum install -y centos-release-openstack-kilo
or
# yum install -y http://rdo.fedorapeople.org/openstack-kilo/rdo-release-kilo.rpm
````

- Liberty をインストールする場合

````
# yum install -y centos-release-openstack-liberty
````

- システムアップデートとパッケージのインストール

````
# yum update -y && yum install -y openstack-packstack
````

###PackstackによるOpenStackのデプロイ

````
# setenforce 0
# packstack --allinone --default-password=password \
 --provision-demo=n --use-epel=y
...
 **** Installation completed successfully ******
````

--provision-demo=yとすると、デモ用のネットワークやユーザーなどが作られ、OpenStackの一通りの操作をすぐ実行できます。ただしデモ用のネットワークはクローズドなので、外部からアクセス不可（後でそれを可能にするには、Neutronネットワークの作り直しが必要）なので注意。

エラーが出ず、インストールが正常に完了すれば「Installation completed successfully」と表示されます。

- NetworkManagerからnetworkサービスへの切り替え

Packstackの構築完了後に切り替えを行います。

```` 
# systemctl disable NetworkManager
# systemctl enable network
````


##Step 2: ブラウザーでアクセス

インストール後に表示されるDashboardのURLにブラウザでアクセスしてみます。ユーザー=admin、パスワード=passwordでログインできます。

/rootディレクトリー上にkeystonerc_adminというRCファイルが作られており、そのファイルでも確認できます。

![Dashboard Login](./images/login.png)


##Step 3: ネットワーク設定の変更

次に外部と通信できるようにするための設定を行います。外部ネットワークとの接続を提供するノード(ネットワークノード、1台構成時はそのマシン)に仮想ネットワークブリッジインターフェイスであるbr-exを設定します。

本例ではホストに二つのNICがあり、eth1がインターネット側につながっている場合を例とします。eth1にゲートウェイが設定されていることを確認します。

###◆public用として使うNICの設定ファイルを修正
Packstackコマンド実行後、eth1をbr-exにつなぐように設定をします(※BOOTPROTOは設定しない)

eth1からIPアドレス、サブネットマスク、ゲートウェイの設定を削除して次の項目だけを記述し、br-exの方に設定を書き込みます｡

````
# vi /etc/sysconfig/network-scripts/ifcfg-eth1
DEVICE=eth1
ONBOOT=yes
HWADDR=xx:xx:xx:xx:xx:xx # Your eth1's hwaddr
TYPE=OVSPort
DEVICETYPE=ovs
OVS_BRIDGE=br-ex
````

###◆ブリッジインターフェイスの作成
br-exにeth1のIPアドレスを設定します。

````
# vi /etc/sysconfig/network-scripts/ifcfg-br-ex
DEVICE=br-ex
ONBOOT=yes
DEVICETYPE=ovs
TYPE=OVSBridge
OVSBOOTPROTO=none
OVSDHCPINTERFACES=eth1 #インターネットに接続されている方のデバイス
IPADDR=192.168.1.10
NETMASK=255.255.255.0  # netmask
GATEWAY=192.168.1.1    # gateway
DNS1=8.8.8.8           # nameserver
DNS2=8.8.4.4
````

###◆SELinuxの設定
SELinuxが有効の状態でも動作するように調整します。All-in-Oneでインストールしたので、次の設定を追加します。

````
# setsebool -P httpd_use_openstack on
# setsebool -P neutron_can_network on
# setsebool -P glance_api_can_network on
# setsebool -P swift_can_network on
````

###◆再起動
ここまでできたらいったんホストを再起動します。

````
# reboot
````

###◆動作確認
Packstackインストーラーによるインストール時にエラー出力がされなければ問題はありませんが、念のためbr-exとNova、Neutronエージェントが導入されてかつ正しく認識されていることを確認しましょう。

まずは再起動後にbr-exが正しく動作し、外のネットワークとつながっていることを確認します。

````
# ip a s br-ex | grep inet
    inet 192.168.1.10/24 brd 192.168.1.255 scope global br-ex
    inet6 fe80::54d3:7dff:fee0:a046/64 scope link
# ping 8.8.8.8 -c 3 -I br-ex | grep "packet loss"
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
````

パケットロスがないことを確認します。

つぎに、OpenStack NovaコンポーネントのステートがOKであることを確認します。

````
# source /root/keystonerc_admin
(adminユーザー認証情報を読み込む)
# nova-manage service list
Binary           Host      Zone             Status     State Updated_At
nova-consoleauth node1     internal         enabled    :-)   2015-10-26 04:30:59
nova-scheduler   node1     internal         enabled    :-)   2015-10-26 04:30:57
nova-conductor   node1     internal         enabled    :-)   2015-10-26 04:30:59
nova-compute     node1     nova             enabled    :-)   2015-10-26 04:31:01
nova-cert        node1     internal         enabled    :-)   2015-10-26 04:30:59
````

最後に、NeutronのエージェントがOKであることを確認します。

````
# neutron agent-list -c agent_type -c host -c alive
+--------------------+---------+-------+
| agent_type         | host    | alive |
+--------------------+---------+-------+
| Metadata agent     | node1   | :-)   |
| L3 agent           | node1   | :-)   |
| Open vSwitch agent | node1   | :-)   |
| DHCP agent         | node1   | :-)   |
+--------------------+---------+-------+
````

KiloでUnable to establish connection to http://xxx.xxx.xxx.xxx:5000/v2.0/tokens といったエラーが出た場合はKeystoneが正常に動いていないので、httpdを再起動してみてください。その後、keystone token-getなどのコマンドで応答が返ってくれば問題ないです。


##この後の設定について

次にNeutron Networkを作成します。「Neutron ネットワークの設定」の手順に従って、Neutronネットワークを作成してください。

- [Junoの場合](https://github.com/ytooyama/rdo-icehouse/blob/master/2-RDO-QuickStart-Networking.md)
- [Kilo以降の場合](https://github.com/ytooyama/rdo-kilo/blob/master/2-RDO-QuickStart-Networking.md)