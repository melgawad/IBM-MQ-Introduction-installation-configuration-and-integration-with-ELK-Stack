IBM MQ Introduction, installation, configuration and integration with ELK Stack 
Introduction To IBM MQ 
•	What is messaging? 
o	Messaging is the wiring that joins your business functions together. 
o	Placing a message queue between two or more applications allows data to move between them without them having to communicate directly. 
o	Whether one application goes away, or whether it is just slower than the other one, message queuing provides a shock absorber by dealing with instability on both sides. 
o	This model is known as asynchronous messaging. 
 










•	MQ Essentials 
o	Queue managers host your messaging resources such as queues and topics. 
o	Queues inside a queue manager, used to store messages until an application is ready to receive them. 
 
o	Listeners are processes that listen for connections to the queue manager using ports. These are required by applications to put and get messages on queues. 
o	Channels are the way queue managers communicate with each other and with the applications. 
 
•	Messaging styles 
o	MQ supports many messaging styles including point-to-point, request-response, and publish-subscribe. 
o	All styles benefit from MQ's reliable asynchronous message delivery and can be applied across any MQ network. 
 
•	Point-to-point messaging 
o	This is the flow of messages from one message producer to one message consumer. 
o	Typically, a message represents an operation or command to be performed by the consumer. 
o	Point-to-point messaging is the basic building block for many messages exchange patterns. 
 
•	Request-response messaging 
o	Some applications require a two-way conversation. 
o	Request-response is a messaging pattern that completes once the requesting application receives a reply message on completion of the operation. 
 
•	Publish-subscribe messaging 
o	This is the flow of messages from a message producer to all message consumers that have a registered interest in a given messaging topic. 
o	In publish-subscribe messaging, messages sent from publisher to subscriber will often indicate a change of state or an event. 
 
Understanding queue types 
•	Local 
o	This kind of queue stores messages within a queue manager and is at the heart of every messaging interaction. This will be the ultimate destination for all messages sent through MQ and is the queue type you will likely be using most of the time. 
 
•	Alias 
o	This kind of queue is a pointer to another queue, often on the same queue manager. This is used as a layer of abstraction between the application and the underlying local queue. For example, to give it different security permissions or default message behavior. 
 
•	Remote 
o	This kind of queue is a pointer to another queue on a different queue manager. This is useful when the application connects to a queue manager that does not have the local queue defined. 
 
•	Model 
o	This kind of queue is used as a template when creating new queues programmatically. You would use this queue when the underlying application does not require messages to be retained after the application has completed. 
 
 
The Installation
•	Step 1 
•	Install Docker Engine on RHEL 
o	$ sudo yum install -y yum-utils 
o	$ sudo yum-config-manager \ 
--add-repo \ 
https://download.docker.com/linux/centos/docker-ce.repo 
o	$ sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin 
o	$ sudo systemctl enable –now docker 
 
•	Step 2 
•	Install IBM MQ 
o	Create Network for IBM MQ Server 
	docker network create mq-demo-network 
o	Create Volume for IBM MQ Server 
	docker volume create qm1data 
o	Docker Run IBM Container 
	$ docker run --env LICENSE=accept --env MQ_QMGR_NAME=QM1 --volume qm1data:/mnt/mqm --publish 1414:1414 --publish 9443:9443 --network mq-demo-network --network-alias qmgr --detach --env MQ_APP_PASSWORD=password ibmcom/mq:latest 
 
	$ docker ps 
 
o	$ docker exec -ti <your container id> /bin/bash 
	Display MQ Version 
•	$ dspmqver 
 
	Display running Queue Manager 
•	$ dspmq 
 
	 
•	Step 3 
•	Login IBM MQ 
o	https://localhost:9443 or https://IP Address:9443  
o	Username: admin 
o	Password: passw0rd 
 

The Configurations  
To integrate Logstash with IBM MQ must there is pre-requirements  
o	Install Logstash Service and the service must be running  
o	Install JMS input plugin 
o	Install MQclinte Package   
o	Create configurations files for Jms.conf and mq.yml 
 
•	  Step 1
•	Install Logstash Service and Running it.
o	Follow this link to install Logstash rpm package https://www.elastic.co/downloads/logstash
o	 $ rpm -i <Logstash rpm package> 	
o	 $ systemctl enable –now logstash.service

•	  Step 2
•	Install JMS input plugin 
o	$ /usr/share/logstash/bin/logstash-plugin install logstash-input-jms

 



•	Step 3
•	Install MQclinte Package   
o	Follow this link to install MQclinte https://ibm.biz/mq91cdclients 
o	Must to be have IBM account to able to download the package
o	Install java open jdk 
o	$ yum install java-1.8.0-openjdk.x86_64
o	Install such as >>  9.2.2.0-IBM-MQ-Install-Java-All.jar   
o	$ java -jar 9.2.2.0-IBM-MQ-Install-Java-All.jar
o	Move all files in jars file to /etc/logstash/conf.d/

•	Step 3
•	JMS file configuration 
o	$ touch /usr/share/logstash/jms.conf
o	$ cat Jms.conf

 
 
•	Step 4
•	MQ file configuration 
o	$ touch /mnt/mq.yml
o	$ cat mq.yml

 
IBM MQ integration with ELK Stack
•	Create massage  
o	Menu >> manage >> Local Queue Managers >> (QM Name) >> DEV.QUEUE.1 >>Create >> (Enter the Application Data) >> Create  

 

•	Run Logstash pipeline 
o	/usr/share/logstash/bin/logstash -f jms.conf

 

•	Kibana index
 
 

•	References 
o	https://docs.docker.com/engine/install/rhel/ >> Docker installation
o	https://www.elastic.co/downloads/logstash >> Logstash installation
o	https://ibm.biz/mq91cdclients >> Download MQclient 
o	https://hub.docker.com/r/ibmcom/mq >> (Pull and Start) Run IBM MQ  
o	https://developer.ibm.com/articles/mq-fundamentals/ >> IBM MQ fundamentals 
o	 https://www.ibm.com/support/pages/system/files/inline-files/Download,%20install%20and%20test%20IBM-MQ-Install-Java-All%20(com.ibm_.mq_.allclient.jar)_0.pdf  >> IBM MQ documentation
o	https://github.com/saudurehman/logstash_jms_ibm_mq >> github configurations

 
 

