# CRUD de Produtos

Este documento descreve o módulo de CRUD de produtos implementado na aplicação.

## 📋 Visão Geral

O módulo de produtos permite que usuários autenticados criem, leiam, atualizem e deletem (CRUD) produtos. Cada produto contém:
- **Nome**: Identificador do produto
- **Descrição**: Detalhes do produto
- **Preço**: Valor em R$
- **Imagem**: Foto do produto

## 🔐 Autenticação e Autorização

- **Leitura**: Qualquer usuário autenticado pode visualizar todos os produtos
- **Criação**: Apenas usuários autenticados podem criar produtos
- **Edição**: Apenas o proprietário do produto pode editá-lo
- **Deleção**: Apenas o proprietário do produto pode deletá-lo

## 📂 Estrutura do Projeto

### Backend (Laravel)

#### Models
- **Product** (`app/Models/Product.php`)
  - Relacionamento com User (belongsTo)
  - Atributos: name, description, price, image, user_id

#### Controladores
- **ProductController** (`app/Http/Controllers/ProductController.php`)
  - Métodos RESTful: index, store, show, update, destroy
  - Validação de entrada
  - Upload automático de imagens

#### Policies
- **ProductPolicy** (`app/Policies/ProductPolicy.php`)
  - Autorização para editar e deletar

#### Migrations
- **create_products_table** (`database/migrations/2026_04_02_233823_create_products_table.php`)
  - Tabela com campos: id, user_id, name, description, price, image, timestamps

#### Rotas
- `GET /products` - Página de produtos (Inertia)
- `GET /api/products` - Listar todos os produtos (JSON)
- `POST /api/products` - Criar novo produto
- `GET /api/products/{id}` - Obter um produto
- `PUT /api/products/{id}` - Atualizar produto
- `DELETE /api/products/{id}` - Deletar produto

### Frontend (Vue.js 3)

#### Páginas
- **Products** (`resources/js/pages/Products.vue`)
  - Grade de 3 colunas responsiva
  - Preview de imagens
  - Botões contextuais (Editar/Deletar)
  - Carregamento e estados vazios

#### Componentes
- **ProductModal** (`resources/js/components/ProductModal.vue`)
  - Modal reutilizável para criar/editar
  - Upload de imagem com preview
  - Validação e tratamento de erros

## 🚀 Como Usar

### Acessar a Página de Produtos

1. Faça login na aplicação
2. Navegue para `/products`

### Criar um Novo Produto

1. Clique no botão **"+ Novo Produto"**
2. Preencha os campos:
   - **Nome**: Nome do produto (obrigatório)
   - **Descrição**: Detalhes do produto (obrigatório)
   - **Preço**: Valor em R$ (obrigatório, positivo)
   - **Imagem**: Upload de imagem (opcional, máx. 2MB)
3. Clique em **"Criar"**

### Editar um Produto

1. Localize seu produto na lista
2. Clique no botão **"Editar"** (disponível apenas para seus produtos)
3. Modifique os campos desejados
4. Clique em **"Atualizar"**

### Deletar um Produto

1. Localize seu produto na lista
2. Clique no botão **"Deletar"** (disponível apenas para seus produtos)
3. Confirme a exclusão

## 📦 Upload de Imagens

- **Localização**: `storage/app/public/products/`
- **Acesso**: `/storage/products/{filename}`
- **Formatos**: JPEG, PNG, JPG, GIF
- **Tamanho máximo**: 2MB
- **Fallback**: Exibe "Sem imagem" se não houver foto

## ✅ Validação

### Cliente (Vue.js)
- Campos obrigatórios
- Validação de número para preço
- Preview de imagem antes do envio

### Servidor (Laravel)
```php
'name' => 'required|string|max:255',
'description' => 'required|string',
'price' => 'required|numeric|min:0',
'image' => 'nullable|image|mimes:jpeg,png,jpg,gif|max:2048'
```

## 🗄️ Banco de Dados

### Tabela: products

```sql
CREATE TABLE products (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    name VARCHAR(255) NOT NULL,
    description TEXT NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    image VARCHAR(255) NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

## 🔧 Configuração

### Variáveis de Ambiente
Nenhuma configuração específica necessária além do `.env` padrão do Laravel.

### Disco de Armazenamento
- Configurado para usar o disco `public`
- Arquivos acessíveis em `/storage/`

## 📝 Exemplo de API

### Criar Produto
```bash
POST /api/products
Content-Type: multipart/form-data

name=Teclado
description=Teclado mecânico RGB
price=250.00
image=<arquivo>
```

### Listar Produtos
```bash
GET /api/products
```

### Atualizar Produto
```bash
POST /api/products/1
Content-Type: multipart/form-data
X-Http-Method-Override: PUT

name=Teclado Atualizado
description=Descrição atualizada
price=300.00
_method=PUT
```

### Deletar Produto
```bash
DELETE /api/products/1
```

## 🐛 Troubleshooting

### Imagens não aparecem
1. Verifique se o arquivo existe em `storage/app/public/products/`
2. Execute `php artisan storage:link` se necessário
3. Limpe o cache do navegador

### Erro de permissão ao editar
1. Verifique se você é o proprietário do produto
2. Faça logout e login novamente

### Erro de banco de dados
1. Execute `php artisan migrate`
2. Verifique as credenciais do banco em `.env`

## 📚 Tecnologias Utilizadas

- **Backend**: Laravel 11
- **Frontend**: Vue.js 3 com Inertia.js
- **Banco de Dados**: SQLite
- **Build Tool**: Vite
- **Autenticação**: Laravel Fortify
