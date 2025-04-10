Sistema Educativo - Microservicios

Este proyecto implementa un sistema educativo distribuido utilizando una arquitectura de microservicios basada en Spring Boot, Spring Cloud y Docker. Incluye servicios para gestión de usuarios, asignaturas, matrículas, configuración centralizada, descubrimiento de servicios y seguridad con JWT.

Tecnologías utilizadas

Java 17

Spring Boot

Spring Cloud (Eureka, Config Server)

Spring Security + JWT

Spring Boot Actuator

Docker & Docker Compose

H2 como base de datos en memoria

Postman para pruebas

📂 Microservicios incluidos

Servicio               Puerto             Descripción

usuarios-servicio       8001              Gestión de usuarios y login JWT

asignaturas-servicio    8002              Gestión de asignaturas

matriculas-servicio     8003              Gestión de matrículas

config-server           8888             Configuración centralizada

eureka-server           8761             Descubrimiento de servicios

Ejecución con Docker Compose

1. Construir los JAR de cada microservicio:

   cd usuarios-servicio/usuarios-servicio
mvn clean package -DskipTests
cd ../../

cd asignaturas-servicio/asignaturas-servicio
mvn clean package -DskipTests
cd ../../

cd matriculas-servicio/matriculas-servicio
mvn clean package -DskipTests
cd ../../

2. Mover los JAR al nivel esperado por los Dockerfile:

copy usuarios-servicio\usuarios-servicio\target\usuarios-servicio-0.0.1-SNAPSHOT.jar usuarios-servicio\target\
copy asignaturas-servicio\asignaturas-servicio\target\asignaturas-servicio-0.0.1-SNAPSHOT.jar asignaturas-servicio\target\
copy matriculas-servicio\matriculas-servicio\target\matriculas-servicio-0.0.1-SNAPSHOT.jar matriculas-servicio\target\

3. Levantar todo el entorno:

   docker compose up --build
Evidencia de funcionamiento

Eureka funcionando:

http://localhost:8761

Todos los microservicios aparecen como "UP":

usuarios-servicio

asignaturas-servicio

matriculas-servicio

config-server

Endpoints Actuator accesibles

Por ejemplo:

http://localhost:8001/actuator/health

http://localhost:8002/actuator/health

http://localhost:8003/actuator/health

Seguridad con JWT

Login con: POST /usuarios/login

Protección de endpoints con token Bearer

Dashboard de monitoreo (básico en HTML + JS):

Muestra estado en vivo de los servicios usando Actuator.

#Dockerización y orquestación

Para poder desplegar todo el sistema de forma conjunta y replicable, se realizó lo siguiente:

- Crear un archivo Dockerfile por microservicio:

Cada microservicio tiene su propio archivo Dockerfile que contiene instrucciones para construir la imagen Docker. Ejemplo:
FROM eclipse-temurin:17-jdk
WORKDIR /app
COPY target/usuarios-servicio-0.0.1-SNAPSHOT.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]

Crear un docker-compose.yml para levantar todo el entorno:
Este archivo define los servicios, los puertos, las dependencias entre ellos, y las rutas para construir las imágenes:
services:
  eureka-server:
    build: ./eureka-server
    ports:
      - "8761:8761"

  config-server:
    build: ./config-server
    ports:
      - "8888:8888"
    depends_on:
      - eureka-server

  usuarios-servicio:
    build: ./usuarios-servicio
    ports:
      - "8001:8001"
    depends_on:
      - config-server
      - eureka-server

  asignaturas-servicio:
    build: ./asignaturas-servicio
    ports:
      - "8002:8002"
    depends_on:
      - config-server
      - eureka-server

  matriculas-servicio:
    build: ./matriculas-servicio
    ports:
      - "8003:8003"
    depends_on:
      - config-server
      - eureka-server
#Ejecución con Docker Compose
1. Construir los JAR de cada microservicio:
cd usuarios-servicio/usuarios-servicio
mvn clean package -DskipTests
cd ../../

cd asignaturas-servicio/asignaturas-servicio
mvn clean package -DskipTests
cd ../../

cd matriculas-servicio/matriculas-servicio
mvn clean package -DskipTests
cd ../../
2. Mover los JAR al nivel esperado por los Dockerfile:
copy usuarios-servicio\usuarios-servicio\target\usuarios-servicio-0.0.1-SNAPSHOT.jar usuarios-servicio\target\
copy asignaturas-servicio\asignaturas-servicio\target\asignaturas-servicio-0.0.1-SNAPSHOT.jar asignaturas-servicio\target\
3. Levantar todo el entorno:
docker compose up --build
copy matriculas-servicio\matriculas-servicio\target\matriculas-servicio-0.0.1-SNAPSHOT.jar matriculas-servicio\target\

1. Construir los JAR de cada microservicio:

Desarrollado por: Anderson Mendieta
Parcial de microservicios 
