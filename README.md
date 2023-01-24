<p align="center">
    <a href="https://halo.run" target="_blank" rel="noopener noreferrer">
        <img width="100" src="https://halo.run/logo" alt="Halo logo" />
    </a>
</p>

<p align="center"><b>Halo</b> [ˈheɪloʊ]，一款现代化的开源博客/CMS系统，值得一试。</p>

<p align="center">
<a href="https://github.com/halo-dev/halo/releases"><img alt="GitHub release" src="https://img.shields.io/github/release/halo-dev/halo.svg?style=flat-square" /></a>
<a href="https://github.com/halo-dev/halo/releases"><img alt="GitHub All Releases" src="https://img.shields.io/github/downloads/halo-dev/halo/total.svg?style=flat-square" /></a>
<a href="https://hub.docker.com/r/halohub/halo"><img alt="Docker pulls" src="https://img.shields.io/docker/pulls/halohub/halo?style=flat-square" /></a>
<a href="https://github.com/halo-dev/halo/commits"><img alt="GitHub last commit" src="https://img.shields.io/github/last-commit/halo-dev/halo.svg?style=flat-square" /></a>
<a href="https://github.com/halo-dev/halo/actions"><img alt="GitHub Workflow Status" src="https://img.shields.io/github/workflow/status/halo-dev/halo/Halo%20CI?style=flat-square" /></a>
<br />
<a href="https://halo.run">官网</a>
<a href="https://docs.halo.run">文档</a>
<a href="https://bbs.halo.run">社区</a>
<a href="https://gitee.com/halo-dev">Gitee</a>
<a href="https://t.me/halo_dev">Telegram 频道</a>
</p>


## 快速开始

详细部署文档请查阅：<https://docs.halo.run>
就是1.6.1 版已更新了两个版本，2.0.0  → 2.0.1 建议暂时使用1.6.1版本
因为2.0.0和2.0.1新版本改了很多东西可能有bug 还有社区主题与插件存在兼容性问题问题等待解决
确保已安装docker环境 复制下面命令直接跑访问8090端口，反向代理即可正常使用，然后申请ssl证书保证网站安全

# halo1.6.1命令请复制下方命令部署即可
```bash
docker run -it -d --name halo -p 8090:8090 -v ~/.halo:/root/.halo --restart=unless-stopped halohub/halo:1.6.1 </a>
```
## halo2.0.1 通过docker配置文件部署
```bash
~/halo/docker-compose.yaml
version: "3"

services:
  halo:
    image: halohub/halo:2.1.0
    container_name: halo
    restart: on-failure:3
    depends_on:
      halodb:
        condition: service_healthy
    networks:
      halo_network:
    volumes:
      - ./:/root/.halo2
    ports:
      - "8090:8090"
    command:
      - --spring.r2dbc.url=r2dbc:pool:mysql://halodb:3306/halo
      - --spring.r2dbc.username=root
      # MySQL 的密码，请保证与下方 MYSQL_ROOT_PASSWORD 的变量值一致。
      - --spring.r2dbc.password=o#DwN&JSa56
      - --spring.sql.init.platform=mysql
      # 外部访问地址，请根据实际需要修改  协议建议https，更安全
      - --halo.external-url=http://填域名:8090/
      # 初始化的超级管理员用户名 修改等于号旁边的用户名，他将作为你登录的用户名
      - --halo.security.initializer.superadminusername=admin
      # 初始化的超级管理员密码 修改等于号后面的密码，他将用于你登录的密码
      - --halo.security.initializer.superadminpassword=P@88w0rd

  halodb:
    image: mysql:8.0.31
    container_name: halodb
    restart: on-failure:3
    networks:
      halo_network:
    command: 
      - --default-authentication-plugin=mysql_native_password
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_general_ci
      - --explicit_defaults_for_timestamp=true
    volumes:
      - ./mysql:/var/lib/mysql
      - ./mysqlBackup:/data/mysqlBackup
    ports:
      - "3306:3306"
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "127.0.0.1", "--silent"]
      interval: 3s
      retries: 5
      start_period: 30s
    environment:
      # 请修改此密码，并对应修改上方 Halo 服务的 SPRING_R2DBC_PASSWORD 变量值 修改下方数据库，用户名以及密码，第一个是密码，第二个是用户名
      - MYSQL_ROOT_PASSWORD=o#DwN&JSa56
      - MYSQL_DATABASE=halo

networks:
  halo_network:
```

## 在线体验

- 环境地址：<https://demo.halo.run>
- 后台地址：<https://demo.halo.run/admin>
- 用户名：demo
- 密码：P@ssw0rd123..
- 使用前请阅读：<https://demo.halo.run/archives/tips>

## 生态

| 项目                                                                         | 状态                                                                                                                                                                             | 描述                                     |
| ---------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
| [halo-admin](https://github.com/halo-dev/halo-admin)                         | <a href="https://github.com/halo-dev/halo-admin/releases"><img alt="GitHub release" src="https://img.shields.io/github/release/halo-dev/halo-admin.svg?style=flat-square" /></a> | Web 管理端 UI，已内置在主应用            |
| [js-sdk](https://github.com/halo-dev/js-sdk)                                 | <a href="https://github.com/halo-dev/js-sdk"><img alt="npm release" src="https://img.shields.io/npm/v/@halo-dev/content-api?style=flat-square"/></a>                             | JavaScript SDK                           |
| [halo-comment](https://github.com/halo-dev/halo-comment)                     | <a href="https://www.npmjs.com/package/halo-comment"><img alt="npm release" src="https://img.shields.io/npm/v/halo-comment?style=flat-square"/></a>                              | 独立评论组件，可以非常方便的集成到主题中 |
| [halo-comment-normal](https://github.com/halo-dev/halo-comment-normal)       | <a href="https://www.npmjs.com/package/halo-comment-normal"><img alt="npm release" src="https://img.shields.io/npm/v/halo-comment-normal?style=flat-square"/></a>                | 另外一款评论组件                         |
| [halo-mobile-app](https://github.com/halo-dev/halo-mobile-app)               | 已停止维护                                                                                                                                                                       | 移动端管理 APP                           |
| [tencent-cloudbase-halo](https://github.com/halo-dev/tencent-cloudbase-halo) | 无                                                                                                                                                                               | 腾讯云 CloudBase 一键部署配置            |
| [halo-theme-\*](https://github.com/topics/halo-theme)                        | 无                                                                                                                                                                               | GitHub 上开源的 Halo 主题集合            |

## 许可证

[![license](https://img.shields.io/github/license/halo-dev/halo.svg?style=flat-square)](https://github.com/halo-dev/halo/blob/master/LICENSE)

Halo 使用 GPL-v3.0 协议开源，请遵守开源协议。

## 贡献

参考 [CONTRIBUTING](https://github.com/halo-dev/halo/blob/master/CONTRIBUTING.md)。

<a href="https://github.com/halo-dev/halo/graphs/contributors"><img src="https://opencollective.com/halo/contributors.svg?width=890&button=false" /></a>

## 状态

![Repobeats analytics](https://repobeats.axiom.co/api/embed/ad008b2151c22e7cf734d2688befaa795d593b95.svg "Repobeats analytics image")
