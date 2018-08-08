---
title: 处理 Chrome 请求错误 net::ERR_RESPONSE_HEADERS_MULTIPLE_CONTENT_DISPOSITION
date: 2018-08-08 20:03:06
tags:
---

疑难杂症。

<!-- more -->

在 OTV Intelligence 里有导出报表的功能。前端发送 POST 请求给后端，后端输出二进制文件给浏览器。

其中一个导出接口报错 `net::ERR_RESPONSE_HEADERS_MULTIPLE_CONTENT_DISPOSITION`，google 了下找到几个链接
- https://www.youtube.com/watch?v=vAuZwirKjWs
- https://forums.asp.net/t/1941627.aspx?How+to+fix+the+problem+of+Error+code+ERR_RESPONSE_HEADERS_MULTIPLE_CONTENT_DISPOSITION+
- （关键）http://www.gangarasa.com/lets-Do-GoodCode/tag/err_response_headers_multiple_content_disposition/

总结下来原因是，服务端返回的 `Content-disposition` header 中，`filename` 包含逗号。解决方案是将 `filename` 部分使用双引号括住。
