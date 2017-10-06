# docker-centos-java
Simple docker image with java JDK installed

We will create a docker image for a JAVA Application (Simple or not)

**[Note]**
- First of all we will add custom JDK because  sometime we MUST use a specific one



**[Pre requisite]**
- Docker installed

- for execute script Docker deamon must be started : dockerd with  --iptables=false



## A - Create a docker file 
Who begin by that:

    # Dockerfile
    FROM  centos

    MAINTAINER  Author Name <author@email.com>
    
    ENV JAVA_VERSION=8 
    JAVA_UPDATE=144 
    JAVA_BUILD=01
    JAVA_PATH=090f390dda5b47b9b721c7dfaa008135    
    JAVA_HOME=/usr/lib/jvm/default-jvm
   

**1. Update the package repository**

    #Upgrading system on Centos
    RUN yum -y upgrade
    RUN yum -y install wget
 
**2 . Downloading & Config Java 8 Oracle JDK **
    RUN wget --no-cookies --no-check-certificate --header "Cookie: oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/$JAVA_VERSION-$BUILD_VERSION/jdk-$JAVA_VERSION-linux-x64.rpm" -O /tmp/jdk-8-linux-x64.rpm
    RUN yum -y install /tmp/jdk-8-linux-x64.rpm
    RUN alternatives --install /usr/bin/java jar /usr/java/latest/bin/java 200000
    RUN alternatives --install /usr/bin/javaws javaws /usr/java/latest/bin/javaws 200000
    RUN alternatives --install /usr/bin/javac javac /usr/java/latest/bin/javac 200000

**3. Expose Container VOLUME**

    #Expose volume
    VOLUME /app
    VOLUME /tmp

**4. Clean your image**

    RUN rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*


**5. Add your Java/SpringBoot component**

#The gs-spring-boot-docker-0.1.0.jar must be in the same directory as the docker file

    ADD gs-spring-boot-docker-0.1.0.jar app.jar
    RUN sh -c 'touch /app.jar'
    #Set your entrypoint
    ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]


## B - Create docker image from docker file

    $ docker build -f Dockerfile -t demo/spring-java:8 --rm=true .

The "." is mandatory :) do not forget it !!!!


## C - RUN the docker image freshly created
    $ docker run -p 8080:8080 -t demo/spring-java:8

Or

    $ docker run -p 8080:8080 -t demo/spring-java:8 <map Volume for log for example>
