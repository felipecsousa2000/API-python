# Documentação da API - E-commerce Whitelabel

## Informações Gerais

- **Base URL**: `http://localhost:3000`
- **Autenticação**: JWT Bearer Token
- **Content-Type**: `application/json`
- **Porta**: 3000

## Autenticação

A API utiliza JWT (JSON Web Token) para autenticação. Após o login bem-sucedido, você receberá um token que deve ser incluído no header `Authorization` de todas as requisições protegidas.

**Formato do Header:**
```
Authorization: Bearer <seu_token_jwt>
```

**Expiração do Token:** 24 horas

---

## Endpoints

### 1. Autenticação

#### 1.1. Login

Realiza o login de um usuário e retorna o token JWT.

**Endpoint:** `POST /auth/login`

**Headers:**
```
Content-Type: application/json
Host: localhost:3000
```

**Body:**
```json
{
  "username": "admin",
  "password": "admin123"
}
```

**Resposta de Sucesso (200):**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": 1,
    "username": "admin",
    "client_id": 1
  },
  "client": {
    "id": 1,
    "name": "E-commerce Dev",
    "domain": "localhost",
    "primary_color": "#2196F3",
    "secondary_color": "#FFFFFF",
    "logo_url": "https://via.placeholder.com/200?text=Dev"
  }
}
```

**Resposta de Erro (401):**
```json
{
  "statusCode": 401,
  "message": "Usuário ou senha inválidos",
  "error": "Unauthorized"
}
```

**Regras de Validação:**
- O domínio no header `Host` deve corresponder ao domínio do cliente do usuário
- Username e password são obrigatórios
- A senha é validada usando bcrypt

---

#### 1.2. Registro

Registra um novo usuário no sistema.

**Endpoint:** `POST /auth/register`

**Headers:**
```
Content-Type: application/json
```

**Body:**
```json
{
  "username": "novo_usuario",
  "password": "senha123",
  "client_id": 1
}
```

**Resposta de Sucesso (201):**
```json
{
  "id": 4,
  "username": "novo_usuario",
  "client_id": 1
}
```

---

### 2. Clientes

#### 2.1. Obter Configuração do Cliente (por domínio)

Retorna a configuração de um cliente baseado no domínio.

**Endpoint:** `GET /clients/config?domain={domain}`

**Headers:**
```
Content-Type: application/json
```

**Query Parameters:**
- `domain` (string, obrigatório): Domínio do cliente (ex: `localhost`, `devnology.com`)

**Exemplo de Requisição:**
```
GET /clients/config?domain=localhost
```

**Resposta de Sucesso (200):**
```json
{
  "id": 1,
  "domain": "localhost",
  "name": "E-commerce Dev",
  "primary_color": "#2196F3",
  "secondary_color": "#FFFFFF",
  "logo_url": "https://via.placeholder.com/200?text=Dev",
  "is_active": true,
  "clientProviders": [
    {
      "client_id": 1,
      "provider_id": 1,
      "is_active": true,
      "provider": {
        "id": 1,
        "name": "Brazilian Provider",
        "base_url": "http://616d6bdb6dacbb001794ca17.mockapi.io/devnology/brazilian_provider",
        "is_active": true
      }
    }
  ]
}
```

---

#### 2.2. Obter Cliente por Domínio (alternativo)

**Endpoint:** `GET /clients/domain/{domain}`

**Exemplo:**
```
GET /clients/domain/localhost
```

---

#### 2.3. Obter Cliente por ID

**Endpoint:** `GET /clients/{id}`

**Exemplo:**
```
GET /clients/1
```

---

#### 2.4. Listar Todos os Clientes

**Endpoint:** `GET /clients`

**Resposta de Sucesso (200):**
```json
[
  {
    "id": 1,
    "domain": "localhost",
    "name": "E-commerce Dev",
    "primary_color": "#2196F3",
    "secondary_color": "#FFFFFF",
    "logo_url": "https://via.placeholder.com/200?text=Dev",
    "is_active": true
  },
  {
    "id": 2,
    "domain": "devnology.com",
    "name": "Devnology",
    "primary_color": "#00AA00",
    "secondary_color": "#FFFFFF",
    "logo_url": "https://via.placeholder.com/200?text=Devnology",
    "is_active": true
  }
]
```

---

### 3. Produtos

**⚠️ Todos os endpoints de produtos requerem autenticação**

#### 3.1. Listar Produtos

Lista todos os produtos disponíveis para o cliente autenticado, agregando dados de todos os fornecedores associados.

**Endpoint:** `GET /products`

**Headers:**
```
Authorization: Bearer <token>
Host: localhost:3000
```

**Query Parameters (opcionais):**
- `search` (string): Termo de busca para filtrar produtos por nome ou descrição
- `page` (number): Número da página para paginação (padrão: 1)

**Exemplos de Requisição:**
```
GET /products
GET /products?search=shirt
GET /products?page=2
GET /products?search=steel&page=1
```

**Resposta de Sucesso (200):**
```json
{
  "data": [
    {
      "id": "1",
      "nome": "range of formal shirts",
      "descricao": "New range of formal shirts are designed keeping you in mind",
      "categoria": "Fantastic",
      "imagem": "http://placeimg.com/640/480/business",
      "preco": "127.00",
      "material": "Metal",
      "departamento": "Grocery"
    },
    {
      "id": "2",
      "nome": "Fantastic Steel Salad",
      "descricao": "The slim & simple Maple Gaming Keyboard",
      "categoria": "Refined",
      "imagem": "http://placeimg.com/640/480/nature",
      "preco": "716.00",
      "material": "Metal",
      "departamento": "Tools"
    }
  ],
  "total": 50,
  "page": 1,
  "pageSize": 10,
  "totalPages": 5
}
```

**Resposta de Erro (401):**
```json
{
  "statusCode": 401,
  "message": "Unauthorized"
}
```

---

#### 3.2. Obter Produto por ID

Retorna os detalhes de um produto específico.

**Endpoint:** `GET /products/{id}`

**Headers:**
```
Authorization: Bearer <token>
Host: localhost:3000
```

**Exemplo de Requisição:**
```
GET /products/1
```

**Resposta de Sucesso (200):**
```json
{
  "id": "1",
  "nome": "range of formal shirts",
  "descricao": "New range of formal shirts are designed keeping you in mind. With fits and styling that will make you stand apart",
  "categoria": "Fantastic",
  "imagem": "http://placeimg.com/640/480/business",
  "preco": "127.00",
  "material": "Metal",
  "departamento": "Grocery"
}
```

**Resposta de Erro (404):**
```json
null
```

---

### 4. Fornecedores

#### 4.1. Listar Todos os Fornecedores

**Endpoint:** `GET /providers`

**Resposta de Sucesso (200):**
```json
[
  {
    "id": 1,
    "name": "Brazilian Provider",
    "base_url": "http://616d6bdb6dacbb001794ca17.mockapi.io/devnology/brazilian_provider",
    "is_active": true
  },
  {
    "id": 2,
    "name": "European Provider",
    "base_url": "http://616d6bdb6dacbb001794ca17.mockapi.io/devnology/european_provider",
    "is_active": true
  }
]
```

---

#### 4.2. Obter Fornecedor por ID

**Endpoint:** `GET /providers/{id}`

---

### 5. Usuários

#### 5.1. Obter Usuário por ID

**Endpoint:** `GET /users/{id}`

---

## Credenciais de Teste

### Cliente: E-commerce Dev (localhost)
- **Domínio**: `localhost`
- **Usuário**: `admin`
- **Senha**: `admin123`
- **Cor**: Azul (#2196F3)

### Cliente: Devnology
- **Domínio**: `devnology.com`
- **Usuário**: `devnology_user`
- **Senha**: `password123`
- **Cor**: Verde (#00AA00)

### Cliente: In8
- **Domínio**: `in8.com`
- **Usuário**: `in8_user`
- **Senha**: `password123`
- **Cor**: Roxo (#800080)

---

## Fluxo de Autenticação

1. **Fazer Login**
   ```bash
   curl -X POST http://localhost:3000/auth/login \
     -H "Content-Type: application/json" \
     -H "Host: localhost:3000" \
     -d '{"username":"admin","password":"admin123"}'
   ```

2. **Extrair o Token**
   ```json
   {
     "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
   }
   ```

3. **Usar o Token nas Requisições**
   ```bash
   curl -X GET http://localhost:3000/products \
     -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
     -H "Host: localhost:3000"
   ```

---

## Códigos de Status HTTP

| Código | Significado |
|--------|-------------|
| 200 | OK - Requisição bem-sucedida |
| 201 | Created - Recurso criado com sucesso |
| 400 | Bad Request - Dados inválidos |
| 401 | Unauthorized - Autenticação necessária ou falhou |
| 404 | Not Found - Recurso não encontrado |
| 500 | Internal Server Error - Erro no servidor |

---

## Middleware e Guards

### ClientMiddleware

Injeta automaticamente o objeto `client` na requisição baseado no header `Host`.

**Como funciona:**
1. Extrai o domínio do header `Host`
2. Busca o cliente no banco de dados
3. Adiciona o cliente em `req.client`

### JwtAuthGuard

Protege rotas que requerem autenticação.

**Rotas Protegidas:**
- `GET /products`
- `GET /products/:id`

---

## Variáveis de Ambiente

Crie um arquivo `.env` na raiz do projeto:

```env
# JWT
JWT_SECRET=your-secret-key
JWT_EXPIRES_IN=24h

