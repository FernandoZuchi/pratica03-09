# Prática da semana - API Rest, CRUD, Server com Node JS

Criar um objeto produto. Produto deve ter um fabricante representado por um elemento fabricante. Utilize detalhes como nome, endereço, etc. como elementos filhos de um elemento fabricante. Cada produto tem os elementos: nome do produto, preço unitário, quantidade e categoria. Insira 3 produtos neste objeto.

Utilize o método GET para através de um enpoint retornar todos os produtos deste objeto.

## Configuração do Ambiente

Criar a pasta lista-de-produtos e inicializar o Node.js

```prompt
mkdir lista-de-produtos // Cria a pasta
cd lista-de-produtos // Entra na pasta
npm init -y // Estrutura básica servidor node.js
npm install express // Instala a dependência express
```

## Desenvolvimento do Backend (Node.js e Express)

- Criar arquivo 'index.js' para configurar o servidor:

```javascript
const express = require('express');
const cors = require('cors');
const app = express();

app.use(cors());

// Array de objetos produtos - Banco de dados falso
const produtos = [
    {
        nome: "Teclado Gamer",
        precoUnitario: 500,
        quantidade: 50,
        categoria: "Periféricos",
        fabricante: {
            nome: "GamerPro",
            endereco: "Rua das Pedras, 321, São Paulo, SP",
            telefone: "(11) 98765-4321"
        }
    },
    {
        nome: "Teclado Mecânico",
        precoUnitario: 350,
        quantidade: 100,
        categoria: "Periféricos",
        fabricante: {
            nome: "TecLab",
            endereco: "Av. dos Jumentos, 456, Rio de Janeiro, RJ",
            telefone: "(21) 91234-5678"
        }
    },
    {
        nome: "Monitor 4K",
        precoUnitario: 2500,
        quantidade: 30,
        categoria: "Eletrônicos",
        fabricante: {
            nome: "UltraVision",
            endereco: "Rua das Gramas, 789, Curitiba, PR",
            telefone: "(41) 99876-5432"
        }
    }
];

// Endpoint para obter produtos
app.get('/produtos', (req, res) => {
    res.status(200).json(produtos);
});

const porta = 3000;
app.listen(porta, () => {
    console.log(`Servidor rodando na porta ${porta}`);
});
```

- Executar o servidor

```prompt
node index.js
```

## Desenvolvimento do Backend (Node.js e Express)

- Criar o arquivo `index.html`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Lista de Produtos</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <h1>Lista de Produtos</h1>
    <div id="produtosContainer" class="produtos-container"></div>

    <script src="script.js"></script>
</body>
</html>
```

- Criar o arquivo `style.css`:

```css
body {
    font-family: Arial, sans-serif;
    background-color: #f5f5f5;
    color: #333;
    margin: 0;
    padding: 20px;
    display: flex;
    flex-direction: column;
    align-items: center;
}

h1 {
    color: #007BFF;
    margin-bottom: 20px;
}

.produtos-container {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
    justify-content: center;
}

.produto-card {
    background-color: #ffffff;
    padding: 15px;
    border-radius: 8px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    width: 300px;
}

.produto-card h2 {
    margin-top: 0;
    color: #007BFF;
}

.produto-card p {
    margin: 5px 0;
}

.produto-card .fabricante {
    font-style: italic;
    color: #555;
}

```

- Criar o arquivo `script.js`:

```javascript
function consumirAPI() {
    fetch('http://localhost:3000/produtos')
    .then(response => response.json())
    .then(produtos => {
        const produtosContainer = document.getElementById('produtosContainer');
        produtosContainer.innerHTML = '';

        produtos.forEach(produto => {
            const produtoCard = document.createElement('div');
            produtoCard.classList.add('produto-card');

            produtoCard.innerHTML = `
                <h2>${produto.nome}</h2>
                <p><strong>Preço:</strong> R$ ${produto.precoUnitario.toFixed(2)}</p>
                <p><strong>Quantidade:</strong> ${produto.quantidade}</p>
                <p><strong>Categoria:</strong> ${produto.categoria}</p>
                <p class="fabricante"><strong>Fabricante:</strong> ${produto.fabricante.nome}, ${produto.fabricante.endereco}</p>
            `;

            produtosContainer.appendChild(produtoCard);
        });
    })
    .catch(error => console.log('Erro ao carregar produtos: ', error));
}

