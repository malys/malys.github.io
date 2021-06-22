---
title: "OIDC for static resources"
date: 2021-06-22
publishdate : 2021-06-22
tags: [static,image,keycloak]
image: "images/logo/keycloak.png"
description: "This is meta description."
author: "Malys"
type: "post"
---

Google Chrome: Same-site

## OIDC for static resources


### Use case

<img class="special-img-class" style="width:40%" src="/images/ConsumeProtectedStaticResourceUC.png" />

SPA consummes Rest API in the **same business domain**.

Customer has been authenticated in SPA using *authorization code flow*. SPA sends AJAX queries with a bearer token to consume REST API.

REST API is protected by a *bearer only* client.


Developer wants to display or allow to download a protected image provided by a server of resources in **the business domain**.

```html
    <a href="http://dns/static/test.png" download="test.png" target="_blank">test</a>
    <img src="http://dns/static/test.png">
```

Unfortunately, GET query to access to *test.png* is not providing bearer token and it's not possible to display this image.

Different stategies are described in these articles:

* [Protected files](https://gaurav5430.medium.com/web-security-file-downloads-behind-auth-c38c4cb1842b)
* [Protected iamges](https://gaurav5430.medium.com/web-security-images-behind-auth-bb0b955c4770)
* [FileSave](https://github.com/eligrey/FileSaver.js/wiki/Saving-a-remote-file#using-http-header)


### Solution

If static resources and SPA are in the **same business domain**.

The easiest solution is to enable direct authentication in the *relying party* of API changing *bearer only* client to *confidencial* client with the same audience.

<img class="special-img-class" style="width:40%" src="/images/ConsumeProtectedStaticResource.png" />

The *relying party* of API will detect the cookie for authentication and apply SSO process.

Consequently, this image will be displayed correctly and the download link will work fine.

<img class="special-img-class" style="width:40%" src="/images/ConsumeProtectedStaticResource-result.png" />
