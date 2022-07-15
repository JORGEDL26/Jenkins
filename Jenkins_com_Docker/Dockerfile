FROM jenkins/jenkins:2.332.3-lts-centos7

###### User access ######
USER root

###### used by docker ######
VOLUME [ "/sys/fs/cgroup" ]

###### Update image ######
RUN yum update -y
######## Install Sudo & Add user ######
RUN yum install sudo -y
RUN echo 'jenkins ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
###### Install AWS CLI ######
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
RUN unzip awscliv2.zip
RUN sudo ./aws/install -i /usr/local/aws-cli -b /usr/local/bin
###### Install Packages ######
RUN yum install -y \
    wget \
    maven \
    epel-release
RUN yum -y update
###### Copiar Java 1 e 11 no alternatives ######
ADD java1.tar.gz /usr/lib/jvm
ADD java11.tar.gz /usr/lib/jvm
###### Alias ######
WORKDIR /usr/lib/jvm
RUN ln -s java-11-openjdk-11.0.8.10-0.el7_8.x86_64 java-11-openjdk-11.0.4.11-0.el7_6.x86_64
RUN ln -s java-1.8.0-openjdk-1.8.0.262.b10-0.el7_8.x86_64 java-1.8.0-openjdk-1.8.0.212.b04-0.el7_6.x86_64
###### Setando como alternativas ######
RUN alternatives --install /usr/bin/java java /usr/lib/jvm/java-11-openjdk-11.0.4.11-0.el7_6.x86_64/bin/java 2
RUN alternatives --install /usr/bin/java java /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.212.b04-0.el7_6.x86_64/jre/bin/java 3
###### Configurando o SonarQube ######
WORKDIR /
RUN wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.7.0.2747.zip
RUN sudo unzip sonar-scanner-cli-4.7.0.2747.zip
RUN sudo mv sonar-scanner-4.7.0.2747 /opt/sonar
RUN rm -rf /opt/sonar/conf/sonar-scanner.properties
COPY sonar-scanner.properties /opt/sonar/conf
RUN echo "PATH=/opt/sonar/bin" >> /etc/environment
RUN export PATH="$PATH:/opt/sonar/bin"
WORKDIR /usr/bin
RUN sudo ln -s /opt/sonar/bin/sonar-scanner sonar-scanner
###### Instalar o Docker (Suporte AWS) ######
RUN sudo yum install -y net-tools
RUN sudo yum install -y iproute
USER jenkins
# >>>>> Os passos a seguir ja estão realizados e salvos no /var/jenkins_home (EFS) <<<<<
#RUN curl -fsSL https://get.docker.com/rootless -o /tmp/install-docker-rootless.sh; \
   #chmod +x /tmp/install-docker-rootless.sh
#COPY init-all.sh /var/jenkins_home/init-all.sh
##ENTRYPOINT ["/sbin/tini", "--", "/usr/local/bin/jenkins.sh"] >>> não utilizado
##ENTRYPOINT ["/var/jenkins_home/init-all.sh"] >>> não utilizado
ENTRYPOINT ["/sbin/tini","--","/var/jenkins_home/init-all.sh"]

###### Instalando o Ansible ######
#RUN sudo yum install ansible -y

###### Instalar o Docker ######
#RUN sudo yum install -y yum-utils
#RUN sudo yum-config-manager \
#    --add-repo \
#    https://download.docker.com/linux/centos/docker-ce.repo
# RUN sudo yum install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
