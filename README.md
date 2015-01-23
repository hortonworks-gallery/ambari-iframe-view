#### iFrame embedded in Ambari view
Embed any webapp or webpage into Ambari!

	
##### Screenshots

- Ranger audits in Ambari
![Image](../master/screenshots/Embedded-Ranger.png?raw=true)

- Beeswax query in Ambari
![Image](../master/screenshots/Embedded-Hue.png?raw=true)

- Sandbox tutorials in Ambari
![Image](../master/screenshots/tutorials.png?raw=true)

- phpLDAP UI in Ambari
![Image](../master/screenshots/phpldap.png?raw=true)

- Tableau visualization in Ambari
![Image](../master/screenshots/Embedded-Tableau.png?raw=true)

- Other ideas:
	- Create an Ambari view of customer website/webapp in front of them to demonstrate ease of setup
	- Demonstrate integration with external webservices:
	  JQuery webapp making external REST calls (in this case to Youtube) from within Ambari View
      ![Image](../master/screenshots/jQuery.png?raw=true)
		
##### Steps

- Download HDP 2.2 sandbox VM image (Sandbox_HDP_2.2_VMware.ova) from [Hortonworks website](http://hortonworks.com/products/hortonworks-sandbox/)
- Import Sandbox_HDP_2.2_VMware.ova into VMWare and set the VM memory size to 8GB
- Now start the VM
- After it boots up, find the IP address of the VM and add an entry into your machines hosts file e.g.
```
192.168.191.241 sandbox.hortonworks.com sandbox    
```
- Connect to the VM via SSH (password hadoop) and start Ambari server
```
ssh root@sandbox.hortonworks.com
/root/start_ambari.sh
```

- Install Maven
```
mkdir /usr/share/maven
cd /usr/share/maven
wget http://mirrors.koehn.com/apache/maven/maven-3/3.2.5/binaries/apache-maven-3.2.5-bin.tar.gz
tar xvzf apache-maven-3.2.5-bin.tar.gz
ln -s /usr/share/maven/apache-maven-3.2.5/ /usr/share/maven/latest
echo 'M2_HOME=/usr/share/maven/latest' >> ~/.bashrc
echo 'M2=$M2_HOME/bin' >> ~/.bashrc
echo 'PATH=$PATH:$M2' >> ~/.bashrc
export M2_HOME=/usr/share/maven/latest
export M2=$M2_HOME/bin
export PATH=$PATH:$M2
```

- To deploy the iFrame view, run below
```
#Pull code
cd
git clone https://github.com/abajwa-hw/iframe-view.git
cd iframe-view

#OPTIONAL STEP: change the iframe to point to any website you want
vi src/main/resources/index.html

#Add ambari jar
mvn install:install-file -Dfile=/usr/lib/ambari-server/ambari-views-1.7.0.169.jar -DgroupId=org.apache.ambari -DartifactId=ambari-views -Dversion=1.3.0-SNAPSHOT -Dpackaging=jar

#Compile view
mvn clean package

#move jar to Ambari dir
cp target/*.jar /var/lib/ambari-server/resources/views
   
```
- Restart Ambari
```
#on HDP 2.2 sandbox
service ambari restart

#on non-sandbox
service ambari-server restart
```

- Now open Ambari and navigate to the Views as shown below and select 'iFrame View'
http://sandbox.hortonworks.com:8080
![Image](../master/screenshots/Open-view.png?raw=true)

- To point the iFrame to another website
```
#change the url here
vi src/main/resources/index.html

#bump up the version e.g. to 1.0.1
vi src/main/resources/view.xml

mvn clean package
cp target/*.jar /var/lib/ambari-server/resources/views
service ambari restart
```

