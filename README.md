# xcash-delegate-server-config
SSL openresty/nginx xcash delegate website configuration



## How to use

Install Openresty server [https://openresty.org/](https://openresty.org/)
Install LuaRocks [https://luarocks.org/](https://luarocks.org/#quick-start)

Install and configure [lua-resty-auto-ssl](https://github.com/auto-ssl/lua-resty-auto-ssl)

```bash
sudo /usr/local/openresty/luajit/bin/luarocks install lua-resty-auto-ssl
sudo mkdir /etc/resty-auto-ssl
sudo chown -R root:www /etc/resty-auto-ssl/
sudo chmod -R 775 /etc/resty-auto-ssl
```

We will need a self signed fallback certificate as a fallback to be able to start nginx.

```bash
sudo openssl req -new -newkey rsa:2048 -days 3650 -nodes -x509 \
  -subj '/CN=sni-support-required-for-valid-ssl' \
  -keyout /etc/ssl/resty-auto-ssl-fallback.key \
  -out /etc/ssl/resty-auto-ssl-fallback.crt
```

Install [Certbot](https://certbot.eff.org/)

Configure certbot certificates. Use your domain names after -d parameters
```bash
sudo certbot certonly -d YOUR_DOMAIN_NAME
```

Use the nginx.conf as example of server configuration. Change YOUR_DOMAIN_NAME to your domain name according to your server.

Prepare your delegate website to serve it by Openresty web server
Copy content of "delegates-pool-website" from "xcash-official" folder to your $USER/ folder

For example:
```
mkdir -p ~/xcash-delegate/www
cp -r ~/xcash-official/delegates-pool-website/ ~/xcash-delegate/www
```

make sure the path in your nginx.conf root location points to the right place

```
    root /home/xcash/xcash-delegate/www;
```

Now edit main.js file at ~/xcash-delegate/www
find 
```
explorer_apiEndPoint:"http://delegates.xcash.foundation"
```
and replace http://delegates.xcash.foundation to 
```
explorer_apiEndPoint:"https://YOUR_DOMAIN_NAME/api/explorer"
```

