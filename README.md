
##halo 博客2.0.1通过docker配置文件安装部署博客
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

      # 外部访问地址，请根据实际需要修改

      - --halo.external-url=http://localhost:8090/

      # 初始化的超级管理员用户名

      - --halo.security.initializer.superadminusername=admin

      # 初始化的超级管理员密码

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

      # 请修改此密码，并对应修改上方 Halo 服务的 SPRING_R2DBC_PASSWORD 变量值

      - MYSQL_ROOT_PASSWORD=o#DwN&JSa56

      - MYSQL_DATABASE=halo

networks:

  halo_network:
  ```
