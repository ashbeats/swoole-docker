## Swoole-coroutine

[**docker-hub**](https://hub.docker.com/r/twosee/swoole-coroutine/) 

[**github**](https://github.com/twose/swoole-coroutine-docker)

### Usage

```Bash
docker pull twosee/swoole-coroutine
docker pull twosee/swoole-coroutine:mysql
docker pull twosee/swoole-coroutine:redis
```
```Bash
docker run -d --name=swoole \
 -v /workdir:/workdir \
 -p 9501:9501 \
 twosee/swoole-coroutine \
 php /app/server.php start
```
OR
```Bash
docker-compose up
```
### Introduction

- 基于最新PHP7.2-cli版本
- 使用swoole2.X最新版本构建, 所有功能火力全开
- 提供Swoole的绝佳搭档:` MySQL`, `Redis`, `Inotify`, 配合`docker-compose`, 实现开箱即用
- 已安装 ["GD", "iconv", "pdo_mysql", "dom", "xml", "curl", "swoole"]等PHP扩展
- 已开启["coroutine", "openssl", "http2", "async-redis", "mysqlnd", "swoole-serialize"]等所有功能
- 纯环境 , 0冗余 , 绿色清洁 , 无任何php代码
- 默认中国上海时区
- inotify提供自动化热更新等支持

---

- Based on PHP7.2-cli
- use swoole 2.* latest stable version, All functions are fully open
- Provide the perfect partner for Swoole such as ` MySQL`, `Redis`, `Inotify` images, you can also use `docker-compose`, Out of the box.
- PHP extension installed: ["GD", "iconv", "pdo_mysql", "dom", "xml", "curl", "swoole"]
- enable ["coroutine", "openssl", "http2", "async-redis", "mysqlnd", "swoole-serialize"]
- this container has no PHP code or framework included
- Asia/Shanghai timezone default (you can remove it on the last RUN line)
- Inotify provides hot auto updates and other support

### Version

| DIR      | INTRO                                                | Tag     |
| -------- | ---------------------------------------------------- | ------- |
| /master  | Latest master version (Experimental type)            | latest  |
| /mysql   | It's a perfect MySQL's docker                        | mysql   |
| /redis   | It's a perfect Redis's docker                        | redis   |
| /inotify | inotify, composer, git, node, to support hot updates | inotify |
| /release | Latest release version                               | release |
| /1.x-lts | Latest version from branch 1.x-lts                   | 1.x-lts |

### Docker-compose

Swoole + mysql + redis

```yaml
version: '3.4'
services:
  swoole:
    image: "twosee/swoole-coroutine"
    ports:
      - "9501:9501"
    volumes:
      - ./src:/app/src:rw
    restart: always
    depends_on:
      - mysql
    command: php /app/src/server.php start

  mysql:
    image: "twosee/swoole-coroutine:mysql"
    ports:
      - "9502:3306"
    volumes:
      - ./data/mysql/data:/var/lib/mysql:rw
      - ./data/mysql/sock:/var/run/mysqld:rw # remove when windows.
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root_password_here
      MYSQL_DATABASE: test
      MYSQL_USER: php
      MYSQL_PASSWORD: php_user_password_here
    
  redis:
    image: "twosee/swoole-coroutine:redis"
    ports:
      - "9503:6379"
    volumes:
      - ./data/redis/data:/var/lib/redis:rw
    sysctls:
        net.core.somaxconn: 65535
    restart: always

  inotify:
    image: "twosee/swoole-coroutine:inotify"
    volumes:
      - ./:/app:rw
    restart: always
    environment:
      APP_ENV: dev # or product
    working_dir: /app/util
    command: /bin/bash inotify.sh
```
You can see [mysqld.cnf](https://github.com/twose/swoole-coroutine-docker/tree/master/mysql).

```php
$options = [
  'host'     => 'mysql', //So there.
  'port'     => 3306,
  'user'     => 'php',
  'password' => 'php_user_password_here',
  'database' => 'test'
];
$db = new \Swoole\Coroutine\Mysql();
$db->connect($options);
```
You can see [redis.conf](https://github.com/twose/swoole-coroutine-docker/tree/master/redis).
```php
$redis = new \Swoole\Coroutine\Redis();
$redis->connect('redis', 6379);
$val = $redis->get('foo');
```
