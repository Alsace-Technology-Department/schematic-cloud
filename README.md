# schematic.cloud

<a href="https://schematic.cloud">
  <picture>
    <source width="400" media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/IntellectualSites/schematic.cloud/master/assets/img/logo.svg">
    <img width="400" src="https://raw.githubusercontent.com/IntellectualSites/schematic.cloud/master/assets/img/logo-darkmode.svg">
  </picture>
</a>

## 拉取Docker镜像:
  ```bash
  docker pull alsaceteam/schematic.cloud
  ```

## 你可以使用docker-compose
  ``` yaml
  version: '3'
  services:
    schematic.cloud:
      container_name: schematic.cloud
      ports:
        - "80:3001"
      image: alsacework/schematic.cloud:latest
      volumes:
        - /data:/app/public
      restart: unless-stopped
  ```

## 构建

```bash
# 安装依赖
$ yarn install

# 在localhost:3001热重载
$ yarn dev

# 构建生产环境
$ yarn build
$ yarn start

# 生成静态项目
$ yarn generate
```

有关工作原理的详细说明，请查看 [Nuxt.js docs](https://nuxtjs.org).
