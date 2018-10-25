# nginx

@@配置nginx worker进程最大打开文件数       
参数语法：worker_rlimit_nofile number        
放置位置：主标签段              
说明：作用是改变worker processes能打开的最大文件数       
worker_rlimit_nofile 65535;     
这个参数受系统文件的最大打开数限制，解决方法： 
vim /etc/security/limits.conf   
* soft nofile 65535
* hard nofile 65535
* soft nproc 65535
* hard nproc 65535

@@开启高效文件传输模式

设置参数 sendfile on;  

sendfile参数用于开启文件的高效传输模式。同时将tcp_nopush和tcp_nodelay两个指令设置为on，可防止网络及磁盘i/o阻塞，提升nginx工作效率。

http {
　　sendfile     on;     #放在http，server，location都可以
}
        
设置参数tcp_nopush； 
激活tcp_nopush参数可以允许把httpresponse header和文件的开始放在一个文件里发布，积极的作用是减少网络报文段的数量（只有sendfile on开启才生效）      
例：
    sendfile   on;      
    tcp_nopush on;      
    tcp_nodelay on;     
    server_tokens off;  
    server_names_hash_bucket_size 128;  
    server_names_hash_max_size 512;     
    keepalive_timeout  65;      
    client_header_timeout 15s;  
    client_body_timeout 15s;    
    send_timeout 60s;   


@@FastCGI相关参数调优 
fastcgi参数是配合nginx向后请求PHP动态引擎服务的相关参数。  
fastcgi_connect_timeout 240;         
fastcgi_send_timeout 240;       
fastcgi_read_timeout 240;       
fastcgi_buffer_size 64k;        
fastcgi_buffers 4 64k;  
fastcgi_busy_buffers_size 128k; 
fastcgi_temp_file_write_size 128k;      
#fastcgi_temp_path /data/ngx_fcgi_tmp;  
fastcgi_cache_path /data/ngx_fcgi_cache levels=2:2 keys_zone=ngx_fcgi_cache:512m inactive=1d max_size=40g;      

