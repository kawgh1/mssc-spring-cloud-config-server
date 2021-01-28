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