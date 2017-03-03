### 使用 Django Commands 和 Supervisor

____

使用 Django management commands 可以定义自己的管理命令。
在相关 app 下以如下目录格式创建文件，其中 `mycommand` 就是命令的名字。

```
├── management
│   ├── __init__.py
│   └── commands
│       ├── __init__.py
│       ├── mycommand.py
```

mycommand.py 模板如下:

```
from django.core.management.base import BaseCommand, CommandError

class Command(BaseCommand):
    help = 'Your command instructions'

    def __init__(self):
        super(Command, self).__init__()

    def handle(self, *args, **options):
		self.stdout.write('Successfully executived mycommand.')
```

在 handle 中写想用命令干什么就可以了。可以将相关 models import 进来。  
写完之后执行 `python manage.py mycommand` 就会执行 handle 中的代码。

然后在 supervisor 如下配置即可：

```
[program:django_mycommand]
directory=/home/light4/django/
command=python manage.py mycommand
autostart=true
autorestart=true
startsecs=10
stopsignal=QUIT
stopwaitsecs=10
stdout_logfile=/var/log/django/mycommand.log
stderr_logfile=/var/log/django/mycommand_error.log
stdout_logfile_backups=5
stderr_logfile_backups=5
```

配合 supervisor 可以完成一些很有趣的事情，比如定时任务之类的。

