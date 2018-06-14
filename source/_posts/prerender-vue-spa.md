---
title: 预渲染 Vue SPA（踩坑记录）
date: 2018-06-14 02:09:06
tags:
---

使用 `prerender-spa-plugin` 在 Netlify 上对 Vue SPA 项目进行预渲染以优化 SEO。

<!-- more -->

最近一直在更新 Hubble 上的 [React vs. Vue 100k star race LIVE](https://hubble.js.org/react-vs-vue) 忙的不亦乐乎。
昨天 Vue 与 React 的差距缩小到 500 以内，想着终于到了加上 Facebook 和 Twitter 分享按钮的时候了，可以吸引一些观众。

按照 Facebook 和 Twitter 各自的文档，配好了相应的链接和 `<meta>` 标签（使用 `vue-meta`），然而实际效果却与预期有些差距。
Hubble 主站的 title 和 description 分别是 "Hubble" 和 "Travel through GitHub Stars' History"，而在 React vs. Vue 的页面，本应已经分别设为 "React vs. Vue · Hubble" 和 "100k stars race between React and Vue is LIVE now."。
然而按照 Facebook 的文档配好了 title 和 description 后，分享出去的链接里仍然显示原来的文字。
{% asset_img facebook-share.png [Facebook share]%}
问题就出在 SEO，解决的方法就是使用 SSR 或者进行预渲染。

那么首先看看 [Vue 的文档怎么说](https://ssr.vuejs.org/zh/#%E4%B8%BA%E4%BB%80%E4%B9%88%E4%BD%BF%E7%94%A8%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%AB%AF%E6%B8%B2%E6%9F%93-ssr-%EF%BC%9F)。
文档里说如果只是为了 SEO，那么可以不必使用 SSR，只需要配合 webpack 使用 [`prerender-spa-plugin`](https://github.com/chrisvfritz/prerender-spa-plugin) 就可以。
按照插件的文档给出的[示例](https://github.com/chrisvfritz/prerender-spa-plugin/blob/master/examples/vue2-webpack-router/webpack.config.js)来配置 webpack

```javascript

{
    plugins: [
        new PrerenderSPAPlugin({
            staticDir: path.join(__dirname, '../dist'),
            routes: [ '/react-vs-vue' ],
            renderer: new Renderer({
                inject: {
                },
                headless: false,
                renderAfterDocumentEvent: 'render-event'
            })
        })
    ]
}
```

要注意 Vue-Router 只有在 history 模式下才支持 SSR 和预渲染，因此要做一些调整，并且把原来代码里的一些链接改掉。
修改完后，提交代码。

接着要在 Netlify 上开启预渲染，具体位置在 `Settings->Build & deploy`。
{% asset_img enable-prerendering-on-netlify.png [Enable prerendering on Netlify]%}

推送分支，让 Netlify 开始构建。

然后报错。:)

```shell
1:30:14 AM: > node build/build.js
1:31:20 AM: Error: Failed to launch chrome!
1:31:20 AM: [0613/173120.242750:ERROR:nacl_helper_linux.cc(310)] NaCl helper process running without a sandbox!
1:31:20 AM: Most likely you need to configure your SUID sandbox correctly
1:31:20 AM: TROUBLESHOOTING: https://github.com/GoogleChrome/puppeteer/blob/master/docs/troubleshooting.md
1:31:20 AM:     at onClose (/opt/build/repo/node_modules/puppeteer/lib/Launcher.js:285:14)
1:31:20 AM:     at Interface.helper.addEventListener (/opt/build/repo/node_modules/puppeteer/lib/Launcher.js:274:50)
1:31:20 AM:     at emitNone (events.js:111:20)
1:31:20 AM:     at Interface.emit (events.js:208:7)
1:31:20 AM:     at Interface.close (readline.js:368:8)
1:31:20 AM:     at Socket.onend (readline.js:147:10)
1:31:20 AM:     at emitNone (events.js:111:20)
1:31:20 AM:     at Socket.emit (events.js:208:7)
1:31:20 AM:     at endReadableNT (_stream_readable.js:1064:12)
1:31:20 AM:     at _combinedTickCallback (internal/process/next_tick.js:138:11)
1:31:20 AM:     at process._tickCallback (internal/process/next_tick.js:180:9)
1:31:20 AM: [Prerenderer - PuppeteerRenderer] Unable to start Puppeteer
1:31:20 AM: (node:1246) UnhandledPromiseRejectionWarning: TypeError: Cannot read property 'close' of null
1:31:20 AM:     at PuppeteerRenderer.destroy (/opt/build/repo/node_modules/@prerenderer/renderer-puppeteer/es6/renderer.js:139:21)
1:31:20 AM:     at Prerenderer.destroy (/opt/build/repo/node_modules/@prerenderer/prerenderer/es6/index.js:87:20)
1:31:20 AM:     at PrerendererInstance.initialize.then.then.then.then.then.then.then.then.catch.err (/opt/build/repo/node_modules/prerender-spa-plugin/es6/index.js:145:29)
1:31:20 AM:     at <anonymous>
1:31:20 AM:     at process._tickCallback (internal/process/next_tick.js:188:7)
```

看起来很明显是 `puppeteer` 出了什么问题。
跟着错误信息里给出的[链接](https://github.com/GoogleChrome/puppeteer/blob/master/docs/troubleshooting.md)，找到相关的文档，里面提到了类似的问题。
解决方法是启动 `puppeteer` 时传入 `--no-sandbox --disable-setuid-sandbox` 两个 flag。

那么就需要去查看 `prerender-spa-plugin` 的文档了，因为 `puppeteer` 是由它启动的。
[在 README 中可以看到](https://github.com/chrisvfritz/prerender-spa-plugin#prerendererrenderer-puppeteer-options)，可以向 `PuppeteerRenderer` 的构造函数传入任何 `puppeteer.launch()` 所接受的选项。据此修改 webpack 配置如下

```diff
new PrerenderSPAPlugin({
    staticDir: path.join(__dirname, '../dist'),
    routes: [ '/react-vs-vue' ],
    renderer: new Renderer({
        inject: {
        },
        headless: false,
        renderAfterDocumentEvent: 'render-event',
+       args: ['--no-sandbox', '--disable-setuid-sandbox'],
    })
})
```

提交代码并推送，让 Netlify 再次构建。

并报错。:)

打开错误日志，发现和上次一样，说明同样的问题并没解决。那么只好在 `prerenderer-spa-plugin` 的 issues 里面搜下。
一搜果然还就[搜到了](https://github.com/chrisvfritz/prerender-spa-plugin/issues/200)。在评论中一位 collaborator 指出，如果出现这个问题，要去掉 Renderer 选项中的 `headless: false`。

```diff
new PrerenderSPAPlugin({
    staticDir: path.join(__dirname, '../dist'),
    routes: [ '/react-vs-vue' ],
    renderer: new Renderer({
        inject: {
        },
-       headless: false,
        renderAfterDocumentEvent: 'render-event',
        args: ['--no-sandbox', '--disable-setuid-sandbox'],
    })
})
```

再提交推送试一次。这次成功了，虽然并不懂为什么。:)

现在再点击 Facebook 分享按钮，分享的链接已经可以爬取正确的 title 和 description 了。
{% asset_img facebook-share-prerendered.png [Facebook share with prerendering]%}

最近可能再写一篇关于 Hubble 以及 React vs. Vue 本身的博客。

耶。