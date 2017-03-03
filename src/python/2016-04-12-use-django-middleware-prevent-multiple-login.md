### 使用 Django middleware 防止多次登录

____

Django 没有开箱即用的防止多次登录的功能，想要实现只能自己动手了。
原理是保存最后登录用户的 session，删除其他的。

在 accounts 目录下建立 `middleware.py`，并写入一下代码：

```
from django.conf import settings
from django.core.cache import cache
from django.contrib.sessions.backends.db import SessionStore


class UserRestrictMiddleware(object):
    def process_request(self, request):
        """
        Checks if different session exists for user and deletes it.
        """
        if request.user.is_authenticated():
            cache_timeout = settings.SESSION_COOKIE_AGE
            cache_key = "user_pk_%s_restrict" % request.user.pk
            cache_value = cache.get(cache_key)

            if cache_value is not None:
                if request.session.session_key != cache_value:
                    session = SessionStore(session_key=cache_value)
                    session.delete()
                    cache.set(cache_key, request.session.session_key, cache_timeout)
            else:
                cache.set(cache_key, request.session.session_key, cache_timeout)
```

然后在 `setting.py` 里的 `MIDDLEWARE_CLASSES` 加上自己编写的 middleware 就行了。

例如加入 `'project.apps.accounts.middleware.UserRestrictMiddleware',`。

这样还不完美，如果结合 Django signal，踢出用户并且通知踢出用户就比较完美了。  
不过暂时先这样，以后补充上。

写完发现有人写了个 middleware，可以用 pip 安装，代码也差不多。详见参考链接 3。

____

#### 参考链接

1. <http://stackoverflow.com/questions/8408823/django-how-to-prevent-multiple-users-login-using-the-same-credetials>
2. <http://stackoverflow.com/questions/821870/how-can-i-detect-multiple-logins-into-a-django-web-application-from-different-lo>
3. <https://github.com/pcraston/django-preventconcurrentlogins>
