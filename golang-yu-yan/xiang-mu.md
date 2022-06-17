# 项目



Nginx替代caddy：[github](https://github.com/caddyserver/caddy) [网址](https://caddyserver.com/) [使用](https://page.syao.fun/2020/09/11/web\_caddy.html)

caddy file-server --listen :2015 --root \~/git\_project --browse

`--listen` 指定端口，默认80； `--root` 指定根目录，默认当前目录； `--browse` 以目录的形式显示文件，如果不存在index文件使用该参数避免显示空白。参数都可以写进Caddyfile里，在当前目录创建Caddyfile，`caddy run`

```json
localhost :2015
root * /home/data
file_server browse
```

