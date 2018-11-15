---
title: Laravel Pjax 总是 abort(422)
date: 2018-11-15 18:15:55
tags:
---

使用 `spatie/laravel-pjax` 的时候遇到总是 `abort(422)` 的情况，查了一圈大多说原因是渲染的页面里找不到监听的 pjax container。
可检查过后我的页面里都是有 `id="pjax-container"` 的，请求头里也有 `X-PJAX-CONTAINER: #pjax-container`。

查源码加调试输出找到问题的原因：

`spatie/laravel-pjax` 里使用了 `Symfony\Component\DomCrawler\Crawler` 来检查渲染的 DOM 中是否存在指定的 `X-PJAX-CONTAINER`。

```php
// Middleware/FilterIfPjax.php
protected function filterResponse(Response $response, $container)
{
    $crawler = $this->getCrawler($response);

    $response->setContent(
        $this->makeTitle($crawler).
       $this->fetchContainer($crawler, $container)
    );

    return $this;
}
```

`abort(422)` 发生在 `fetchContainer()` 中：

```php
protected function fetchContainer(Crawler $crawler, $container)
{
    $content = $crawler->filter($container);

    if (!$content->count()) {
        abort(422);
    }

    return $content->html();
}
```

注意到在 `filterResponse()` 中是把当前的 `$response` 传给了 `$crawler` 的，那么如果当前的 `$response` 其实是个错误页面呢？这里就会发生找不到 `#pjax-container` 的情况了。

输出一看果然，`$response` 是个异常信息页面。我遇到的问题是忘了 `migrate`。
