#### 说明
 - nginx限流自定义模块
 - 利用nginx原生模块进行改造支持变量，需要改limit_req源码
 - 自定义模块(limit_rate)支持每次请求从lib库获取对应rate,burst值


###### 原生指令

```
limit_req_zone $binary_remote_addr zone=one:3m rate=1r/s;
limit_req_zone $binary_remote_addr $uri zone=two:3m rate=1r/s;
limit_req_zone $binary_remote_addr $request_uri zone=three:3m rate=1r/s;
location / {
    limit_req zone=one burst=5;
    limit_req zone=two forbid_action=@test1;
    limit_req zone=three burst=3 forbid_action=@test2;
}
location /off {
    limit_req off;
}
location @test1 {
    rewrite ^ /test1.html;
}
location @test2 {
    rewrite ^  /test2.html;
}


```
 
 

###### 定制化模块指令
```

limit_req_zone $uri zone=one:3m rate=$rater/s;
server {
        listen       80;
        server_name  localhost;
 
        set $rate "100000";//默认限速，如果不需要限速设置为0
        set $burst "0";
 
        limit_req zone=one burst=$burst;
 
        location /test {
            set $rate "500";
            set $burst "0";
            root   html;
        }
        location /test1 {
            set $rate "1000";
            set $burst "0";
            root   html;
        }
        location / {
            root   html;
            index  index.html index.htm;
        }
}


```
