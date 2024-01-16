# $ChatGPT-web$

## $nginx.conf$

```nginx
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
    worker_connections 768;
    # multi_accept on;
}

http {

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    #ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
    #ssl_prefer_server_ciphers on;

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;


    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;

    # 打字机效果
    # proxy_http_version 1.1;
    # proxy_set_header Upgrade $http_upgrade;
    # proxy_set_header Connection "Upgrade";
    proxy_buffering off; 

     server {
        listen 23420;
        server_name 8.138.86.207;
        set $upstream "http://$server_name:23333";
        location / {
		    proxy_pass  $upstream;
        }
        if ($http_user_agent ~* "python|360Spider|JikeSpider|Spider|spider|bot|Bot|2345Explorer|curl|wget|webZIP|qihoobot|Baiduspider|Googlebot|Googlebot-Mobile|Googlebot-Image|Mediapartners-Google|Adsbot-Google|Feedfetcher-Google|Yahoo! Slurp|Yahoo! Slurp China|YoudaoBot|Sosospider|Sogou spider|Sogou web spider|MSNBot|ia_archiver|Tomato Bot|NSPlayer|bingbot")
        {
            return 403;
        }
     }

    #  server{
    #     listen 23333;
    #     server_name 8.138.86.207;
    #     location / {
	# 	    return 404;
    #     }
    #  }
}
```

## $docker$

```shell
temp:
docker run --restart=always  -e TZ="Asia/Shanghai"  --name nginx_temp -d -p 23420:23420  -v /home/hzh/nginx_conf/nginx_chatgpt.conf:/etc/nginx/nginx.conf   -v /home/hzh/logs/chatgpt_logs/:/var/log/nginx/ nginx:latest
```

```shell
docker run --restart=always  -e TZ="Asia/Shanghai"  --name nginx -d -p 23420:23420  -v /home/hzh/nginx_conf/nginx_chatgpt.conf:/etc/nginx/nginx.conf   -v /home/hzh/logs/chatgpt_logs/:/var/log/nginx/ orrero/nginx-geoip:latest
```

```shell
sudo docker run -d --restart=always  --name chatgpt-web  --env OPENAI_API_BASE_URL=https://oneapi.xty.app  --env OPENAI_API_KEY=sk-KF86lEfUCmui7STj2176294b19D843CcBd4b76FfBb708b0d --env HTTPS_PROXY= orrero/chatgpt-web:1.0
```



$network$

```shell
docker network create -d bridge nginx_chatgpt
docker network connect nginx_chatgpt chatgpt-web
docker network connect nginx_chatgpt nginx
```

## $Dockerfile$

```dockerfile
# 使用官方的 Nginx 镜像作为基础镜像
FROM nginx:latest
RUN apt-get update
RUN apt-get upgrade
RUN apt-get install -y wget build-essential 
RUN apt-get install -y libgeoip-dev geoip-database
RUN apt-get install -y libpcre3 libpcre3-dev
RUN apt-get install -y zlib1g zlib1g-dev
RUN apt-get install -y libmaxminddb0 libmaxminddb-dev mmdb-bin
RUN apt install -y libssl-dev
RUN apt install -y unzip

# 下载并解压 libmaxminddb 源代码
RUN mkdir /usr/local/lib/libmaxminddb \
    && cd /usr/local/lib/libmaxminddb \
    && wget -e use_proxy=yes -e https_proxy=https://8.138.86.207:12301 https://github.com/maxmind/libmaxminddb/releases/download/1.3.2/libmaxminddb-1.3.2.tar.gz \
    && tar -zxvf libmaxminddb-1.3.2.tar.gz\
    && cd libmaxminddb-1.3.2 \
    && ./configure && make && make install \
    && echo /usr/local/lib  >> /etc/ld.so.conf.d/local.conf \
    && ldconfig 


# 下载Geoip数据
RUN cd /usr/share/GeoIP/ \
    && wget -e use_proxy=yes -e https_proxy=https://8.138.86.207:12301 https://github.com/wp-statistics/GeoLite2-Country/archive/refs/tags/1.tar.gz \
    && tar -zxvf 1.tar.gz 


# 下载ngx_http_geoip2_module模块
RUN mkdir /usr/share/temp/  \
    && cd /usr/share/temp/ \
    && wget -e use_proxy=yes -e https_proxy=https://8.138.86.207:12301 https://github.com/leev/ngx_http_geoip2_module/archive/refs/tags/3.4.tar.gz  \
    && tar -zxvf 3.4.tar.gz

#下载一个nginx包，使用自带的./configure 去编译
RUN wget https://nginx.org/download/nginx-1.25.3.tar.gz \
    && tar -zxvf nginx-1.25.3.tar.gz \
    && cd nginx-1.25.3\
    && ./configure --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-compat --with-file-aio --with-threads --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-http_v3_module --with-mail --with-mail_ssl_module --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module --with-cc-opt='-g -O2 -ffile-prefix-map=/data/builder/debuild/nginx-1.25.3/debian/debuild-base/nginx-1.25.3=. -fstack-protector-strong -Wformat -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -fPIC' --with-ld-opt='-Wl,-z,relro -Wl,-z,now -Wl,--as-needed -pie' --add-module=/usr/share/temp/ngx_http_geoip2_module-3.4/ \
    && make \
    && make install 


```





# $Orrero/Ubuntu$

```shell
sudo docker run -itd --restart=always -p 8888:22  --name ubuntu orrero/ubuntu:2.0
```

## $nginx$

```shell
 sudo apt install libmaxminddb0 libmaxminddb-dev mmdb-bin geoipupdate
sudo apt install libpcre3 libpcre3-dev
sudo apt install libxml2 libxml2-dev libxslt1.1 libxslt1-dev
sudo apt install libgd-devYT
sudo apt install libssl-dev

wget https://github.com/leev/ngx_http_geoip2_module/archive/refs/tags/3.4.tar.gz
apt install unzip

wget http://mirror.cnop.net/web/module/ngx_http_geoip2_module-master.zip 

 ./configure --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-compat --with-file-aio --with-threads --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-http_v3_module --with-mail --with-mail_ssl_module --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module --with-cc-opt='-g -O2 -ffile-prefix-map=/data/builder/debuild/nginx-1.25.3/debian/debuild-base/nginx-1.25.3=. -fstack-protector-strong -Wformat -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -fPIC' --with-ld-opt='-Wl,-z,relro -Wl,-z,now -Wl,--as-needed -pie' --add-module=/usr/share/temp/ngx_http_geoip2_module-master/
make && make install

```









# $careywong/subweb$

```shell
docker run -d -p 58080:80 --restart always --name subweb careywong/subweb:latest
```



# $tindy2013/subconverter$

```shell
docker run -d -p 58070:25500 --restart always --name subconverter tindy2013/subconverter:latest

http://8.138.86.207:58070/sub?
```

