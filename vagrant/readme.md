# 기본 명령어
`vagrant up`
`vagrant ssh-config <serverName>`
`vagrant ssh <serverName>`
`vagrant halt`

# vabrant VM 올리기
1. 필수 플러그인 설치
    ```bash
    $ vagrant plugin install vagrant-vbguest
    ```
2. 베이그란트 설치
    ```bash
    $ vagrant up
    ```
3. 베이그란트 종료
    ```bash
    $ vagrant halt
    ```
## 0. install java
1. We will be using open java for our demo, Get the latest version from http://openjdk.java.net/install/
    ```bash
    $ yum install java-1.8*
    # yum -y install java-1.8.0-openjdk-devel
    ```
2. Confirm java Version and set the java home
    ```bash
    $ java -version
    $ find /usr/lib/jvm/java-1.8* | head -n 3
    $ vi ~/.bash_profile
    ```
    ```vi
    # ~/.bash_profile
    ...
    JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-<Java version which seen in the above output>
    JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.252.b09-2.el7_8.x86_64
    export JAVA_HOME
    PATH=$PATH:$JAVA_HOME
    ...
    ```
3. The output should be something like this,
    ```bash
    $ java -version
    ~~
    ```
## 0. ssh open 
1. sshd_config 비밀번호 인증 설정
    ```bash
    $ sudo vi /etc/ssh/sshd_config
    ```
    `PasswordAuthentication no` => `PasswordAuthentication yes`
2. sshd 서비스 재시작
    ```bash
    $ sudo systemctl restart sshd
    ```

# 1. jenkins-server
```bash
$ vagrant ssh jenkins-server
```

## 1.1 install jenkins
1. Get the latest version of jenkins from `https://pkg.jenkins.io/redhat-stable/` and install
    ```bash
    [vagrant@jenkins-server ~]$ yum -y install wget
    [vagrant@jenkins-server ~]$ sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
    [vagrant@jenkins-server ~]$ sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
    [vagrant@jenkins-server ~]$ yum -y install jenkins
    ```

2. Start Jenkins
    ### 2.1 yum 으로 설치 후 실행
    ```bash
    #Start jenkins service
    [vagrant@jenkins-server ~]$ service jenkins start

    #Setup Jenkins to start at boot,
    [vagrant@jenkins-server ~]$ chkconfig jenkins on

    #Show jenkins password
    [vagrant@jenkins-server ~]$ sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

    ### 2.3. war 파일로 실행
    ```bash
    $ curl -O http://mirrors.jenkins.io/war-stable/latest/jenkins.war
    $ java -jar jenkins.war
    ```

3. Accessing Jenkins
By default jenkins runs al port `8080`, You can access jenkins at
    ```
    http://YOUR-SERVER-PUBLIC-IP:8080
    ```

4. install plugin
- GitHub Integration: GitHub Integration Plugin for Jenkins
- GitHub: This plugin integrates GitHub to Jenkins.
- maven invoker
- maven Integration
- Publish Over SSH

# 2. tomcat-server
```bash
$ vagrant ssh tomcat-server
```
## 2.1 install tomcat
| 구분 | 버전 |
|:---|:---|
| java | 1.8 |
| tomcat | 9 |

localhost:8080
192.168.0.3:8080/manager/html

1. tomcat 설치
    ```bash
    $ yum install wget -y
    $ wget http://apache.mirror.cdnetworks.com/tomcat/tomcat-9/v9.0.35/bin/apache-tomcat-9.0.35.tar.gz
    $ tar -zxpvf apache-tomcat-9.0.35.tar.gz -C /opt/
    $ cd /opt/
    $ mv apache-tomcat-9.0.35 tomcat
    $ echo "export CATALINA_HOME='/opt/tomcat/'" >> ~/.bashrc
    $ source ~/.bashrc
    ```

2. tomcat-users.xml 수정
    ```bash
    $ sudo vi /opt/tomcat/conf/tomcat-users.xml
    ```
    ```xml
    <!--
        <role rolename="tomcat"/>
        <role rolename="role1"/>
        <user username="tomcat" password="<must-be-changed>" roles="tomcat"/>
        <user username="both" password="<must-be-changed>" roles="tomcat,role1"/>
        <user username="role1" password="<must-be-changed>" roles="role1"/>
    -->
    ```
3. tomcat 실행
    `sudo /opt/tomcat/bin/startup.sh`
    ```bash
    $ cd /opt/tomcat
    $ sudo ./bin/catalina.sh start #or) $ sudo ./bin/startup.sh
    ```
    - when want to off
        ```bash
        $ ./bin/shutdown.sh
        ```
    - see logs
        ```bash
        $ tail -f ./logs/catalina.out
        ```
4. 권한설정
```bash
$ find ./ -name context.xml
./conf/context.xml
./webapps/host-manager/META-INF/context.xml
./webapps/manager/META-INF/context.xml
$ sudo vi ./webapps/manager/META-INF/context.xml
```
```
<Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
```
```
<!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
```
```bash
$ sudo ./bin/catalina.sh stop
$ sudo ./bin/catalina.sh start
```

```bash
$ vi ./conf/tomcat-users.xml
```

# 패키지 설치
jenkins server, apache server
## git 설치
```bash
$ sudo yum install git -y
```
# maven 설치
```bash
$ sudo yum install maven -y
```