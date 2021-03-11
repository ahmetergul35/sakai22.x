<!-- GETTING STARTED -->
## <div align="center"><p>SAKAI 22.x INSTALL GUIDE - RAM:16GB CENTOS7x64</p></div>
Sakai ayrı bir sunucuya mysql ayrı bir sunucuya kurulacaktır.
Öncelikle her iki sunucuda da şu komutları çalıştırın;

## Giriş
```sh
yum -y install vim nano wget git epel-release
service firewalld stop
chkconfig firewalld off
```
  
## JAVA JDK Kurulumu
Sakai sunucusuna geçin; Kurulum için gerekli dosyaları indirebileceğimiz bir <b>download</b> dizini oluşturalım.
```sh
mkdir ~/download  
cd ~/download
```

<b>Download</b> dizinine geçiş yaptıktan sonra aşağıdaki dosyaları indirip <b>tar</b> komutu ile açıyoruz.
```sh
wget http://linuxpanel.net/sakai/jdk-8u271-linux-x64.tar.gz
tar xzf jdk-8u271-linux-x64.tar.gz
mv ~/download/jdk1.8.0_271/ /opt/
cd /opt/jdk1.8.0_271/
```
  
Aşağıdaki komutu çalıştırdıktan sonra bu şekilde  bir ekran çıktısı almanız gerekmektedir. Sizin sunucunuzda başka java sürümü yok ise sadece <b>1</b> sürüm ile karşılaşabilirsiniz.
```sh
alternatives --install /usr/bin/java java /opt/jdk1.8.0_271/bin/java 2
alternatives --config java
```

```sh
There are 4 programs which provide 'java'.  
 Selection    Command
-------------------------------------------
*+ 1           /opt/jdk1.8.0_271/bin/java

Enter to keep the current selection[+], or type selection number: 1
```


Sunucumuza java'yı başarılı bir şekilde kurduk. Sıra geldi <b>javac</b> ve <b>jar</b> komutlarının sorunsuz çalışması için yollarını belirlemeye. <b>/opt/jdk1.8.0_271</b> dizininin içinde aşağıdaki komutları çalıştırıyoruz.
```sh
alternatives --install /usr/bin/jar jar /opt/jdk1.8.0_271/bin/jar 2
alternatives --install /usr/bin/javac javac /opt/jdk1.8.0_271/bin/javac 2
alternatives --set jar /opt/jdk1.8.0_271/bin/jar
alternatives --set javac /opt/jdk1.8.0_271/bin/javac
```
  
Java'nın sunucumuzda kurulu olup olmadığını <b>java -version</b> komutu ile kontrol edelim.
```sh
java -version
```
Ekran çıktısı şu şekilde olmalıdır;
```sh
java version "1.8.0_271"
Java(TM) SE Runtime Environment (build 1.8.0_271-b09)
Java HotSpot(TM) 64-Bit Server VM (build 25.271-b09, mixed mode)
```

Sunucu yeniden başladığında bu çevresel değişkenlerin tanımlı olarak gelmesini istiyorsanız. <b>.bashrc</b> dosyasını aşağıdaki gibi düzenlemelisiniz.
```sh
vim ~/.bashrc
```

```sh
# Java
export JAVA_HOME=/opt/jdk1.8.0_271  
export JRE_HOME=/opt/jdk1.8.0_271/jre  
export PATH=$PATH:/opt/jdk1.8.0_271/bin:/opt/jdk1.8.0_271/jre/bin

# .bashrc
# User specific aliases and functions

alias rm='rm -i'  
alias cp='cp -i'  
alias mv='mv -i'

# Source global definitions
if [ -f /etc/bashrc ]; then  
        . /etc/bashrc
fi
```
  
## MAVEN Kurulumu
Sakai projesinin geliştirme ve kurulum adımlarını kolaylaştırmak ve kütüphane bağımlılığını ortadan kaldırmak için <b>Maven</b> kurulumuna geçelim.
```sh
cd ~/download
wget https://kozyatagi.mirror.guzel.net.tr/apache/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
tar xpfz apache-maven-3.6.3-bin.tar.gz
mv apache-maven-3.6.3 /opt/
cd /opt
ln -s apache-maven-3.6.3/ maven
vim ~/.bashrc
```

