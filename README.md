Aqui está a versão **refatorada do README** adaptada para uso com **Fastify** no lugar do Express e com **SQLite3** como banco de dados persistente, substituindo o array em memória. O conteúdo original foi preservado onde relevante, e modernizado para refletir a nova stack.

---

# 📦 Lista de Produtos - API RESTful com Fastify e SQLite3

Este projeto é uma aplicação completa de CRUD (Create, Read, Update, Delete) de produtos. Ele utiliza **Fastify** como servidor backend e **SQLite3** como banco de dados local, fornecendo uma API RESTful integrada a um frontend simples com HTML, CSS e JavaScript.

---

## 📋 Tabela de Conteúdos

* [📦 Lista de Produtos - API RESTful com Fastify e SQLite3](#-lista-de-produtos---api-restful-com-fastify-e-sqlite3)
* [📋 Tabela de Conteúdos](#-tabela-de-conteúdos)
* [🚀 Tecnologias Utilizadas](#-tecnologias-utilizadas)
* [⚙️ Configuração do Projeto](#️-configuração-do-projeto)
* [📁 Estrutura de Pastas](#-estrutura-de-pastas)
* [🖥️ Backend - API com Fastify e SQLite3](#️-backend---api-com-fastify-e-sqlite3)
* [🌐 Frontend - Interface Web](#-frontend---interface-web)
* [🧪 Testando o Projeto](#-testando-o-projeto)
* [📚 Endpoints da API](#-endpoints-da-api)
* [❗ Possíveis Problemas](#-possíveis-problemas)
* [👨‍💻 Contribuidores](#-contribuidores)
* [📄 Licença](#-licença)

---

## 🚀 Tecnologias Utilizadas

* [Fastify](https://www.fastify.io/)
* [SQLite3](https://www.sqlite.org/index.html)
* [Node.js](https://nodejs.org/)
* HTML, CSS, JavaScript (Vanilla)

---

## ⚙️ Configuração do Projeto

### 🔧 Instalação Backend

```bash
mkdir lista-de-produtos
cd lista-de-produtos
mkdir backend && cd backend
npm init -y

# Instalar dependências
npm install fastify fastify-cors fastify-sensible sqlite3
npm install -D nodemon
```

Crie o arquivo `index.js` e configure seu servidor Fastify e rotas da API.

Crie também o arquivo `db.js` para gerenciar a conexão SQLite3.

### 🌐 Instalação Frontend

No diretório `lista-de-produtos`, crie a pasta `frontend` com os arquivos:

* `index.html`
* `style.css`
* `script.js`

---

## 📁 Estrutura de Pastas

```
lista-de-produtos/
├── backend/
│   ├── db.js
│   ├── index.js
│   ├── package.json
│   └── ...
├── frontend/
│   ├── index.html
│   ├── style.css
│   └── script.js
```

---

## 🖥️ Backend - API com Fastify e SQLite3

### 🔌 Servidor Fastify (`index.js`)

Inclui endpoints REST para produtos:

```js
// index.js
const Fastify = require('fastify');
const cors = require('@fastify/cors');
const db = require('./db');

const fastify = Fastify({ logger: true });

fastify.register(cors);

// Listar todos os produtos
fastify.get('/produtos', async () => {
  return await db.all('SELECT * FROM produtos');
});

// Obter um produto
fastify.get('/produtos/:id', async (req, reply) => {
  const produto = await db.get('SELECT * FROM produtos WHERE id = ?', [req.params.id]);
  if (!produto) return reply.code(404).send({ message: 'Produto não encontrado' });
  return produto;
});

// Criar produto
fastify.post('/produtos', async (req, reply) => {
  const { nome, precoUnitario, quantidade, categoria, fabricante } = req.body;
  await db.run(
    `INSERT INTO produtos (nome, precoUnitario, quantidade, categoria, fabricante)
     VALUES (?, ?, ?, ?, ?)`,
    [nome, precoUnitario, quantidade, categoria, fabricante]
  );
  reply.code(201).send({ message: 'Produto criado com sucesso' });
});

// Atualizar produto
fastify.put('/produtos/:id', async (req, reply) => {
  const { nome, precoUnitario, quantidade, categoria, fabricante } = req.body;
  await db.run(
    `UPDATE produtos SET nome=?, precoUnitario=?, quantidade=?, categoria=?, fabricante=?
     WHERE id=?`,
    [nome, precoUnitario, quantidade, categoria, fabricante, req.params.id]
  );
  reply.send({ message: 'Produto atualizado com sucesso' });
});

// Deletar produto
fastify.delete('/produtos/:id', async (req, reply) => {
  await db.run('DELETE FROM produtos WHERE id = ?', [req.params.id]);
  reply.send({ message: 'Produto removido com sucesso' });
});

fastify.listen({ port: 3000 }, err => {
  if (err) throw err;
});
```

### 💾 Banco de Dados (`db.js`)

```js
// db.js
const sqlite3 = require('sqlite3');
const { open } = require('sqlite');

module.exports = (async () => {
  const db = await open({
    filename: './produtos.db',
    driver: sqlite3.Database
  });

  await db.exec(`
    CREATE TABLE IF NOT EXISTS produtos (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      nome TEXT,
      precoUnitario REAL,
      quantidade INTEGER,
      categoria TEXT,
      fabricante TEXT
    )
  `);

  return db;
})();
```

---

## 🌐 Frontend - Interface Web

O frontend permanece o mesmo, com integração via `fetch()` à nova API Fastify.

Acesse `frontend/index.html` no navegador e garanta que o backend esteja rodando para carregar os dados.

---

## 🧪 Testando o Projeto

1. Inicie o backend:

   ```bash
   npm run dev
   ```

2. Abra `frontend/index.html` no navegador.

3. Teste as funcionalidades: Adicionar, Visualizar, Editar e Deletar produtos.

---

## 📚 Endpoints da API

| Método | Rota            | Descrição                     |
| ------ | --------------- | ----------------------------- |
| GET    | `/produtos`     | Lista todos os produtos       |
| GET    | `/produtos/:id` | Obtém um produto específico   |
| POST   | `/produtos`     | Cria um novo produto          |
| PUT    | `/produtos/:id` | Atualiza um produto existente |
| DELETE | `/produtos/:id` | Remove um produto do banco    |

---

## ❗ Possíveis Problemas

* Certifique-se de que o backend está ativo antes de abrir o `index.html`.
* Verifique se a porta `3000` está disponível.
* Se estiver com problemas no banco, delete o arquivo `produtos.db` e reinicie o backend.

---

## 👨‍💻 Contribuidores

* **Fernando Zuchi**
  WhatsApp: (32) 99164-1182

---

## 📄 Licença

Este projeto está sob a licença MIT. Sinta-se livre para estudar, modificar e reutilizar este código para fins educacionais ou comerciais.

---

Se quiser que eu também converta os arquivos `index.js`, `db.js` e os scripts do frontend para trabalhar com Fastify e SQLite3, posso gerar para você! Deseja isso?
