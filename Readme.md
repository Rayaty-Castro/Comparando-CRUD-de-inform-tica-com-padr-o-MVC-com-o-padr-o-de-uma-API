# Comparativo Arquitetural: CRUD MVC vs. API RESTful

Este documento descreve as principais diferenças conceituais, estruturais e práticas entre a arquitetura **MVC Monolítica tradicional** (Server-Side Rendering) e uma **API RESTful desacoplada** (Client-Server).

---

## Sumário

1. [Diferenças Arquiteturais](#1-diferenças-arquiteturais)
2. [Responsabilidades de Cada Camada](#2-responsabilidades-de-cada-camada)
3. [Relações entre as Camadas](#3-relações-entre-as-camadas)
4. [Persistência em Banco de Dados](#4-persistência-em-banco-de-dados)
5. [Padrão Arquitetural Mais Utilizado](#5-padrão-arquitetural-mais-utilizado)
6. [Como Testar uma API](#6-como-testar-uma-api)
7. [Como Documentar uma API](#7-como-documentar-uma-api)
8. [Como Consumir uma API no Front-end](#8-como-consumir-uma-api-no-front-end)

---

## 1. Diferenças Arquiteturais

A diferença fundamental entre uma aplicação **MVC Monolítica** (SSR) e uma **API RESTful** está na **forma como a resposta é entregue ao cliente**: o MVC tradicional devolve páginas HTML renderizadas no servidor, enquanto a API devolve dados estruturados (geralmente JSON).

###  MVC Monolítico
* **Acoplamento:** Forte acoplamento entre a interface gráfica (Views) e a lógica do servidor.
* **Renderização:** Processada no servidor via motores de template (Blade, Thymeleaf, JSP, EJS).
* **Comunicação:** Submissões de formulários e recarregamento de páginas via HTTP (GET/POST).

###  API RESTful (Arquitetura Desacoplada)
* **Desacoplamento:** Back-end e Front-end são projetos independentes (*Headless*).
* **Renderização:** O Front-end (React, Vue, Mobile) roda no dispositivo do cliente e constrói a interface com os dados recebidos.
* **Comunicação:** Troca de mensagens *stateless* utilizando verbos HTTP explícitos (`GET`, `POST`, `PUT`, `DELETE`, `PATCH`) e payload JSON.

| Característica | MVC Monolítico | API RESTful |
| :--- | :--- | :--- |
| **Resposta do Servidor** | HTML + CSS + JS | JSON / XML |
| **Estado (Statefulness)** | *Stateful* (Sessões no Servidor) | *Stateless* (Tokens JWT / OAuth2) |
| **Multiplataforma** | Restrito a navegadores web | Atende Web, Apps Mobile, IoT e serviços de terceiros |
| **Roteamento** | Gerenciado no servidor | Gerenciado pelo cliente (Client-Side Router) |

---

## 2. Responsabilidades de Cada Camada

###  MVC Monolítico
* **Model:** Representa a estrutura de dados, validações e regras do domínio.
* **View:** Camada de apresentação visual (HTML/CSS/JS).
* **Controller:** Processa as requisições, obtém dados do Model e retorna a View pronta para o navegador.

###  API RESTful
* **Controller / Endpoint Layer:** Trata requisições HTTP, valida entradas e formata as respostas.
* **Service Layer:** Contém a regra de negócio e inteligência da aplicação.
* **Data Access Layer / Repository (Model):** Interface de consulta e persistência no banco de dados.
* **DTO (Data Transfer Object):** Define os esquemas de entrada e saída de dados transmitidos na API.

---

## 3. Relações entre as Camadas

### Fluxo MVC Monolítico
$$\text{Navegador} \xrightarrow{\text{HTTP GET/POST}} \text{Controller} \leftrightarrow \text{Model} \rightarrow \text{Renderiza View (HTML)} \xrightarrow{\text{Resposta}} \text{Navegador}$$

### Fluxo API RESTful
$$\text{Front-end/App} \xrightarrow{\text{HTTP GET/POST/PUT/DELETE}} \text{Controller} \rightarrow \text{Service} \rightarrow \text{Repository/Model} \rightarrow \text{BD}$$
$$\text{BD} \rightarrow \text{Repository} \rightarrow \text{Service} \rightarrow \text{Controller (Converte DTO/JSON)} \xrightarrow{\text{JSON}} \text{Front-end/App}$$

---

## 4. Persistência em Banco de Dados

A persistência no banco de dados segue princípios idênticos nas duas abordagens:

* **ORMs (Mapeamento Objeto-Relacional):** Utilização de ferramentas como Entity Framework, JPA/Hibernate, Prisma, TypeORM ou Eloquent.
* **Mapeamento CRUD:**
  * **Create:** `INSERT INTO` / `.save()`
  * **Read:** `SELECT` / `.find()`
  * **Update:** `UPDATE` / `.update()`
  * **Delete:** `DELETE` / `.delete()`

*Diferença prática:* No MVC a operação é iniciada por submissão de um formulário HTML; na API, a operação é acionada por um payload JSON direcionado a um endpoint especifico.

---

## 5. Padrão Arquitetural Mais Utilizado

O modelo **API RESTful + Single Page Application (SPA) / App Mobile** é o padrão predominante no mercado moderno.

* **Vantagens:** Reutilização de código do back-end para múltiplos clientes (Web, iOS, Android, Smart TVs).
* **Evoluções recentes:** Frameworks modernos como **Next.js**, **Nuxt.js** e **Remix** adotam abordagens híbridas (SSR para páginas iniciais/SEO + consumo de APIs REST/GraphQL para interações).

---

## 6. Como Testar uma API

###  Testes Manuais de Endpoints
* **Clientes HTTP:** Postman, Insomnia, Bruno, VS Code Thunder Client.
* **CLI:** Utilitário `cURL` para execuções em linha de comando.

###  Testes Automatizados
* **Testes de Unidade e Integração:** Verificação de rotas, controllers e regras da camada Service com simulação (mock) do banco de dados.
* **Ferramentas por Ecossistema:**
  * **JavaScript / Node.js:** Jest, Supertest, Vitest.
  * **Java:** JUnit, REST Assured.
  * **Python:** PyTest, HTTPX.
  * **C#:** xUnit, Moq.

---

## 7. Como Documentar uma API

* **OpenAPI / Swagger:** Padrão da indústria. Gera documentação interativa baseada em anotações no código ou arquivos YAML/JSON. Permite testar endpoints via navegador.
* **Postman Collections:** Coleções exportáveis contendo exemplos de requisições, respostas e variáveis de ambiente.
* **Redoc:** Gera documentações estáticas modernas a partir de especificações OpenAPI.

---

## 8. Como Consumir uma API no Front-end

As aplicações front-end consomem APIs via requisições HTTP assíncronas (AJAX/Fetch).

### Exemplo em JavaScript (Fetch API)

```javascript (essa parte foi feita com ia)
// 1. Buscar dados (GET)
async function buscarProdutos() {
  try {
    const response = await fetch('https://api.exemplo.com/v1/produtos', {
      method: 'GET',
      headers: {
        'Authorization': 'Bearer SEU_TOKEN_JWT',
        'Content-Type': 'application/json'
      }
    });

    if (!response.ok) throw new Error(`Status: ${response.status}`);

    const produtos = await response.json();
    console.log('Produtos carregados:', produtos);
  } catch (erro) {
    console.error('Erro ao buscar produtos:', erro);
  }
}

// 2. Criar novo registro (POST)
async function criarProduto(novoProduto) {
  const response = await fetch('https://api.exemplo.com/v1/produtos', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(novoProduto)
  });

  return await response.json();
}
```

### Principais Bibliotecas e Gerenciadores:
* **Axios:** Cliente HTTP baseado em Promises com recursos de interceptores e tratamento simplificado de erros.
* **TanStack Query (React Query) / SWR:** Ferramentas para gerenciamento de estado assíncrono, cache e revalidação no front-end.
