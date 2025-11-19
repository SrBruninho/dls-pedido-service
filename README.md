# Distributed Logistics System - Pedido Service

Objetivo

Simular um sistema distribuído de logística com microservices, filas e streaming de eventos, focando em resiliência e integração de bancos diferentes.

Tecnologias

Java 21+, Spring Boot 3+

Kafka ou RabbitMQ

MongoDB + PostgreSQL

Docker Compose

Testcontainers para testes automatizados

Arquitetura e Microservices
1️⃣ Pedidos Service

Função: Cria pedidos de entrega e publica eventos de novos pedidos.
Endpoints REST:

POST /orders → cria um novo pedido (JSON com id, origem, destino, itens)

GET /orders/{id} → busca pedido por ID

Banco: PostgreSQL (para persistência de pedidos)
Eventos: Publica mensagem no Kafka/RabbitMQ para cálculo de prazo

```mermaid
flowchart LR

    %% Serviços
    subgraph PedidoService["pedido-service"]
        PS[pedido-service]
    end

    subgraph PrazoService["prazo-service"]
        PR[prazo-service]
    end

    subgraph NotificacaoService["notificacao-service"]
        NS[notificacao-service]
    end

    %% Cliente e provedor de email
    Cliente((Cliente))
    EmailProvider["Email Provider<br/>(SendGrid / SES / SMTP)"]

    %% Bancos
    PSDB[(pedido-db<br/>MongoDB)]
    PRDB[(prazo-db<br/>Postgres)]
    NSDB[(notificacao-db<br/>Postgres)]

    %% Fluxo dos serviços
    PS -->|novo pedido| PR
    PR -->|prazo calculado| NS

    %% Ligações com bancos
    PS --> PSDB
    PR --> PRDB
    NS --> NSDB

    %% Envio de email
    NS -->|enviar e-mail| EmailProvider --> Cliente

```