<b>.bashrc</b> dosyasının içeriği aşağıdaki gibi olmalıdır. “16Gb ram için değer”!
```sh
# Maven
export MAVEN_HOME=/opt/maven  
export PATH=$PATH:$MAVEN_HOME/bin  
export MAVEN_OPTS='-Xms1024m -Xmx2048m -Djava.util.Arrays.useLegacyMergeSort=true'

# Java
export JAVA_HOME=/opt/jdk1.8.0_271  
export JRE_HOME=/opt/jdk1.8.0_271/jre  
export PATH=$PATH:/opt/jdk1.8.0_271/bin:/opt/jdk1.8.0_271/jre/bin

# .bashrc

# User specific aliases and functions

alias rm='rm -i'  
alias cp='cp -i'  
alias mv='mv -i'
```
<b>.baschrc</b> dosyamızı reload edelim;
```sh
source ~/.bashrc
```

Maven sürümümüzü kontrol edelim;
```sh
mvn --version
```

Aşağıdaki gibi bir çıktı almamız gerekiyor;
```sh
Apache Maven 3.6.3 (cecedd343002696d0abb50b32b541b8a6ba2883f)
Maven home: /opt/maven
Java version: 1.8.0_271, vendor: Oracle Corporation, runtime: /opt/jdk1.8.0_271/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "3.10.0-1160.15.2.el7.x86_64", arch: "amd64", family: "unix"
```

## TOMCAT Kurulumu
<b>download</b> dizinine indirdiğimiz <b>apache-tomcat-9.0.43.tar.gz</b> dosyasını tar komutu ile arşivden çıkartalım. Daha sonra mv komutu ile <b>/opt</b> dizinine taşıyalım <b>apache-tomcat-9.0.43</b> dizine <b>tomcat</b> ismiyle sembolik link verelim.
```sh
cd ~/download
wget https://kozyatagi.mirror.guzel.net.tr/apache/tomcat/tomcat-9/v9.0.43/bin/apache-tomcat-9.0.43.tar.gz
tar xpfz apache-tomcat-9.0.43.tar.gz
mv apache-tomcat-9.0.43 /opt/
cd /opt
ln -s apache-tomcat-9.0.43/ tomcat
vim ~/.bashrc
```
<b>.baschrc</b> dosyamıza ekleyelim;
```sh
# Tomcat
export CATALINA_HOME=/opt/tomcat
export PATH=$PATH:$CATALINA_HOME/bin
```
Ayarları reload edelim ve tomcat’i başlatalım;
```sh
source ~/.bashrc
/opt/tomcat/bin/startup.sh
```
Aşağıdaki gibi ekran çıktısı almanız gerekiyor.
```sh
Using CATALINA_BASE:   /opt/tomcat  
Using CATALINA_HOME:   /opt/tomcat  
Using CATALINA_TMPDIR: /opt/tomcat/temp  
Using JRE_HOME:        /opt/jdk1.8.0_271/jre  
Using CLASSPATH:       /opt/tomcat/bin/bootstrap.jar:/opt/tomcat/bin/tomcat-juli.jar  
Tomcat started.
```
Aşağıdaki komut ile bir problem olup olmadığına bakabilirsiniz;
```sh
tail -f /opt/tomcat/logs/catalina.out
```
Tomcat'i durdurmak için shutdown.sh dosyayı çalıştırabilirsiniz.
```sh
sudo /opt/tomcat/bin/shutdown.sh
```

