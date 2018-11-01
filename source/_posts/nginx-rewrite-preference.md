title: 'nginx rewrite preference '
date: 2017-04-19 22:40:15
tags:
categories:
description:
---


nginx 的一个location里面如果有多个rewrite ， rewrite的优先级是按配置的先后顺序进行匹配请求， 而不是像location一样根据不同的优先级匹配

```
Syntax:	rewrite regex replacement [flag];
Default:	—
Context:	server, location, if
If the specified regular expression matches a request URI, URI is changed as specified in the replacement string. The rewrite directives are executed sequentially in order of their appearance in the configuration file. It is possible to terminate further processing of the directives using flags. If a replacement string starts with “http://”, “https://”, or “$scheme”, the processing stops and the redirect is returned to a client.
```
