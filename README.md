# 🛒 SGC - Sistema de Gestão Comercial (Loja de Informática)

Este repositório contém o ecossistema completo do **SGC (Sistema de Gestão Comercial)** desenvolvido para a disciplina de Desenvolvimento de Sistemas. A aplicação foi construída em uma arquitetura de alta coesão e baixo acoplamento, integrando uma API REST robusta em Spring Boot com um Frontend Desktop dinâmico desenvolvido em Java Swing.

---

## 📈 Histórico de Evolução do Projeto

### 📑 Entrega 1: Fundamentos e Base do Sistema
* **Modelagem Inicial:** Criação e estruturação das entidades fundamentais do negócio (Clientes e Produtos).
* **Camada de Persistência:** Implementação da comunicação inicial com o banco de dados relacional através do Spring Data JPA.
* **Operações Básicas (CRUD):** Disponibilização dos primeiros endpoints de cadastro, consulta, atualização e exclusão.

### 🔒 Entrega 2: Refatoração Arquitetural e Segurança
* **Arquitetura em Camadas:** Divisão estrita de responsabilidades:
  * **Entities:** Mapeamento das tabelas do banco de dados.
  * **Repositories:** Interfaces para abstração total da camada de persistência.
  * **Services:** Isolação e centralização de todas as regras de negócio.
  * **Controllers:** Exposição dos endpoints REST e manipulação de requisições HTTP.
* **Segurança com Spring Security & JWT:**
  * Integração nativa com a biblioteca **Java JWT (Auth0)**.
  * Implementação de rotas para cadastro e autenticação (Login) de usuários com senhas criptografadas por BCrypt.
  * Geração de tokens JWT seguros com expiração automática.
* **Tratamento Global de Exceções:** Centralização de falhas de validação através de `@RestControllerAdvice`, garantindo payloads JSON limpos.

### 🎨 Entrega 3: Interface Gráfica, Regras de Negócio e Relatórios (Fase Final)
* **Interface Gráfica Desktop (Java Swing):** Construção de telas completas para interação do usuário final consumindo a API localmente através do cliente HTTP nativo do Java:
  * `LoginView`: Tela de autenticação integrada com a segurança da API.
  * `PainelPrincipalView`: Dashboard gerencial com abas divididas para vendas e painel de controle.
* **Regras de Negócio e Controle de Estoque:** Mecanismo transacional integrado que valida a quantidade de produtos disponíveis em estoque antes de autorizar o fechamento de uma venda, efetuando a baixa automatizada na tabela de produtos após o sucesso.
* **Sistema de Relatórios de Faturamento:** Criação de consultas customizadas (`@Query`) e endpoints específicos para computar o faturamento total acumulado e exibi-lo de forma dinâmica em tempo real no dashboard gráfico.
* **Compatibilidade Universal:** Remoção e ajuste de dependências externas complexas para garantir que o projeto compile e execute de primeira em qualquer ambiente de correção.

---

## 🛠️ Tecnologias Utilizadas
* **Java 17**
* **Spring Boot 3.x**
* **Spring Data JPA & Hibernate**
* **Spring Security**
* **Java JWT (Auth0)**
* **MySQL** (Persistência de Dados)
* **Java Swing** (Interface Visual Desktop)
* **Maven** (Gerenciador de Dependências)

---

## 🗄️ Estrutura do Banco de Dados (Scripts SQL)

O Hibernate está configurado no modo `update`, o que significa que **todas as tabelas abaixo são geradas de forma automatizada na inicialização**, mas seguem rigorosamente a seguinte estrutura física relacional:

```sql
CREATE TABLE usuarios ( 
    id BIGINT AUTO_INCREMENT PRIMARY KEY, 
    username VARCHAR(50) UNIQUE NOT NULL, 
    senha VARCHAR(255) NOT NULL, 
    perfil ENUM('ADMIN', 'FUNCIONARIO') NOT NULL 
);

CREATE TABLE clientes ( 
    id BIGINT AUTO_INCREMENT PRIMARY KEY, 
    nome VARCHAR(100) NOT NULL, 
    cpf VARCHAR(14) UNIQUE NOT NULL, 
    email VARCHAR(100), 
    telefone VARCHAR(20), 
    endereco VARCHAR(255) 
);

CREATE TABLE produtos ( 
    id BIGINT AUTO_INCREMENT PRIMARY KEY, 
    nome VARCHAR(100) NOT NULL, 
    descricao TEXT, 
    preco DECIMAL(10,2) NOT NULL, 
    quantidade_estoque INT NOT NULL 
);

CREATE TABLE vendas ( 
    id BIGINT AUTO_INCREMENT PRIMARY KEY, 
    data_venda DATETIME DEFAULT CURRENT_TIMESTAMP, 
    cliente_id BIGINT, 
    usuario_id BIGINT, 
    valor_total DECIMAL(10,2), 
    FOREIGN KEY (cliente_id) REFERENCES clientes(id), 
    FOREIGN KEY (usuario_id) REFERENCES usuarios(id) 
);

CREATE TABLE itens_venda ( 
    id BIGINT AUTO_INCREMENT PRIMARY KEY, 
    venda_id BIGINT, 
    produto_id BIGINT, 
    quantidade INT, 
    preco_unitario DECIMAL(10,2), 
    FOREIGN KEY (venda_id) REFERENCES vendas(id), 
    FOREIGN KEY (produto_id) REFERENCES produtos(id)
);
