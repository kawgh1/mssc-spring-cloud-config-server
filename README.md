## Spring Cloud Config Server
pulls its configurations from [Brewery Cloud Config Repo](https://github.com/kawgh1/mssc-brewery-cloud-config-repo)

![brewery microservices diagram](https://raw.githubusercontent.com/kawgh1/kwg-microservices-brewery/master/cloud-config-server-diagram1.png)

### Default Port Mappings - For Single Host (Cloud)
| Project ||
| --------| -----|
| **[KWG MICROSERVICES BREWERY](https://github.com/kawgh1/kwg-microservices-brewery)** ||
| **Service Name** | **Port** | 
| | |
| **[Brewery Beer Service](https://github.com/kawgh1/mssc-beer-service)** | 8080 |
| **[Brewery Beer Order Service](https://github.com/kawgh1/mssc-beer-order-service)** | 8081 |
| **[Brewery Beer Inventory Service](https://github.com/kawgh1/mssc-beer-inventory-service)** | 8082 |
| **[Inventory Failover Service](https://github.com/kawgh1/mssc-inventory-failover)** | 8083 |
| **[Netflix Eureka Server](https://github.com/kawgh1/brewery-eureka-server)** | 8761
| **[Spring Cloud Gateway Server](https://github.com/kawgh1/mssc-brewery-gateway)** | 9090
| **[Spring Cloud Config Server](https://github.com/kawgh1/mssc-spring-cloud-config-server)** | 8888
| | |
| | |
| | |
| **[Brewery Cloud Config Repo](https://github.com/kawgh1/mssc-brewery-cloud-config-repo)** |  -----|


### [Docker](#docker)


- ### Spring Cloud Config Client
	- Spring Cloud Config Client by default will look for a URL property
		- 'spring.cloud.config.url' - default is http://localhost:8888



- ### Spring Cloud Config

	- Spring Cloud Config provides externalized configuration for distributed environments
	- Provides a RESTful style API for Spring microservices to lookup configuration values
	- Spring Boot applications on startup obtain configuration values from Spring Cloud Config Server
	- Properties can be global and application specific
	- Properties can be stored by Spring Profiles
	- Easily encrypt and decrypt files

- ### Property Storage
	- Spring Cloud Config provides a number of options for property storage:
		- Git (default) or SVN
		- File System
		- HashiCorp's Vault
		- JDBC, Redis
		- AWS S3
		- CredHub

- ### Spring Cloud Config Client
	- Spring Cloud Config Client by default will look for a URL property
		- 'spring.cloud.config.url' - default is http://localhost:8888
	- If using discovery client, client will look for service called 'configserver'
	- Fail Fast - optionally configure client to fail with exception if config server cannot be reached

- ### Configuration Resources
	- Resources served as: /application/profile/label
	- **application** = spring.application.name
	- **profile** = Spring active profile(s)
	- **label** = spring.cloud.config.label

- ### Create Config Server Steps
	- Use Spring Initialzr
	- Spring Boot (latest release)
	- Config Server
	- Eureka Discovery Client
	
### - Cloud Security

- Spring Cloud Security
- Property Encryption / Decryption
	- At Rest Encryption with Spring CLoud Config
		- Spring Cloud Configuration supports property encryption and decryption
		- Should be used for sensitive data and passwords
	- Java Cryptography Extension (JCE)
		- Prior to Java 8 u162 required additional setup
		- Older examples will refer to download and install JCE
		- Included by default in Java 11+

- Configuration
	- Spring Cloud Configuration will store encrypted properties as:
		- {cyper}<your encrypted value here>
	- When a Spring Cloud Config client requests an encrypted property the value is decrypted and presented to the client in the request
	- Must set a symmetric key in property 'encrypt.key' - should prefer setting this as an environment variable
	- Asymmetric (public / private) keys also supported - see docs for details

- Encryption / Decryption
	- Spring CLoud Configuration provides endpoints for property encryption / decryption
		- POST /encrypt - will encrypt body of post
		- POST /decrypt - will decrypt body of post

- Security Retrospective

	- Problems with HTTP Basic Autentication
		- Eureka Clients sending id / passwords plain text
		- Other clients send in header with base64 encoding
			- Easily converted to plain text
		- Sent in each request - thus more exposure to attack
		- HTTPS can be used to mitigate security riks with HTTP Basic

	- Problems with using User ID / Password
		- Sending password to multiple servers
		- Validation overhead
			- ie, need to verify credentials with authentication server, which becomes a scalability bottleneck
		- Mitigation - Use tokens such as JWT which can be trusted and authenticated by each service
	
	- Encryption in Flight
		- HTTPS for RESTful services
			- Should be a 'must' for public networks
			- Should be highly considered for internal networks
				- There is a CPU cost to using HTTPS - but less of concern than in the past
		- Use encrypted connections for network communication to databases and message brokers (ActiveMQ - JMS, etc.)
			- Typically is not, options vary per implementation

	- Encryption at rest:
		- Consider using encrypted file systems
		- Databases and message brokers have encryption options for data stored
		- Don't overlook backups. Backup files should also be encrypted
		
[Top](#top)
		

### [Docker](#docker)
- JVM Resource Limits
	- Java 11 or higher is recommended
	- Earlier versions of Java did not recognize limits on memory and CPUs of a container
		- Java would see system memory, not the container
		- Docker will terminate a container when resource limits are exceeded
		- Difficult to troubleshoot
		- ex.) you're running your container and all of a sudden it crashes 
			- you might spend hours debugging the container/program when it's Java itself

- Docker Host Considerations
	- Running multiple Docker contaienrs on a host can be resource intensive
	- Can also consume a lot of disk space on host system
	- Recommendations:
		- Use 'slim' base images - you don't need a full featured linux OS for Java runtime
		- Be aware of build layers as you build images
			- A host system only needs one copy of a layer
			- ex.) you have 12 microservices and 5 layers, but the first 4 layers are all shared by the 12 microservices
				- so you only need 1 of each of the first 4 layers - not 12 x 4 = 48 layers

- Which Base Image?
	- Highly debated and opinionated in Java Community
	- We will be using OpenJDK Slim - appropriate for ~95% of applications
	- Opionated options are from Fabric8 are a good choice
	- Azul Systems published curated JVM Docker images, good choice for commercial applications
	- For security and compliance, companies might want to build their own base image

- Building Docker Images with Maven
	- Maven can be configured to build and work with Docker images
	- Capability is done with Maven plugins
	- Several very good options available
	- Will be using Fabric8's Maven Docker Plugin (pronounce 'fabricate')
		- Very versatile plugin w/ rich capabilities - only using for build
		- Fabric8 is a DevOps platofrm for Kubernetes and Openshit - worth becoming more familiar with
			- This Maven Docker Plugin is 1 tool of many in that platform

- Docker Integration Under the Hood
	- The Docker Maven Plugins work with Docker installed on your system
		- Generally, will autodetect the Docker daemon
		- This can be different depending on OS
	- If Fabric8 cannot connect to Docker you may need to configure the plugin
		- Under the Maven POM properties element:
			- Set property **'docker.host'** for your OS
			- Difficulties with versions of Windows older than Windows 10

- Building Docker Images with Maven
	- For microservices using common BOM
		- Fabric8 is configured in parent
		- Each service will need a Dockerfile in /src/main/docker
	- For microservices **NOT** using common BOM
		- Fabric8 will need to be configured in Build element of Maven POM
		- Each service will need a Dockerfile in /src/main/docker

- Spring Boot Layered Builds
	- Common best practice
	- Layered builds is a new feature with Spring Boot 2.3.0
		- [https://springframework.guru/why-you-should-be-using-spring-boot-docker-layers/](#https://springframework.guru/why-you-should-be-using-spring-boot-docker-layers/)
	- We will be configuring our builds to perform layered builds
	- Services using BOM need to use 1.0.17 or higher
	- Services not using BOM need to use Spring Boot 2.3.0 or higher

- Publishing to Docker Hub
	- If you've created a Docker Hub account and wish to publish to your own account:
		- Configure server credentials in settings.xml (User home dir/.m2)
		- In servers element, add server with id of 'docker.io'
		- add your username and password to respective elements
		- https://www.udemy.com/course/spring-boot-microservices-with-spring-cloud-beginner-to-guru/learn/lecture/20071480#questions/11674030
		- Depends what OS you are using.
          
          Unix/Mac OS X – ~/. m2/repository.
          
          Windows – C:\Users\{your-username}\. m2\repository.
          
          
          Create a settings.xml file if one does not exist and add
          
              <?xml version="1.0" encoding="UTF-8"?>
              <settings>
              <servers>
                  <server>
                      <id>registry.hub.docker.com</id>
                      <username><DockerHub Username></username>
                      <password><DockerHub Password></password>
                  </server>
              </servers>
              </settings>
		
- Running Docker build in IntelliJ
    - first run maven clean, maven package
    - Under Maven/ {application} / Plugins / docker / docker:build to build a local image on the computer
     - to push to DockerHub using Maven
            - before pushing, remember to log out, then log in from the command line to your docker hub account
                - # you may need log out first `docker logout` ref. https://stackoverflow.com/a/53835882/248616
                  docker login
                  
                  According to the docs:
                  
                  You need to include the namespace for Docker Hub to associate it with your account.
                  The namespace is the same as your Docker Hub account name.
                  You need to rename the image to YOUR_DOCKERHUB_NAME/docker-whale.
                  
                  So, this means you have to tag your image before pushing:
                  
                  docker tag firstimage YOUR_DOCKERHUB_NAME/firstimage
                  
                  and then you should be able to push it.
                  
                  docker push YOUR_DOCKERHUB_NAME/firstimage

            - mvn clean package docker:build docker:push
     - need to push each server/microservice up to DockerHub
     
     - Docker interview with John Thompson and James Labocki of Redhat on Docker
     - https://www.udemy.com/course/spring-boot-microservices-with-spring-cloud-beginner-to-guru/learn/lecture/20118108#questions
     
     [Top](#top)
     
### [Consolidated Logging (ELK Stack)](#consolidated-logging-elk-stack)
  
  ![elk stack diagram](https://raw.githubusercontent.com/kawgh1/kwg-microservices-brewery/master/server-images/ELK-Stack-diagram.png)

#### - E - Elasticsearch
#### - L - Logstash
#### - K - Kibana
  
  
- All products open source, supported by company called elastic
- **Elasticsearch**
    - JSON based search engine based on Lucene
        - Highly scalable - 100s of nodes (cloud scale)
- **Logstash**
    - Data processing pipeline for log data
    - Similar to ETL tool "Extract-Transform-Load"
        - Allows to:
            - Collect log data from multiple sources
            - Transform that log data
            - Send log data to Elasticsearch
            
- **Kibana**
    - Data visualization fool for Elasticsearch
    - Can query data and act as a dashboard
    - Can also create charts, graphs and alerts
        - Many many more features
        
- FileBeat
    - FileBeat is the log shipper
    - Moves log data from a client machine to a destination
    - Often destination is a **logstash server**
        - Logstash is used for further transformation before sending to Elasticsearch
        
- ELK Without Logstash
    - Filebeat has ability to do ***some*** transformations
    - Thus, possible to skip Logstash and write directly to Elasticsearch
    - Previously we setup JSON log output
    - Filebeat can convert JSON logs to JSON objects for Elasticsearch
    
  
  [Top](#top)