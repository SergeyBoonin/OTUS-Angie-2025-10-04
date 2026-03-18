# Домашняя работа OTUS-Angie-2025-10-04 OTUS_Angie_04_Миграция_с_Nginx_на_Angie

#### Замечание.
Поскольку в исходной конфигурации локации "\\" использовался upstream, ссылающийся на порты 9000-9003, которые в конфигурации не были представлены, я создал для них отдельный сервер (см. [/http.d/upstream.conf](https://github.com/SergeyBoonin/OTUS-Angie-2025-10-04/blob/main/http.d/upstream.conf)), чтоб было удобнее видеть ответы, а не ошибку 500.

#### Работающий сервер Nginx
```
root@ubuntu01:/home/user# curl -v --compressed http://192.168.31.122/

*   Trying 192.168.31.122:80...

* Connected to 192.168.31.122 (192.168.31.122) port 80

> GET / HTTP/1.1

> Host: 192.168.31.122

> User-Agent: curl/8.5.0

> Accept: */*

> Accept-Encoding: deflate, gzip, br, zstd

>

< HTTP/1.1 200 OK

< Server: nginx/1.29.6

< Date: Wed, 18 Mar 2026 13:16:16 GMT

< Content-Type: text/plain

< Transfer-Encoding: chunked

< Connection: keep-alive

< Vary: Accept-Encoding

< Content-Encoding: br

<

Hello 127.0.0.1:35876!

This is 127.0.0.1:9002 speaking.

* Connection #0 to host 192.168.31.122 left intact

root@ubuntu01:/home/user# curl -v --compressed http://192.168.31.122/

*   Trying 192.168.31.122:80...

* Connected to 192.168.31.122 (192.168.31.122) port 80

> GET / HTTP/1.1

> Host: 192.168.31.122

> User-Agent: curl/8.5.0

> Accept: */*

> Accept-Encoding: deflate, gzip, br, zstd

>

< HTTP/1.1 200 OK

< Server: nginx/1.29.6

< Date: Wed, 18 Mar 2026 13:16:19 GMT

< Content-Type: text/plain

< Transfer-Encoding: chunked

< Connection: keep-alive

< Vary: Accept-Encoding

< Content-Encoding: br

<

Hello 127.0.0.1:57270!

This is 127.0.0.1:9003 speaking.

* Connection #0 to host 192.168.31.122 left intact
```

#### Переключение на Angie
```
root@ubuntu01:/home/user# systemctl stop nginx && systemctl start angie
```

#### Работающий сервер Angie
```
root@ubuntu01:/home/user# curl -v --compressed http://192.168.31.122/

*   Trying 192.168.31.122:80...

* Connected to 192.168.31.122 (192.168.31.122) port 80

> GET / HTTP/1.1

> Host: 192.168.31.122

> User-Agent: curl/8.5.0

> Accept: */*

> Accept-Encoding: deflate, gzip, br, zstd

>

< HTTP/1.1 200 OK

< Server: Angie/1.11.3

< Date: Wed, 18 Mar 2026 13:19:47 GMT

< Content-Type: text/plain

< Transfer-Encoding: chunked

< Connection: keep-alive

< Vary: Accept-Encoding

< Content-Encoding: br

<

Hello 127.0.0.1:58844!

This is 127.0.0.1:9002 speaking.

* Connection #0 to host 192.168.31.122 left intact

root@ubuntu01:/home/user# curl -v --compressed http://192.168.31.122/

*   Trying 192.168.31.122:80...

* Connected to 192.168.31.122 (192.168.31.122) port 80

> GET / HTTP/1.1

> Host: 192.168.31.122

> User-Agent: curl/8.5.0

> Accept: */*

> Accept-Encoding: deflate, gzip, br, zstd

>

< HTTP/1.1 200 OK

< Server: Angie/1.11.3

< Date: Wed, 18 Mar 2026 13:19:49 GMT

< Content-Type: text/plain

< Transfer-Encoding: chunked

< Connection: keep-alive

< Vary: Accept-Encoding

< Content-Encoding: br

<

Hello 127.0.0.1:57200!

This is 127.0.0.1:9001 speaking.

* Connection #0 to host 192.168.31.122 left intact
```

#### Удаление Nginx
```
root@ubuntu01:/home/user# apt-get remove --purge nginx

Reading package lists... Done

Building dependency tree... Done

Reading state information... Done

The following packages will be REMOVED:

  nginx*

0 upgraded, 0 newly installed, 1 to remove and 0 not upgraded.

After this operation, 3,855 kB disk space will be freed.

Do you want to continue? [Y/n] y

(Reading database ... 128863 files and directories currently installed.)

Removing nginx (1.29.6-1~noble) ...

Processing triggers for man-db (2.12.0-4build2) ...

(Reading database ... 128846 files and directories currently installed.)

Purging configuration files for nginx (1.29.6-1~noble) ...

dpkg: warning: while removing nginx, directory '/usr/lib/nginx/modules' not empty so not removed



root@ubuntu01:/home/user# ll /usr/lib/nginx/modules

total 184

drwxr-xr-x 2 root root  4096 Mar 18 08:00 ./

drwxr-xr-x 3 root root  4096 Mar 18 07:52 ../

-rwxr-xr-x 1 root root 95664 Mar 18 08:00 ngx_http_brotli_filter_module.so*

-rwxr-xr-x 1 root root 79904 Mar 18 07:59 ngx_http_brotli_static_module.so*



root@ubuntu01:/home/user# rm -rf /usr/lib/nginx



root@ubuntu01:/home/user# ll /usr/lib/nginx/modules

ls: cannot access '/usr/lib/nginx/modules': No such file or directory



root@ubuntu01:/home/user# ll /etc/nginx

ls: cannot access '/etc/nginx': No such file or directory



root@ubuntu01:/home/user# angie -t

angie: the configuration file /etc/angie/angie.conf syntax is ok

angie: configuration file /etc/angie/angie.conf test is successful

root@ubuntu01:/home/user# service angie restart
```

#### Работающий сервер Angie
```
root@ubuntu01:/home/user# curl -v --compressed http://192.168.31.122/

*   Trying 192.168.31.122:80...

* Connected to 192.168.31.122 (192.168.31.122) port 80

> GET / HTTP/1.1

> Host: 192.168.31.122

> User-Agent: curl/8.5.0

> Accept: */*

> Accept-Encoding: deflate, gzip, br, zstd

>

< HTTP/1.1 200 OK

< Server: Angie/1.11.3

< Date: Wed, 18 Mar 2026 13:23:16 GMT

< Content-Type: text/plain

< Transfer-Encoding: chunked

< Connection: keep-alive

< Vary: Accept-Encoding

< Content-Encoding: br

<

Hello 127.0.0.1:47886!

This is 127.0.0.1:9002 speaking.

* Connection #0 to host 192.168.31.122 left intact
```

#### Итоговая колфигурация Angie
```
root@ubuntu01:/etc/angie# angie -T

angie: the configuration file /etc/angie/angie.conf syntax is ok

angie: configuration file /etc/angie/angie.conf test is successful

# configuration file /etc/angie/angie.conf:

user  angie;

worker_processes  auto;

worker_rlimit_nofile 65536;



error_log  /var/log/angie/error.log notice;

pid        /run/angie.pid;



load_module modules/ngx_http_brotli_filter_module.so;

load_module modules/ngx_http_brotli_static_module.so;



events {

    worker_connections  65536;

}





http {

    include             /etc/angie/mime.types;

    default_type        application/octet-stream;



    log_format  main    '$remote_addr - $remote_user [$time_local] "$request" '

                        '$status $body_bytes_sent "$http_referer" '

                        '"$http_user_agent" "$http_x_forwarded_for"';



    log_format extended '$remote_addr - $remote_user [$time_local] "$request" '

                        '$status $body_bytes_sent "$http_referer" rt="$request_time" '

                        '"$http_user_agent" "$http_x_forwarded_for" '

                        'h="$host" sn="$server_name" ru="$request_uri" u="$uri" '

                        'ucs="$upstream_cache_status" ua="$upstream_addr" us="$upstream_status" '

                        'uct="$upstream_connect_time" urt="$upstream_response_time"';



    access_log          /var/log/angie/access.log  extended;

    error_log           /var/log/angie/error.log;



    sendfile                  on;

    tcp_nopush                on;

    types_hash_max_size       2048;



    proxy_cache_valid         1m;

    proxy_cache_key           $scheme$host$request_uri;

    proxy_cache_path          /var/www/cache levels=1:2 keys_zone=one:10m inactive=48h max_size=800m;



    ssl_protocols             TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE

    ssl_prefer_server_ciphers on;



    keepalive_timeout  65;



    gzip              on;

    gzip_vary         on;

    gzip_proxied      any;

    gzip_comp_level   6;

    gzip_buffers      16 8k;

    gzip_http_version 1.1;

    gzip_types        text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;



    brotli_static     on;

    brotli            on;

    brotli_comp_level 5;

    brotli_types      text/plain text/css text/xml application/javascript application/json image/x-icon image/svg+xml;



    #zstd             on;

    #zstd_min_length  256;

    #zstd_comp_level  5;

    #zstd_static      on;

    #zstd_types       text/plain text/css text/xml application/javascript application/json image/x-icon image/svg+xml;



    map $http_accept $webp_suffix {

        "~*webp"  ".webp";

    }



    map $http_accept $avif_suffix {

        "~*avif"  ".avif";

        "~*webp"  ".webp";

    }



    map $msie $cache_control {

        default   "max-age=31536000, public, no-transform, immutable";

        "1"       "max-age=31536000, private, no-transform, immutable";

    }



    map $msie $vary_header {

        default   "Accept";

        "1"       "";

    }



    include /etc/angie/http.d/*.conf;

}



#stream {

#    include /etc/angie/stream.d/*.conf;

#}



# configuration file /etc/angie/mime.types:



types {

    text/html                                        html htm shtml;

    text/css                                         css;

    text/xml                                         xml;

    image/gif                                        gif;

    image/jpeg                                       jpeg jpg;

    application/javascript                           js;

    application/atom+xml                             atom;

    application/rss+xml                              rss;



    text/mathml                                      mml;

    text/plain                                       txt;

    text/vnd.sun.j2me.app-descriptor                 jad;

    text/vnd.wap.wml                                 wml;

    text/x-component                                 htc;



    image/avif                                       avif;

    image/bmp                                        bmp;

    image/png                                        png;

    image/svg+xml                                    svg svgz;

    image/tiff                                       tif tiff;

    image/vnd.wap.wbmp                               wbmp;

    image/webp                                       webp;

    image/x-icon                                     ico;

    image/x-jng                                      jng;



    font/woff                                        woff;

    font/woff2                                       woff2;



    application/java-archive                         jar war ear;

    application/json                                 json;

    application/mac-binhex40                         hqx;

    application/msword                               doc;

    application/pdf                                  pdf;

    application/postscript                           ps eps ai;

    application/rtf                                  rtf;

    application/vnd.apple.mpegurl                    m3u8;

    application/vnd.debian.binary-package            deb udeb;

    application/vnd.google-earth.kml+xml             kml;

    application/vnd.google-earth.kmz                 kmz;

    application/vnd.ms-excel                         xls;

    application/vnd.ms-fontobject                    eot;

    application/vnd.ms-powerpoint                    ppt;

    application/vnd.oasis.opendocument.graphics      odg;

    application/vnd.oasis.opendocument.presentation  odp;

    application/vnd.oasis.opendocument.spreadsheet   ods;

    application/vnd.oasis.opendocument.text          odt;

    application/vnd.openxmlformats-officedocument.presentationml.presentation

                                                     pptx;

    application/vnd.openxmlformats-officedocument.spreadsheetml.sheet

                                                     xlsx;

    application/vnd.openxmlformats-officedocument.wordprocessingml.document

                                                     docx;

    application/vnd.rar                              rar;

    application/vnd.wap.wmlc                         wmlc;

    application/wasm                                 wasm;

    application/x-7z-compressed                      7z;

    application/x-cocoa                              cco;

    application/x-java-archive-diff                  jardiff;

    application/x-java-jnlp-file                     jnlp;

    application/x-makeself                           run;

    application/x-perl                               pl pm;

    application/x-pilot                              prc pdb;

    application/x-redhat-package-manager             rpm;

    application/x-sea                                sea;

    application/x-shockwave-flash                    swf;

    application/x-stuffit                            sit;

    application/x-tcl                                tcl tk;

    application/x-x509-ca-cert                       der pem crt;

    application/x-xpinstall                          xpi;

    application/xhtml+xml                            xhtml;

    application/xspf+xml                             xspf;

    application/zip                                  zip;



    application/octet-stream                         bin exe dll;

    application/octet-stream                         dmg;

    application/octet-stream                         iso img;

    application/octet-stream                         msi msp msm;



    audio/midi                                       mid midi kar;

    audio/mpeg                                       mp3;

    audio/ogg                                        ogg;

    audio/x-m4a                                      m4a;

    audio/x-realaudio                                ra;



    video/3gpp                                       3gpp 3gp;

    video/mp2t                                       ts;

    video/mp4                                        mp4;

    video/mpeg                                       mpeg mpg;

    video/quicktime                                  mov;

    video/webm                                       webm;

    video/x-flv                                      flv;

    video/x-m4v                                      m4v;

    video/x-mng                                      mng;

    video/x-ms-asf                                   asx asf;

    video/x-ms-wmv                                   wmv;

    video/x-msvideo                                  avi;

}



# configuration file /etc/angie/http.d/default.conf:

upstream backend {

    #hash $request_uri;

    server 127.0.0.1:9000 weight=2;

    server 127.0.0.1:9001 weight=5;

    server 127.0.0.1:9002 weight=8;

    server 127.0.0.1:9003 weight=4 fail_timeout=5s;

}





server {

    listen 80 default_server;

    listen [::]:80 default_server;



    root /var/www/html;



    index index.html index.htm;



    server_name _;



    access_log /var/log/angie/default.log extended;





    location / {

        #proxy_cache          one;

        proxy_cache_valid     200 1h;

        proxy_cache_lock      on;

        proxy_cache_use_stale updating error timeout invalid_header http_500 http_502 http_504;

        proxy_cache_background_update on;



        #proxy_pass     http://127.0.0.1:8008;

        proxy_pass      http://backend;

        #try_files      $uri $uri/ =404;

    }





    location /stat {

        include         /etc/angie/uwsgi_params;

        uwsgi_pass      127.0.0.1:81;

    }





    location /status/ {

        api     /status/;

        allow   127.0.0.1;

        deny    all;

    }





    # Static files location

    location ~* \.(jpg|jpeg|gif|png)$ {

        add_header   Vary             $vary_header;

        add_header   Cache-Control    $cache_control;

        try_files    $uri$avif_suffix $uri$webp_suffix $uri =404;



    }





    # Static files location

    location ~* \.(ttf|eot|svg|woff|woff2|css|js|json|ico|zip|tgz|gz|rar|bz2|doc|docx|xlsx|pptx|xls|exe|pdf|ppt|txt|tar|mid|midi|wav|bmp|rtf|avi|swf|flv|mp3|mp4|fla)$ {

        expires max;

        add_header Cache-Control "max-age=31536000, public, no-transform, immutable";

    }





    location ~ \.php$ {

                include snippets/fastcgi-php.conf;

                # With php-fpm (or other unix sockets):

                fastcgi_pass unix:/run/php/php8.1-fpm.sock;

        #       # With php-cgi (or other tcp sockets):

        #       fastcgi_pass 127.0.0.1:9000;

        }



    #error_page  404              /404.html;



    # redirect server error pages to the static page /50x.html

    #

    error_page   500 502 503 504  /50x.html;

    location = /50x.html {

        root   /usr/share/angie/html;

    }



    # proxy the PHP scripts to Apache listening on 127.0.0.1:80

    #

    #location ~ \.php$ {

    #    proxy_pass   http://127.0.0.1;

    #}



    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000

    #

    #location ~ \.php$ {

    #    root           html;

    #    fastcgi_pass   127.0.0.1:9000;

    #    fastcgi_index  index.php;

    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;

    #    include        fastcgi_params;

    #}



    # deny access to .htaccess files, if Apache's document root

    # concurs with angie's one

    #

    #location ~ /\.ht {

    #    deny  all;

    #}

}





# configuration file /etc/angie/uwsgi_params:



uwsgi_param  QUERY_STRING       $query_string;

uwsgi_param  REQUEST_METHOD     $request_method;

uwsgi_param  CONTENT_TYPE       $content_type;

uwsgi_param  CONTENT_LENGTH     $content_length;



uwsgi_param  REQUEST_URI        $request_uri;

uwsgi_param  PATH_INFO          $document_uri;

uwsgi_param  DOCUMENT_ROOT      $document_root;

uwsgi_param  SERVER_PROTOCOL    $server_protocol;

uwsgi_param  REQUEST_SCHEME     $scheme;

uwsgi_param  HTTPS              $https if_not_empty;



uwsgi_param  REMOTE_ADDR        $remote_addr;

uwsgi_param  REMOTE_PORT        $remote_port;

uwsgi_param  SERVER_PORT        $server_port;

uwsgi_param  SERVER_NAME        $server_name;



# configuration file /etc/angie/snippets/fastcgi-php.conf:

# regex to split $uri to $fastcgi_script_name and $fastcgi_path

fastcgi_split_path_info ^(.+?\.php)(/.*)$;



# Check that the PHP script exists before passing it

try_files $fastcgi_script_name =404;



# Bypass the fact that try_files resets $fastcgi_path_info

# see: http://trac.nginx.org/nginx/ticket/321

set $path_info $fastcgi_path_info;

fastcgi_param PATH_INFO $path_info;



fastcgi_index index.php;

include fastcgi.conf;



# configuration file /etc/angie/fastcgi.conf:



fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;

fastcgi_param  QUERY_STRING       $query_string;

fastcgi_param  REQUEST_METHOD     $upstream_request_method;

fastcgi_param  CONTENT_TYPE       $content_type;

fastcgi_param  CONTENT_LENGTH     $content_length;



fastcgi_param  SCRIPT_NAME        $fastcgi_script_name;

fastcgi_param  REQUEST_URI        $request_uri;

fastcgi_param  DOCUMENT_URI       $document_uri;

fastcgi_param  DOCUMENT_ROOT      $document_root;

fastcgi_param  SERVER_PROTOCOL    $server_protocol;

fastcgi_param  REQUEST_SCHEME     $scheme;

fastcgi_param  HTTPS              $https if_not_empty;



fastcgi_param  GATEWAY_INTERFACE  CGI/1.1;

fastcgi_param  SERVER_SOFTWARE    Angie/$angie_version;



fastcgi_param  REMOTE_ADDR        $remote_addr;

fastcgi_param  REMOTE_PORT        $remote_port;

fastcgi_param  SERVER_ADDR        $server_addr;

fastcgi_param  SERVER_PORT        $server_port;

fastcgi_param  SERVER_NAME        $server_name;



# PHP only, required if PHP was built with --enable-force-cgi-redirect

fastcgi_param  REDIRECT_STATUS    200;



# configuration file /etc/angie/http.d/upstream.conf:

server {

    listen *:9000;

    listen *:9001;

    listen *:9002;

    listen *:9003;



    location / {

        default_type text/plain;

        return 200 "Hello $remote_addr:$remote_port!\nThis is $server_addr:$server_port speaking.\n";

    }

}
```
