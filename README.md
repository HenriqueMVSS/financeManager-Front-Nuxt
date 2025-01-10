# Finance Frontend

Este é o frontend do projeto de gerenciamento de receitas e despesas, desenvolvido com Nuxt 3.

## Pré-requisitos

Antes de começar, certifique-se de ter o seguinte instalado em sua máquina:

- [Node.js](https://nodejs.org/) (versão 18 ou superior)
- [npm](https://www.npmjs.com/) (geralmente instalado junto com o Node.js)
- [Vue CLI](https://cli.vuejs.org/) (opcional, mas recomendado)

## Instalação

1. Clone o repositório para sua máquina local:

    ```sh
    git clone https://github.com/HenriqueMVSS/financeManager-Front-Nuxt.git
    ```

2. Navegue até o diretório do projeto:

    ```sh
    cd financeManager-Front-Nuxt
    ```

3. Instale as dependências do projeto:

    ```sh
    npm install
    ```

## Execução

1. Inicie o servidor de desenvolvimento:

    ```sh
    npm run serve
    ```

2. Abra seu navegador e acesse `http://localhost:3000` para ver a aplicação em execução.

## Estrutura do Projeto

  - [pages](http://_vscodecontentref_/1): Páginas da aplicação.
- [public](http://_vscodecontentref_/2): Arquivos públicos.
  
## Utilização

### Login de Usuário
     
    Acesse http://localhost:3000/login.
    Preencha o formulário de login e envie.
   
### Dashboard
```
Após o login, você será redirecionado para o dashboard.
No dashboard, você pode visualizar todas as receitas (Incomes) e despesas (Expenses).
Registrar Receita ,Editar Receita, Registrar Despesa, Editar Despesa
```

## Comandos Úteis

- `npm run serve`: Inicia o servidor de desenvolvimento.
- `npm run build`: Compila o projeto para produção.
- `npm run lint`: Executa o linter para verificar problemas de estilo e sintaxe.


## Licença

Este projeto está licenciado sob a MIT License.