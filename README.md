# Laravel + Vue - CRUD de Produtos

## 📋 Introdução

Este é um kit iniciador robusto e moderno para construir aplicações Laravel com frontend Vue usando [Inertia](https://inertiajs.com), incluindo um **CRUD completo de Produtos**.

O Inertia permite construir aplicações Vue modernas single-page usando roteamento clássico server-side e controladores. Isso permite desfrutar do poder do frontend Vue combinado com a incrível produtividade do backend Laravel e compilação ultra-rápida do Vite.

Este kit iniciador utiliza:
- **Vue 3** com Composition API
- **TypeScript** para segurança de tipos
- **Tailwind CSS** para estilização
- **shadcn-vue** para componentes UI
- **Inertia.js** para sincronização frontend-backend
- **SQLite** como banco de dados

## 🚀 Inicio Rápido

### Pré-requisitos

Antes de começar, certifique-se de ter instalado:
- **PHP** >= 8.2
- **Node.js** >= 18
- **Composer** (gerenciador de dependências PHP)
- **NPM** ou **Yarn** (gerenciador de pacotes JavaScript)

### 📦 Instalação

1. **Clone o repositório**
```bash
git clone <seu-repositorio>
cd crud-laravel
```

2. **Instale as dependências PHP**
```bash
composer install
```

3. **Instale as dependências JavaScript**
```bash
npm install
```

4. **Configure o arquivo .env**
```bash
cp .env.example .env
php artisan key:generate
```

5. **Execute as migrations (crie as tabelas)**
```bash
php artisan migrate
```

6. **Seed do banco de dados (crie usuário de teste)**
```bash
php artisan db:seed
```

### ▶️ Como Rodar a Aplicação

**Abra dois terminais no diretório do projeto:**

**Terminal 1 - Compile os assets em tempo real:**
```bash
npm run dev
```

**Terminal 2 - Inicie o servidor Laravel:**
```bash
php artisan serve
```

A aplicação estará acessível em: [http://localhost:8000](http://localhost:8000)

## 🔐 Credenciais de Teste

Use as seguintes credenciais para fazer login:

| Campo | Valor |
|-------|-------|
| **Email** | `test@example.com` |
| **Senha** | `password` |

## 📝 Uso do CRUD de Produtos

### Acessar o Módulo de Produtos

1. Faça login com as credenciais acima
2. Clique em **"Produtos"** no menu lateral esquerdo

### Criar um Novo Produto

1. Clique no botão **"+ Novo Produto"**
2. Preencha os campos obrigatórios:
   - **Nome**: Nome do produto (máx. 255 caracteres)
   - **Descrição**: Descrição detalhada
   - **Preço**: Valor em R$ (positivo)
   - **Imagem**: Upload opcional (JPG, PNG, GIF - máx. 2MB)
3. Clique em **"Criar"**

### Editar um Produto

1. Localize seu produto na lista
2. Clique no botão **"Editar"** (disponível apenas para produtos seus)
3. Modifique os campos desejados
4. Clique em **"Atualizar"**

### Deletar um Produto

1. Localize seu produto na lista
2. Clique no botão **"Deletar"** (disponível apenas para produtos seus)
3. Confirme a exclusão na caixa de diálogo

## 🏗️ Estrutura do Projeto

```
📦 crud-laravel
├── 📂 app/
│   ├── Models/
│   │   └── Product.php          # Modelo de Produto
│   ├── Http/
│   │   └── Controllers/
│   │       └── ProductController.php  # Controlador REST
│   ├── Policies/
│   │   └── ProductPolicy.php    # Política de Autorização
│   └── Providers/
│       └── AppServiceProvider.php
├── 📂 database/
│   ├── migrations/
│   │   └── *_create_products_table.php
│   └── seeders/
│       └── DatabaseSeeder.php
├── 📂 resources/
│   ├── js/
│   │   ├── pages/
│   │   │   └── Products.vue     # Página principal
│   │   └── components/
│   │       ├── ProductModal.vue # Modal de criação/edição
│   │       └── AppSidebar.vue   # Menu lateral
│   └── views/
│       └── app.blade.php
├── 📂 routes/
│   ├── web.php                  # Rotas da aplicação
│   └── api.php
├── 📄 package.json
├── 📄 composer.json
├── 📄 vite.config.ts
└── 📄 .env
```

## 📚 API REST Endpoints

Todos os endpoints requerem autenticação:

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `GET` | `/api/products` | Listar todos os produtos |
| `POST` | `/api/products` | Criar novo produto |
| `GET` | `/api/products/{id}` | Obter um produto específico |
| `PUT` | `/api/products/{id}` | Atualizar um produto |
| `DELETE` | `/api/products/{id}` | Deletar um produto |

### Exemplo de Requisição

```bash
# Criar um produto
curl -X POST http://localhost:8000/api/products \
  -H "Content-Type: multipart/form-data" \
  -H "X-Requested-With: XMLHttpRequest" \
  -F "name=Notebook" \
  -F "description=Notebook de alta performance" \
  -F "price=3500.00" \
  -F "image=@/path/to/image.jpg"
```

## 🔍 Troubleshooting

### Erro: "SQLSTATE[HY000] [1045]"
- **Causa**: Driver PDO MySQL não habilitado
- **Solução**: Edite `php.ini` e descomente a linha `extension=pdo_mysql` ou use SQLite

### Erro: "Arquivo não encontrado" nas imagens
- **Causa**: Symlink de storage não criado
- **Solução**: Execute `php artisan storage:link`

### Porta 8000 já está em uso
- **Solução**: Use outra porta com `php artisan serve --port=8001`

### Assets não carregam corretamente
- **Solução**: Limpe o cache com:
```bash
php artisan cache:clear
php artisan config:clear
php artisan route:clear
npm run build
```

## 🛠️ Comandos Úteis

```bash
# Iniciar servidor de desenvolvimento
php artisan serve

# Compilar assets em produção
npm run build

# Compilar assets em tempo real (desenvolvimento)
npm run dev

# Executar migrations
php artisan migrate

# Desfazer última migration
php artisan migrate:rollback

# Resetar banco de dados (cuidado!)
php artisan migrate:fresh --seed

# Limpar todos os caches
php artisan cache:clear && php artisan config:clear && php artisan route:clear

# Criar arquivo de symlink para storage
php artisan storage:link

# Executar testes
php artisan test
```

## 📋 Validações

### Backend (Laravel)
```
Nome: obrigatório, string, máximo 255 caracteres
Descrição: obrigatória, string
Preço: obrigatório, numérico, maior ou igual a 0
Imagem: opcional, arquivo de imagem, máximo 2MB
```

### Frontend (Vue.js)
- Validação em tempo real dos campos
- Preview de imagem antes do envio
- Mensagens de erro informativas

## 🔐 Segurança

- ✅ Autenticação obrigatória em todas as rotas
- ✅ Autorização verificada (apenas proprietário pode editar/deletar)
- ✅ CSRF protection habilitado
- ✅ Validação de entrada no servidor
- ✅ Armazenamento seguro de imagens

## 📱 Design Responsivo

A interface é totalmente responsiva:
- **Desktop**: Grid de 3 colunas
- **Tablet**: Grid de 2 colunas
- **Mobile**: Grid de 1 coluna

## 🌙 Dark Mode

A aplicação possui suporte completo a dark mode que se adapta automaticamente às preferências do sistema.

## 📖 Documentação Oficial

- [Laravel](https://laravel.com/docs)
- [Vue.js](https://vuejs.org/)
- [Inertia.js](https://inertiajs.com/)
- [Tailwind CSS](https://tailwindcss.com/)

## 🤝 Contribuindo

Contribuições são bem-vindas! Siga o guia de contribuição padrão de Laravel em [laravel.com/docs/contributions](https://laravel.com/docs/contributions).

## 📄 Licença

O Laravel + Vue starter kit é software de código aberto sob licença MIT.
