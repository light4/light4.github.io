### 5 分钟上手 Django 日志

____

Django 的 logging 设置有一点复杂，这里给几个必要的步骤可以快速上手。  
设置时注意以下 4 个组件就行了：

> 1. Loggers: 传送消息到日志系统的对象
2. Handlers: 处理 logger 传过来的消息
3. Filters: 过滤从 logger 传送到 handler 的消息
4. Formatters: 格式化日志输出

#### 项目设置

Django 日志模块在项目的 `settings.py` 里设置。以下通过四个组件说明具体如何设置。

#### Formatters

日志记录的格式，下面用了两个 formater, `verbose` 和 `simple`，分别表示详细的和简单的。

```
'formatters': {
        'verbose': {
            'format' : "[%(asctime)s] %(levelname)s [%(name)s:%(lineno)s] %(message)s",
            'datefmt' : "%d/%b/%Y %H:%M:%S"
        },
        'simple': {
            'format': '%(levelname)s %(message)s'
        },
    },
```

#### Filters

我的项目什么都不过滤，不过要是过滤的话，按需要参考以下设置：

```
'filters': {
        'special': {
            '()': 'project.logging.SpecialFilter',
            'foo': 'bar',
        }
    },
```

上边定义了一个 `project.logging.SpecialFilter` 过滤器，别名叫 special。bar 会作为 foo 的值传入过滤器。

#### Handlers

有趣的地方来了，现在可以让 Django 记录所有输出到文件：

```
'handlers': {
        'file': {
            'level': 'DEBUG',
            'class': 'logging.FileHandler',
            'filename': 'mysite.log',
            'formatter': 'verbose'
        },
    },
```

`level` 意味着所有 `DEBUG` 或更高级别的都会记录，`filename` 就是日志文件的路径，`formatter` 就是刚才设置的 `verbose`。

#### Loggers

最后，设置两个 logger，一个记录 Django 本身的，一个记录项目的日志。

```
'loggers': {
        'django': {
            'handlers':['file'],
            'propagate': True,
            'level':'DEBUG',
        },
        'MYAPP': {
            'handlers': ['file'],
            'level': 'DEBUG',
        },
    }
```

#### 完整设置

根据上边的设置，可以在项目 `setting.py` 如下设置：

```
# settings.py
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'verbose': {
            'format' : "[%(asctime)s] %(levelname)s [%(name)s:%(lineno)s] %(message)s",
            'datefmt' : "%d/%b/%Y %H:%M:%S"
        },
        'simple': {
            'format': '%(levelname)s %(message)s'
        },
    },
    'handlers': {
        'file': {
            'level': 'DEBUG',
            'class': 'logging.FileHandler',
            'filename': 'mysite.log',
            'formatter': 'verbose'
        },
    },
    'loggers': {
        'django': {
            'handlers':['file'],
            'propagate': True,
            'level':'DEBUG',
        },
        'MYAPP': {
            'handlers': ['file'],
            'level': 'DEBUG',
        },
    }
}
```

#### 使用

在 views.py 如下调用就行了：

```
import logging
logger = logging.getLogger(__name__)

def myfunction():
	logger.error("this is an error message!")
```

```
python manage.py shell
>>> from polls.views import *
>>> myfunction()
```

执行之后 `mysite.log` 输出如下：

```
[12/Apr/2016 19:09:20] ERROR [polls.views:5] this is an error message!
```

____

#### 参考链接

编译自 <http://ianalexandr.com/blog/getting-started-with-django-logging-in-5-minutes.html>

