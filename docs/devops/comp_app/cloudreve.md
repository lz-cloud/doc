# Cloudreve 网盘服务

> Cloudreve 可以让您快速搭建起公私兼备的网盘系统。Cloudreve 在底层支持不同的云存储平台，用户在实际使用时无须关心物理存储方式。你可以使用 Cloudreve 搭建个人用网盘、文件分享系统，亦或是针对大小团体的公有云系统。


## 安装 & 启动

- `mkdir cloudreve && cd cloudreve`
- 主程序下载：https://github.com/cloudreve/Cloudreve/releases
- `tar -zxvf cloudreve_*`
- `chmod +x ./cloudreve`
- `nohup ./cloudreve &` # 启动过程会打印初始 账号：admin@cloudreve.org,  密码: Z7Sbh1Ji
- systemctl 方式，见：https://docs.cloudreve.org/getting-started/install
- 默认端口：`5212`, 使用 nginx 代理一层，以不需要记忆此端口

## 使用

- 管理面板，可添加三方存储策略。服务器只跑应用
- web 上传，下载。
- 包含 iOS 客户端
- 更多的，自行安装探索
