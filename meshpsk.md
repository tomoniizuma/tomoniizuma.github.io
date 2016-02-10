安全な無線メッシュネットワークの作り方(WPA2-PSK編)

#はじめに
アドホックネットワークや無線メッシュネットワークを扱うウェブ上の記事は学術的な理論について解説したものが多く，具体的な構築方法について解説された記事はあまり見当たりません．
また，数少ない構築方法に関する記事で構築されるネットワークシステムの多くは，認証機能を持たない，IEEE 802.11のアドホックモードを用いるものであることが多いです．

本稿ではある程度のセキュリティ機能を有する無線メッシュネットワークの構築方法を解説します．メッシュネットワークのルーティングにはbatman-advを用います．

#安全な無線メッシュネットワークとは？


「安全な無線メッシュネットワーク」として，以下の機能を持つ無線メッシュネットワークを想定します．

* 利用者端末の認証機能
* メッシュノードの認証機能
* 無線通信区間の暗号化

本稿では，WPA2-PSK方式を用いて利用者端末，メッシュノードを認証し,無線通信区間を暗号化します．

また，通信区間を暗号化する方法として，２つの方式が考えられます．

* エンドツーエンドの暗号化：各メッシュノード↔ゲートウェイ(無線メッシュネットワークと外部ネットワークの接続点)間で鍵を共有して暗号化する方法．
* ホップバイホップの暗号化：各メッシュノード↔隣接ノード間で鍵を共有して暗号化する方法．

本稿では，後者の方法を用いて通信区間を暗号化します.


  
    
#メッシュノードの作成

## 用意するもの
メッシュノードとして，以下の要件を満たす計算機を用意します．


* Linuxが動作すること．本記事ではこれ以降，Debianを用いたメッシュノードの作成方法を解説します．
* 無線NICを2つ以上有すること．
利用者端末用のAPとなる場合，ゲートウェイとなる場合については，それぞれ追加のNICが必要です．

無線NICが足りない場合は適宜USBの無線NICを足してください．
例:http://buffalo.jp/product/wireless-lan/client/wli-uc-gnm2/

##ソフトウェアのインストールとbatman-advのロード

用意した計算機にOSをインストールした後，以下のソフトウェアをapt-getなどを用いてインストールします．

* hostapd：アクセスポイントとなるソフトウェア．サプリカントから接続を受けます．また，今回はWPA2-PSKで利用者とメッシュノードを認証します．
* wpa_supplicant：サプリカントとなるソフトウェア．APに接続します．
* bridge-utils：仮想ブリッジソフトウェア
* batctl：batman-advのコマンドラインから設定するツール


次に，batman-advをカーネルにロードします．

```xml:
# modprobe batman-adv
```

起動時に自動で読み込まれるように
/etc/modules
に`batman-adv`と書き込んでおくと良いでしょう．

## ネットワークの設定
Debianでは，/etc/network/interfacesに設定を書き込みます．
また，以下に示すノードの種類に応じてネットワークに関する設定を行います．

* 普通のノード: 無線メッシュネットワークを形成するだけのノード
* 利用者端末用AP: 無線メッシュネットワークを形成する機能を持ち，メッシュルーティング機能を持たない端末から接続されるAP
* ゲートウェイノード: 無線メッシュネットワークを形成する機能を持ち，外部ネットワークとの接続点となるノード

ノードごとに/etc/network/interfacesを以下に記載します．
### 普通のノード

普通のノードでは，bat0にIPアドレスを割り当て，bat0におけるルーティングをbatman-advが制御します．
イメージ的にはbat0がL2のスイッチになるといった感じでしょうか．

NICの構成図

```perl:
       bat0 (<- IPアドレス)
       /    \
  wlan0      wlan1
```

/etc/network/interfaces

```perl:
auto lo
iface lo inet loopback

auto bat0
iface bat0 inet dhcp

auto wlan0
iface wlan0 inet manual
pre-up /sbin/ifconfig wlan0 mtu 1532
pre-up /usr/sbin/batctl if add wlan0
pre-up /sbin/wpa_supplicant -B -iwlan0 -Dnl80211 -c /etc/wpa_supplicant/wpa_supplicant.conf

auto wlan1
iface wlan1 inet manual
pre-up /sbin/ifconfig wlan1 mtu 1532
pre-up /usr/sbin/batctl if add wlan1

```

* wlan0: サプリカントとなるインターフェース
* wlan1: AP(メッシュノード用)となるインターフェース
* bat0: メッシュルーティング用の仮想ブリッジ

### 利用者端末用AP

/etc/network/interfacesの末尾に仮想ブリッジbr0用の設定を追加します．
このタイプのノードでは，利用者用のAPとなるwlan2が加わり，bat0とwlan2がbr0にブリッジされます．
/etc/network/interfacesにwlan2の記載はありませんが，後述するhostapd.confの設定(利用者端末用)で登場します．

NICの構成図

```perl:
              br0  (<- IPアドレス)
            /    \
        bat0    wlan2
       /    \
  wlan0      wlan1
```

/etc/network/interfacesに追加する部分

```perl:
auto br0
iface br0 inet dhcp
bridge_ports bat0
```

### ゲートウェイノード

/etc/network/interfacesにイーサネットeth0と仮想ブリッジbr0用の設定を追加しときます．

NICの構成図

```perl:
              br0  (<- IPアドレス)
            /    \
        bat0     eth0
       /    \
  wlan0      wlan1
```

/etc/network/interfacesに追加する部分

```perl:
auto eth0
iface eth0 inet manual

auto br0
iface br0 inet dhcp
bridge_ports bat0 eth0
```

###hostapd.confの設定(メッシュノード用)
メッシュノード用のAPの設定です．

```
interface=wlan1 #APとなるインターフェース
driver=nl80211

ctrl_interface_group=0

hw_mode=g
ssid= MESHSSID
wpa_passphrase=XXXXXXXX #パスフレーズ
wpa=2
channel=11
wpa_key_mgmt=WPA-PSK
wpa_pairwise=CCMP
logger_syslog_level=3

```

###hostapd.confの設定(利用者端末用)
利用者端末用のAPの設定です．

```
interface=wlan2　
bridge=br0 #ここを追加
driver=nl80211

ctrl_interface_group=0

hw_mode=g
ssid= SSID #ここを変更
wpa_passphrase=YYYYYYYY 
wpa=2
channel=7 #チャンネルも変えたほうがよい
wpa_key_mgmt=WPA-PSK
wpa_pairwise=CCMP
logger_syslog_level=3

```

###wpa_supplicant.confの設定

```
network={
	ssid="MESHSSID"
	psk= XXXXXXX #パスフレーズ
}
```

#いざ構築

1. ゲートウェイノードをLANに接続する．
2. ゲートウェイノードにおいて`# hostapd /etc/hostapd/hostapd.conf`などでhostapdを起動
3. 他のメッシュノードを接続させる．
4. これをくりかえす．

2では，自動起動設定にしておいても良いでしょう．
利用者端末のAPも2と同様の方法で起動させます．

#まとめ
本稿では，利用者端末，メッシュノードの認証機能，無線通信区間の暗号化機能を持つ無線メッシュネットワークシステムをbatman-advを用いて構築する方法を解説しました．

