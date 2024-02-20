---
title: "自分用メモ: 相互発見可能な複数の仮想マシンを VirtualBox 上で作成する"
date: 2024-02-21T00:01:13+09:00
draft: false
---

本当は構成自動化ツールとかを使ったほうがいい。

## 作るもの

Debian 仮想マシンの OVA ファイル。
VirtualBox で繰り返しインポートすることで、相互に名前解決可能 & ホストから SSH ログイン可能な複数の仮想マシンができる。

## ベースマシンの作成

https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-12.5.0-amd64-netinst.iso

仮想マシンを作成。
注意点
* 「自動インストールをスキップ」にチェックを入れる
* デスクトップ環境をインストールしない
* SSH デーモンをインストールする
* `apt install avahi-daemon vim`
* `PermitRootLogin yes`
* ネットワークアダプタを追加
  * NAT
  * ホストオンリーアダプター (DHCP 有効)
  * 内部ネットワーク (DHCP 有効)
* `/etc/network/interfaces`
```
auto enp0s8
iface enp0s8 inet dhcp

auto enp0s9
iface enp0s9 inet dhcp
```
* `root@bookworm-base:~/.ssh/authorized_keys` にホストの公開鍵をコピー
* `ssh-keygen`, `cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys`

作成できたら、VirtualBox の「仮想アプライアンスをエクスポート」から OVA ファイルを作成。

## VirtualBox のグローバル設定

* GUI からホストオンリーアダプターの DHCP を有効化
* `PS C:\Program Files\Oracle\VirtualBox> .\vboxmanage dhcpserver add --network=intnet --server-ip=10.10.1.1 --netmask=255.255.255.0 --lower-ip=10.10.1.100 --upper-ip=10.10.1.200 --enable`
  * アドレスは適当に決める

## インポート

* 「仮想アプライアンスのインポート」から適当に名前を変えてインポート
* `hostnamectl hostname <name>`
* `systemclt restart avahi-daemon`
