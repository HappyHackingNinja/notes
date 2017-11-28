---
title: Initial Server Setup with CentOS 7
date: 2017-11-25 18:00:00
tags:
---

# Initial Server Setup with CentOS 7

趁者 Black Friday 特價入手了 VPS 主機，在此記錄基本的配置路程

## 錯誤的磁碟空間（SolusVM）

如果你的供應商使用的是 SolusVM 的 VPS 管理方案，使用 CentOS KVM template 可能會遇到[ disk size 錯誤的問題](https://forum.solusvm.com/topic/6643-kvm-templates/)，必需透過 `resize2fs` 來獲取正確的空間分配

```sh
resize2fs /dev/sda1
```

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
adduser -G wheel -c "Demo User" demo
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

重新啟動 `sshd` 服務

```sh
systemctl restart sshd.service
```

## 確認防火牆是否運作

確認 `Firewalld` 是否正在運行

```sh
systemctl status firewalld
```

確認當前 `Firewalld` 的設定

```sh
firewall-cmd  --list-all --permanent 
```

## 變更 SSH 通訊埠

基於安全性考慮，將 SSH 通訊埠設為非預設的 22，尤其是使用密碼登入而非 SSH 金鑰

使用文本編輯器開啟 `/etc/ssh/sshd_config`，在此使用 Vim

```sh
vim /etc/ssh/sshd_config
```

將選項 `Port` 設置為非 22 的值，避免衝突可參考 [List of TCP and UDP port numbers](https://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers)

```yaml
Port 1337
```

在 [SELinux](https://wiki.centos.org/zh-tw/HowTos/SELinux) 預設的 `Enforcing` 模式下，可以[透過 `semanage` 新增連接埠](https://wiki.centos.org/zh-tw/HowTos/SELinux#head-d2fe217dac7ca26c1bd517fb864b9b8809d6f5bf)

如果出現 `semanage command not found` 錯誤，需先安裝 `policycoreutils-python` 套件

```sh
yum -y install policycoreutils-python
```

於 `ssh_port_t` 類型中增加新設定的 SSH 通訊埠值

```sh
semanage port -a -t ssh_port_t -p tcp 1337
```

可以列出 SELinux 僅 `ssh_port_t` 類型可存取的通訊埠來檢查剛寫入的設定

```sh
semanage port -l | grep ssh_port_t
```

此外還需要將新設定的 SSH Port 加入到防火牆規則中

```sh
firewall-cmd --permanent --zone=public --add-port=1337/tcp
```

接著重新載入防火牆規則

```sh
firewall-cmd --reload
```

重新啟動 `sshd` 服務

```sh
systemctl restart sshd.service
```

透過 `ss -tlnp` 確認 `sshd` 是否載入新的設定

```sh
ss -tlnp | grep ssh
```


## Ref.

* [DigitalOcean: New CentOS 7 Server Checklist](https://www.digitalocean.com/community/tutorial_series/new-centos-7-server-checklist)
* [安裝 CentOS 7 後必做的七件事](https://www.hkpug.net/2014/09/18/%E5%AE%89%E8%A3%9D-centos-7-%E5%BE%8C%E5%BF%85%E5%81%9A%E7%9A%84%E4%B8%83%E4%BB%B6%E4%BA%8B/)
* [How To Change OpenSSH Port On CentOS 7](https://www.liberiangeek.net/2014/11/change-openssh-port-centos-7/)
