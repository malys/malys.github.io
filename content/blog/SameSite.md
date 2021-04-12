---
title: "Chrome SameSite feature"
date: 2021-04-12
publishdate : 2021-04-12
tags: [chrome,same-site,keycloak]
image: "images/logo/security.png"
description: "This is meta description."
author: "Malys"
type: "post"
---

Google Chrome: Same-site

## What is SameSite ?

[Heroku blog](https://blog.heroku.com/chrome-changes-samesite-cookie) explains very well this feature.

Google provides also a webpage with last [SameSite updates](https://www.chromium.org/updates/same-site).

## Use cases

### [IFrame](https://web.dev/samesite-cookie-recipes/#content-within-an-lessiframegreater)

<iframe  style="border: 0; width: 100%; height: 600px;" src="https://web.dev/samesite-cookie-recipes/#content-within-an-lessiframegreater"></iframe>

### [POST data](https://web.dev/samesite-cookie-recipes/#%22unsafe%22-requests-across-sites)

<iframe  style="border: 0; width: 100%; height: 600px;" src="https://web.dev/samesite-cookie-recipes/#%22unsafe%22-requests-across-sites"></iframe>

### Authentication

Authentication is an sub-usecase of both and it's described in this [Auth0 blog post](https://auth0.com/blog/browser-behavior-changes-what-developers-need-to-know/).

## Browser compatibility

THe SameSite solution is [not supported by all browsers](https://caniuse.com/mdn-http_headers_set-cookie_samesite_lax_default) and can create problem in Safari.

<iframe  style="border: 0; width: 100%; height: 600px;" src="https://caniuse.com/mdn-http_headers_set-cookie_samesite_lax_default"></iframe>


## Solutions/Workaround

### Cookie duplication

  As these changes to include None and update default behavior are still relatively new, there are inconsistencies amongst browsers as to how these changes are handled. You can refer to the updates page on chromium.org for the issues currently known, however it's not possible to say if this is exhaustive. While this is not ideal, there are workarounds you can employ during this transitionary phase. The general rule though is to treat incompatible clients as the special case. Do not create an exception for browsers implementing the newer rules.

  The first option is to set both the new and old style cookies:

  Set-cookie: 3pcookie=value; SameSite=None; Secure
  Set-cookie: 3pcookie-legacy=value; Secure
  Browsers implementing the newer behavior will set the cookie with the SameSite value, while other browsers may ignore or incorrectly set it. However, those same browsers will set the 3pcookie-legacy cookie. When processing included cookies, the site should first check for the presence of the new style cookie and if it's not found, then fallback to the legacy cookie.

#### Reverse Proxy cookie rewriting

Reverse Proxy can duplicate cookie and rewrite the second one to add `SameSite=none; Secure;` and not change legacy behavior.

* NGINX rule
  
```
proxy_cookie_path ~^/(.+)$ "/$1; SameSite=none; Secure; ";
```

* Apache rule
  * [Google Github issue](https://github.com/GoogleChromeLabs/samesite-examples/issues/4)
  * [SameSite cookies with Apache - blog post](https://dev.to/pfreitag/samesite-cookies-with-apache-6bd)

### User agent detection

  Alternatively at the point of sending the Set-Cookie header, you can choose to detect the client via the user agent string. Refer to the list of incompatible clients and then make use of an appropriate library for your platform, for example ua-parser-js library on Node.js. It's advisable to find a library to handle user agent detection as you most probably don't want to write those regular expressions yourself.
  
  The benefit of this approach is that it only requires making one change at the point of setting the cookie. However, the necessary warning here is that user agent sniffing is inherently fragile and may not catch all of the affected users.

### Keycloak implementation

Keycloak is impacted by `SameSite` feature when Keycloak adapter tries to checkSSO status to allow *Single Sign Out".
An [Iframe](https://github.com/keycloak/keycloak/blob/master/adapters/oidc/js/src/main/resources/login-status-iframe.html) checks cookie and session state every 5 minutes.

Keycloak generates 2 cookies for compatibility and reads them in [Keycloak.js adapter](https://www.keycloak.org/docs/latest/securing_apps/index.html#_modern_browse) (see [Pull Request](https://github.com/keycloak/keycloak/pull/6658/commits/6b658283da86f5f1cf53b555fca649ade2b9a789))

## Proactiveness

* [Browserstack](https://www.browserstack.com/) test on beta version of browsers
* [Technology intelligence](https://portswigger.net/daily-swig/browsers)
