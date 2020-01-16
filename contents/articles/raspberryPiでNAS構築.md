# ラズパイでNAS構築をする

![漬物石](https://pbs.twimg.com/media/EMJWMlFU4AAVUQb?format=jpg&name=4096x4096)

- RaspberryPi 3B
- BAFFALOのHDD 4TB

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

- fdiskの画面でやること
  - "n"を入力でパーティションの作成
    - 分割数、容量などはよしなに
  - "p"でパーティションの確認
  - 問題なければ"w"で反映


```.bash
sudo mkfs.exfat /dev/sdb1
```

```.bash
sudo blkid /dev/sdb1
// UUIDの確認をする。
```

```.bash
sudo vim /etc/auto.master

-----

/mnt    /etc/auto.misc
// マウント先, 詳細ファイル
```

```
df -h
```
でマウント先をしっかり確認する！

```.bash
sudo vim /etc/auto.misc

//以下記述
ssd -fstype=exfat-fuse,rw,umask=000 :/dev/disk/by-uuid/さっきのUUID
```

- sambaの設定

```.bash
sudo vim /etc/samba/smb.conf
```

```.yml
[pi]
  comment = yosinani
  path = df -hで出てきたマウント先
  public = yes
  read only = no
  browsable = yes
  force user = pi
```

- 起動

```.bash
sudo systemctl enable smbd
sudo systemctl enable nmbd
sudo reboot
```

- Windows: 適当なファイル開く画面で ¥¥192.168.0.xxx
- Mac: ネットワーク画面からアクセスみたいな画面があるはず。192.168.0.xxxを入れて

基本的に同一ネットワーク上であればRaspberryPiの端末が見えるので問題ないとは思います。

> ![](https://pbs.twimg.com/media/EMJ1Iy3UUAI2whM?format=jpg&name=small)
>
> 出来た。いえーい。

- 高速化について
  - https://blog.bnikka.com/raspberrypi/raspberrypi-samba.html
