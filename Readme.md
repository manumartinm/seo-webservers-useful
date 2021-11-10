# Useful seo comands for web servers

## Apache

### Redirect from HTTP to HTTPS

  RewriteEngine On
  RewriteCond %{HTTP_HOST} ^yourdomain\.com [NC]
  RewriteCond %{SERVER_PORT} 80
  RewriteRule ^(.*)$ https://www.yourdomain.com/$1 [R,L]
  
### Redirect from www to no-www

  RewriteEngine on
  RewriteCond %{HTTP_HOST} ^www.yoursite.com 
  RewriteRule (.*) https://yoursite.com/$1 [R=301,L]
  
### Redirect from no-www to wwww

  RewriteEngine on
  RewriteCond %{HTTP_HOST} ^yoursite.com 
  RewriteRule (.*) https://www.yoursite.com/$1 [R=301,L]
  
### Redirect to an specific page when there is a 404 error

  ErrorDocument 404 errors/404.html
  
### Redirect 301
  
  Redirect 301 /old/file.html http://yourdomain.com/new/file.html
  RedirectMatch 301 /blog(.*) http://yourdomain.com/$1

### Caching your Website

  <FilesMatch "\.(ico|pdf|flv|jpg|jpeg|png|gif|swf|mp3|mp4)$">
  Header set Cache-Control "public"
  Header set Expires "Thu, 15 Apr 2010 20:00:00 GMT"
  Header unset Last-Modified
  </FilesMatch>
  #2 hours
  <FilesMatch "\.(html|htm|xml|txt|xsl)$">
  Header set Cache-Control "max-age=7200, must-revalidate"
  </FilesMatch>
  <FilesMatch "\.(js|css)$">
  SetOutputFilter DEFLATE
  Header set Expires "Thu, 15 Apr 2010 20:00:00 GMT"
  </FilesMatch>
  
### Only allow an specific IP

  order deny,allow
  deny from all
  allow from 192.168.0.0/24
  
### Avoid hotlinking (only for wordpress)

  RewriteCond %{HTTP_REFERER} !^$
  RewriteCond %{REQUEST_URI} !^/(wp-login.php|wp-admin/|wp-content/plugins/|wp-includes/).* [NC]
  RewriteCond %{HTTP_REFERER} !^http://www.askapache.com.*$ [NC]
  RewriteRule \.(ico|pdf|flv|jpg|jpeg|mp3|mpg|mp4|mov|wav|wmv|png|gif|swf|css|js)$ - [F,NS,L]
  
### Set a cookies

  Header set Set-Cookie "language=%{lang}e; path=/;" env=lang
  
### Block a request based on a user agent

  SetEnvIfNoCase ^User-Agent$ .*(craftbot|download|extract|stripper|sucker|ninja|clshttp|webspider|leacher|collector|grabber|webpictures) HTTP_SAFE_BADBOT
  SetEnvIfNoCase ^User-Agent$ .*(libwww-perl|aesop_com_spiderman) HTTP_SAFE_BADBOT
  Deny from env=HTTP_SAFE_BADBOT
  
### Avoid some hacking techniques

  RewriteEngine On
  RewriteCond %{QUERY_STRING} proc/self/environ [OR]
  RewriteCond %{QUERY_STRING} mosConfig_[a-zA-Z_]{1,21}(=|\%3D) [OR]
  RewriteCond %{QUERY_STRING} base64_encode.*(.*) [OR]
  RewriteCond %{QUERY_STRING} (<|%3C).*script.*(>|%3E) [NC,OR]
  RewriteCond %{QUERY_STRING} GLOBALS(=|[|\%[0-9A-Z]{0,2}) [OR]
  RewriteCond %{QUERY_STRING} _REQUEST(=|[|\%[0-9A-Z]{0,2})
  RewriteRule ^(.*)$ index.php [F,L]
  
### Block access to your htaccess
  
  <Files .htaccess>
    order allow,deny
    deny from all
  </Files>

  <Files secretfile.jpg>
   order allow,deny
   deny from all
  </Files>

  <FilesMatch ".(htaccess|htpasswd|ini|phps|fla|psd|log|sh)$">
   Order Allow,Deny
   Deny from all
  </FilesMatch>
  
## Nginx

### Hide Nginx Version Information from Hacker
  
  nano /etc/nginx/nginx.conf
  http {
    server_tokens off;
  }

### Reload Nginx.conf without restart service

  nginx -s reload
  
### Add black and white list

  location / ops-coffee / {
    deny 192.168.1.0/24;
    allow all;
  }
  
### Deny user agent

  if ($ http_user_agent ~ * LWP :: Simple | BBBike | wget | curl) {
    return 444;
  }
  
### Avoid hotlinking

  location ~* (\.jpg|\.png|\.gif|\.jpeg)$ {
       valid_referers blocked www.domain.com domain.com;
       if ($invalid_referer) {
          return 403;
       }
  }
  
### Redirect from www to no-www

  if ($host = domain.com) {
    rewrite ^/(.*)$ http://www.domain.com/$1 permanent;
  }
  
### Redirect from no-www to www

  if ($host = www.domain.com) {
    rewrite ^/(.*)$ http://domain.com/$1 permanent;
  }

### Enable Browser chache

  location ~* \.(?:ico|css|js|gif|jpe?g|png)$ {
    expires 120d;
    add_header Pragma public;
    add_header Cache-Control "public";
  }
  
  location ~* .(jpg|jpeg|png|gif|ico|css|js)$ {
    expires 365d;
  }
