# Prática da semana - API Rest, CRUD, Server com Node JS

Esse roteiro instrui à criação de uma API com Node JS no servidor backend, e integração desse backend com frontend, através de um CRUD de produtos. Usaremos um array de objetos produtos no servidor backend para simular um banco de dados e sua interação com o cliente(usuário)/frontend.

Vamos começar!

## Configuração do Ambiente

Criar a pasta lista-de-produtos, backend e inicializar o Node.js 

```prompt
mkdir backend // Cria a pasta
cd backend // Entra na pasta
npm init -y // Estrutura básica servidor node.js
npm install express // Instala a dependência express
npm install cors // Instala a dependência cors (Interação multiplataformas)
```

## Desenvolvimento do Backend (Node.js e Express)

- Criar arquivo 'index.js' na pasta backend para configurar o servidor:

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
        fabricante: "Toyota"
    },
    {
        nome: "Teclado Mecânico",
        precoUnitario: 350,
        quantidade: 100,
        categoria: "Periféricos",
        fabricante: "Nokia"
    },
    {
        nome: "Monitor 4K",
        precoUnitario: 2500,
        quantidade: 30,
        categoria: "Eletrônicos",
        fabricante: "Dell"
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
cd backend
node index.js
```

Acesse `localhost:3000/produtos` para acessar o endpoint com uma resposta dos produtos no formato JSON, que configuramos no código.

Ja temos nosso primeiro endpoint criado e rodando no servidor.

## Desenvolvimento do FrontEnd (HTML, CSS & JS)

Cria uma pasta frontend, essa pasta deve ser irmã da pasta backend.

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
/* Estiliza o corpo da página */
body {
    font-family: Arial, sans-serif; /* Define a fonte padrão */
    background-color: #f5f5f5; /* Cor de fundo cinza claro */
    color: #333; /* Cor do texto cinza escuro */
    margin: 0; /* Remove as margens padrão */
    padding: 20px; /* Adiciona um espaço interno */
    display: flex; /* Utiliza flexbox para layout */
    flex-direction: column; /* Organiza os elementos em uma coluna */
    align-items: center; /* Centraliza os itens horizontalmente */
}

/* Estiliza os títulos de nível 1 */
h1 {
    color: #007BFF; /* Cor azul */
    margin-bottom: 20px; /* Adiciona uma margem inferior */
}

/* Estiliza o container de produtos */
.produtos-container {
    display: flex; /* Utiliza flexbox para layout */
    flex-wrap: wrap; /* Permite que os elementos envolvam para a próxima linha */
    gap: 20px; /* Adiciona um espaço entre os elementos */
    justify-content: center; /* Centraliza os elementos horizontalmente */
}

/* Estiliza cada card de produto */
.produto-card {
    background-color: #ffffff; /* Cor de fundo branca */
    padding: 15px; /* Adiciona um espaço interno */
    border-radius: 8px; /* Arredonda os cantos */
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1); /* Adiciona uma sombra */
    width: 300px; /* Define a largura do card */
}

/* Estiliza os títulos de nível 2 dentro dos cards */
.produto-card h2 {
    margin-top: 0; /* Remove a margem superior */
    color: #007BFF; /* Cor azul */
}

/* Estiliza os parágrafos dentro dos cards */
.produto-card p {
    margin: 5px 0; /* Define as margens superior e inferior */
}

/* Estiliza o texto do fabricante */
.produto-card .fabricante {
    font-style: italic; /* Define a fonte como itálico */
    color: #555; /* Cor cinza */
}

```

- Criar o arquivo `script.js`:

```javascript
function consumirAPI() {
  // Faz uma requisição para a API local em busca dos dados dos produtos
  fetch('http://localhost:3000/produtos')
    // Converte a resposta da API para formato JSON
    .then(response => response.json())
    // Processa os dados dos produtos recebidos
    .then(produtos => {
      // Seleciona o elemento HTML onde os cards dos produtos serão inseridos
      const produtosContainer = document.getElementById('produtosContainer');
      // Limpa o conteúdo anterior do container, caso exista
      produtosContainer.innerHTML = '';

      // Itera sobre cada produto da lista recebida
      produtos.forEach(produto => {
        // Cria um novo elemento div para representar o card do produto
        const produtoCard = document.createElement('div');
        // Adiciona a classe 'produto-card' para aplicar os estilos definidos no CSS
        produtoCard.classList.add('produto-card');

        // Constrói o conteúdo HTML do card, inserindo os dados do produto
        produtoCard.innerHTML = `
          <h2>${produto.nome}</h2>
          <p><strong>Preço:</strong> R$ ${produto.precoUnitario.toFixed(2)}</p>
          <p><strong>Quantidade:</strong> ${produto.quantidade}</p>
          <p><strong>Categoria:</strong> ${produto.categoria}</p>
          <p class="fabricante"><strong>Fabricante:</strong> ${produto.fabricante}</p>
        `;

        // Adiciona o card recém-criado ao container de produtos
        produtosContainer.appendChild(produtoCard);
      });
    })
    // Captura e loga qualquer erro que ocorra durante o processo
    .catch(error => console.log('Erro ao carregar produtos: ', error));
}

// Executa a função consumirAPI quando a página é carregada
window.onload = consumirAPI;
```

Agora, temos nosso frontend integrado à nossa API Rest no servidor backend, explorando o método GET dessa API conseguimos obter os produtos cadastrados no banco de dados simulado.

Para testar isso funcionando, você deve estar com o servidor `index.js` no backend rodando, e abrir a página `index.html` do frontend no navegador.

## **Criando um CRUD - Explorando todos métodos da API Rest**

Adiante, vamos expandir a aplicação anterior adicionando os demais métodos REST para completar a API:

- GET: Obter todos os produtos ou um produto específico.
- POST: Adicionar um novo produto.
- PUT: Atualizar um produto existente.
- DELETE: Remover um produto.
  
**Instalar Dependências**

No terminal da pasta `backend`

```prompt
npm install body-parser nodemon
```

Descrição das dependências que usamos até aqui:

- express: Framework web para Node.js.
- cors: Middleware para habilitar CORS (Cross-Origin Resource Sharing).
- **body-parser**: Middleware para converter o corpo das requisições.
- **nodemon**: Ferramenta para reiniciar automaticamente o servidor quando alterações são feitas no código.

Estrutura do projeto até aqui

```
lista-de-produtos/
├── backend/
│   └── index.js
    └── node_modules
    ├── package.json
    └── package-lock.json
├── frontend/
│   ├── index.html
│   ├── style.css
│   └── script.js
```

## **Implementando o Servidor Backend**

No arquivo do servidor `backend/index.js`, faça as seguintes alterações:

```javascript
const express = require('express');
const cors = require('cors');
const bodyParser = require('body-parser');
const app = express();

// Middleware
app.use(cors());
app.use(bodyParser.json());

// Dados iniciais (simulando um banco de dados em memória)
const produtos = [
  {
    id: 1, // Adicionar um id único em cada produto
    nome: "Teclado Gamer",
    precoUnitario: 500,
    quantidade: 50,
    categoria: "Periféricos",
    fabricante: "Toyota"
  },
  {
    id: 2, // Adicionar um id único em cada produto
    nome: "Teclado Mecânico",
    precoUnitario: 350,
    quantidade: 100,
    categoria: "Periféricos",
    fabricante: "Nokia"
  },
  {
    id: 3, // Adicionar um id único em cada produto
    nome: "Monitor 4K",
    precoUnitario: 2500,
    quantidade: 30,
    categoria: "Eletrônicos",
    fabricante: "Dell"
  }
];

// Endpoint para obter produtos
app.get('/produtos', (req, res) => {
    res.status(200).json(produtos);
})


// Inicia o servidor
const porta = 3000;
app.listen(porta, () => {
    console.log(`Servidor rodando na porta localhost:${porta}`);
})
```

Explicação:

- Express: Configuramos o servidor utilizando o Express. (Dependência já existente no projeto)
- CORS: Habilitamos o CORS para permitir requisições do frontend. (Dependência já existente no projeto sendo utilizada)
- Body-Parser: Permite que o servidor interprete o corpo das requisições em JSON. (Nova dependência adicionada)
- Dados Iniciais: Definimos um array de produtos simulando um banco de dados. (Banco de dados simulado que já existia, porém adicionamos um ID único neles)
- Nodemon: Dependência nova adicionada que facilita o desenvolvimento com node, uma vez que não precisamos reiniciar o servidor sempre que fizermos uma alteração

Para rodarmos o servidor utilizando nodemon, altere os scripts do `package.json`:

```json
{
  "name": "pratica03-09-fernandozuchi",
  "version": "1.0.0",
  "description": "fala dev",
  "main": "index.js",
  "scripts": {
    "start": "nodemon index.js",// Adicione essa linha
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "body-parser": "^1.20.2",
    "cors": "^2.8.5",
    "express": "^4.19.2",
    "nodemon": "^3.1.4"
  }
}
```

Agora para rodar o servidor apenas uma vez, vá no terminal na pasta do backen e rode o comando:

```prompt
npm start
```

## Implementação dos Endpoints REST no servidor `Backend/index.js`

- **GET /produtos - Obter todos os produtos**

```javascript
// Obter todos os produtos
app.get('/produtos', (req, res) => {
  res.status(200).json(produtos);
});
```

- **GET /produtos - Obter um produto específico**

```javascript
// Obter um produto específico pelo ID
app.get('/produtos/:id', (req, res) => {
    const produtoId = parseInt(req.params.id);
    const produto = produtos.find(p => p.id === produtoId);

    if (produto) {
        res.status(200).json(produto);
    } else {
        res.status(404).json({ message: 'Produto não encontrado' });
    }
});
```

Obs: Teste esse endpoint no navegador: `localhost:3000/produtos/2`



- **POST /produtos - Adicionar um novo produto**
  
```javascript
// Adicionar um novo produto
app.post('/produtos', (req, res) => {
    // Recebe os dados do novo produto do corpo da requisição (req.body)
    const novoProduto = req.body;
    // Gerar um ID único para o novo produto
    novoProduto.id = produtos.length + 1; 
    produtos.push(novoProduto);
    // Retorna o novo produto com status 201 (Criado)
    res.status(201).json(novoProduto);
});
```

**OBS:** Para adicionar um novo produto, precisamos de uma requisição no lado do cliente (FrontEnd). Então, vamos tratar isso futuramente, mas nosso endpoint para criação de produtos já está criado. Será na rota `localhost:3000/produtos`

- **PUT /produtos - Atualizar um produto existente**

```javascript
// Atualizar um produto existente pelo ID
app.put('/produtos/:id', (req, res) => {
    const produtoId = parseInt(req.params.id);
    const produtoIndex = produtos.findIndex(p => p.id === produtoId);

    // Verifica se encontrou o produto (índice diferente de -1)
    if (produtoIndex !== -1) {
        // Atualiza o produto existente, com novos dados recebidos no corpo da requisição
        produtos[produtoIndex] = { ...produtos[produtoIndex], ...req.body };
        /* 
            Explicando melhor o spread(...produtos, ...req.body)

            1 - produtos[produtoIndex]: Aqui, você está acessando um item específico na lista produtos usando o índice produtoIndex.,
            2 - { ...produtos[produtoIndex] }: Isso cria um novo objeto que contém todas as propriedades do objeto encontrado em produtos[produtoIndex]. Ou seja, você está "desestruturando" o objeto existente e copiando suas propriedades para um novo objeto.
            3 - { ...req.body }: Isso cria um novo objeto que contém todas as propriedades do objeto req.body. req.body é geralmente utilizado em uma aplicação web para representar os dados enviados no corpo de uma requisição HTTP (por exemplo, quando um formulário é enviado).
            4 - Combinação dos Objetos: A sintaxe { ...obj1, ...obj2 } cria um novo objeto que combina as propriedades de obj1 e obj2. Se houver propriedades com o mesmo nome em ambos os objetos, o valor de obj2 sobrescreverá o valor de obj1.
        */
        // Retorna o produto atualizado com status 200 (sucesso)
        res.status(200).json(produtos[produtoIndex]);
    } else {
        res.status(404).json({ message: 'Produto não encontrado' });
    }
});
```

**OBS:** Para atualizar um produto existente, precisamos de uma requisição no lado do cliente (FrontEnd). Então, vamos tratar isso futuramente, mas nosso endpoint para atualizar um produto específico já está criado. Será na rota `localhost:3000/produtos/:id`

- **DELETE /produtos - Remover um produto**

```javascript
// Remover um produto pelo ID
app.delete('/produtos/:id', (req, res) => {
    const produtoId = parseInt(req.params.id);
    const produtoIndex = produtos.findIndex(p => p.id === produtoId);

    if (produtoIndex !== -1) {
        produtos.splice(produtoIndex, 1);
        res.status(200).json({ message: 'Produto removido com sucesso' });
    } else {
        res.status(404).json({ message: 'Produto não encontrado' });
    }
});
```

**OBS:** Para deletar um produto existente, precisamos de uma requisição no lado do cliente (FrontEnd). Então, vamos tratar isso futuramente, mas nosso endpoint para deletar um produto específico já está criado. Será na rota `localhost:3000/produtos/:id`


## Integrando funcionalidades da API de CRUD no FrontEnd

Você pode atualizar o frontend `index.html`, `style.css` & `script.js` para permitir a adição, atualização, e remoção de produtos.

## Adição de produtos - Método POST(API Rest) / Método Create(CRUD)

Adicione no HTML um formulário para criar novos produtos:

```html
<!-- Formulário para adicionar produtos -->
    <form id="produtoForm" class="produto-form">
        <div class="form-group">
            <label for="nome">Nome do Produto:</label>
            <input type="text" id="nome" name="nome" required>
            <label for="precoUnitario">Preço:</label>
            <input type="number" id="precoUnitario" name="precoUnitario" required>
            <label for="quantidade">Quantidade:</label>
            <input type="number" id="quantidade" name="quantidade" required>
            <label for="categoria">Categoria:</label>
            <input type="text" id="categoria" name="categoria" required>
            <label for="fabricante">Nome do Fabricante:</label>
            <input type="text" id="fabricante" name="fabricante" required>
            <button type="submit">Adicionar Produto</button>
        </div>
    </form>
```

Vamnos aproveitar e adicionar a estilização para esse formulário:

```css
/* Estiliza o formulário de produto */
.produto-form {
    background-color: #ffffff; /* Cor de fundo branca */
    padding: 20px; /* Adiciona um espaço interno */
    border-radius: 8px; /* Arredonda os cantos */
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1); /* Adiciona uma sombra */
    margin-bottom: 20px; /* Adiciona uma margem inferior */
    width: 100%; /* Define a largura como 100% */
    max-width: 400px; /* Limita a largura máxima a 400 pixels */
}

/* Estiliza os grupos de campos do formulário */
.form-group {
    margin-bottom: 15px; /* Adiciona uma margem inferior */
    display: flex; /* Utiliza flexbox para layout */
    align-items: center; /* Alinha os itens no centro verticalmente */
    justify-content: center; /* Alinha os itens no centro horizontalmente */
    flex-direction: column; /* Organiza os elementos em uma coluna */
    gap: 10px; /* Adiciona um espaço entre os elementos */
}

/* Estiliza os rótulos dos campos */
.form-group label {
    display: block; /* Define como bloco */
    margin-bottom: 5px; /* Adiciona uma margem inferior */
    color: #007BFF; /* Cor azul */
    font-weight: bold; /* Define o estilo da fonte como negrito */
}

/* Estiliza os campos de entrada de texto */
.form-group input {
    width: 100%; /* Define a largura como 100% */
    padding: 8px; /* Adiciona um espaço interno */
    border: 1px solid #ccc; /* Adiciona uma borda cinza */
    border-radius: 4px; /* Arredonda os cantos */
}

/* Estiliza o botão de envio */
button[type="submit"] {
    background-color: #007BFF; /* Cor de fundo azul */
    color: #fff; /* Cor do texto branca */
    padding: 10px 15px; /* Adiciona um espaço interno */
    border: none; /* Remove a borda */
    border-radius: 4px; /* Arredonda os cantos */
    cursor: pointer; /* Define o cursor como ponteiro */
    font-size: 16px; /* Define o tamanho da fonte */
}

/* Estiliza o botão de envio ao passar o mouse */
button[type="submit"]:hover {
    background-color: #0056b3; /* Altera a cor de fundo para um tom mais escuro */
}
```

E no `script.js`, adicione a lógica para enviar o produto à API:

```javascript
// Adiciona um event listener ao formulário para capturar o evento de envio
document.getElementById('produtoForm').addEventListener('submit', (event) => {
  // Impede o comportamento padrão do formulário, que é recarregar a página
  event.preventDefault();

  // Cria um objeto com os dados do novo produto, coletados dos campos do formulário
  const novoProduto = {
    nome: document.getElementById('nome').value,
    precoUnitario: parseFloat(document.getElementById('precoUnitario').value),
    quantidade: parseInt(document.getElementById('quantidade').value),
    categoria: document.getElementById('categoria').value,
    fabricante: document.getElementById('fabricante').value
  };

  // Faz uma requisição POST para a API para adicionar o novo produto
  fetch('http://localhost:3000/produtos', {
    method: 'POST', // Indica que é uma requisição POST
    headers: { 'Content-Type' : 'application/json' }, // Define o tipo de conteúdo como JSON
    body: JSON.stringify(novoProduto) // Envia os dados do novo produto no corpo da requisição em formato JSON
  })
    // Processa a resposta da API
    .then(response => response.json())
    // Executa uma ação após a resposta ser recebida com sucesso
    .then(data => {
      // Exibe um alerta indicando que o produto foi adicionado com sucesso
      alert('Produto adicionado!');
      // Atualiza a lista de produtos na página, chamando a função consumirAPI()
      consumirAPI();
    })
    // Captura e loga qualquer erro que possa ocorrer durante o processo
    .catch(error => console.log('Erro ao adicionar produto', error));
});
```

## Atualização de produtos - Método PUT(API Rest) / Método Update(CRUD)
## Remoção de produtos - Método DELETE(API Rest) / Método Delete(CRUD)

Vamos agora, utilizar os Endpoints da API que estão criados, porém não integrados no FrontEnd. Vamos fazer essa integração agora criando botões de **DELETAR** e **ATUALIZAR** produtos existentes.

Para isso, vamos começar alterando o `produtoCard.innerHTML` localizado dentro do `produtos.forEach` do nosso arquivo `script.js`:

```javascript
produtoCard.innerHTML = `
                <h2>${produto.nome}</h2>
                <p><strong>Preço:</strong> R$ ${produto.precoUnitario}</p>
                <p><strong>Quantidade:</strong> ${produto.quantidade}</p>
                <p><strong>Categoria:</strong> ${produto.categoria}</p>
                <p><strong>Fabricante:</strong> ${produto.fabricante}</p>
                <button class="editar-btn" data-id="${produto.id}">Editar</button>
                <button class="deletar-btn" data-id="${produto.id}">Deletar</button>
            `
```

No arquivo `script.js`, vamos modificar a função consumirAPI para incluir os botões "Editar" e "Deletar" dentro de cada cartão de produto. Adicionaremos também as funções necessárias para manipular esses eventos de clique nos botões:

Dentro do segundo `.then()` presente na função `consumirAPI()`, após a criação(appendChild) do produtoContainer, adicione o seguinte: 

```javascript
 // Adicionar event listeners para os botões "Editar" e "Deletar"
        document.querySelectorAll('.editar-btn').forEach(button => {
            button.addEventListener('click', editarProduto);
        });

        document.querySelectorAll('.deletar-btn').forEach(button => {
            button.addEventListener('click', deletarProduto);
        });
```

Após isso podemos criar a função `editarProduto()` responsável por conectar-nos ao endpoint da API Rest criado para essa funcionalidade do CRUD.

```javascript
function editarProduto(event) {
    const produtoId = event.target.getAttribute('data-id');
    fetch(`http://localhost:3000/produtos/${produtoId}`)
        .then(response => response.json())
        .then(produto => {
            // Preencher o formulário com os dados do produto para edição
            document.getElementById('nome').value = produto.nome;
            document.getElementById('precoUnitario').value = produto.precoUnitario;
            document.getElementById('quantidade').value = produto.quantidade;
            document.getElementById('categoria').value = produto.categoria;
            document.getElementById('fabricante').value = produto.fabricante;
            
            // Remover o evento de submit atual e adicionar um novo para atualizar o produto
            const form = document.getElementById('produtoForm');
            form.removeEventListener('submit', adicionarProduto);
            form.addEventListener('submit', (event) => {
                event.preventDefault();
                atualizarProduto(produtoId);
            });
        })
        .catch(error => console.log('Erro ao carregar produto para edição: ', error));
}
```

Agora criaremos a função que envia o produto atualizado para o servidor backend, usando novamente um endpoint da API que criamos:

```javascript
function atualizarProduto(produtoId) {
    const produtoAtualizado = {
        nome: document.getElementById('nome').value,
        precoUnitario: parseFloat(document.getElementById('precoUnitario').value),
        quantidade: parseInt(document.getElementById('quantidade').value),
        categoria: document.getElementById('categoria').value,
        fabricante: document.getElementById('fabricante').value
    };

    fetch(`http://localhost:3000/produtos/${produtoId}`, {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(produtoAtualizado)
    })
    .then(response => response.json())
    .then(data => {
        alert('Produto atualizado com sucesso!');
        consumirAPI(); // Atualiza a lista de produtos

        // Restaurar o formulário para adicionar novos produtos
        const form = document.getElementById('produtoForm');
        form.reset();
        form.removeEventListener('submit', atualizarProduto);
        form.addEventListener('submit', adicionarProduto);
    })
    .catch(error => console.log('Erro ao atualizar produto', error));
}
```

Após isso, podemos criar mais uma função para utilizar o endpoint criado para remover produtos do fake banco de dados.

```javascript
function deletarProduto(event) {
    const produtoId = event.target.getAttribute('data-id');

    fetch(`http://localhost:3000/produtos/${produtoId}`, {
        method: 'DELETE'
    })
    .then(response => response.json())
    .then(data => {
        alert('Produto removido com sucesso!');
        consumirAPI(); // Atualiza a lista de produtos
    })
    .catch(error => console.log('Erro ao deletar produto', error));
}
```

Para finalizar com chave de ouro, podemos reorganizar esse uso do endpoint POST para criar produtos, vamos transformá-lo também numa função:

```javascript
function adicionarProduto(event) {
    event.preventDefault();

    const novoProduto = {
        nome: document.getElementById('nome').value,
        precoUnitario: parseFloat(document.getElementById('precoUnitario').value),
        quantidade: parseInt(document.getElementById('quantidade').value),
        categoria: document.getElementById('categoria').value,
        fabricante: document.getElementById('fabricante').value
    };

    fetch('http://localhost:3000/produtos', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(novoProduto)
    })
    .then(response => response.json())
    .then(data => {
        alert('Produto adicionado!');
        consumirAPI(); // Atualiza a lista de produtos
    })
    .catch(error => console.log('Erro ao adicionar produto', error));
}

document.getElementById('produtoForm').addEventListener('submit', adicionarProduto);
window.onload = consumirAPI;
```


Perfeito! Estamos utilizando os métodos GET, POST, PUT, DELETE da API Rest & os métodos Create, Update, Read, Delete do CRUD no Backend e Frontend integrados uns aos outros!

## Conclusão

Agora, você tem uma aplicação completa que utiliza Node.js e Express para fornecer uma **API RESTful** com suporte completo para operações **CRUD**. O frontend interage com a API para permitir a **visualização**, **adição**, **atualização** e **remoção** de produtos. Essa prática oferece uma excelente introdução ao desenvolvimento de aplicações web com backend em Node.js.

Parábens por chegar até aqui! Tenho certeza que o esforço e dedicação de vocês nas aulas práticas fazem diferença em suas formações. Por isso venho buscando sempre criar práticas envolvendo conceitos o mais voltados pro mercado possível! Espero que tenham gostado dessa prática e a partir do momento que vocês entrar neste link, o repositório é de vocês. Recomendo fortemente que refaçam o que foi feito na prática seguindo o roteiro preparado pra vocês, qualquer dúvida falem comigo!

Fernando Zuchi - (32) 99164-1182

Até mais, dev's!
