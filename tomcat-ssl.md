## Install Certbot
```sh
sudo yum install certbot
sudo certbot certonly --standalone -d example.com
cd /etc/letsencrypt/live/example.com
ls
```
File list;
```sh
cert.pem
chain.pem
fullchain.pem
privkey.pem
```
Openssl PKCS12 Export;
```sh
openssl pkcs12 -export -out /tmp/example.com.p12 \
-in /etc/letsencrypt/live/example.com/fullchain.pem \
-inkey /etc/letsencrypt/live/example.com/privkey.pem \
-name tomcat
```
```sh
cd  /tmp/
ls
mv example.com.p12 /opt/tomcat/conf/
```
FTomcat settings (server.xml);
```sh
vim /opt/tomcat/conf/server.xml 
```
```sh
    <Connector
               protocol="org.apache.coyote.http11.Http11NioProtocol"
               port="443" maxThreads="2400"
               scheme="https" secure="true" SSLEnabled="true"
               keystoreFile="conf/example.com.p12"
               keystorePass="PASSWORD"
               keystoreType="PKCS12"
               clientAuth="false" sslProtocol="TLS"/>
```
