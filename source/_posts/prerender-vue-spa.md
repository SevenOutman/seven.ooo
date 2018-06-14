---
title: 预渲染 Vue SPA（踩坑记录）
date: 2018-06-14 02:09:06
tags:
---

使用 `prerender-spa-plugin` 在 Netlify 上对 Vue SPA 项目进行预渲染以优化 SEO。

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

接着要在 Netlify 上开启预渲染。
