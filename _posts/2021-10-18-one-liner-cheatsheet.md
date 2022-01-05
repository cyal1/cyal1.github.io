---
layout: post
title: "One-liner CheatSheet"
date: 2021-10-18
favorite: "true"
lastUpdate: 2021-11-13
---

##### find subdomain by certfications

```shell
curl https://crt.sh/\?q\=%.jd.com\&output\=json |jq '.[].name_value' |sed 's/"//g'|sed "s/\\\n/,/g"|tr "," "\n" |tr "*" "www" |perl -ne 'print lc'|sort -u|httpx -o /tmp/jd.com.txt
```

##### find spring-boot

```shell
cat urls.txt|httpx -no-fallback -paths '/env,/actuator/env' -title -status-code -content-length -content-type -follow-redirects -match-string "java.runtime.name"

cat urls.txt|httpx -no-fallback -paths '/actuator/mappings,/mappings' -title -status-code -content-length -content-type -follow-redirects -match-string "dispatcherServlet"
```

##### find swagger-ui

```bash
cat urls.txt|httpx -no-fallback -paths '/api/docs/,/swagger-resources,/v2/api-docs/,/api/swagger/index.yaml,/swagger/index.yaml' -title -status-code -content-length -content-type -follow-redirects -match-regex "\"swagger-ui\"|swaggerVersion|\"swagger\":|swagger:"
```

##### find shiro

```shell
cat urls.txt | httpx -no-fallback -H "Cookie: rememberMe=123" -match-string "rememberMe=deleteMe"
```

##### wordlists

https://wordlists.assetnote.io/

