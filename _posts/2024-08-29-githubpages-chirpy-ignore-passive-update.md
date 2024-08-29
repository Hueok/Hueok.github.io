---
title: "Github Pages - Jekyll Chirpy 테마에서 수동 업데이트 없이 변경사항 라이브에 적용하기"
categories: [Others]
tags: [github] # TAG names should always be lowercase
---

> Github pages에 글을 추가해도 브라우저에서 Force Refresh 없이는 변경사항이 적용되지 않는것 처럼 보여지는 문제를 해결하는 방법에 대해 정리한다.

> PWA를 통해 브라우저에 저장된 캐시를 로드하는 기능이 있어서 생기는 문제인듯 하다. 


# Solution

`@/_config.yml` 파일의 `pwa`를 disable하자.

```yml
...
pwa:
  enabled: false # the option for PWA feature (installable)
  cache:
    enabled: true # the option for PWA offline cache
    # Paths defined here will be excluded from the PWA cache.
    # Usually its value is the `baseurl` of another website that
    # shares the same domain name as the current website.
    deny_paths:
      # - "/example"  # URLs match `<SITE_URL>/example/*` will not be cached by the PWA
...
```

