# RentMate - Spring Cloud Config Server

This repository contains the **Config Server** for the RentMate microservices platform.  
It reads configuration files from the `rentmate-config-repo` and exposes them to all microservices dynamically.

---

## 🚀 Features

- Centralized configuration for all microservices  
- Environment-specific configs (`dev`, `prod`, etc.)  
- Automatic Git synchronization (clones config repo on startup)  
- Supports both **public** and **private** GitHub repositories  
- Works across all RentMate microservices (user-service, delivery-service, payment-service, etc.)

---

## 🏗️ Architecture

````

Microservice  →  Config Server  →  rentmate-config-repo (GitHub)

````

Every service loads configuration based on:

- `spring.application.name`
- active profile (e.g., `dev`, `prod`)
- Git branch (`master` by default)

---

## ⚙️ Configuration (application.yml)

The server configuration:

```yaml
server:
  port: 8888

spring:
  application:
    name: config-server

  cloud:
    config:
      server:
        git:
          uri: https://github.com/semi-conductors/rentmate-config-repo.git
          default-label: master
          clone-on-start: true

          search-paths:
            - '{application}'
            - 'services/{application}'

management:
  endpoints:
    web:
      exposure:
        include: health,info,refresh
  endpoint:
    health:
      show-details: always
````

---

## 📦 Running the Server

### 1️⃣ Using Maven

```
mvn spring-boot:run
```

### 2️⃣ Using JAR

```
java -jar config-server.jar
```

The server starts on:

```
http://localhost:8888
```

---

## 🔍 Testing the Config Server

### Fetch default profile

```
GET http://localhost:8888/user-service/default
```

### Fetch dev profile

```
GET http://localhost:8888/user-service/dev
```

Expected result: configuration JSON.

---

## 🔒 Using a Private GitHub Repo

Enable:

```yaml
spring.cloud.config.server.git.username: ${GIT_USERNAME}
spring.cloud.config.server.git.password: ${GIT_PASSWORD}
```

Use GitHub **Personal Access Token**.

---

## 🌱 Setting Up a Microservice to Use Config Server

Include a `bootstrap.yml`:

```yaml
spring:
  application:
    name: user-service

  cloud:
    config:
      uri: http://localhost:8888
      profile: dev
      label: master
```

---

## 🧪 Actuator Endpoints

Useful for debugging:

| Endpoint            | Description                    |
| ------------------- | ------------------------------ |
| `/actuator/health`  | Check if server is running     |
| `/actuator/info`    | Metadata                       |
| `/actuator/refresh` | Refresh config without restart |

---

## 📝 Notes

* Ensure `rentmate-config-repo` is publicly accessible or properly authenticated.
* Config Server auto-reloads the Git repository when restarted.

---
