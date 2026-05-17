# README.md — Projeto de Automação de Testes com Cypress + BrowserStack Demo

![Cypress](https://img.shields.io/badge/Cypress-E2E-green)
![JavaScript](https://img.shields.io/badge/JavaScript-ES6-yellow)
![Node](https://img.shields.io/badge/Node.js-20.x-brightgreen)
![License](https://img.shields.io/badge/license-MIT-blue)

---

# Cypress BrowserStack Demo

Projeto de automação de testes End-to-End (E2E) utilizando Cypress na aplicação pública da BrowserStack.

Aplicação alvo:

* [https://bstackdemo.com/](https://bstackdemo.com/)

O projeto foi desenvolvido seguindo padrões profissionais de QA Automation, incluindo:

* Page Object Model (POM)
* Fixtures
* Custom Commands
* Relatórios automatizados
* Estrutura escalável
* Integração CI/CD
* Boas práticas de automação

---

# Objetivo do Projeto

Este projeto tem como finalidade demonstrar:

* Conhecimento em automação E2E
* Estruturação profissional com Cypress
* Organização de testes escaláveis
* Separação de responsabilidades
* Implementação de fluxo de compra completo
* Criação de testes reutilizáveis
* Integração com pipelines CI/CD

Também pode ser utilizado como:

* Projeto de portfólio QA
* Base para projetos enterprise
* Template inicial para automação
* Laboratório de estudos Cypress

---

# Tecnologias Utilizadas

| Tecnologia        | Finalidade             |
| ----------------- | ---------------------- |
| Cypress           | Automação E2E          |
| JavaScript ES6+   | Linguagem principal    |
| Node.js           | Ambiente de execução   |
| Mochawesome       | Relatórios de execução |
| GitHub Actions    | CI/CD                  |
| BrowserStack Demo | Aplicação de testes    |

---

# Cenários Automatizados

## Login

* Login válido
* Validação de autenticação
* Persistência de sessão

## Carrinho

* Adição de produto
* Validação de item no carrinho
* Quantidade de produtos

## Checkout

* Fluxo completo de compra
* Preenchimento de formulário
* Finalização de pedido

---

# Estrutura do Projeto

## Objetivo

Criar um projeto E2E utilizando:

* Cypress
* JavaScript
* Page Object Model (POM)
* Fixtures
* Custom Commands
* Relatórios
* Integração com BrowserStack Demo

Aplicação alvo:

* [https://bstackdemo.com/](https://bstackdemo.com/)

---

# Estrutura do Projeto

```bash
cypress-browserstack-demo/
│
├── cypress/
│   ├── e2e/
│   │   ├── login.cy.js
│   │   ├── purchase.cy.js
│   │   └── cart.cy.js
│   │
│   ├── fixtures/
│   │   └── users.json
│   │
│   ├── pages/
│   │   ├── LoginPage.js
│   │   ├── ProductsPage.js
│   │   ├── CartPage.js
│   │   └── CheckoutPage.js
│   │
│   ├── support/
│   │   ├── commands.js
│   │   └── e2e.js
│   │
│   └── reports/
│
├── package.json
├── cypress.config.js
└── README.md
```

---

# 1. Inicialização do Projeto

## Criar projeto

```bash
mkdir cypress-browserstack-demo
cd cypress-browserstack-demo
npm init -y
```

## Instalar dependências

```bash
npm install cypress --save-dev
npm install mochawesome --save-dev
```

---

# 2. Configuração do Cypress

## Arquivo: `cypress.config.js`

```javascript
const { defineConfig } = require('cypress')

module.exports = defineConfig({
  e2e: {
    baseUrl: 'https://bstackdemo.com',
    viewportWidth: 1440,
    viewportHeight: 900,
    video: true,
    screenshotOnRunFailure: true,

    setupNodeEvents(on, config) {
      return config
    }
  },

  reporter: 'mochawesome',
  reporterOptions: {
    reportDir: 'cypress/reports',
    overwrite: false,
    html: true,
    json: true
  }
})
```

---

# 3. Fixtures

## Arquivo: `cypress/fixtures/users.json`

```json
{
  "standard_user": {
    "username": "demouser",
    "password": "testingisfun99"
  }
}
```

---

# 4. Custom Commands

## Arquivo: `cypress/support/commands.js`

```javascript
Cypress.Commands.add('login', (username, password) => {
  cy.get('#signin').click()

  cy.get('#username').click()
  cy.contains(username).click()

  cy.get('#password').click()
  cy.contains(password).click()

  cy.get('#login-btn').click()
})
```

---

# 5. Page Objects

## Arquivo: `cypress/pages/LoginPage.js`

```javascript
class LoginPage {
  open() {
    cy.visit('/')
  }

  login(username, password) {
    cy.login(username, password)
  }
}

export default new LoginPage()
```

---

## Arquivo: `cypress/pages/ProductsPage.js`

```javascript
class ProductsPage {
  addFirstProductToCart() {
    cy.get('.shelf-item__buy-btn').first().click()
  }

  openCart() {
    cy.get('.float-cart__button').click()
  }

  validateProductAdded() {
    cy.get('.bag__quantity').should('contain', '1')
  }
}

export default new ProductsPage()
```

---

## Arquivo: `cypress/pages/CartPage.js`

```javascript
class CartPage {
  validateCartItem() {
    cy.get('.float-cart__content').should('be.visible')
  }

  proceedCheckout() {
    cy.contains('Checkout').click()
  }
}

export default new CartPage()
```

---

## Arquivo: `cypress/pages/CheckoutPage.js`

```javascript
class CheckoutPage {
  fillCheckoutData() {
    cy.get('#firstNameInput').type('Joao')
    cy.get('#lastNameInput').type('Silva')
    cy.get('#addressLine1Input').type('Rua Cypress')
    cy.get('#provinceInput').type('SP')
    cy.get('#postCodeInput').type('16000-000')
  }

  submitOrder() {
    cy.get('#checkout-shipping-continue').click()
  }
}

export default new CheckoutPage()
```

---

# 6. Testes E2E

## Arquivo: `cypress/e2e/login.cy.js`

```javascript
import LoginPage from '../pages/LoginPage'

describe('Login Tests', () => {
  beforeEach(() => {
    LoginPage.open()
  })

  it('Should login successfully', () => {
    cy.fixture('users').then((users) => {
      LoginPage.login(
        users.standard_user.username,
        users.standard_user.password
      )
    })

    cy.contains('demouser').should('be.visible')
  })
})
```

---

## Arquivo: `cypress/e2e/cart.cy.js`

```javascript
import LoginPage from '../pages/LoginPage'
import ProductsPage from '../pages/ProductsPage'
import CartPage from '../pages/CartPage'

describe('Cart Tests', () => {
  beforeEach(() => {
    cy.fixture('users').then((users) => {
      cy.visit('/')

      LoginPage.login(
        users.standard_user.username,
        users.standard_user.password
      )
    })
  })

  it('Should add product to cart', () => {
    ProductsPage.addFirstProductToCart()

    ProductsPage.validateProductAdded()

    ProductsPage.openCart()

    CartPage.validateCartItem()
  })
})
```

---

## Arquivo: `cypress/e2e/purchase.cy.js`

```javascript
import LoginPage from '../pages/LoginPage'
import ProductsPage from '../pages/ProductsPage'
import CartPage from '../pages/CartPage'
import CheckoutPage from '../pages/CheckoutPage'

describe('Purchase Flow', () => {
  beforeEach(() => {
    cy.fixture('users').then((users) => {
      cy.visit('/')

      LoginPage.login(
        users.standard_user.username,
        users.standard_user.password
      )
    })
  })

  it('Should complete purchase successfully', () => {
    ProductsPage.addFirstProductToCart()

    ProductsPage.openCart()

    CartPage.proceedCheckout()

    CheckoutPage.fillCheckoutData()

    CheckoutPage.submitOrder()

    cy.contains('Thanks for your order').should('be.visible')
  })
})
```

---

# 7. Executando os Testes

## Abrir interface gráfica

```bash
npx cypress open
```

## Executar em modo headless

```bash
npx cypress run
```

---

# 8. Gerar Relatórios

Após execução:

```bash
npx cypress run
```

Relatórios serão gerados em:

```bash
cypress/reports
```

---

# 9. Melhorias Recomendadas

## Integração CI/CD

Exemplos:

* GitHub Actions
* GitLab CI
* Jenkins
* Azure DevOps

---

## Recursos Avançados

### API Testing

```javascript
cy.request('GET', '/api/products')
```

### Retry Strategy

```javascript
retries: {
  runMode: 2,
  openMode: 0
}
```

### Dados Dinâmicos

Utilizar:

* faker-js
* factories
* fixtures dinâmicas

---

# 10. Exemplo de GitHub Actions

## Arquivo: `.github/workflows/cypress.yml`

```yaml
name: Cypress Tests

on:
  push:
    branches:
      - main

jobs:
  cypress-run:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Install dependencies
        run: npm install

      - name: Run Cypress
        run: npx cypress run
```

---

# 11. Boas Práticas

## Estrutura

* Separar Page Objects
* Evitar seletores frágeis
* Centralizar comandos reutilizáveis
* Utilizar fixtures para dados de teste

## Estabilidade

* Evitar `cy.wait()` fixo
* Preferir asserts explícitos
* Trabalhar com intercepts

## Manutenibilidade

* Nomear testes claramente
* Isolar responsabilidades
* Manter testes independentes

---

# 12. Próximos Passos

Você pode evoluir esse projeto para:

* Cypress + TypeScript
* Arquitetura com App Actions
* Integração BrowserStack Automate
* Execução paralela
* Visual Testing
* Performance Testing
* BDD com Cucumber
* Allure Reports
* Testes Mobile Responsivos
* Pipeline enterprise-ready

---

# 13. Comandos Úteis

## Executar teste específico

```bash
npx cypress run --spec cypress/e2e/login.cy.js
```

## Executar em Chrome

```bash
npx cypress run --browser chrome
```

## Limpar cache

```bash
npx cypress cache clear
```

---

# Resultado Esperado

O projeto cobre:

* Login
* Adição ao carrinho
* Fluxo de checkout
* Estrutura escalável
* Padrões profissionais de automação
* Base sólida para portfólio QA Automation
