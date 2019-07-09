# Nginx配置笔记

## 禁止访问某子目录及面所有文件
````bash
# 禁止访问目录
location ^~ /server/ {
    deny all;
}
# 禁止访问目录下文件
location ^~ /server {
    deny all;
}
````