## MySQL Kurulumu
<b>MySQL</b> sunucusuna geçin;
```sh
yum -y install vim nano wget git epel-release
service firewalld stop
chkconfig firewalld off
mkdir ~/download
cd ~/download
wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
rpm -ivh mysql-community-release-el7-5.noarch.rpm
ls -l /etc/yum.repos.d/mysql-community* 
```
Ekran çıktısı aşağıdaki gibi olmalıdır.
```sh
-rw-r--r--. 1 root root 1209 Jan 29  2014 /etc/yum.repos.d/mysql-community.repo
-rw-r--r--. 1 root root 1060 Jan 29  2014 /etc/yum.repos.d/mysql-community-source.repo
```
Repolarımız olduğuna göre mysql sunucusunu kurabiliriz.
```sh
yum -y install mysql-server
```
Mysql'i başlatalım.
```sh
systemctl start mysqld
systemctl status mysqld
```
Mysql'i daha güvenli hale getirmek için aşağıdaki komutu kullanıp sırasıyla adımları izleyelim.
```sh
mysql_secure_installation
```
* Önce root şifresi soracak şifre “boş” olduğu için birşey yazmadan Enter tuşuna basın.
* Root şifresini belirtmek isteyip istemediğimizi soracak Y harfini yazıp Enter'a basın.
* Root şifresini yazıp Enter'a basın.
* Root şifresini tekrar yazıp Enter'a basın.
* Remove anonymous users? Y harfine basıp Enter'a basın.
* Mysql sunucusuna uzaktan bağlantı kapatılsın mı? Y harfine basıp Enter'a basın.
* Test veritabanını ve erişimlerini sileyim mi? Y harfine basıp Enter'a basın.
* Tablo ayrıcalıklarını yeniden yükle? Y harfine basıp Enter'a basın.
```sh
systemctl restart mysqld
```

## SAKAI için TOMCAT ayarları
<b>TOMCAT</b> başladığında hangi özellikler ile başlayacağını <b>setenv.sh</b> dosyası içinde belirliyoruz. Özelliklerden kastımız maximum kaç gb ram kullanacağı, varsayılan dil, timezone vs...

<b>setenv.sh</b> dosyasını oluşturalım;
```sh
cd /opt/tomcat/bin
vim setenv.sh
```
<b>setenv.sh</b> dosyasına aşağıdaki parametlereri ekleyelim;
```sh
export JAVA_OPTS="-server -d64 -Xms2g -Xmx24g -Djava.awt.headless=true -XX:+UseCompressedOops -XX:+UseConcMarkSweepGC -XX:+DisableExplicitGC"
JAVA_OPTS="$JAVA_OPTS -Dhttp.agent=Sakai"
JAVA_OPTS="$JAVA_OPTS -Dorg.apache.jasper.compiler.Parser.STRICT_QUOTE_ESCAPING=false"
JAVA_OPTS="$JAVA_OPTS -Dsakai.security=$CATALINA_HOME/sakai/"
JAVA_OPTS="$JAVA_OPTS -Duser.timezone=Europe/Istanbul"
JAVA_OPTS="$JAVA_OPTS -Duser.language=tr"
JAVA_OPTS="$JAVA_OPTS -Duser.region=TR"
JAVA_OPTS="$JAVA_OPTS -Dsakai.cookieName=SAKAI2SESSIONID"
JAVA_OPTS="$JAVA_OPTS -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=8089 -Dcom.sun.management.jmxremote.local.only=false -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false"
```
<b>setenv.sh</b> dosyasına çalıştırma izni verelim ve <b>server.xml</b> dosyasına <b>UTF-8</b> desteği ekleyelim.
```sh
chmod a+x *.sh
cd ../conf
vim server.xml
```
Önceki;
```sh
<Connector port="8080" protocol="HTTP/1.1"  
               connectionTimeout="20000"
               redirectPort="8443" />
```
Sonraki;
```sh
<Connector port="8080" protocol="HTTP/1.1"  
               connectionTimeout="20000"
               redirectPort="8443" URIEncoding="UTF-8"/>
```
<b>TOMCAT</b>'in <b>webapps</b> dizini altındaki varsayılan dosya ve dizinlerini temizleyelim;
```sh
cd ..
rm -rf webapps/*
```
<b>TOMCAT</b>'in sağlıklı başlaması için aşağıdaki klasörleri oluşturuyoruz;
```sh
mkdir -p shared/classes shared/lib common/classes common/lib server/classes server/lib
```
## TOMCAT'i hızlı başlatmak;
```sh
vim /opt/tomcat/conf/context.xml
```
Context altına şu şekilde ekleyin;
```sh
<Context>
    <JarScanner>
    <JarScanFilter defaultPluggabilityScan="false" />
    </JarScanner>
```
## SAKAI Kurulumu;
<b>Sakai</b> dosyalarını git ile indirip kurmak istediğimiz <b>branch</b> üzerine geçiş yapalım. Örneğin <b>21</b> versiyonun son hali için <b>21.x</b> diyebilirsiniz. Biz <b>master</b> dizinine geçiş yapıp daha release olmamış <b>22</b> sürümünü kuracağız.

