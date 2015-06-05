#### iFrame embedded in Ambari view
Embed any webapp or webpage into Ambari!

	
##### Screenshots

- Remote desktop into your sandbox from within Ambari by combining the view with the [VNC stack](https://github.com/abajwa-hw/vnc-stack):
![Image](../master/screenshots/screenshot-VNC-view.png?raw=true)

  - ...and bring up Ambari in a Firefox browser to create an Ambari Droste effect
  ![Image](../master/screenshots/resursive-ambari.png?raw=true)

- Ranger audits in Ambari
![Image](../master/screenshots/Embedded-Ranger.png?raw=true)

- Beeswax query in Ambari
![Image](../master/screenshots/Embedded-Hue.png?raw=true)

- iPython Notebook in Ambari - see [here](https://github.com/abajwa-hw/hdp-datascience-demo#ipython-notebook-embedded-in-ambari-view) for additional iPython notebook instructions
![Image](../master/screenshots/Embedded-iPython.png?raw=true)

- Document crawler in Ambari 
![Image](../master/screenshots/document-crawler.png?raw=true)

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
curl -o /etc/yum.repos.d/epel-apache-maven.repo https://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo
yum -y install apache-maven
```

- To deploy the iFrame view, run below. On non-sandbox env, these steps should be run on node running Ambari server
```
#Pull code (pom.xml, view.xml, index.html)
cd
git clone https://github.com/abajwa-hw/iframe-view.git
cd iframe-view

#OPTIONAL STEP: change the iframe to point to any website you want. The default is set to Ranger admin (sandbox:6080)
vi src/main/resources/index.html

#OPTIONAL STEP: change the view label that will display in Ambari, or the internal view name. The default is "iFrame View"
vi src/main/resources/view.xml


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

#Now restart Ambari 

#on HDP 2.2 sandbox
service ambari restart
#on non-sandbox
service ambari-server restart
```

