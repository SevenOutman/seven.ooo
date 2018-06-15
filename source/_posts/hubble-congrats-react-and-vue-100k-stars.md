---
title: Hubble 见证 Vue 与 React 突破 10 万 GitHub Stars!
date: 2018-06-16 00:07:42
tags:
---
昨天 19 时 9 分前后，Vue 与 React 两个著名现代前端框架双双突破 10 万 GitHub Stars。
并且在同日，晚于 React 诞生的 Vue 首次在 GitHub Star 数量上超越 React，最终率先达到 10 万。

{% asset_img react-and-vue-both-reach-100k-stars.png [React and Vue both reach 100k stars]%}

<!-- more -->

### Hubble

Hubble 取名自哈勃空间望远镜，寓意观测星星的历史。
主要功能是查询一个 GitHub 仓库的 star 历史，以及查看[自己今年获得了多少 star](https://hubble.js.org/my-stars-this-year)。
![Screenshot](https://i.loli.net/2018/05/29/5b0d1c3190a01.png)
做这个项目的起因其实只是我想要知道自己今年获得了多少 star 而已。顺带想起用过的几个查 star 历史的工具都不是很好看或者好用，于是就想也做上查询 star 历史的功能。
在此过程中对 GitHub API 的使用也有了更加深入的学习。
{% asset_img my-stars-this-year.png [How many stars have I earned this year?]%}

关于 Hubble 的更多内容将在我的[另一篇博客](https://seven.ooo/hubble)中记录。


### React vs. Vue 100k star race LIVE

Hubble 上线之后，有用户提议增加同时查看两个 repo 历史的功能，看看两个仓库互相追逐的样子。我觉得听起来很有趣，就把『Repo race』这个功能列入了计划当中。
作为 React 和 Vue 的用户和粉丝，听到这样的功能第一个想到的就是它俩，于是我在 Repo race 中加入了一个彩蛋：
因为要区分两个仓库的图线，所以需要用户来为两个仓库选择各自图线的颜色，而当输入的 repo 是 `facebook/react` 和 `vuejs/vue` 时，将会自动使用它们的主题色(`#61dafb` 和 `#41b883`)。
{% asset_img react-vue-repo-race.png [Vue is gaining faster]%}
在开发 Repo race 的过程中，我自己已经看过完整的 React 和 Vue star 历史。我注意到很长时间以来，Vue 的增长速度比 React 要快很多，并且最近差距已经缩小到 1000 多。
我就突发奇想，想要做一个实况，亲眼见证 Vue 赶超 React 的时刻。就这样，React vs. Vue 在三周前上线了。
{% asset_img live-is-on.png [React vs. Vue 100k stars race is LIVE now.]%}
几周以来我听取一些访问者的意见和建议，逐步优化性能和视觉效果，让访客能清晰的体验这场竞赛的走势（尤其感谢 [@gusto](https://github.com/gustojs) 的许多建设性意见）。除了原本的柱状图外，还增加了折线图，显示最近 400 天两者的 star 趋势。
这三周以来大约每天分差都会缩小几十到一百左右，预计在 6 月 20 日前后 Vue 将追平 React。到了前天晚上，差距缩小到 500，我增加了 Facebook 和 Twitter 分享按钮，希望更多的粉丝能一起观看。并且打算过几天即将追平的时候，把折线图换成每秒趋势图，这样可以实时看到走势变化，并且最终赶超时将会看到一个 "X" 形。

结果一觉醒来，Vue 已经领先 React。
{% asset_img boost-overnight.png [Boost overnight]%}

也就是说一夜之间分差的缩小速度从 100 猛增到了 500 多。社区的粉丝提醒我，不仅是 Vue 突然开始猛增，React 其实也突然开始猛涨。事实上，如果去看看 Repo race 就会发现，两个仓库这一天的增长速度都是各自创建以来前所未有的。
当然这也让我不得不手忙脚乱紧急开始上线每秒趋势图，幸好还是赶上了。有了每秒趋势图，可以清楚地看到两条图线分分合合缠绕在一起，不停地相互超越，非常精彩刺激。
这下我不光看到了 "X"，甚至看到了一条 DNA…
{% asset_img final-stage.png [Final stage]%}
最终的结果也是十分戏剧性，两个仓库在同一分钟双双达到 10 万 star，皆大欢喜。一些粉丝在 [Hubble](https://hubble.js.org/react-vs-vue) 全程关注了这一精彩的实况，其中包括 Vue 核心团队成员（也是上图的提供者）。

最后贴一张截图，是昨天 Vue 冲线时一位 Hubble 访客的评论。我深有同感。
{% asset_img comments.png [Comments from community]%}

总之大家都对喜爱的东西抱有热情，开开心心的就好啦。也欢迎大家关注 Hubble 以及我的其他项目。
