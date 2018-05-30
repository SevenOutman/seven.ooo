---
title: 使用 GPG key 签名你的 Git 提交
date: 2018-05-27 14:48:10
tags:
---
偶然在 GitHub 上注意到有些 commit 记录上有一个绿色的 "**Verified**" 标记，感觉酷酷的，就给自己也弄了一个。
过程和遇到的一些小坑做下记录。

{% asset_img commit-with-verified-badge.png [Commit with "Verified" badge]%}

<!-- more -->

首先点开 Verified 徽章，弹出了一个说明。

{% asset_img verified-badge-tooltip.png ["Verified" badge tooltip]%}

这里说这个提交使用 GPG key 进行了签名，下面给出了文档链接，就跟着文档一步步做吧。
这里把[文档链接](https://help.github.com/articles/signing-commits-with-gpg/)贴出来，
或者可以看[别人总结的精简版](https://gist.github.com/LauLaman/51f054e9656a707d6df441e9fe1e14bc)，
另外记下自己的流程。

### 步骤

1.因为我还没有在 GitHub 上保存过 GPG key，所以第一步要在自己电脑上生成一个 GPG key。

```bash
$ gpg --full-generate-key
```
> - 如果你的 Mac 上没有自带 gpg 命令，你可能需要先 `brew install gnupg pinentry-mac`
> - 在 Ubuntu 16.04, gpg 1.4.20 中是
> ```bash
> $ gpg --gen-key
> ```


然后会有一些交互式的选项，选择的值依次是：
  - RSA and RSA (default)
  - 4096
  - 0 (default)

选好后，下一步输入自己的身份信息，主要是 GitHub ID 和邮箱。有可能还需要输入一个备注，我备注了个 "GitHub GPG key"。

回车后会要求设置一个密码来保护这个 GPG key。
设置密码后这个 GPG key 就生成好了。

2.将完整的 GPG key 保存到 GitHub。

这需要在电脑上打印出刚刚生成的 GPG key 完整信息。

首先显示自己所有的 GPG key，
```bash
$ gpg --list-secret-keys --keyid-format LONG
/Users/hubot/.gnupg/secring.gpg
------------------------------------
sec   4096R/3AA5C34371567BD2 2016-03-10 [expires: 2017-03-10]
uid                          Hubot
ssb   4096R/42B317FD4BA89E7A 2016-03-10
```

其中 `sec` 部分的 **3AA5C34371567BD2** 就是我们刚刚生成的 GPG key 的 ID，我们用它来显示完整的 GPG key 内容，
```bash
$ gpg --armor --export 3AA5C34371567BD2
# 下面会显示完整的 GPG key
```

将包括开头的 `-----BEGIN PGP PUBLIC KEY BLOCK-----` 和末尾的 `-----END PGP PUBLIC KEY BLOCK-----` 在内的完整 GPG key 复制下来。

访问 `GitHub -> Settings -> SSH and GPG keys -> New GPG key` ([快速链接](https://github.com/settings/gpg/new))，将复制好的 GPG key 粘贴到输入框并保存。

3.为本地的 Git 启用 GPG key 签名

首先设置自己的 GPG key，
```bash
$ git config --global user.signingkey 3AA5C34371567BD2
```

在提交时使用 `-S` flag 来告诉 Git 对本次 commit 进行签名，
```bash
$ git commit -S -m "awereghtyjui"
```

此时会弹出输入密码的界面，输入密码正确即签名成功。

> 如果在你的 Mac 上提示
```bash
error: gpg failed to sign the data
fatal: failed to write commit object
```
那么你需要将下面一行加入你的命令行配置文件，比如 `~/.zshrc` 或者 `~/.bash_profile`
```bash
export GPG_TTY=$(tty)
```

签名成功后推送到 GitHub，就会有 Verified 标记啦。

{% asset_img with-and-without-gpg-sign.png [With vs without GPG sign]%}

4.(可选)对每次提交都进行签名

执行如下命令，以后提交时不用加 `-S` 标记，Git 也会对提交进行签名了。
```bash
$ git config --global commit.gpgsign true
```

耶。
