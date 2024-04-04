### Task
The Nautilus application development team recently finished the beta version of one of their Java-based applications, which they are planning to deploy on one of the app servers in Stratos DC. After an internal team meeting, they have decided to use the tomcat application server. Based on the requirements mentioned below complete the task:


a. Install tomcat server on App Server 1.
  1. Install jdk
  ```
  sudo yum install -y java-17-openjdk
  ```
  2. Download tomcat and extraxt tarball. I will install in ```/opt```.
  ```
  sudo curl -O https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.20/bin/apache-tomcat-10.1.20.tar.gz
  sudo tar -xzf apache-tomcat-10.1.20.tar.gz
  sudo rm -f /opt/apache-tomcat-10.1.20.tar.gz 
  ```
b. Configure it to run on port 8082.
  ```
  # In file /opt/apache-tomcat-10.1.20/conf/server.xml
  # In Connector section
  # modify port to 8082
  ```
c. There is a ROOT.war file on Jump host at location /tmp. Deploy it on this tomcat server and make sure the webpage works directly on base URL i.e curl http://stapp01:8082
  ```
  # on jump host
  scp /tmp/ROOT.war tony@stapp01:~

  # move war to appBase
  sudo mv ~/ROOT.war /opt/apache-tomcat-10.1.20/webapps/

  #Remove ROOT directory in webapps
  sudo rm -rf /opt/apache-tomcat-10.1.20/webapps/ROOT
  ```
d. Start tomcat: 
  ```sudo /opt/apache-tomcat-10.1.20/bin/startup.sh```

*Useful: Creating a service for tomcat: https://www.digitalocean.com/community/tutorials/install-tomcat-on-linux*
