BOILERPLATE — CONTEXTO DO PROJETO
OBJETIVO

Criar uma API REST simples usando:

    Node.js
    TypeScript
    Express

Arquitetura obrigatória:

Route
  ↓
Controller
  ↓
Service
  ↓
Repository

Use esta arquitetura para novas funcionalidades.

O projeto deve ser simples.

Não criar complexidade sem necessidade.
1. STACK

    Node.js
    TypeScript
    Express
    tsx
    tsup

Configuração TypeScript:

target: ES6
module: CommonJS
strict: true
esModuleInterop: true

Variáveis de ambiente:

--env-file=.env

Não usar dotenv.
2. ESTRUTURA OBRIGATÓRIA

Criar:

src/
├── server.ts
├── app.ts
├── routes.ts
├── controllers/
├── services/
├── repositories/
├── models/
└── utils/
    └── http-helper.ts

3. RESPONSABILIDADE DOS ARQUIVOS
server.ts

Somente inicia o servidor.

app.listen(...)

Não colocar:

    rotas;
    controllers;
    services;
    repositories;
    regras de negócio.

app.ts

Configura o Express.

Responsabilidades:

    criar express();
    express.json();
    middlewares globais;
    importar routes;
    registrar routes.

Exemplo:

const app = express()

app.use(express.json())
app.use(routes)

export default app

routes.ts

Registra todas as rotas.

Exemplo:

router.get('/name', nameController)

Não colocar regra de negócio aqui.

Não acessar repository aqui.
controllers/

Recebem requisições HTTP.

Responsabilidades:

    req;
    res;
    body;
    params;
    query;
    validação da entrada HTTP;
    chamar service;
    retornar resposta HTTP.

Fluxo:

req
 ↓
Controller
 ↓
Service
 ↓
Controller
 ↓
res

Controller NÃO deve acessar Repository diretamente.
services/

Contêm regras de negócio.

Responsabilidades:

    regras da aplicação;
    processamento;
    validações de negócio;
    chamar Repository.

Service NÃO deve usar:

req
res

Service NÃO deve retornar resposta HTTP.

Exemplo:

const result = await repository.execute(data)

return result

repositories/

Acessam os dados.

Responsabilidades:

    banco;
    mock;
    memória;
    arquivos;
    API externa quando usada como fonte de dados.

Exemplos:

findById()
findByName()
create()
update()
delete()

Repository NÃO deve:

    usar req;
    usar res;
    retornar status HTTP;
    conter regras de negócio.

models/

Contêm os tipos da aplicação.

Exemplo:

export interface Name {
  id: string
  name: string
}

Tipos compartilhados devem ficar em models/.
utils/http-helper.ts

Centraliza respostas HTTP.

Criar helpers para:

ok
created
badRequest
notFound
serverError

Exemplo:

export const ok = (data: any) => ({
  statusCode: 200,
  body: data
})

Não usar any no código final se houver tipo adequado.
4. FLUXO OBRIGATÓRIO

Uma requisição deve seguir:

HTTP
 ↓
Route
 ↓
Controller
 ↓
Service
 ↓
Repository
 ↓
Data

Nunca fazer:

Controller → Repository

Nunca fazer:

Route → Repository

Nunca fazer:

Route → Service

A rota chama o Controller.

O Controller chama o Service.

O Service chama o Repository.
5. CRIAÇÃO DE UMA FUNCIONALIDADE

Quando criar uma nova funcionalidade, seguir esta ordem:

1. Model
2. Repository
3. Service
4. Controller
5. Route

Nem sempre todas as etapas são necessárias.
Exemplo

Para criar:

GET /name

criar:

src/models/name-model.ts
src/repositories/name-repository.ts
src/services/name-service.ts
src/controllers/name-controller.ts

Depois registrar em:

src/routes.ts

Fluxo:

GET /name
    ↓
nameController
    ↓
nameService
    ↓
nameRepository

6. PADRÃO DE NOMES

Arquivos:

kebab-case

Exemplos:

name-controller.ts
name-service.ts
name-repository.ts
name-model.ts
http-helper.ts

Classes:

PascalCase

Exemplo:

class NameService {}

Funções:

camelCase

Exemplo:

getName()
createName()

Interfaces e Types:

PascalCase

Exemplo:

interface Name {}
type CreateNameInput = {}

7. TYPESCRIPT

Usar TypeScript com strict: true.

Não usar:

any

sem necessidade.

Preferir:

string
number
boolean
unknown
interface
type

Exemplo:

interface CreateNameInput {
  name: string
}

Não usar as sem necessidade.

Não desabilitar:

strict

8. HTTP

Usar os seguintes status:

200 = sucesso
201 = criado
204 = sucesso sem conteúdo
400 = entrada inválida
404 = não encontrado
409 = conflito
500 = erro interno

Usar http-helper.ts.

Não escrever respostas HTTP diferentes em cada controller.
9. VALIDAÇÃO

Existem dois tipos.
Controller

