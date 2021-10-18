---
layout: post
title: "One-liner CheatSheet"
date: 2021-10-18
favorite: "true"
lastUpdate: 2021-10-18
---

##### find subdomain by certfications

```shell
curl https://crt.sh/\?q\=%.jd.com\&output\=json |jq '.[].name_value' |sed 's/"//g'|sed "s/\\\n/,/g"|tr "," "\n" |tr "*" "www" |perl -ne 'print lc'|sort -u|httpx -o /tmp/jd.com.txt
```

##### find spring-boot

```shell
cat urls.txt|httpx -H "User-Agent: ${UA}" -paths '/env,/actuator/env' -title -status-code -content-length -content-type -follow-redirects -match-string "java.runtime.name"

cat urls.txt|httpx -H "User-Agent: ${UA}" -paths '/actuator/mappings,/mappings' -title -status-code -content-length -content-type -follow-redirects -match-string "dispatcherServlet"
```

