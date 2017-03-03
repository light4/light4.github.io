### 在 Django Admin 中显示布尔属性 (boolean property)

____

在 Django 中显示布尔方法很简单，给方法设置一个 boolean 属性就行了，例如：

```
class MyModel(models.Model):
    def is_something(self):
        if self.something == 'something':
            return True
        return False
    is_something.boolean = True
```

但是对于 property 却不行：

```
class MyModel(models.Model):
    @property
    def is_something(self):
        if self.something == 'something':
            return True
        return False
```

目前没有发现什么好的方法，下边是一个变通（workaround）：

定义一个私有方法，然后让属性等于 property(前边的方法)

```
class MyModel(models.Model):
    def _is_something(self):
        if self.something == 'something':
            return True
        return False
    _is_something.boolean = True
    is_something = property(_is_something)
```

在 Django Admin 里引用方法就好：

```
class MyModelAdmin(admin.ModelAdmin):
    list_display = ['_is_something']
```

其他地方引用属性就好：

```
if my_model_instance.is_something:
    print("I'm something")
```

____

#### 参考链接

* [stackoverflow](http://stackoverflow.com/questions/12842095/how-to-display-a-boolean-property-in-the-django-admin)
