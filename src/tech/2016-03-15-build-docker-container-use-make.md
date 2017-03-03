# 使用 Make 构建 docker 容器
____

作为构建工具，make 已经使用了几十年，全世界无数的大项目都用它构建，本文介绍下 make 在 docker 中的应用。

### 使用 Makefile

开始构建之前，要编写 Makefile 文件。它是 make 命令的配置文件。所有任务的构建规则，都写在这个文件。

```make
CONTAINER = docker-novnc
PORT = 6080

all: $(CONTAINER)

$(CONTAINER):
  docker build -t $(CONTAINER) .

run:
  docker run -d -p $(PORT):$(PORT) $(CONTAINER)

stop:
  docker stop $$(docker ps -q)

.PHONY: clean
clean:
  docker rm $$(docker ps -a -q)
```

1. 使用 `make` 命令即可自动构建 `docker-novnc` 容器
2. 使用 `make run` 命令即可启动 `docker-novnc` 容器
3. 使用 `make stop` 命令停止当前运行的容器
4. 使用 `make clean` 删除所有容器
5. 自己使用请修改 **CONTAINER** 和 **PORT**, 必要时修改命令

更多详情请参考我的 [docker-novnc](https://github.com/light4/docker-novnc) 项目

### 参考链接：

0. <https://dockerone.io/Linux-C/ch22.html>
1. <http://www.ruanyifeng.com/blog/2015/03/build-website-with-make.html>
