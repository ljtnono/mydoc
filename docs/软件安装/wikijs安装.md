# wikijs安装

## wikijs简介

一个开源的wiki软件
官网文档：[https://docs.requarks.io/](https://docs.requarks.io/)
github仓库：[https://github.com/Requarks/wiki](https://github.com/Requarks/wiki)

## wikijs特点

wiki.js有哪些优点？

1、可以部署在小型树莓派（Raspberry Pi )上，也可以部署其他高性能服务器上（几乎在任何平台上运行）
2、与PostgreSQL、MySQL、MariaDB、MS SQL Server 或 SQLite 兼容（数据存储方式丰富）
3、可在线编辑内容，有对应的管理后台，丰富的扩展集成插件，可上传并编辑对应的媒体资源（图片不必强依赖云图床，如markdown）
4、有版本记录，可以查看，对比，恢复旧的文档版本内容
5、支持协同编辑，适合小型团队文档协作，记录，支持权限设置和用户管理
6、支持自定义样式，支持明暗模式，支持各种主题模版

![Snipaste_2022-08-06_18-20-32](http://f.lingjiatong.cn:30090/rootelement/articleQuote/Snipaste_2022-08-06_18-20-32.webp)

wiki.js有哪些缺点？

1、不支持三级目录或者无线目录（暂时没发现如何设置）
2、三栏展示，中间的标题导航栏无法设置显隐，只能固定展示（不是很喜欢这种风格）

![Snipaste_2022-08-06_18-19-03](http://f.lingjiatong.cn:30090/rootelement/articleQuote/Snipaste_2022-08-06_18-19-03.webp)


## docker-componse安装

```yaml

version: "3"
services:

  db:
    image: postgres:11-alpine
    environment:
      POSTGRES_DB: wiki
      POSTGRES_PASSWORD: wikijsrocks
      POSTGRES_USER: wikijs
    logging:
      driver: "none"
    restart: unless-stopped
    volumes:
      - db-data:/var/lib/postgresql/data

  wiki:
    image: ghcr.io/requarks/wiki:2
    depends_on:
      - db
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: wikijs
      DB_PASS: wikijsrocks
      DB_NAME: wiki
    restart: unless-stopped
    ports:
      - "7007:3000"

volumes:
  db-data:

```

如果需要改为外部数据库，需要提前创建数据库。

## 运行命令,启动服务

```bash

docker-compose up -d

```

