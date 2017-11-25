---
title: Initial Server Setup with CentOS 7
date: 2017-11-25 18:00:00
tags:
---

# Initial Server Setup with CentOS 7

趁者 Black Friday 特價入手了 VPS 主機，在此記錄基本的配置路程

## 修改 root 密碼

以 root 的身份使用 `passwd` 指令即可更改密碼

```sh
passwd
```

## 重新產生 SSH Host keys

移除既存的 SSH Host Keys

```sh
rm -f /etc/ssh/ssh_host_*
```

透過 `ssh-keygen` 重新自動產生

```sh
ssh-keygen -A
```

如果無法透過 `-A` 參數自動生成，亦可透過以下指令產生

```sh
ssh-keygen -t dsa -N "" -f /etc/ssh/ssh_host_dsa_key
ssh-keygen -t rsa -N "" -f /etc/ssh/ssh_host_rsa_key
ssh-keygen -t ecdsa -N "" -f /etc/ssh/ssh_host_ecdsa_key
ssh-keygen -t ed25519 -N "" -f /etc/ssh/ssh_host_ed25519_key
```

## 新增使用者


新增一名使用者並給與 `sudo` 的權限（透過加入 `wheel` 群組）

```sh
add user -G wheel -c "Demo User" demo
```

設置新使用者的密碼

```sh
passwd demo
```

## 禁止 root 使用 ssh 登入

使用文本編輯器開啟 `/etc/ssh/sshd_config`，在此使用 Vim

```sh
vim /etc/ssh/sshd_config
```

將選項 `PermitRootLogin` 設置為 `no`

```yaml
PermitRootLogin no
```

重新啟動 sshd 服務

```sh
systemctl restart sshd.service
```

## 修改 SSH Port

確認 `Firewalld` 是否正在運行

```
systemctl status firewalld
```

確認當前 `Firewalld` 的設定

```
firewall-cmd  --list-all --permanent 
```

## Ref.

* [DigitalOcean: New CentOS 7 Server Checklist](https://www.digitalocean.com/community/tutorial_series/new-centos-7-server-checklist)
* [安裝 CentOS 7 後必做的七件事](https://www.hkpug.net/2014/09/18/%E5%AE%89%E8%A3%9D-centos-7-%E5%BE%8C%E5%BF%85%E5%81%9A%E7%9A%84%E4%B8%83%E4%BB%B6%E4%BA%8B/)
* [在 CentOS 7 中修改 sshd 的端口](https://github.com/zbinlin/blog/blob/master/change-sshd-port-in-centos7.md)

