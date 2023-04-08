# SPRING APPLICATION

---

- [CONNECTION APP WITH DATABASE](#CONNECTION-APP-WITH-DATABASE)
  - [LOCAL APP     <----> LOCAL DB](#1-LOCAL-APP-------LOCAL-DB)
  - [LOCAL APP     <----> CONTAINER DB](#2-LOCAL--APP-------CONTAINER-POSTGRES-DB)
  - [CONTAINER APP <----> LOCAL DB](#3-DOCKER--APP-------LOCAL-DB)
  - [CONTAINER APP <----> CONTAINER DB](#4-DOCKER--APP-------DOCKER-CONTAINER)

---
<br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/>

## CONNECTION APP WITH DATABASE

1. LOCAL APP     <----> LOCAL DB     
2. LOCAL APP     <----> CONTAINER DB
3. CONTAINER APP <----> LOCAL DB
4. CONTAINER APP <----> CONTAINER DB 

---

## 1. LOCAL APP <-----> LOCAL DB

SET CORRECT URL TO YOUR DATA BASE IN APP PROPERTIES

EXAMPLE `url: "jdbc:postgresql://localhost:5432/demo_db"`



## 2. LOCAL  APP <-----> CONTAINER POSTGRES DB

SET PROPER URL TO CONTAINER DATABASE IN APP PROPERTIES  
`url: "jdbc:postgresql://localhost:5432/postgres"`
### BASH COMMAND
```
docker run --name postgres_db -p 5432:5432 -e POSTGRES_PASSWORD=postgres -e POSTGRES_USER=postgres -d postgres
```

## 3. DOCKER  APP <-----> LOCAL DB

SET CORRECT URL TO YOUR DATA BASE IN APP PROPERTIES

EXAMPLE `url: "jdbc:postgresql://localhost:5432/demo_db"`

### DOCKER FILE

```
FROM openjdk:11.0-jdk
COPY target/*.jar app.jar
CMD java -jar app.jar
```
### BASH COMMAND
```
docker build -f DockerFile -t app:v1 .
docker run --name spring_app --network=host app:v1
```


## 4. DOCKER  APP <-----> DOCKER CONTAINER

SET CORRECT URL TO YOUR DATA BASE IN APP PROPERTIES

`url:  "jdbc:postgresql://<COINTAINER_NAME_DB>/postgres"`

### DOCKER FILE
IN DOCKER FILE SET PROPER ENVIRONMENT VARIABLE FOR DB CONNECTION

`--spring.datasource.url=jdbc:postgresql://<COINTAINER_NAME_DB>/postgres`
```
FROM openjdk:11.0-jdk
COPY target/*.jar app.jar
EXPOSE 8080
CMD java -jar app.jar --spring.datasource.url=jdbc:postgresql://postgres_db/postgres
```
### BASH COMMAND
```
docker network create test-db_postgres
docker build -f DockerFile -t app:v1 .
docker run --name postgres_db -p 5432:5432 --network test-db_postgres -e POSTGRES_PASSWORD=postgres -e POSTGRES_USER=postgres -d postgres
docker run --name spring_app  -p 8080:8080 --network test-db_postgres app:v1
```

