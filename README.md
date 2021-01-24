## Spring Cloud Config Server

### Default Port Mappings - For Single Host
| Service Name | Port | 
| --------| -----|
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