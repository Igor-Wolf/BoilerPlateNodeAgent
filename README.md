# E-commerce REST API Boilerplate

Este projeto é um boilerplate de API RESTful desenvolvido em Node.js com TypeScript, seguindo rigorosamente uma arquitetura em camadas para promover manutenibilidade, testabilidade e clareza de responsabilidades.

## 📚 Arquitetura em Camadas (Obrigatório)

A estrutura segue o fluxo de chamadas:
**`Route` $\rightarrow$ `Controller` $\rightarrow$ `Service` $\rightarrow$ `Repository` $\rightarrow$ `Data`**

Cada camada tem uma responsabilidade estrita, prevenindo a mistura de lógica de negócios, requisições HTTP e acesso a dados.

### 📖 Responsabilidades Detalhadas

| Camada | Responsabilidade Principal | O que deve fazer | O que **NÃO** deve fazer |
| :--- | :--- | :--- | :--- |
| **`Router`** (`src/routes.ts`) | Mapeamento de URIs e verbos HTTP. | Chamar métodos de um `Controller`. | Lógica de negócio, chamar repositórios. |
| **`Controller`** (`src/controllers/`) | Lidar com o ciclo de vida da requisição HTTP. | Receber `req`, validar a **entrada** HTTP (params, body, query), chamar um `Service` e retornar a resposta HTTP. | Acessar o serviço de domínio (Service) deve ser o próximo passo. Não deve acessar o Repository diretamente. |
| **`Service`** (`src/services/`) | Contém a **Lógica de Negócio** da aplicação. | Processar dados, aplicar regras de fundo, orquestrar chamadas a vários Repositórios. | Não deve saber nada sobre HTTP. Não deve retornar status HTTP. |
| **`Repository`** (`src/repositories/`) | Abstração de persistência de dados. | Fazer I/O: consultar DB (mock, má memória, arquivo). | Não deve conter regras de negócio (deve ser simples CRUD). Não deve usar `req`/`res`. |
| **`Model`** (`src/models/`) | Definição de tipos e estruturas de dados. | Exportar interfaces TypeScript. | Nenhuma lógica de negócio. |
| **`Utils`** (`src/utils/http-helper.ts`) | Centralização das respostas HTTP. | Fornecer helpers tipados (`ok`, `created`, `badRequest`, etc.). | Não deve conter regras de negócio. |

---

## ⚙️ Configuração e Execução

### 🚀 Iniciar o Projeto

1.  **Instalar Dependências:**
    ```bash
    npm install
    ```
2.  **Executar em Desenvolvimento (Watch Mode):**
    O `tsx watch` iniciará o servidor e fará o *reloading* automático ao salvar arquivos.
    ```bash
    npm run start:watch
    ```
3.  **Construir (Build) para Produção:**
    Compila o código TS para JavaScript em `/dist`.
    ```bash
    npm run dist
    ```
4.  **Iniciar em Produção (Executando o JS compilado):**
    ```bash
    npm run start:dist
    ```

### 🛠️ Manutenção e Implementação de Funcionalidade

Ao adicionar qualquer nova funcionalidade (`CRUD` em outra entidade), **SEMPRE** siga esta ordem:

1.  **Model:** Criar o arquivo de tipos (`*.model.ts`).
2.  **Repository:** Criar a camada de acesso a dados (`*.repository.ts`).
3.  **Service:** Implementar as regras de negócio (`*.service.ts`).
4.  **Controller:** Implementar o handler HTTP, chamando o Service (`*.controller.ts`).
5.  **Route:** Registrar a rota principal (`src/routes/*.ts` ou `src/routes.ts`).

### ✨ Melhores Práticas

*   **Tipagem:** Usar TypeScript com `strict: true` em todos os lugares. Evitar `any` é mandatório.
*   **Padrões de Nomes:**
    *   **Arquivos/Módulos:** `kebab-case` (`user-service.ts`).
    *   **Classes:** `PascalCase` (`UserService`).
    *   **Funções/Métodos:** `camelCase` (`getUserById()`).
*   **Status HTTP:** Utilize sempre os helpers em `src/utils/http-helper.ts` para padronizar a resposta (ex: 200, 201, 400, 404, 409, 500).
*   **Assincronicidade:** Usar `async/await` sempre que houver I/O de dados.

---

## 📜 Checklist de Boas Práticas (Regras de Ouro)

*   **✅ EXISTENTE > NOVO:** Priorize reutilizar código e arquitetura existente.
*   **✅ SIMPLES > COMPLEXO:** A solução mais simples que atende ao requisito é a correta. Não inventar complexidade (evitar DDD excessivo, CQRS, etc. sem necessidade).
*   **✅ REUTILIZAR > DUPLICAR:** Não duplicar código.

---
*Criado com foco em manutenibilidade e arquitetura limpa.*