# Database
DB_TYPE=better-sqlite3
DB_DATABASE=ecommerce.db

# Server
PORT=3000
```

---

## CORS

O servidor está configurado para aceitar requisições de qualquer origem (desenvolvimento).

**⚠️ Em produção, configure CORS adequadamente:**

```typescript
app.enableCors({
  origin: ['http://localhost:8000', 'http://devnology.com:8000'],
  credentials: true,
});
```

---

## Erros Comuns

### 1. Erro 401 no Login

**Causa**: Domínio do cliente não corresponde ao header Host

**Solução**: Certifique-se de que o header `Host` contém o domínio correto do cliente

### 2. Erro 401 em /products

**Causa**: Token JWT ausente ou inválido

**Solução**: Inclua o token no header `Authorization: Bearer <token>`

### 3. Produtos vazios

**Causa**: Cliente não tem fornecedores associados

**Solução**: Verifique a tabela `client_providers` no banco de dados

---

## Testando com cURL

### Login
```bash
curl -X POST http://localhost:3000/auth/login \
  -H "Content-Type: application/json" \
  -H "Host: localhost:3000" \
  -d '{"username":"admin","password":"admin123"}'
```

### Listar Produtos
```bash
curl -X GET http://localhost:3000/products \
  -H "Authorization: Bearer SEU_TOKEN_AQUI" \
  -H "Host: localhost:3000"
```

### Buscar Produtos
```bash
curl -X GET "http://localhost:3000/products?search=steel" \
  -H "Authorization: Bearer SEU_TOKEN_AQUI" \
  -H "Host: localhost:3000"
```

---

## Collection do Postman/Insomnia

Um arquivo JSON de collection será fornecido separadamente para importação no Postman ou Insomnia.
