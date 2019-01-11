介绍
=============
这个镜像集成了fastdfs和nginx反向代理，使之能够通过url直接访问分布式文件系统中的资源。

镜像中各个组件的版本
-------------
* [libfastcommon-1.0.35](https://github.com/happyfish100/libfastcommon)
* [fastdfs-master (20170712)](https://github.com/happyfish100/fastdfs)
* [nginx-1.13.3](http://nginx.org/)
* [fastdfs-nginx-module-master (20170710)](https://github.com/happyfish100/fastdfs-nginx-module)
* [echo-nginx-module-master (20170710)](https://github.com/openresty/echo-nginx-module)
* [nginx-eval-module-master (20170712)](https://github.com/vkholodkov/nginx-eval-module)
* [ngx_http_redis-0.3.8](https://www.nginx.com/resources/wiki/modules/redis)

配置文件
-------------
* storage.conf
``````
## 请修改tracker_ip，默认为139.159.157.156，华为云04
tracker_server=tracker_ip:22122 
``````
* mod_fastdfs.conf
``````
tracker_server=127.0.0.1:22122
``````
* nginx.conf
``````
## 如果需要使用redis,请加上这个
upstream redisbackend {
  server    redis-server-ip:port;
  keepalive 1024;
}
``````
* 其他的配置文件可自行修改

Build 镜像
-------------
``````
## 修改配置文件或者sh脚本后，重新build镜像，可以随意取名，这里名字是fastdfs-nginx
docker build -t fastdfs-nginx .
``````

运行
-------------
``````
## 运行build后的镜像，输入如下命令
docker run -itd \
  --name fastdfs-nginx \
  --network=host \
  -v /etc/localtime:/etc/localtime:ro \
  -v /var/log/fdfs/:/data/fdfs/logs/ \
  -v /data/fdfs/data/:/data/fdfs/data/ \
  -v /var/log/nginx/:/var/log/nginx/ \
  fastdfs-nginx \
  sh -c "/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf restart && /usr/bin/fdfs_storaged /etc/fdfs/storage.conf restart && /usr/sbin/nginx -g 'daemon off;'"

``````

通过ngnix代理直接访问文件资源
-------------
``````
## fetch file from server, if you need check token
http://ip:24001/group1/M00/00/00/xxxxxx?tk=zzz&&typ=yyy

## fetch file from server directly
http://ip:24002/group1/M00/00/00/xxxxxx.yyy
``````
