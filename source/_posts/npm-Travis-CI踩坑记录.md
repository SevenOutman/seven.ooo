---
title: npm + Travis CI踩坑记录
date: 2018-05-11 15:27:28
tags:
---

自从 `Vue-APlayer@1.1` 开始，因为比较经常更新 npm 上的版本，于是配了 Travis 的自动构建和 npm 发布。
本来一切都很顺畅，直到最近给 `RSUITE` 配 CI 时出了各种各样的问题，才发现原来有这么多坑。于是记录下。

## Node 版本选择

Node 版本并不能随便乱选，一般最好不要选不是 lts 的版本，比如 ７。

RSUITE 最初用的就是 7，结果在第一次自动发布之后有用户提出 issue 说报了奇怪的错，经过检查发现 npm 上的包只有源码，没有构建后的文件。
因为我没有写 `before_deploy` 而是在 prepublishOnly 钩子里跑的构建命令 npm run build，所以有可能是钩子没有执行。检查了 job logs 发现 node 版本是 7 而 npm 的版本是 3.10，而 prepublishOnly 是 npm@4 开始才有的钩子，所以没有调。

为什么 Node 6 自带 npm@5，而 Node 7 竟然只有 npm@3 呢？这个在最新的 npm@6 中有所回答。如果你在非 lts 版本下执行 npm install -g npm，npm@6 会提醒你 npm 并不支持非 lts 版本。也就是说非 lts 版本的 Node 并不同捆最新的 npm。

把 Node 版本改成 8 之后，再看 job logs，npm 版本就是 5 了，构建和发布也正常了。
