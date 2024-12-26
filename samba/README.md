# 搭建samba服务

## 说明
使用docker部署samba，基础镜像为alpine


## 构建过程
使用docker compose搭建

### docker compose
```yaml
version: "3"
services:
  samba:
    image: samba:v1.0
    container_name: samba
    hostname: samba-example
    restart: always
#    network_mode: host
    networks:
      front-samba:
        ipv4_address: ${container_ip}
    ports:
      - 137:137/udp
      - 138:138/udp
      - 139:139
      - 445:445
    volumes:
      - ${host_samba_config}:/config/config.yml # samba服务配置
      - ${data_path}:/data     # 挂载分享磁盘
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Asia/Shanghai
      - IP_ADDR=${container_ip}
      - SAMBA_HOSTS_ALLOW=0.0.0.0/0 127.0.0.0/8 10.0.0.0/8 172.16.0.0/12 192.168.0.0/16
    privileged: true
networks:
  front-samba:
    driver: bridge
    ipam:
      driver: default
      config:
        - subnet: "${subnet_config}"
```
#### 变量说明
必须变量:
> `host_samba_config`:该目录为配置文件目录，默认配置文件为`config.yml`，第一次运行自动生产，根据实际情况修改

可选参数:
> `container_ip`: 容器IP，当`networ_mode`为`host`可不配置
> `data_path`: 根据`config.yml`文件映射目录至容器
> `subnet_config`: 网络模式为bridge时配置子网


### 配置文件
`auth:
  - user: admin # 用户名
    group: admin # 组
    uid: 1000 # 用户 uid
    gid: 1000 # 组 gid
    password: password # 密码
    #password_file: /your_path/secrets/password # 密码文件位置
  - user: guest
    group: guest
    uid: 405
    gid: 100
    password: guest

global:
  - "force user = admin,guest" # 管理员用户
  - "force group = admin,guest" # 管理员组

share:
  - name: share # 共享目录名
    comment: Description # 共享描述
    path: /your_path/share # 共享路径
    browsable: yes # 是否可见，若为`no`则必须手动输入路径访问
    readonly: no # 是否只读
    guestok: yes # 是否允许访客
    validusers: admin,guest # 允许访问用户
    writelist: admin,guest # 白名单,白名单用户拥有写入权限
    veto: yes # 是不可见或不可访问的预定义文件和目录的列表
    hidefiles: /_*/ # 隐藏文件
    recycle: yes # 是否启用回收站``yml

```