```sh
cd /opt/tomcat
git clone https://github.com/sakaiproject/sakai.git
cd sakai && git checkout master
```
Master dizinine geçiş yapıp maven ile proje kütüphanelerini indiriyoruz;
```sh
cd /opt/tomcat/sakai/master
mvn clean install
```
Ekran çıktısı aşağıdaki gibi olmalıdır. <b>BUILD SUCCESS</b> ifadesini göremiyorsanız bir şeyler ters gitmiş demektir. Dönüp adımları tekrar kontrol edin!
```sh
[INFO] Installing /opt/apache-tomcat-9.0.43/sakai/master/pom.xml to /root/.m2/repository/org/sakaiproject/master/SAKAI22-SNAPSHOT/master-22-SNAPSHOT.pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 55.236 s
[INFO] Finished at: 2021-01-06T20:02:59+02:00
[INFO] Final Memory: 13M/661M
[INFO] ------------------------------------------------------------------------
```
Sonraki adımda bir üst dizine geçip <b>SAKAI</b>'yi derliyoruz;
```sh
cd ..
mvn clean install sakai:deploy -Dmaven.tomcat.home=/opt/tomcat -Dsakai.home=/opt/tomcat/sakai -Djava.net.preferIPv4Stack=true -Dmaven.test.skip=true
```
Ekran çıktısı aşağıdaki gibi olmalıdır. Bu kısımda da<b>BUILD SUCCESS</b> ifadesini görmeniz gerekmektedir.
```sh
[INFO] Sakai Soap (CXF) ................................... SUCCESS [ 12.892 s]
[INFO] Sakai base pom ..................................... SUCCESS [  0.004 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 10:31 min
[INFO] Finished at: 2021-01-06T20:22:22+02:00
[INFO] Final Memory: 532M/973M
[INFO] ------------------------------------------------------------------------
```
<!-- SAKAI MySQL Settings -->
<b>JAVA</b> ile <b>MySQL</b> haberleşmesi için <b>mysql-connector</b> indirip tomcat'in kütüphanelerine ekleyelim.
```sh
cd ~/download
wget http://linuxpanel.net/sakai/mysql-connector-java-8.0.19.jar
cp mysql-connector-java-8.0.19.jar /opt/tomcat/common/lib/
```
<b>SAKAI</b> için bir veritabanı, kullanıcı adı ve şifre oluşturalım;
```sh
mysql -u root -p
Enter password:
mysql> create database sakaidb default character set utf8;
mysql> GRANT ALL PRIVILEGES ON sakaidb.* TO 'sakaiuser'@'%' IDENTIFIED BY 'sakaipassword';
mysql> FLUSH PRIVILEGES;
mysql> quit
```
<b>MySQL</b> için <b>my.cnf</b> ayarları şu şekilde düzenlenebilir, ben burada <b>PERCONA</b>'dan faydalandım. Kurumunuzun ders, öğrenci, eğitmen sayılarına ve MySQL sunucunuzun fiziksel kapatisesine bağlı olarak düzenleme yapmak için sizde ücretsiz ölçekleme yapabilirsiniz. [PERCONA - The Database Performance Experts](https://www.percona.com/)
```sh
cp /etc/my.cnf /etc/my.cnf.orj
vim /etc/my.cnf
```
```sh
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html

[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M
skip-name-resolve
open_files_limit                = 65536
thread-cache-size               = 100
table-definition-cache          = 4096
table-open-cache                = 10000
datadir                         = /var/lib/mysql
socket                          = /var/lib/mysql/mysql.sock
symbolic-links                  = 0
max_connections                 = 65536
thread_cache_size               = 100
key_buffer_size                 = 32M
low_priority_updates            = 1
max_allowed_packet              = 20M
query_cache_size                = 128M
default-storage-engine          = InnoDB
lower_case_table_names          = 1
# innodb_buffer_pool_size = Fiziksel Ram'in %70'i
innodb_buffer_pool_size         = 6G
innodb_thread_concurrency       = 50
innodb_flush_method             = O_DIRECT
local-infile                    = 0
max_heap_table_size             = 128M
tmp-table-size                  = 32M
innodb_file_per_table           = 1



[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```
<b>MySQL</b> servisini yeniden başlatalım ve servisin sağlıklı çalıştığından emin olalım;
```sh
service mysqld restart
service mysqld status
```
Sakai için veritabanı ve diğer ayarları sakai.properties dosyasına ekliyoruz;
```sh
cp /opt/tomcat/sakai/config/configuration/bundles/src/bundle/org/sakaiproject/config/bundle/default.sakai.properties /opt/tomcat/sakai/sakai.properties
vim /opt/tomcat/sakai/sakai.properties
```
Veritabanı ile ilgili tanımlamaları yapmak için aşağıdaki gibi satırları düzenleyin;
```sh
# The database username and password. The defaults are for the out-of-the-box HSQLDB.  
# Change to match your setup. Do NOT enable access to your database without a password.
# Defaults: HSQLDB default user: "sa"/""
username@javax.sql.BaseDataSource=sakaiuser
password@javax.sql.BaseDataSource=sakaipassword

# Colon (":") separated list of tables to cache. Start the list with a colon. Use :all: to cache all tables
# DEFAULT: none (null)
# DbFlatPropertiesCache= 

## HSQLDB settings (active/in-memory by DEFAULT)
# vendor@org.sakaiproject.db.api.SqlService=hsqldb
# driverClassName@javax.sql.BaseDataSource=org.hsqldb.jdbcDriver
# hibernate.dialect=org.hibernate.dialect.HSQLDialect
# validationQuery@javax.sql.BaseDataSource=select 1 from INFORMATION_SCHEMA.SYSTEM_USERS
# Two hsqldb storage options: first for in-memory (no persistence between runs), second for disk based.
# url@javax.sql.BaseDataSource=jdbc:hsqldb:mem:sakai
# url@javax.sql.BaseDataSource=jdbc:hsqldb:file:${sakai.home}db/sakai.db

## MySQL settings
vendor@org.sakaiproject.db.api.SqlService=mysql
driverClassName@javax.sql.BaseDataSource=com.mysql.jdbc.Driver
# driverClassName@javax.sql.BaseDataSource=org.mariadb.jdbc.Driver
hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
url@javax.sql.BaseDataSource=jdbc:mysql://localhost:3306/sakaidb?useUnicode=true&characterEncoding=UTF-8&useServerPrepStmts=false&cachePrepStmts=true&prepStmtCacheSize=4096&prepStmtCacheSqlLimit=4096
validationQuery@javax.sql.BaseDataSource=select 1 from DUAL
defaultTransactionIsolationString@javax.sql.BaseDataSource=TRANSACTION_READ_COMMITTED

# To get accurate MySQL query throughput statistics (e.g. for graphing) from the mysql command show status like 'Com_select'
# This alternate validation query should be used so as not to increment the query counter unnecessarily when validating the connection:
# validationQuery@javax.sql.BaseDataSource=show variables like 'version'
```
<b>SAKAI</b>'yi başlatıyoruz;
```sh
catalina.sh start
```
Catalina.out ile tomcat loglarına bakıyoruz;
```sh
tail -f /opt/tomcat/logs/catalina.out
```

Ekran çıktısı aşağıdaki gibi olmalıdır.
```sh
INFO [main] org.apache.catalina.startup.Catalina.start Server startup in 44804 ms
```
## SAKAI Kuruldu;
http://127.0.0.1:8080/portal
<!-- CONTACT -->
## Contact

A. Volkan ERGÜL - ahmet.ergul@gmail.com

Project Link: [https://github.com/ahmetergul35/sakai22-SNAPSHOT/blob/main/installguide.md](https://github.com/ahmetergul35/sakai22-SNAPSHOT/blob/main/installguide.md)
