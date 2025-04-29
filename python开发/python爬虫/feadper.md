# feapder爬虫框架

## 一、简介

feapder是一个上手简单，功能强大的python爬虫框架，使用方式类似scrapy，方便有scrapy框架切换，内置4种爬虫

1. AirSpider爬虫比较轻量，学习成本低。面对一些数据量较少，无需断电续爬，无需分布式采集的需求，可采用此爬虫
2. Spider是基于redis的分布式爬虫，适用于海量数据采集，支持断点续爬、爬虫报警、数据自动入库等功能
3. TaskSpider是一款分布式爬虫，内部封装了取种子任务的逻辑，内置支持从redis或者mysql获取任务，也可自定义实现从其他来源获取任务
4. BatchSpider是一款分布式批次爬虫，对于周期性采集的数据，优先考虑使用本爬虫

> 国内文档：[https://boris-code.gitee.io/feapder](https://boris-code.gitee.io/feapder)
>
> 官方文档：[http://feapder.com](http://feapder.com)
>
> github：[https://github.com/Boris-code/feapder](https://github.com/Boris-code/feapder)

## 二、安装

精简版

```shell
pip install feapder
```

浏览器渲染版

```shell
pip install "feapder[render]"
```

完整版

```shell
pip install "feapder[all]"
```

三个版本区别：

1. 精简版：不支持浏览器渲染、不支持基于内存去重、不支持入库mongo
2. 浏览器渲染版：不支持基于内存去重、不支持入库mongo
3. 完整版：支持所有功能

```
> feapder

███████╗███████╗ █████╗ ██████╗ ██████╗ ███████╗██████╗
██╔════╝██╔════╝██╔══██╗██╔══██╗██╔══██╗██╔════╝██╔══██╗
█████╗  █████╗  ███████║██████╔╝██║  ██║█████╗  ██████╔╝
██╔══╝  ██╔══╝  ██╔══██║██╔═══╝ ██║  ██║██╔══╝  ██╔══██╗
██║     ███████╗██║  ██║██║     ██████╔╝███████╗██║  ██║
╚═╝     ╚══════╝╚═╝  ╚═╝╚═╝     ╚═════╝ ╚══════╝╚═╝  ╚═╝

Version: 1.9.2
Document: https://feapder.com

Usage:
  feapder <command> [options] [args]

Available commands:
  create        create project、spider、item and so on
  retry         retry failed request or item
  shell         debug response
  zip           zip project

Use "feapder <command> -h" to see more info about a command
```

```
> feapder create -h
usage: feapder [-h] [-p] [-s] [-i] [-t] [-init] [-j] [-sj] [-c] [--params] [--setting] [--host] [--port] [--username] [--password] [--db]

生成器

options:
  -h, --help        show this help message and exit
  -p , --project    创建项目 如 feapder create -p <project_name>
  -s , --spider     创建爬虫 如 feapder create -s <spider_name>
  -i , --item       创建item 如 feapder create -i <table_name> 支持模糊匹配 如 feapder create -i %table_name%
  -t , --table      根据json创建表 如 feapder create -t <table_name>
  -init             创建__init__.py 如 feapder create -init
  -j, --json        创建json
  -sj, --sort_json  创建有序json
  -c, --cookies     创建cookie
  --params          解析地址中的参数
  --setting         创建全局配置文件feapder create --setting
  --host            mysql 连接地址
  --port            mysql 端口
  --username        mysql 用户名
  --password        mysql 密码
  --db              mysql 数据库名
```

## 三、模板学习

### 执行流程

<img src="http://markdown-media.oss-cn-beijing.aliyuncs.com/2020/06/08/borisspider-1.png"  />

#### 模块说明

- spider => 框架调度核心
- parser_control => 模板控制器
- collector => 任务收集器
- parser => 数据解析器
- start_request => 初始任务下发函数
- item_buffer => 数据缓冲队列
- request_buffer => 请求任务缓冲队列
- request => 数据下载器
- response => 请求响应

#### 流程说明

1. spider调度**start_request**生产任务
2. **start_request**下发任务到request_buffer中
3. spider调度**request_buffer**批量将任务存储到任务队列数据库中
4. spider调度**collector**从任务队列中批量获取任务到内存队列
5. spider调度**parser_control**从collector的内存队列中获取任务
6. **parser_control**调度**request**请求数据
7. **request**请求与下载数据
8. request将下载后的数据给**response**，进一步封装
9. 将封装好的**response**返回给**parser_control**（图示为多个parser_control，表示多线程）
10. parser_control调度对应的**parser**，解析返回的response（图示多组parser表示不同的网站解析器）
11. parser_control将parser解析到的数据item及新产生的request分发到**item_buffer**与**request_buffer**
12. spider调度**item_buffer**与**request_buffer**将数据批量入库

## 四、结尾

> 由于官方文档写的十分详细，故此不做过多赘述，请直接访问[http://feapder.com](http://feapder.com)

