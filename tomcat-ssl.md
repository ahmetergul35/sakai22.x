# Tomcat SSL

sudo yum install certbot
sudo certbot certonly --standalone -d example.com
cd /etc/letsencrypt/live/example.com
ls


Files List:
  cert.pem
  chain.pem
  fullchain.pem
  privkey.pem




openssl pkcs12 -export -out /tmp/example.com_fullchain_and_key.p12 \
    -in /etc/letsencrypt/live/example.com/fullchain.pem \
    -inkey /etc/letsencrypt/live/example.com/privkey.pem \
    -name tomcat


cd  /tmp/
ls
mv example.com_fullchain_and_key.p12 /opt/tomcat/conf/

vim /opt/tomcat/conf/server.xml 


    <Connector
               protocol="org.apache.coyote.http11.Http11NioProtocol"
               port="443" maxThreads="2400"
               scheme="https" secure="true" SSLEnabled="true"
               keystoreFile="conf/example.com_fullchain_and_key.p12.p12"
               keystorePass="PASSWORD"
               keystoreType="PKCS12"
               clientAuth="false" sslProtocol="TLS"/>
