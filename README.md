# Sistema de Gestão de Empregados e Ponto Eletrônico
Projeto de Bloco Infnet

![Java](https://img.shields.io/badge/Java-22-orange)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.3.3-green)
![Docker](https://img.shields.io/badge/Docker-blue)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15-blue)
![RabbitMQ](https://img.shields.io/badge/RabbitMQ-3.13-orange)

## Descrição

Este projeto é uma aplicação baseada em microserviços desenvolvida para facilitar a gestão de empregados domésticos. O sistema integra diversas funcionalidades, como o registro de ponto eletrônico, banco de horas, validação de pontos pelo empregador e futuramente a geração de relatórios. A comunicação entre os microserviços é feita por **RabbitMQ** para alguns casos pontuais, e o FeignClient. Os dados são armazenados principalmente no **PostgreSQL**. Para os arquivos, no Mongo.

## Tecnologias Utilizadas

- **Java 22**
- **Spring Boot 3.3.3**
- **Spring Cloud Gateway**
- **Eureka Discovery Server**
- **RabbitMQ**
- **PostgreSQL**
- **Docker e Docker Compose**
- **Swagger** para documentação das APIs

## Como Rodar o Projeto

### Clonar o Repositório

```bash
git clone https://github.com/taohansens/infnet_gestaocerta.git
docker-compose up --build (em cada módulo)
Na utilização do docker, deve ser criada a network externa:
networks:
  gestaocerta-private:
    external: true
````
Isso irá subir todos os microserviços (Eureka, Gateway, Empregado, Ponto Eletrônico, Banco de Horas) o rabbit está no docker-compose da raiz do diretório.

Os endpoints estão disponíveis na pasta api-docs.

Modelo DockerCompose
```bash
services:
  eureka-server:
    image: taohans/gestaocerta:eureka-server
    container_name: eureka-server
    environment:
      - SERVER_PORT=8761
    ports:
      - "8761:8761"
    networks:
      - gestaocerta-private

  micro-arquivos:
    image: taohans/gestaocerta:micro-arquivos
    container_name: micro-arquivos
    depends_on:
      - mongodb
    environment:
      - APP_PROFILE=dev
      - SERVER_PORT=8082
      - GATEWAY_URL=http://localhost:8080/arq/
      - SPRING_DATA_MONGODB_URI=mongodb://root:root@mongodb:27017/db_arquivos?authSource=admin
    networks:
      - gestaocerta-private
      - int-arquivos-net

  mongodb:
    image: mongo:latest
    container_name: mongodb
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: root
    volumes:
      - mongo-data:/data/db
    networks:
      - int-arquivos-net

  micro-audit:
    image: taohans/gestaocerta:micro-audit
    container_name: micro-audit
    depends_on:
      - postgres-db-consumer
    environment:
      - APP_PROFILE=dev
      - SERVER_PORT=8085
      - DB_HOST=postgres-db-consumer
      - DB_PORT=5432
      - DB_NAME=microservice_db
      - DB_USER=postgres
      - DB_PASS=postgres
      - GATEWAY_URL=http://localhost:8080/audit/
      - RABBITMQ_HOST=rabbitmq-server
      - RABBITMQ_USER=rabbitmq
      - RABBITMQ_PASS=rabbitmq
    networks:
      - gestaocerta-private
      - int-consumer-net

  postgres-db-consumer:
    image: postgres:15
    container_name: postgres-db-consumer
    environment:
      POSTGRES_DB: microservice_db
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    networks:
      - int-consumer-net
    volumes:
      - postgres_data_consumer:/var/lib/postgresql/data

  micro-ponto:
    image: taohans/gestaocerta:micro-ponto
    container_name: micro-ponto
    depends_on:
      - postgres-ponto-db
    environment:
      - APP_PROFILE=dev
      - SERVER_PORT=8083
      - DB_HOST=postgres-ponto-db
      - DB_PORT=5432
      - DB_NAME=microservice_db
      - DB_USER=postgres
      - DB_PASS=postgres
      - GATEWAY_URL=http://localhost:8080/ponto/
      - RABBITMQ_HOST=rabbitmq-server
      - RABBITMQ_USER=rabbitmq
      - RABBITMQ_PASS=rabbitmq
    networks:
      - gestaocerta-private
      - int-ponto-net

  api-gateway:
    image: taohans/gestaocerta:api-gateway
    container_name: api-gateway
    environment:
      - SERVER_PORT=8080
    ports:
      - "8080:8080"
    networks:
      - gestaocerta-public
      - gestaocerta-private

  postgres-ponto-db:
    image: postgres:15
    container_name: postgres-ponto-db
    environment:
      POSTGRES_DB: microservice_db
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    networks:
      - int-ponto-net
    volumes:
      - postgres_data_ponto:/var/lib/postgresql/data

volumes:
  postgres_data_consumer:
    driver: local
  postgres_data_ponto:
    driver: local
  postgres_data_emp:
    driver: local
  mongo-data:
    driver: local


networks:
  gestaocerta-public:
    driver: bridge
  gestaocerta-private:
    external: true
  int-ponto-net:
  int-gestao-net:
  int-consumer-net:
  int-arquivos-net:
  int-empregados-net:
````
