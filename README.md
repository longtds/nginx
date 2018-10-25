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


@@配置nginx gzip压缩实现性能优化  
　　nginx gzip压缩模块提供了压缩文件内容的功能，用户请求的内容在发送给客户端之前，nginx服务器会根据一些具体的策略实施压缩，   
  以节省网站出口带宽，同时加快了数据传输效率，提升了用户的访问体验。     
压缩的对象：  
　　纯文本内容压缩比很高，因此，纯文本的内容最好要压缩     
　　被压缩的纯文本文件必须要大于1KB，由于压缩算法的特殊原因，极小的文件压缩反而变大     
　　图片、视频（流媒体）等文件尽量不要压缩，因为这些文件大多都是经过压缩的，如果再压缩很可能不会减小或减小很少，或者有可能增大，        
  而在压缩时还会消耗大量的CPU、内存资源  
参数介绍及配置说明：      
gzip on;        #表示开启压缩功能       

gzip_min_length  1k; 　#表示允许压缩的页面最小字节数，页面字节数从header头的Content-Length中获取。默认值是0，表示不管页面多大都进行压缩，建议设置成大于1K。如果小于1K可能会越压越大       

gzip_buffers     4 32k;　　   #压缩缓存区大小    
gzip_http_version 1.1; 　　　　 #压缩版本       
gzip_comp_level 9;　　　　   #压缩比率  
gzip_types  text/css text/xml application/javascript;　　#指定压缩的类型 
gzip_vary on;　　　　 #vary header支持        


