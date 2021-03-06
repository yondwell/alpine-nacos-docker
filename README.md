# alpine nacos docker image 支持mysql8
[alpine-nacos](https://hub.docker.com/r/yondwell/alpine-nacos)

- 基于官方nacos包增加了MySQL8的驱动

> docker-compose.yml
```
version: "3"
services:
  nacos1:
    hostname: nacos1
    container_name: nacos1
    image: yondwell/alpine-nacos:latest
    volumes:
      - ./data/cluster-logs/nacos:/home/nacos/logs
    ports:
      - "8848:8848"
      - "9555:9555"
    restart: always
    environment:
      - PREFER_HOST_MODE=hostname
      - NACOS_SERVERS=nacos1:8848 nacos2:8848
      - MYSQL_SERVICE_HOST=mysql
      - MYSQL_SERVICE_DB_NAME=nacos
      - MYSQL_SERVICE_PORT=3306
      - MYSQL_SERVICE_USER=mysql-user-nacos
      - MYSQL_SERVICE_PASSWORD=mysql-password-nacos
    depends_on:
      - mysql
    links: 
      - mysql

  nacos2:
    hostname: nacos2
    image: yondwell/alpine-nacos:latest
    container_name: nacos2
    volumes:
      - ./data/cluster-logs/nacos:/home/nacos/logs
    ports:
      - "8849:8848"
    restart: always
    environment:
      - PREFER_HOST_MODE=hostname
      - NACOS_SERVERS=nacos1:8848 nacos2:8848
      - MYSQL_SERVICE_HOST=mysql
      - MYSQL_SERVICE_DB_NAME=nacos
      - MYSQL_SERVICE_PORT=3306
      - MYSQL_SERVICE_USER=mysql-user-nacos
      - MYSQL_SERVICE_PASSWORD=mysql-password-nacos
    depends_on:
      - nacos1 
      - mysql
    links: 
      - mysql

  mysql:
    container_name: mysql
    image: mysql
    command: --default-authentication-plugin=mysql_native_password
    security_opt:
        - seccomp:unconfined
    restart: always
    environment:
        - MYSQL_ROOT_PASSWORD=mysql-root-pass
        - MYSQL_DATABASE=nacos
        - MYSQL_USER=mysql-user-nacos
        - MYSQL_PASSWORD=mysql-password-nacos
    volumes:
        - ./db:/docker-entrypoint-initdb.d/ #将nacos初始化sql放到db目录
        - ./data/mysql:/var/lib/mysql
```
### nacos数据:
- [nacos-mysql.sql](https://github.com/alibaba/nacos/blob/develop/distribution/conf/nacos-mysql.sql)
