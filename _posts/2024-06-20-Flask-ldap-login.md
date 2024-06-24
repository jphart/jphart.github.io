---
layout: post
title:  "Flask LDAP login issues"
date:   2024-06-29 12:00:00 +0000
categories: 
    - flask
    - python
    - ldap
    - debugging
---

Using the flask-ldap3-login pip package to secure a flask app & get this error:

```python
flask-ldap-login ImportError: cannot import name '_app_ctx_stack' from 'flask'
```
 
Downgrade flask to 2.3.3 or try the beta builds of flask-ldap3-login (1.x or above). If you're using flask 3.x as I was the 0.9.x version it isn't compatible at present. 