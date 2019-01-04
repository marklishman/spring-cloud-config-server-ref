
# Configuration Server

You may be familar with config stored in files such as `application.properties` and `application-dev.properties` 
and their yaml equivalents but there is another way. Instead of having fragmented configuration in a mixture of 
property files and environment variables we can have use a configuration server to store it in a central repository.

Include the `spring-cloud-config-server` dependency.

```java
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```

Add the `@EnableConfigServer` annotation.

```java
@SpringBootApplication
@EnableConfigServer
public class ConfigServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConfigServerApplication.class, args);
    }
}
```

The configuration files can be stored on the local file system or in git.

To use the local file system, add the `native` profile and specify the file locations in `application.yml`.

```yaml
spring:
  profiles:
    active: native
  cloud:
    config:
      server:
        native:
          searchLocations: src/main/resources/config,src/main/resources/config/{application}
```

To use git, set the location of the repo in `application.yaml`

```yaml
spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/marklishman/spring-cloud-config-server-ref
          searchPaths: src/main/resources/config,src/main/resources/config/*
```

* Files in resources (`config` scope)
*  `/green/dev`, `green-dev.yml`, `green-dev.properties` and  `green-dev.json`

### Secure Properties

Add the encryption key to `bootstrap.yml`

```yaml
encrypt:
  key: my_secret_symmetric_encryption_key
``` 

Prefix the property with `{ciper}` 

      secret: '{cipher}075912c9...3811'
      
This will expose the `/encrypt` and `/decrypt` endpoints (make sure to secure these)     
      
The default is encrypt and decrypt the values on the server.
This means that the values are secured at rest but not over the wire.

To encrypt & decrypt on the client, include this property on the server in `bootstrap.yml`

```yaml
spring:
  cloud:
    config:
      server:
        encrypt:
          enabled: false
``` 

and add the encryption key to each client `bootstrap.yml`

```yaml
encrypt:
  key: my_secret_symmetric_encryption_key
``` 

This will encrypt the property over the wire.

# Some URLs to try

* `localhost:8888/green/default`
* `localhost:8888/green/dev`
* `localhost:8888/green-dev.yaml`
* `localhost:8888/green-dev.properties`
* `localhost:8888/green-dev.json`

# Encryption

See `bootstrap.yml` file for encryption key and client side decryption config.

Exposes (POST)

* `localhost:8888/encrypt`
* `localhost:8888/decrypt`