window.onload = consumirAPI;
```

Agora, temos nosso frontend integrado à nossa API Rest no servidor backend, explorando o método GET dessa API conseguimos obter os produtos cadastrados no banco de dados simulado.

## **Criando um CRUD - Explorando todos métodos da API Rest**

Adiante, vamos expandir a aplicação anterior adicionando os demais métodos REST para completar a API:

- GET: Obter todos os produtos ou um produto específico.
- POST: Adicionar um novo produto.
- PUT: Atualizar um produto existente.
- DELETE: Remover um produto.
  
**Instalar Dependências**

```prompt
npm install cors body-parser nodemon
```

Descrição das dependências que usamos até aqui:

- express: Framework web para Node.js.
- cors: Middleware para habilitar CORS (Cross-Origin Resource Sharing).
- body-parser: Middleware para parsear o corpo das requisições.
- nodemon: Ferramenta para reiniciar automaticamente o servidor quando alterações são feitas no código.

Estrutura do projeto até aqui

```
lista-de-produtos/
├── backend/
│   └── index.js
├── frontend/
│   ├── index.html
│   ├── style.css
│   └── script.js
├── package.json
└── package-lock.json
```

## **Implementando o Servidor Backend**

No arquivo `backend/index.js`, faça as seguintes alterações:

```
const express = require('express');
const cors = require('cors');
const bodyParser = require('body-parser');
const app = express();
const PORT = 3000;

// Middleware
app.use(cors());
app.use(bodyParser.json());

// Dados iniciais (simulando um banco de dados em memória)
let produtos = [
  {
    id: 1,
    nome: "Teclado Gamer",
    precoUnitario: 500,
    quantidade: 50,
    categoria: "Periféricos",
    fabricante: {
      nome: "GamerPro",
      endereco: "Rua das Pedras, 321, São Paulo, SP",
      telefone: "(11) 98765-4321"
    }
  },
  {
    id: 2,
    nome: "Teclado Mecânico",
    precoUnitario: 350,
    quantidade: 100,
    categoria: "Periféricos",
    fabricante: {
      nome: "TecLab",
      endereco: "Av. dos Jumentos, 456, Rio de Janeiro, RJ",
      telefone: "(21) 91234-5678"
    }
  },
  {
    id: 3,
    nome: "Monitor 4K",
    precoUnitario: 2500,
    quantidade: 30,
    categoria: "Eletrônicos",
    fabricante: {
      nome: "UltraVision",
      endereco: "Rua das Gramas, 789, Curitiba, PR",
      telefone: "(41) 99876-5432"
    }
  }
];

// Iniciar o servidor
app.listen(PORT, () => {
  console.log(`Servidor rodando na porta ${PORT}`);
});
```

Explicação:

- Express: Configuramos o servidor utilizando o Express. (Dependência já existente no projeto)
- CORS: Habilitamos o CORS para permitir requisições do frontend. (Nova dependência adicionada)
- Body-Parser: Permite que o servidor interprete o corpo das requisições em JSON. (Nova dependência adicionada)
- Dados Iniciais: Definimos um array de produtos simulando um banco de dados. (Banco de dados simulado que já existia)


## Implementação dos Endpoints REST

- ** GET /produtos - Obter todos os produtos**

```javascript
// Obter todos os produtos
app.get('/produtos', (req, res) => {
  res.status(200).json(produtos);
});
```

** PAREI AQUI! **


## Conclusão
-- DESCONSIDERAR
Parabéns! Você agora tem uma calculadora de Bitcoin que não só busca o preço atual de Bitcoin, mas também calcula e exibe o valor em USD das posses de Bitcoin que o usuário insere. Além disso, você aprendeu a usar o localStorage para armazenar dados localmente e a manipular o DOM com JavaScript puro.

Agora é hora de adicionar funcionalidades ou personalizações ao projeto! Por exemplo, você pode adicionar um seletor de moeda ou exibir gráficos de preços históricos. As possibilidades são infinitas!
