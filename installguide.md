<!-- GETTING STARTED -->
## <div align="center"><p>Sakai 22.x Install Guide - Ram:16GB Centos7x64</p> </div>
Sakai ayrı bir sunucuya mysql ayrı bir sunucuya kurulacaktır.
Öncelikle her iki sunucuda da şu komutları çalıştırın;

## Bileşenler
```sh
yum -y install vim nano wget git epel-release
service firewalld stop
chkconfig firewalld off
```
  
## JAVA JDK Kurulumu
Sakai sunucusuna geçin; Kurulum için gerekli dosyaları indirebileceğimiz bir <b>download</b> dizini oluşturalım.
```sh
yum -y install vim nano wget git epel-release
service firewalld stop
chkconfig firewalld off
```

<b>Download</b> dizinine geçiş yaptıktan sonra aşağıdaki dosyaları indirip <b>tar</b> komutu ile açıyoruz.
```sh
wget http://linuxpanel.net/sakai/jdk-8u271-linux-x64.tar.gz
tar xzf jdk-8u271-linux-x64.tar.gz
```

Java kurulumu için gerekli dosyaları indirdik ve tar komutu ile açtık. Kurulumu <b>/opt</b> dizini altında yapılandıracağız. <b>tar</b> ile açtığımız dosyaları <b>/opt</b> dizinine taşıyalım ve bu dizine geçiş yapalım.
```sh
mv ~/download/jdk1.8.0_271/
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
* java version "1.8.0_271"  
* Java(TM) SE Runtime Environment (build 1.8.0_271-b11)  
* Java HotSpot(TM) 64-Bit Server VM (build 25.271-b11, mixed mode)  

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
mv apache-maven-3.6.3 /opt
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
Apache Maven 3.6.3 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-10T16:41:47+00:00)  
Maven home: /opt/maven  
Java version: 1.8.0_271, vendor: Oracle Corporation  
Java home: /opt/jdk1.8.0_271/jre  
Default locale: en_US, platform encoding: UTF-8  
OS name: "linux", version: "3.10.0-514.6.1.el7.x86_64", arch: "amd64", family: "unix"  
```

## TOMCAT Kurulumu
<b>download</b> dizinine indirdiğimiz <b>apache-tomcat-8.0.32.tar.gz</b> dosyasını tar komutu ile arşivden çıkartalım. Daha sonra mv komutu ile <b>/opt</b> dizinine taşıyalım <b>apache-tomcat-9.0.43</b> dizine <b>tomcat</b> ismiyle sembolik link verelim.

```sh
cd ~/download
wget https://kozyatagi.mirror.guzel.net.tr/apache/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
tar xpfz apache-maven-3.6.3-bin.tar.gz
mv apache-maven-3.6.3 /opt
cd /opt
ln -s apache-maven-3.6.3/ maven
vim ~/.bashrc
```
  
  
  









<!-- CONTACT -->
## Contact

A. Volkan ERGÜL - ahmet.ergul@gmail.com

Project Link: [https://github.com/ahmetergul35/sakai22-SNAPSHOT/blob/main/installguide.md](https://github.com/ahmetergul35/sakai22-SNAPSHOT/blob/main/installguide.md)
