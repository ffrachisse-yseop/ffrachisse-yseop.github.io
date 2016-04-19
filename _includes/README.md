# Yseop Savvy API

## Overview

**Yseop Savvy API** provides some tools to understand graphs, diagrams and charts, by generating clear and intelligible descriptions. Those texts are not technical, they simply highlight the major graph variations, without trying to speculate on the reasons for these changes. This API is mainly aimed to BI purposes but can be used in various sectors, as it is flexible and not semantically bound to a specific domain.

For now, it already has been integrated in **Yseop Savvy for Qlik** (plugin for Qlik Sense Desktop), and **Yseop Savvy for Microsoft Excel** (add-in for Microsoft Office 2007 and further).

See more information on Yseop Savvy at https://savvy.github.io

## Limits
Be nice. If you're sending too many requests too quickly, we'll send back a
`503` error code (server unavailable).
You are limited to 5000 requests per hour per `access_token` or `client_id`
overall. Practically, this means you should (when possible) authenticate
users so that limits are well outside the reach of a given user.


### Version information
Version: 1.0.0

### Contact information
Contact Email: savvy-api@yseop.com

### License information
License: Yseop Savvy API
License URL: http://KJLKLKJKJLKJ

Terms of service: https://savvy.yseop.com/api/terms

### URI scheme
Host: savvy-api.yseop-cloud.com
BasePath: /api/v1
Schemes: HTTPS