Validação da requisição.

Exemplo:

body não existe
campo obrigatório não existe
param inválido

Service

Validação da regra de negócio.

Exemplo:

nome já existe
usuário não pode realizar operação
recurso não pode ser alterado

Regra:

HTTP → Controller
NEGÓCIO → Service
DADOS → Repository

10. TRATAMENTO DE ERROS

Não retornar informações internas.

Não expor:

stack trace
senha
credenciais
dados internos do banco

Usar o padrão de erro existente.

Não criar outro sistema de erro se já existir um.
11. ASYNC

Operações assíncronas devem usar async/await.

Correto:

const user = await repository.findById(id)

Evitar:

const user = repository.findById(id)

quando for necessário o resultado.
12. DEPENDÊNCIAS

Não instalar bibliotecas sem necessidade.

Antes de instalar uma biblioteca:

    verificar se Node.js resolve;
    verificar se já existe biblioteca instalada;
    verificar se realmente é necessária.

Não usar:

dotenv

porque o projeto usa:

--env-file=.env

13. NÃO FAZER

Não:

    criar novas camadas;
    criar novos frameworks;
    criar DI;
    criar Factory sem necessidade;
    criar Strategy sem necessidade;
    criar CQRS;
    criar Event Bus;
    criar DDD complexo;
    criar abstrações genéricas;
    criar arquivos common.ts;
    criar arquivos helpers.ts genéricos;
    duplicar código existente;
    alterar arquitetura;
    instalar dependências sem necessidade;
    refatorar código não relacionado à tarefa.

Preferir sempre a solução mais simples.
14. CONFIGURAÇÃO
package.json

Scripts obrigatórios:

{
  "scripts": {
    "dist": "tsup src/server.ts --out-dir dist",
    "start": "tsx --env-file=.env src/server.ts",
    "start:watch": "tsx watch --env-file=.env src/server.ts",
    "start:dist": "npm run dist && node dist/server.js"
  }
}

Dependências de desenvolvimento:

@types/express
tsup
tsx
typescript

15. tsconfig.json

Usar:

{
  "compilerOptions": {
    "target": "ES6",
    "module": "CommonJS",
    "outDir": "./dist",
    "strict": true,
    "esModuleInterop": true
  }
}

16. CHECKLIST DE CRIAÇÃO

Ao criar o boilerplate:

[ ] package.json
[ ] tsconfig.json
[ ] src/server.ts
[ ] src/app.ts
[ ] src/routes.ts
[ ] src/controllers/
[ ] src/services/
[ ] src/repositories/
[ ] src/models/
[ ] src/utils/http-helper.ts

Depois verificar:

[ ] npm install
[ ] npm run start:watch
[ ] API inicia sem erro
[ ] TypeScript compila
[ ] rota funciona

17. CHECKLIST DE NOVA FUNCIONALIDADE

Antes de finalizar:

[ ] Model criado ou reutilizado
[ ] Repository criado ou reutilizado
[ ] Service criado ou reutilizado
[ ] Controller criado ou reutilizado
[ ] Route registrada
[ ] Tipos definidos
[ ] Sem any desnecessário
[ ] Sem regra de negócio no Controller
[ ] Sem HTTP no Service
[ ] Sem banco no Controller
[ ] Sem regra de negócio no Repository
[ ] Status HTTP correto
[ ] http-helper reutilizado
[ ] Código compila

18. REGRA FINAL

Sempre seguir:

EXISTENTE > NOVO

SIMPLES > COMPLEXO

REUTILIZAR > DUPLICAR

ARQUITETURA ATUAL > NOVA ARQUITETURA

ALTERAÇÃO MÍNIMA > REFACTOR GRANDE

Se uma solução simples resolver o problema, use a solução simples.

Não invente complexidade. :::

Essa versão é mais adequada para o seu caso porque o modelo local consegue usá-la quase como uma especificação operacional.

Eu faria ainda uma distinção importante: esse arquivo não precisa explicar o projeto inteiro. Ele precisa responder ao modelo:

    "Quando eu for gerar este boilerplate, exatamente o que devo criar?"

Por isso removi bastante texto conceitual e coloquei regras como:

Controller → Service
Service → Repository

e:

NÃO:
Controller → Repository

Isso tende a ser muito mais fácil para modelos menores seguirem.
Um detalhe que eu mudaria no seu http-helper.ts

No seu projeto original você mencionou helpers como ok, badRequest e serverError. Eu evitaria deixar o modelo inventar a assinatura deles. Definiria um padrão concreto no contexto, por exemplo:

type HttpResponse<T> = {
  statusCode: number
  body?: T
}

export const ok = <T>(data: T): HttpResponse<T> => ({
  statusCode: 200,
  body: data
})

export const created = <T>(data: T): HttpResponse<T> => ({
  statusCode: 201,
  body: data
})

export const badRequest = (message: string): HttpResponse<string> => ({
  statusCode: 400,
  body: message
})