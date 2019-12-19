# ラズパイでNAS構築をする

![漬物石](https://pbs.twimg.com/media/EMJWMlFU4AAVUQb?format=jpg&name=4096x4096)

- RaspberryPi 3B
- BAFFALOのHDD 4TB

- 参考にしました： https://debimate.jp/2019/03/24/raspberry-pi3%E3%82%92%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%82%B5%E3%83%BC%E3%83%90samba%E5%8C%96%E3%81%97%E3%80%81linux-mac-win%E3%81%A7%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E5%85%B1%E6%9C%89/

<br/>

## 使用コマンド

- パッケージ更新

```.bash
sudo apt update
sudo apt upgrade
```

- パッケージインストール

```.bash
sudo apt install samba exfat-fuse cifs-utils autofs
```

- IPアドレス固定

```.bash
hostoname -I
sudo vim /etc/dhcpcd.conf
```

``` dhcpcd.conf
- # static ip_address=192.268.0.xxx/24
+ static ip_address=192.268.0.xxx/24
```

- IPアドレス反映のためにreboot

```.bash
reboot
```

- HDD, SSDの接続諸々確認

```.bash
sudo dmesg | tail
sudo wipefs -a /dev/(sda | sdb)
sudo fdisk /dev/sd(hogehoge)
```

> (注釈)：次の順で実行
　n：パーティションの作成
    ⇒ 以降の選択肢は全て[Enter]
　p：パーティションの表示
    ⇒ /dev/sdb1にType=Linux のパーティションが表示されればOK
　w：書き込み（設定を反映）
　q：終了

```.bash
sudo mkfs.exfat /dev/sdb1
```

```.bash
$ sudo blkid /dev/sdb1  (注釈)：UUIDの確認
/dev/sdb1: LABEL="NFS" UUID="5B2B-6E3C" TYPE="exfat" PARTUUID="6b70f462-01"

$ sudo vi /etc/auto.master

[以下、/etc/auto.master内]
# 以下を追記。
# マウントポイント　マウント対処を記載したファイル名　　オプション
/mnt    /etc/auto.misc
```

```
df -h
```
でマウント先をしっかり確認する！

- Windows: 適当なファイル開く画面で ¥¥192.168.0.xxx
- Mac: ネットワーク画面からアクセスみたいな画面があるはず。192.168.0.xxx入れてどうぞ

基本的に同一ネットワーク上であればRaspberryPiの端末が見えるので問題ないとは思います。

> ![](https://pbs.twimg.com/media/EMJ1Iy3UUAI2whM?format=jpg&name=small)
> 出来た。いえーい。