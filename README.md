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
