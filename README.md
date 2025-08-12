# 📚 DOCUMENTAÇÃO COMPLETA - CHECKOUTPRO BACKEND API

## 🚀 Visão Geral

Esta é a documentação completa de todas as APIs disponíveis no CheckoutPro Backend.

**Base URL**: `http://localhost:5000`
**Ambiente de Produção**: Configurável via variáveis de ambiente

---

## �️ Segurança e Autenticação

### ⚠️ **IMPORTANTE**: Mudanças de Segurança Implementadas

A partir desta versão, **TODAS as rotas que lidam com dados sensíveis agora requerem autenticação JWT**. Isso inclui:

**🎓 Rotas de Cursos Protegidas:**
- `GET /api/courses/enrollment-status/{courseId}/{studentEmail}` - Status de matrícula
- `POST /api/courses/{course_id}/modules` - Criar módulo
- `PUT /api/courses/{course_id}/modules/{module_id}` - Atualizar módulo
- `DELETE /api/courses/{course_id}/modules/{module_id}` - Excluir módulo
- `POST /api/courses/{course_id}/modules/{module_id}/lessons` - Criar aula
- `PUT /api/courses/{course_id}/modules/{module_id}/lessons/{lesson_id}` - Atualizar aula
- `DELETE /api/courses/{course_id}/modules/{module_id}/lessons/{lesson_id}` - Excluir aula
- `POST /api/courses/{course_id}/enroll` - Matricular no curso
- `PUT /api/courses/{course_id}/progress` - Atualizar progresso
- `GET /api/courses/{course_id}/enrollments` - Listar matrículas

**📧 Rotas de Pedidos Protegidas:**
- `GET /api/orders/customer/{email}` - Pedidos por email de cliente

**📊 Rotas de Estatísticas Protegidas:**
- `GET /api/products/{id}/stats` - Estatísticas do produto
- `GET /api/products/{id}/full` - Produto completo
- `GET /api/partners/commissions/stats/{partnerId}` - Estatísticas de comissões

**⚙️ Configurações Protegidas:**
- `GET /api/settings` - Listar configurações
- `PUT /api/settings` - Atualizar configurações
- `GET /api/settings/{key}` - Buscar configuração específica
- `DELETE /api/settings/{key}` - Excluir configuração

**🔗 Webhooks Protegidos:**
- `GET /api/webhooks` - Listar webhooks
- `POST /api/webhooks` - Criar webhook
- `PUT /api/webhooks/{id}` - Atualizar webhook
- `DELETE /api/webhooks/{id}` - Excluir webhook
- `POST /api/webhooks/{id}/test` - Testar webhook
- `GET /api/webhooks/{id}/logs` - Logs do webhook
- `GET /api/webhooks/stats` - Estatísticas de webhooks

### 🔑 Como Autenticar

Todas as rotas protegidas requerem o header:
```
Authorization: Bearer {seu_jwt_token}
```

### 🚫 Rotas Públicas Restantes

Apenas estas rotas permanecem públicas (por questões técnicas específicas):
- `POST /api/auth/login` - Login
- `POST /api/auth/register` - Registro 
- `POST /api/auth/forgot-password` - Recuperação de senha
- `POST /api/pix/webhook/*` - Webhooks de pagamento (validação própria)
- `GET /api/products/slug/{slug}` - Página pública de produto

### ⚠️ **BREAKING CHANGES**

**🔥 Atenção Desenvolvedores:** Estas mudanças são **BREAKING CHANGES** que podem afetar integrações existentes. Certifique-se de:

1. **Atualizar seu frontend/app** para incluir o token JWT em todas as requisições para as rotas protegidas
2. **Implementar tratamento de erro 401** para redirecionamento de login quando o token expirar
3. **Revisar automações/scripts** que fazem chamadas para as rotas que agora requerem autenticação
4. **Testar todas as funcionalidades** após implementar as mudanças

---

## �📋 Índice

- [🔐 Autenticação](#-autenticação)
- [📧 Emails](#-emails)
- [👥 Usuários](#-usuários)
- [🛒 Produtos & Pedidos](#-produtos--pedidos)
- [🎓 Cursos Online](#-cursos-online)
- [🤝 Co-Produtores/Afiliados](#-co-produtoresafiliados)
- [💳 Pagamento com Cartão](#-pagamento-com-cartão)
- [💰 PIX](#-pix)
- [📊 Rastreamento Facebook](#-rastreamento-facebook)
- [🔗 Webhooks](#-webhooks)
- [⚙️ Configurações](#️-configurações)
- [📋 KYC & Verificação](#-kyc--verificação)
- [💰 Planos & Assinaturas](#-planos--assinaturas)
- [📈 Analytics](#-analytics)
- [🎨 Templates](#-templates)
- [🔧 Sistema](#-sistema)

---

## 🔐 Autenticação

Sistema completo de autenticação com JWT, 2FA e recuperação de senha.

### 🔑 Login
```http
POST /api/auth/login
```

**Body:**
```json
{
  "email": "usuario@email.com",
  "password": "senha123"
}
```

**Resposta de Sucesso:**
```json
{
  "success": true,
  "message": "Login realizado com sucesso",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "user-uuid",
    "name": "Nome do Usuário",
    "email": "usuario@email.com",
    "role": "user",
    "email_verified": true,
    "twofa_enabled": false
  }
}
```

**Resposta de Erro:**
```json
{
  "success": false,
  "error": "Email ou senha inválidos"
}
```

### ✍️ Registro
```http
POST /api/auth/register
```

**Body:**
```json
{
  "name": "Nome Completo",
  "email": "novo@email.com",
  "password": "senha123",
  "phone": "11999999999"
}
```

**Resposta:**
```json
{
  "success": true,
  "message": "Usuário registrado com sucesso",
  "user": {
    "id": "user-uuid",
    "name": "Nome Completo",
    "email": "novo@email.com"
  }
}
```

### 🚪 Logout
```http
POST /api/auth/logout
```

**Headers:**
```
Authorization: Bearer {token}
```

### 📧 Enviar Código de Verificação
```http
POST /api/auth/send-verification-email
```

**Body:**
```json
{
  "email": "usuario@email.com"
}
```

### ✅ Verificar Email
```http
POST /api/auth/verify-email-code
```

**Body:**
```json
{
  "email": "usuario@email.com",
  "code": "123456"
}
```

### 🔐 Esqueci Minha Senha
```http
POST /api/auth/forgot-password
```

**Body:**
```json
{
  "email": "usuario@email.com"
}
```

### 🔄 Redefinir Senha
```http
POST /api/auth/reset-password
```

**Body:**
```json
{
  "token": "reset-token",
  "newPassword": "novaSenha123"
}
```

### 🛡️ 2FA - Configurar Google Authenticator
```http
POST /api/auth/2fa/setup-google
```

**Headers:**
```
Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "success": true,
  "qrCodeUrl": "data:image/png;base64,...",
  "manualEntryKey": "JBSWY3DPEHPK3PXP"
}
```

### ✅ 2FA - Verificar Código
```http
POST /api/auth/2fa/verify-google
```

**Body:**
```json
{
  "token": "123456"
}
```

### 🔐 Login com 2FA
```http
POST /api/auth/login-2fa
```

**Body:**
```json
{
  "email": "usuario@email.com",
  "password": "senha123",
  "twoFactorToken": "123456"
}
```

### ❌ Desativar 2FA
```http
DELETE /api/auth/2fa/disable
```

**Headers:**
```
Authorization: Bearer {token}
```

---

## 📧 Emails

Sistema de envio de emails com SMTP real configurado.

### 📤 Enviar Email de Boas-vindas
```http
POST /api/auth/send-welcome
```

**Body:**
```json
{
  "email": "usuario@email.com",
  "name": "Nome do Usuário"
}
```

### 🎯 Enviar Notificação de Produto
```http
POST /api/auth/send-product-notification
```

**Body:**
```json
{
  "email": "usuario@email.com",
  "productName": "Nome do Produto",
  "productPrice": 99.90
}
```

### 📋 Confirmação de Pedido
```http
POST /api/auth/send-order-confirmation
```

**Body:**
```json
{
  "orderId": "order-uuid",
  "customerEmail": "cliente@email.com"
}
```

### 💰 Notificação de Venda
```http
POST /api/auth/send-sale-notification
```

**Body:**
```json
{
  "orderId": "order-uuid",
  "sellerEmail": "vendedor@email.com"
}
```

### 🧪 Teste de Email
```http
GET /api/auth/test-email
```

**Resposta:**
```json
{
  "success": true,
  "message": "Teste de email enviado com sucesso"
}
```

---

## 👥 Usuários

Gerenciamento completo de usuários com perfis e estatísticas.

### 📋 Listar Usuários
```http
GET /api/users
```

**Query Parameters:**
- `page`: Página (padrão: 1)
- `limit`: Itens por página (padrão: 20)
- `search`: Busca por nome/email

**Resposta:**
```json
{
  "success": true,
  "users": [
    {
      "id": "user-uuid",
      "name": "Nome do Usuário",
      "email": "usuario@email.com",
      "role": "user",
      "created_at": "2025-01-01T00:00:00Z"
    }
  ],
  "pagination": {
    "currentPage": 1,
    "totalPages": 5,
    "totalUsers": 100
  }
}
```

### 👤 Buscar Usuário por ID
```http
GET /api/users/{id}
```

### ✏️ Atualizar Usuário
```http
PUT /api/users/{id}
```

**Body:**
```json
{
  "name": "Novo Nome",
  "phone": "11999999999",
  "avatar": "https://exemplo.com/avatar.jpg"
}
```

### 🔐 Atualizar Segurança
```http
PUT /api/users/{id}/security
```

**Body:**
```json
{
  "currentPassword": "senhaAtual123",
  "newPassword": "novaSenha123"
}
```

### 💰 Atualizar Carteira
```http
PUT /api/users/{id}/wallet
```

**Body:**
```json
{
  "balance": 150.75,
  "operation": "add" // ou "subtract"
}
```

### 📊 Estatísticas do Usuário
```http
GET /api/users/{id}/stats
```

**Resposta:**
```json
{
  "success": true,
  "stats": {
    "totalProducts": 10,
    "totalOrders": 45,
    "totalRevenue": 4500.00,
    "completedCourses": 3,
    "walletBalance": 150.75
  }
}
```

### ❌ Excluir Usuário
```http
DELETE /api/users/{id}
```

---

## 🛒 Produtos & Pedidos

Sistema completo de gerenciamento de produtos e pedidos com isolamento por usuário.

### 📋 Listar Produtos
```http
GET /api/products
```

**Headers:**
```
Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "success": true,
  "products": [
    {
      "id": "product-uuid",
      "name": "Meu Produto",
      "description": "Descrição do produto",
      "price": 99.90,
      "slug": "meu-produto",
      "image": "https://exemplo.com/imagem.jpg",
      "status": "active",
      "commission_percentage": 10.5,
      "seller": {
        "id": "user-uuid",
        "name": "Nome do Vendedor",
        "email": "vendedor@email.com"
      },
      "created_at": "2025-01-01T00:00:00Z"
    }
  ]
}
```

### 🎯 Buscar Produto por ID
```http
GET /api/products/{id}
```

**Headers:**
```
Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "success": true,
  "product": {
    "id": "product-uuid",
    "name": "Meu Produto",
    "description": "Descrição completa",
    "price": 99.90,
    "slug": "meu-produto",
    "image": "https://exemplo.com/imagem.jpg",
    "video_url": "https://exemplo.com/video.mp4",
    "status": "active",
    "commission_percentage": 10.5,
    "tags": ["digital", "curso"],
    "seller": {
      "id": "user-uuid",
      "name": "Nome do Vendedor",
      "email": "vendedor@email.com"
    }
  }
}
```

### 🔗 Buscar por Slug
```http
GET /api/products/slug/{slug}
```

**Exemplo:**
```http
GET /api/products/slug/meu-produto-incrivel
```

### ✅ Verificar Disponibilidade de Slug
```http
GET /api/products/check-slug/{slug}
```

**Resposta:**
```json
{
  "available": true,
  "slug": "meu-novo-produto"
}
```

### ➕ Criar Produto
```http
POST /api/products
```

**Headers:**
```
Authorization: Bearer {token}
```

**Body:**
```json
{
  "name": "Novo Produto",
  "description": "Descrição do produto",
  "price": 147.50,
  "image": "https://exemplo.com/imagem.jpg",
  "video_url": "https://exemplo.com/video.mp4",
  "commission_percentage": 15.0,
  "tags": ["digital", "ebook"],
  "status": "active"
}
```

**Resposta:**
```json
{
  "success": true,
  "message": "Produto criado com sucesso",
  "product": {
    "id": "new-product-uuid",
    "name": "Novo Produto",
    "slug": "novo-produto",
    "price": 147.50,
    "seller_id": "user-uuid",
    "created_at": "2025-01-01T00:00:00Z"
  }
}
```

### ✏️ Atualizar Produto
```http
PUT /api/products/{id}
```

**Headers:**
```
Authorization: Bearer {token}
```

**Body:**
```json
{
  "name": "Nome Atualizado",
  "price": 199.90,
  "description": "Nova descrição",
  "status": "active"
}
```

### ❌ Excluir Produto
```http
DELETE /api/products/{id}
```

**Headers:**
```
Authorization: Bearer {token}
```

### 📊 Estatísticas do Produto
```http
GET /api/products/{id}/stats
```

**Headers:**
```
Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "success": true,
  "stats": {
    "totalViews": 1250,
    "totalSales": 45,
    "totalRevenue": 4455.00,
    "conversionRate": 3.6,
    "averageRating": 4.8
  }
}
```

### 🎨 Criar Produto a Partir de Template
```http
POST /api/products/from-template
```

**Body:**
```json
{
  "templateId": "template-uuid",
  "customizations": {
    "name": "Meu Produto Personalizado",
    "price": 97.00
  }
}
```

### 📄 Buscar Produto Completo
```http
GET /api/products/{id}/full
```

**Headers:**
```
Authorization: Bearer {token}
```

**Resposta com todos os detalhes, incluindo módulos, aulas, estatísticas, etc.**

---

## 📦 Pedidos

### 📋 Listar Pedidos
```http
GET /api/orders
```

**Headers:**
```
Authorization: Bearer {token}
```

**Query Parameters:**
- `status`: pending, paid, cancelled, refunded
- `page`: Página (padrão: 1)
- `limit`: Itens por página (padrão: 20)

**Resposta:**
```json
{
  "success": true,
  "orders": [
    {
      "id": "order-uuid",
      "customer_name": "João Silva",
      "customer_email": "joao@email.com",
      "amount": 99.90,
      "status": "paid",
      "payment_method": "pix",
      "product": {
        "id": "product-uuid",
        "name": "Produto Comprado"
      },
      "created_at": "2025-01-01T00:00:00Z",
      "paid_at": "2025-01-01T00:05:00Z"
    }
  ],
  "pagination": {
    "currentPage": 1,
    "totalPages": 10,
    "totalOrders": 200
  }
}
```

### 🎯 Buscar Pedido por ID
```http
GET /api/orders/{id}
```

### 📧 Buscar Pedidos por Email
```http
GET /api/orders/customer/{email}
```

**Headers:**
```
Authorization: Bearer {token}
```

**Descrição:** Busca pedidos de um cliente específico (requer autenticação)

### ➕ Criar Pedido
```http
POST /api/orders
```

**Body:**
```json
{
  "product_id": "product-uuid",
  "customer_name": "João Silva",
  "customer_email": "joao@email.com",
  "customer_phone": "11999999999",
  "amount": 99.90,
  "payment_method": "pix"
}
```

### ✏️ Atualizar Pedido
```http
PUT /api/orders/{id}
```

### ❌ Excluir Pedido
```http
DELETE /api/orders/{id}
```

### 📊 Estatísticas de Pedidos
```http
GET /api/orders/stats
```

**Headers:**
```
Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "success": true,
  "stats": {
    "totalOrders": 150,
    "totalRevenue": 14850.00,
    "pendingOrders": 5,
    "paidOrders": 140,
    "cancelledOrders": 5,
    "averageOrderValue": 99.00,
    "conversionRate": 12.5
  }
}
```

---

## 🎓 Cursos Online

Sistema completo de cursos online com módulos, aulas e progresso de alunos.

### 📚 Meus Cursos (Aluno)
```http
GET /api/courses/my-courses
```

**Headers:**
```
Authorization: Bearer {token}
```

**Descrição:** Busca todos os cursos em que o aluno está matriculado

**Resposta:**
```json
{
  "success": true,
  "courses": [
    {
      "enrollment": {
        "id": "enrollment-uuid",
        "enrolledAt": "2025-01-01T00:00:00Z",
        "status": "active",
        "progress": 29,
        "expiresAt": "2026-01-01T00:00:00Z"
      },
      "course": {
        "id": "course-uuid",
        "title": "Curso Completo de JavaScript Moderno",
        "description": "Aprenda JavaScript do zero ao avançado",
        "thumbnailUrl": "https://example.com/javascript-course.jpg",
        "trailerUrl": null,
        "duration": 1200,
        "level": "intermediate",
        "category": "programacao",
        "status": "published",
        "product": {
          "id": "product-uuid",
          "name": "Curso Completo de JavaScript Moderno",
          "price": 497.00,
          "image_url": "https://example.com/javascript-course.jpg"
        },
        "instructor": {
          "id": "instructor-uuid",
          "name": "Carlos Mendoza",
          "email": "professor@javascript.com"
        },
        "modules": [
          {
            "id": "module-uuid",
            "title": "Fundamentos do JavaScript",
            "orderIndex": 0,
            "duration": 180,
            "isFree": false,
            "lessonsCount": 3,
            "lessons": [
              {
                "id": "lesson-uuid",
                "title": "Introdução ao JavaScript",
                "duration": 30,
                "isFree": true,
                "orderIndex": 0
              },
              {
                "id": "lesson-uuid-2",
                "title": "Variáveis e Tipos de Dados",
                "duration": 45,
                "isFree": true,
                "orderIndex": 1
              }
            ]
          }
        ]
      },
      "purchase": {
        "id": "order-uuid",
        "amount": 497.00,
        "status": "paid",
        "payment_method": "pix"
      }
    }
  ],
  "total": 1,
  "user": {
    "id": "user-uuid",
    "name": "João Silva",
    "email": "aluno@teste.com"
  }
}
```

### 📋 Listar Cursos (Instrutor/Seller)
```http
GET /api/courses
```

**Headers:**
```
Authorization: Bearer {token}
```

**Query Parameters:**
- `category`: Categoria do curso
- `status`: active, draft, published, archived
- `student_id`: ID do estudante (para buscar cursos matriculados)

**Descrição:** Lista cursos do instrutor logado. Se for aluno, lista cursos matriculados.

**Resposta:**
```json
[
  {
    "id": "course-uuid",
    "title": "Curso Completo de JavaScript Moderno",
    "description": "Aprenda JavaScript do zero ao avançado",
    "thumbnailUrl": "https://example.com/javascript-course.jpg",
    "trailerUrl": null,
    "durationMinutes": 1200,
    "level": "intermediate",
    "category": "programacao",
    "tags": ["javascript", "programacao", "web"],
    "status": "published",
    "createdAt": "2025-01-01T00:00:00Z",
    "product": {
      "id": "product-uuid",
      "name": "Curso Completo de JavaScript Moderno",
      "price": 497.00,
      "image_url": "https://example.com/javascript-course.jpg"
    },
    "instructor": {
      "id": "instructor-uuid",
      "name": "Carlos Mendoza",
      "email": "professor@javascript.com"
    },
    "enrollment": null
  }
]
```

### 🎯 Buscar Curso por ID
```http
GET /api/courses/{course_id}
```

**Query Parameters:**
- `student_id`: ID do estudante (opcional, para incluir progresso)

**Descrição:** Busca curso específico com todos os módulos, aulas e progresso (se student_id fornecido)

**Resposta Completa:**
```json
{
  "success": true,
  "course": {
    "id": "course-uuid",
    "title": "Curso Completo de JavaScript Moderno",
    "description": "Aprenda JavaScript do zero ao avançado",
    "thumbnailUrl": "https://example.com/javascript-course.jpg",
    "trailerUrl": null,
    "durationMinutes": 1200,
    "level": "intermediate",
    "category": "programacao",
    "tags": ["javascript", "programacao", "web"],
    "settings": {},
    "status": "published",
    "createdAt": "2025-01-01T00:00:00Z",
    "product": {
      "id": "product-uuid",
      "name": "Curso Completo de JavaScript Moderno",
      "price": 497.00,
      "image_url": "https://example.com/javascript-course.jpg"
    },
    "instructor": {
      "id": "instructor-uuid",
      "name": "Carlos Mendoza",
      "email": "professor@javascript.com"
    },
    "modules": [
      {
        "id": "module-uuid",
        "title": "Fundamentos do JavaScript",
        "description": "Aprenda os conceitos básicos",
        "orderIndex": 0,
        "durationMinutes": 180,
        "isFree": false,
        "lessons": [
          {
            "id": "lesson-uuid",
            "title": "Introdução ao JavaScript",
            "description": "Primeira aula do curso",
            "contentType": "video",
            "contentUrl": "https://example.com/aula1.mp4",
            "contentData": {},
            "durationMinutes": 30,
            "orderIndex": 0,
            "isFree": true,
            "transcript": ""
          },
          {
            "id": "lesson-uuid-2",
            "title": "Variáveis e Tipos de Dados",
            "description": "Aprenda sobre variáveis",
            "contentType": "video",
            "contentUrl": "https://example.com/aula2.mp4",
            "contentData": {},
            "durationMinutes": 45,
            "orderIndex": 1,
            "isFree": true,
            "transcript": ""
          }
        ]
      }
    ],
    "enrollment": {
      "id": "enrollment-uuid",
      "enrolledAt": "2025-01-01T00:00:00Z",
      "status": "active",
      "progressPercentage": 29,
      "progress": [
        {
          "lessonId": "lesson-uuid",
          "status": "completed",
          "progressPercentage": 100,
          "timeSpentMinutes": 30,
          "lastPosition": 1800,
          "completedAt": "2025-01-01T01:00:00Z"
        }
      ]
    }
  }
}
```

### ➕ Criar Curso
```http
POST /api/courses
```

**Headers:**
```
Authorization: Bearer {token}
```

**Descrição:** Cria um novo curso. Se não fornecer productId, um produto será criado automaticamente.

**Body:**
```json
{
  "title": "Novo Curso de React",
  "description": "Aprenda React do zero ao avançado",
  "thumbnailUrl": "https://example.com/react-course.jpg",
  "trailerUrl": "https://example.com/trailer.mp4",
  "durationMinutes": 800,
  "level": "beginner",
  "category": "programacao",
  "tags": ["react", "javascript", "frontend"],
  "settings": {
    "allowDownloads": false,
    "certificateEnabled": true
  },
  "status": "draft",
  "productId": "product-uuid"
}
```

**Resposta:**
```json
{
  "success": true,
  "message": "Curso criado com sucesso",
  "course": {
    "id": "new-course-uuid",
    "title": "Novo Curso de React",
    "description": "Aprenda React do zero ao avançado",
    "thumbnailUrl": "https://example.com/react-course.jpg",
    "trailerUrl": "https://example.com/trailer.mp4",
    "durationMinutes": 800,
    "level": "beginner",
    "category": "programacao",
    "tags": ["react", "javascript", "frontend"],
    "settings": {
      "allowDownloads": false,
      "certificateEnabled": true
    },
    "status": "draft",
    "createdAt": "2025-01-01T00:00:00Z"
  }
}
```

### ✏️ Atualizar Curso
```http
PUT /api/courses/{course_id}
```

**Headers:**
```
Authorization: Bearer {token}
```

**Descrição:** Atualiza curso (apenas se for do instrutor logado)

**Body:**
```json
{
  "title": "Título Atualizado",
  "description": "Nova descrição",
  "level": "intermediate",
  "status": "published"
}
```

### ❌ Excluir Curso
```http
DELETE /api/courses/{course_id}
```

**Headers:**
```
Authorization: Bearer {token}
```

**Descrição:** Exclui curso completo (apenas se for do instrutor logado)

**Resposta:**
```json
{
  "success": true,
  "message": "Curso deletado com sucesso"
}
```

---

## 📘 Módulos do Curso

### ➕ Criar Módulo
```http
POST /api/courses/{course_id}/modules
```

**Headers:**
```
Authorization: Bearer {token}
```

**Body:**
```json
{
  "title": "Novo Módulo",
  "description": "Descrição do módulo",
  "orderIndex": 2,
  "durationMinutes": 120,
  "isFree": false
}
```

**Resposta:**
```json
{
  "success": true,
  "message": "Módulo criado com sucesso",
  "module": {
    "id": "module-uuid",
    "title": "Novo Módulo",
    "description": "Descrição do módulo",
    "orderIndex": 2,
    "durationMinutes": 120,
    "isFree": false,
    "createdAt": "2025-01-01T00:00:00Z"
  }
}
```

### ✏️ Atualizar Módulo
```http
PUT /api/courses/{course_id}/modules/{module_id}
```

**Headers:**
```
Authorization: Bearer {token}
```

**Body:**
```json
{
  "title": "Título Atualizado",
  "description": "Nova descrição",
  "orderIndex": 1,
  "isFree": true
}
```

### ❌ Excluir Módulo
```http
DELETE /api/courses/{course_id}/modules/{module_id}
```

**Headers:**
```
Authorization: Bearer {token}
```

---

## 📝 Aulas do Curso

### ➕ Criar Aula
```http
POST /api/courses/{course_id}/modules/{module_id}/lessons
```

**Headers:**
```
Authorization: Bearer {token}
```

**Body:**
```json
{
  "title": "Nova Aula",
  "description": "Descrição da aula",
  "contentType": "video",
  "contentUrl": "https://example.com/video.mp4",
  "contentData": {
    "videoProvider": "vimeo",
    "videoId": "123456789"
  },
  "durationMinutes": 25,
  "orderIndex": 1,
  "isFree": false,
  "transcript": "Transcrição da aula..."
}
```

**Resposta:**
```json
{
  "success": true,
  "message": "Aula criada com sucesso",
  "lesson": {
    "id": "lesson-uuid",
    "title": "Nova Aula",
    "description": "Descrição da aula",
    "contentType": "video",
    "contentUrl": "https://example.com/video.mp4",
    "contentData": {
      "videoProvider": "vimeo",
      "videoId": "123456789"
    },
    "durationMinutes": 25,
    "orderIndex": 1,
    "isFree": false,
    "transcript": "Transcrição da aula...",
    "createdAt": "2025-01-01T00:00:00Z"
  }
}
```

### ✏️ Atualizar Aula
```http
PUT /api/courses/{course_id}/modules/{module_id}/lessons/{lesson_id}
```

**Headers:**
```
Authorization: Bearer {token}
```

### ❌ Excluir Aula
```http
DELETE /api/courses/{course_id}/modules/{module_id}/lessons/{lesson_id}
```

**Headers:**
```
Authorization: Bearer {token}
```

---

## 🎓 Matrículas e Progresso

### 📝 Matricular no Curso
```http
POST /api/courses/{course_id}/enroll
```

**Headers:**
```
Authorization: Bearer {token}
```

**Body:**
```json
{
  "studentId": "student-uuid",
  "orderId": "order-uuid"
}
```

**Resposta:**
```json
{
  "success": true,
  "message": "Matrícula realizada com sucesso",
  "enrollment": {
    "id": "enrollment-uuid",
    "courseId": "course-uuid",
    "studentId": "student-uuid",
    "orderId": "order-uuid",
    "enrolledAt": "2025-01-01T00:00:00Z",
    "status": "active",
    "progressPercentage": 0
  }
}
```

### 📊 Atualizar Progresso da Aula
```http
PUT /api/courses/{course_id}/progress
```

**Headers:**
```
Authorization: Bearer {token}
```

**Body:**
```json
{
  "studentId": "student-uuid",
  "lessonId": "lesson-uuid",
  "progressPercentage": 85,
  "timeSpentMinutes": 22,
  "lastPosition": 1320,
  "status": "in_progress"
}
```

**Resposta:**
```json
{
  "success": true,
  "message": "Progresso atualizado com sucesso",
  "progress": {
    "lessonId": "lesson-uuid",
    "status": "in_progress",
    "progressPercentage": 85,
    "timeSpentMinutes": 22,
    "lastPosition": 1320,
    "completedAt": null
  },
  "courseProgress": {
    "percentage": 45.5,
    "completedLessons": 5,
    "totalLessons": 11
  }
}
```

### 📋 Listar Matrículas do Curso
```http
GET /api/courses/{course_id}/enrollments
```

**Headers:**
```
Authorization: Bearer {token}
```

**Descrição:** Lista todas as matrículas de um curso (para instrutor)

**Resposta:**
```json
{
  "success": true,
  "enrollments": [
    {
      "id": "enrollment-uuid",
      "enrolledAt": "2025-01-01T00:00:00Z",
      "status": "active",
      "progressPercentage": 29,
      "completedAt": null,
      "student": {
        "id": "student-uuid",
        "name": "João Silva",
        "email": "aluno@teste.com"
      },
      "order": {
        "id": "order-uuid",
        "amount": 497.00,
        "created_at": "2025-01-01T00:00:00Z"
      }
    }
  ]
}
```

### 🔍 Verificar Status de Matrícula
```http
GET /api/courses/enrollment-status/{courseId}/{studentEmail}
```

**Headers:**
```
Authorization: Bearer {token}
```

**Descrição:** Verifica se um aluno está matriculado em um curso

**Resposta (Matriculado):**
```json
{
  "enrolled": true,
  "hasAccount": true,
  "enrollment": {
    "id": "enrollment-uuid",
    "status": "active",
    "enrolledAt": "2025-01-01T00:00:00Z",
    "progressPercentage": 29,
    "accessData": {}
  },
  "student": {
    "id": "student-uuid",
    "name": "João Silva",
    "email": "aluno@teste.com"
  },
  "course": {
    "id": "course-uuid",
    "title": "Curso Completo de JavaScript Moderno",
    "status": "published"
  },
  "order": {
    "id": "order-uuid",
    "status": "paid",
    "created_at": "2025-01-01T00:00:00Z"
  }
}
```

**Resposta (Não Matriculado):**
```json
{
  "enrolled": false,
  "hasAccount": true,
  "student": {
    "id": "student-uuid",
    "name": "João Silva",
    "email": "aluno@teste.com"
  },
  "message": "Não matriculado neste curso"
}
```

**Body:**
```json
{
  "lesson_id": "lesson-uuid",
  "completed": true,
  "watch_time_seconds": 900
}
```

### 👥 Listar Matrículas
```http
GET /api/courses/{id}/enrollments
```

**Resposta:**
```json
{
  "success": true,
  "enrollments": [
    {
      "id": "enrollment-uuid",
      "student": {
        "id": "student-uuid",
        "name": "Aluno",
        "email": "aluno@email.com"
      },
      "enrolledAt": "2025-01-01T00:00:00Z",
      "progress": 45.5,
      "status": "active",
      "lastAccess": "2025-01-07T10:30:00Z"
    }
  ]
}
```

### ✅ Verificar Status de Matrícula
```http
GET /api/courses/enrollment-status/{courseId}/{studentEmail}
```

**Resposta:**
```json
{
  "enrolled": true,
  "enrollment": {
    "id": "enrollment-uuid",
    "progress": 35.5,
    "status": "active",
    "enrolledAt": "2025-01-01T00:00:00Z"
  }
}
```

---

## 🤝 Co-Produtores/Afiliados

Sistema de parcerias e comissões para co-produtores e afiliados.

### ➕ Adicionar Parceiro
```http
POST /api/partners
```

**Body:**
```json
{
  "productId": "product-uuid",
  "partnerId": "user-uuid",
  "partnerType": "affiliate",
  "commissionPercentage": 25.5,
  "settings": {
    "canEditProduct": false,
    "accessLevel": "basic"
  }
}
```

**Tipos de Parceiro:**
- `co_producer`: Co-produtor (pode editar produto)
- `affiliate`: Afiliado (apenas vendas)

**Resposta:**
```json
{
  "success": true,
  "message": "Parceiro adicionado com sucesso",
  "partnership": {
    "id": "partnership-uuid",
    "product_id": "product-uuid",
    "partner_id": "user-uuid",
    "partner_type": "affiliate",
    "partner_code": "ABC12345",
    "commission_percentage": 25.5,
    "status": "active",
    "created_at": "2025-01-01T00:00:00Z"
  }
}
```

### 🎯 Listar Parceiros de Produto
```http
GET /api/partners/product/{productId}
```

**Resposta:**
```json
{
  "success": true,
  "partners": [
    {
      "id": "partnership-uuid",
      "partner_type": "affiliate",
      "partner_code": "ABC12345",
      "commission_percentage": 25.5,
      "status": "active",
      "partner": {
        "id": "user-uuid",
        "name": "João Afiliado",
        "email": "joao@email.com"
      },
      "stats": {
        "totalSales": 15,
        "totalRevenue": 1485.00,
        "totalCommission": 371.25,
        "conversionRate": 5.2
      },
      "created_at": "2025-01-01T00:00:00Z"
    }
  ]
}
```

### 👤 Listar Parcerias do Usuário
```http
GET /api/partners/user/{userId}
```

**Resposta:**
```json
{
  "success": true,
  "partnerships": [
    {
      "id": "partnership-uuid",
      "partner_code": "DEF67890",
      "partner_type": "co_producer",
      "commission_percentage": 50.0,
      "status": "active",
      "product": {
        "id": "product-uuid",
        "name": "Produto Parceiro",
        "price": 197.00,
        "image": "https://exemplo.com/produto.jpg"
      },
      "stats": {
        "totalSales": 8,
        "totalRevenue": 1576.00,
        "totalCommission": 788.00
      }
    }
  ]
}
```

### ✏️ Atualizar Parceria
```http
PUT /api/partners/{id}
```

**Body:**
```json
{
  "commission_percentage": 30.0,
  "status": "active",
  "settings": {
    "canEditProduct": true,
    "accessLevel": "advanced"
  }
}
```

### ❌ Remover Parceiro
```http
DELETE /api/partners/{id}
```

**Resposta:**
```json
{
  "success": true,
  "message": "Parceria removida com sucesso"
}
```

### 💰 Listar Comissões
```http
GET /api/partners/{id}/commissions
```

**Query Parameters:**
- `status`: pending, approved, paid, cancelled
- `period`: daily, weekly, monthly
- `start_date`: Data inicial (YYYY-MM-DD)
- `end_date`: Data final (YYYY-MM-DD)

**Resposta:**
```json
{
  "success": true,
  "commissions": [
    {
      "id": "commission-uuid",
      "order_id": "order-uuid",
      "amount": 24.75,
      "commission_percentage": 25.0,
      "status": "approved",
      "created_at": "2025-01-01T00:00:00Z",
      "paid_at": null,
      "order": {
        "id": "order-uuid",
        "customer_name": "Cliente",
        "amount": 99.00,
        "status": "paid"
      }
    }
  ],
  "summary": {
    "totalCommissions": 247.50,
    "pendingAmount": 49.50,
    "approvedAmount": 123.75,
    "paidAmount": 74.25,
    "totalOrders": 10
  }
}
```

### 🔍 Buscar por Código de Parceiro
```http
GET /api/partners/code/{partnerCode}
```

**Exemplo:**
```http
GET /api/partners/code/ABC12345
```

**Resposta:**
```json
{
  "success": true,
  "partnership": {
    "id": "partnership-uuid",
    "partner_code": "ABC12345",
    "partner_type": "affiliate",
    "commission_percentage": 25.5,
    "status": "active",
    "partner": {
      "id": "user-uuid",
      "name": "João Afiliado"
    },
    "product": {
      "id": "product-uuid",
      "name": "Produto",
      "price": 99.00,
      "slug": "produto-incrivel"
    }
  }
}
```

### ✅ Validar Código de Parceiro
```http
POST /api/partners/validate-code
```

**Body:**
```json
{
  "partnerCode": "ABC12345",
  "productId": "product-uuid"
}
```

**Resposta:**
```json
{
  "valid": true,
  "partnership": {
    "id": "partnership-uuid",
    "partner_code": "ABC12345",
    "commission_percentage": 25.5
  }
}
```

### 💼 Processar Comissões
```http
POST /api/partners/commissions/process
```

**Body:**
```json
{
  "orderId": "order-uuid",
  "partnerCode": "ABC12345"
}
```

### ✅ Aprovar Comissão
```http
PUT /api/partners/commissions/{id}/approve
```

### 💳 Marcar Comissão como Paga
```http
PUT /api/partners/commissions/{id}/pay
```

**Body:**
```json
{
  "payment_method": "pix",
  "payment_reference": "PIX123456",
  "notes": "Pagamento via PIX"
}
```

### ❌ Cancelar Comissão
```http
PUT /api/partners/commissions/{id}/cancel
```

**Body:**
```json
{
  "reason": "Produto devolvido"
}
```

### 📊 Estatísticas de Comissões
```http
GET /api/partners/commissions/stats/{partnerId}
```

**Headers:**
```
Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "success": true,
  "stats": {
    "totalEarned": 1247.50,
    "pendingAmount": 125.00,
    "thisMonth": 287.50,
    "lastMonth": 156.25,
    "averageCommission": 31.19,
    "totalOrders": 40,
    "conversionRate": 4.8,
    "topProducts": [
      {
        "product_id": "product-uuid",
        "product_name": "Produto Top",
        "sales": 15,
        "revenue": 1485.00,
        "commission": 371.25
      }
    ]
  }
}
```

---

## 💰 PIX (Sistema Unificado)

Sistema unificado de pagamentos PIX integrado com múltiplas adquirentes. Não há mais sistema de split - todos os pagamentos são diretos para o vendedor.

### 🎯 Criar Pagamento PIX
- **URL:** `/api/pix/create`
- **Método:** `POST`
- **Descrição:** Cria um novo pagamento PIX unificado
- **Autenticação:** Não necessária (checkout público)

**Body:**
```json
{
  "orderId": "order_123456",
  "amount": 199.90,
  "customerEmail": "cliente@email.com",
  "customerName": "João Silva",
  "customerDocument": "12345678901",
  "productId": 1,
  "description": "Curso de Marketing Digital",
  "expiresInMinutes": 30,
  "metadata": {
    "source": "landing_page",
    "campaign": "black_friday"
  }
}
```

**Sucesso:** `201 Created`
```json
{
  "success": true,
  "message": "PIX criado com sucesso",
  "data": {
    "pixId": "pix_abc123def456",
    "orderId": "order_123456",
    "qrCodeText": "00020126580014BR.GOV.BCB.PIX...",
    "qrCodeImage": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...",
    "pixKey": "pix@empresa.com",
    "amount": 199.90,
    "expiresAt": "2024-01-10T16:00:00.000Z",
    "status": "pending",
    "acquirer": "stone",
    "createdAt": "2024-01-10T15:30:00.000Z"
  }
}
```

### 📊 Consultar Status
- **URL:** `/api/pix/status/:orderId`
- **Método:** `GET`
- **Descrição:** Consulta status de um pagamento PIX
- **Autenticação:** Não necessária

**Sucesso:** `200 OK`
```json
{
  "success": true,
  "data": {
    "orderId": "order_123456",
    "pixId": "pix_abc123def456",
    "status": "paid", // "pending", "paid", "expired", "cancelled"
    "amount": 199.90,
    "paidAmount": 199.90,
    "paidAt": "2024-01-10T15:45:00.000Z",
    "acquirer": "stone",
    "transactionId": "txn_stone_987654321",
    "pixEndToEndId": "E12345678202401101545123456789",
    "customerInfo": {
      "name": "João Silva",
      "document": "12345678901"
    }
  }
}
```

### 🔔 Webhook PIX
- **URL:** `/api/pix/webhook/:acquirerSlug`
- **Método:** `POST`
- **Descrição:** Recebe notificações das adquirentes
- **Autenticação:** Validação por assinatura

**Parâmetros de Rota:**
- `acquirerSlug`: stone, pagar-me, mercado-pago, asaas, etc.

**Body (exemplo Stone):**
```json
{
  "id": "txn_stone_987654321",
  "status": "paid",
  "amount": 19990,
  "paid_amount": 19990,
  "metadata": {
    "order_id": "order_123456"
  },
  "pix": {
    "end_to_end_id": "E12345678202401101545123456789"
  },
  "created_at": "2024-01-10T15:45:00.000Z"
}
```

**Sucesso:** `200 OK`
```json
{
  "success": true,
  "message": "Webhook processado com sucesso",
  "data": {
    "orderId": "order_123456",
    "previousStatus": "pending",
    "newStatus": "paid",
    "processedAt": "2024-01-10T15:45:00.000Z"
  }
}
```

### 🏦 Adquirentes Disponíveis
- **URL:** `/api/pix/acquirers`
- **Método:** `GET`
- **Descrição:** Lista adquirentes PIX disponíveis
- **Autenticação:** Obrigatória (JWT)

**Sucesso:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "slug": "stone",
      "name": "Stone",
      "isActive": true,
      "isDefault": true,
      "fees": {
        "pix": 1.99,
        "type": "percentage"
      },
      "limits": {
        "min": 1.00,
        "max": 50000.00
      }
    },
    {
      "slug": "pagar-me",
      "name": "Pagar.me",
      "isActive": true,
      "isDefault": false,
      "fees": {
        "pix": 2.49,
        "type": "percentage"
      },
      "limits": {
        "min": 5.00,
        "max": 100000.00
      }
    }
  ]
}
```

### ⚡ Health Check
- **URL:** `/api/pix/health`
- **Método:** `GET`
- **Descrição:** Verifica saúde do sistema PIX
- **Autenticação:** Obrigatória (JWT)

**Sucesso:** `200 OK`
```json
{
  "success": true,
  "data": {
    "systemStatus": "healthy",
    "acquirers": [
      {
        "slug": "stone",
        "status": "online",
        "responseTime": 250,
        "lastCheck": "2024-01-10T15:30:00.000Z"
      },
      {
        "slug": "pagar-me", 
        "status": "online",
        "responseTime": 180,
        "lastCheck": "2024-01-10T15:30:00.000Z"
      }
    ],
    "stats": {
      "totalTransactions": 1500,
      "successRate": 98.5,
      "averageResponseTime": 215
    }
  }
}
```

---

## 💳 PAGAMENTO COM CARTÃO

Sistema completo de processamento de pagamentos com cartão de crédito/débito usando múltiplas adquirentes.

### 💳 Criar Pagamento
- **URL:** `/api/card-payment/create`
- **Método:** `POST`
- **Descrição:** Cria um novo pagamento com cartão
- **Autenticação:** Não necessária (checkout público)

**Body:**
```json
{
  "orderId": "order_123456",
  "amount": 299.90,
  "installments": 3,
  "customerInfo": {
    "name": "João Silva",
    "email": "joao@email.com",
    "document": "12345678901",
    "phone": "11999999999"
  },
  "billingAddress": {
    "street": "Rua das Flores, 123",
    "neighborhood": "Centro",
    "city": "São Paulo",
    "state": "SP",
    "zipCode": "01234567",
    "country": "BR"
  },
  "cardData": {
    "number": "4111111111111111",
    "holderName": "JOAO SILVA",
    "expirationMonth": "12",
    "expirationYear": "2025",
    "cvv": "123"
  },
  "productId": 1,
  "description": "Curso Avançado de Marketing",
  "metadata": {
    "source": "checkout_page",
    "campaign": "summer_sale"
  }
}
```

**Sucesso:** `201 Created`
```json
{
  "success": true,
  "message": "Pagamento criado com sucesso",
  "data": {
    "chargeId": "charge_abc123def456",
    "orderId": "order_123456",
    "status": "authorized", // "authorized", "paid", "failed"
    "amount": 299.90,
    "installments": 3,
    "installmentAmount": 99.97,
    "acquirer": "stone",
    "transactionId": "txn_stone_123456789",
    "authorizationCode": "AUTH123456",
    "nsu": "000123456",
    "receipt": {
      "acquirerName": "Stone",
      "merchantId": "MERCHANT123",
      "terminal": "TERM001",
      "authCode": "AUTH123456"
    },
    "createdAt": "2024-01-10T15:30:00.000Z"
  }
}
```

### ✅ Validar Cartão
- **URL:** `/api/card-payment/validate`
- **Método:** `POST`
- **Descrição:** Valida dados do cartão sem processar pagamento
- **Autenticação:** Não necessária

**Body:**
```json
{
  "cardNumber": "4111111111111111",
  "expirationMonth": "12",
  "expirationYear": "2025",
  "cvv": "123",
  "holderName": "JOAO SILVA"
}
```

**Sucesso:** `200 OK`
```json
{
  "success": true,
  "message": "Cartão válido",
  "data": {
    "isValid": true,
    "brand": "visa",
    "type": "credit",
    "bin": "411111",
    "lastFour": "1111",
    "issuer": "Banco do Brasil",
    "country": "BR"
  }
}
```

### 📊 Status da Transação
- **URL:** `/api/card-payment/status/:transactionId`
- **Método:** `GET`
- **Descrição:** Consulta status de uma transação
- **Autenticação:** Não necessária

**Sucesso:** `200 OK`
```json
{
  "success": true,
  "data": {
    "transactionId": "txn_stone_123456789",
    "chargeId": "charge_abc123def456",
    "orderId": "order_123456",
    "status": "paid",
    "amount": 299.90,
    "paidAmount": 299.90,
    "installments": 3,
    "acquirer": "stone",
    "paymentMethod": "credit_card",
    "cardBrand": "visa",
    "cardLastFour": "1111",
    "authorizationCode": "AUTH123456",
    "nsu": "000123456",
    "createdAt": "2024-01-10T15:30:00.000Z",
    "paidAt": "2024-01-10T15:30:15.000Z",
    "liquidation": {
      "expectedDate": "2024-01-11",
      "netAmount": 289.90,
      "fee": 10.00
    }
  }
}
```

### 💰 Capturar Pagamento
- **URL:** `/api/card-payment/capture/:chargeId`
- **Método:** `POST`
- **Descrição:** Captura um pagamento autorizado
- **Autenticação:** Obrigatória (JWT)

**Body:**
```json
{
  "amount": 299.90 // opcional, se menor que autorizado = captura parcial
}
```

**Sucesso:** `200 OK`
```json
{
  "success": true,
  "message": "Pagamento capturado com sucesso",
  "data": {
    "chargeId": "charge_abc123def456",
    "status": "paid",
    "capturedAmount": 299.90,
    "capturedAt": "2024-01-10T15:45:00.000Z",
    "liquidation": {
      "expectedDate": "2024-01-11",
      "netAmount": 289.90,
      "fee": 10.00
    }
  }
}
```

### ❌ Cancelar Pagamento
- **URL:** `/api/card-payment/void/:chargeId`
- **Método:** `POST`
- **Descrição:** Cancela um pagamento autorizado ou estorna um pago
- **Autenticação:** Obrigatória (JWT)

**Body:**
```json
{
  "reason": "Solicitação do cliente",
  "amount": 299.90 // opcional, para estorno parcial
}
```

**Sucesso:** `200 OK`
```json
{
  "success": true,
  "message": "Pagamento cancelado com sucesso",
  "data": {
    "chargeId": "charge_abc123def456",
    "status": "voided",
    "voidedAmount": 299.90,
    "voidedAt": "2024-01-10T16:00:00.000Z",
    "refund": {
      "refundId": "refund_123456",
      "expectedDate": "2024-01-15",
      "method": "original_payment"
    }
  }
}
```

### 🔔 Webhook Pagamentos
- **URL:** `/api/card-payment/webhook`
- **Método:** `POST`
- **Descrição:** Recebe notificações de mudanças de status
- **Autenticação:** Validação por assinatura

**Body:**
```json
{
  "event": "transaction.paid",
  "data": {
    "id": "txn_stone_123456789",
    "status": "paid",
    "amount": 29990,
    "metadata": {
      "order_id": "order_123456"
    },
    "created_at": "2024-01-10T15:30:00.000Z"
  }
}
```

**Sucesso:** `200 OK`
```json
{
  "success": true,
  "message": "Webhook processado com sucesso"
}
```

### 🧪 Teste de Integração
- **URL:** `/api/card-payment/test`
- **Método:** `GET`
- **Descrição:** Testa conectividade com adquirentes
- **Autenticação:** Obrigatória (JWT)

**Sucesso:** `200 OK`
```json
{
  "success": true,
  "data": {
    "stone": {
      "status": "online",
      "responseTime": 180,
      "lastTest": "2024-01-10T15:30:00.000Z"
    },
    "pagar_me": {
      "status": "online", 
      "responseTime": 220,
      "lastTest": "2024-01-10T15:30:00.000Z"
    }
  }
}
```

### 🔬 Simular Webhook
- **URL:** `/api/card-payment/simulate-webhook`
- **Método:** `POST`
- **Descrição:** Simula webhook para testes (apenas em desenvolvimento)
- **Autenticação:** Obrigatória (JWT)

**Body:**
```json
{
  "transactionId": "txn_test_123456",
  "event": "transaction.paid",
  "status": "paid"
}
```

---

## 🎨 TEMPLATES & MODELOS

Sistema de gerenciamento de templates para emails, páginas e outros conteúdos reutilizáveis.

### 📋 Listar Templates
- **URL:** `/api/templates`
- **Método:** `GET`
- **Descrição:** Lista todos os templates disponíveis
- **Autenticação:** Obrigatória (JWT)

**Parâmetros Query:**
```
?type=email&category=marketing&isActive=true&limit=20&offset=0
```

**Sucesso:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "name": "Email de Boas-vindas",
      "type": "email",
      "category": "transactional",
      "description": "Template para email de boas-vindas aos novos usuários",
      "isActive": true,
      "usage": {
        "timesUsed": 250,
        "lastUsedAt": "2024-01-10T15:30:00.000Z"
      },
      "createdAt": "2024-01-01T10:00:00.000Z",
      "updatedAt": "2024-01-10T09:00:00.000Z"
    }
  ],
  "pagination": {
    "total": 25,
    "page": 1,
    "limit": 20
  }
}
```

### 🔍 Detalhes do Template
- **URL:** `/api/templates/:id`
- **Método:** `GET`
- **Descrição:** Busca detalhes completos de um template
- **Autenticação:** Obrigatória (JWT)

**Sucesso:** `200 OK`
```json
{
  "success": true,
  "data": {
    "id": 1,
    "name": "Email de Boas-vindas",
    "type": "email",
    "category": "transactional",
    "description": "Template para email de boas-vindas aos novos usuários",
    "content": {
      "subject": "Bem-vindo(a) à {{company_name}}!",
      "html": "<html>...",
      "text": "Bem-vindo(a)...",
      "variables": [
        {
          "name": "user_name",
          "description": "Nome do usuário",
          "required": true
        }
      ]
    },
    "settings": {
      "fromEmail": "noreply@empresa.com",
      "fromName": "Equipe {{company_name}}"
    },
    "isActive": true,
    "usage": {
      "timesUsed": 250,
      "successRate": 98.5
    },
    "createdAt": "2024-01-01T10:00:00.000Z"
  }
}
```

### 🏷️ Templates por Tipo
- **URL:** `/api/templates/type/:type`
- **Método:** `GET`
- **Descrição:** Lista templates de um tipo específico
- **Autenticação:** Obrigatória (JWT)

**Tipos:** `email`, `sms`, `landing_page`, `checkout_page`

### ➕ Criar Template
- **URL:** `/api/templates`
- **Método:** `POST`
- **Descrição:** Cria um novo template
- **Autenticação:** Obrigatória (JWT)

### ✏️ Atualizar Template
- **URL:** `/api/templates/:id`
- **Método:** `PUT`
- **Descrição:** Atualiza um template
- **Autenticação:** Obrigatória (JWT)

### 🗑️ Deletar Template
- **URL:** `/api/templates/:id`
- **Método:** `DELETE`
- **Descrição:** Remove um template
- **Autenticação:** Obrigatória (JWT)

### 📋 Duplicar Template
- **URL:** `/api/templates/:id/duplicate`
- **Método:** `POST`
- **Descrição:** Duplica um template existente
- **Autenticação:** Obrigatória (JWT)

---

## 📊 ANALYTICS & MÉTRICAS

Dashboard com métricas de vendas, usuários e performance detalhada.

### 🎯 Dashboard Principal
- **URL:** `/api/analytics/dashboard`
- **Método:** `GET`
- **Descrição:** Métricas principais do dashboard
- **Autenticação:** Obrigatória (JWT)

**Parâmetros Query:**
```
?period=30d&timezone=America/Sao_Paulo
```

**Sucesso:** `200 OK`
```json
{
  "success": true,
  "data": {
    "summary": {
      "totalRevenue": 125000.50,
      "totalOrders": 850,
      "totalUsers": 1250,
      "conversionRate": 3.2
    },
    "growth": {
      "revenueGrowth": 15.5,
      "ordersGrowth": 22.1,
      "usersGrowth": 18.7
    },
    "charts": {
      "dailyRevenue": [
        {
          "date": "2024-01-01",
          "revenue": 1200.50,
          "orders": 8
        }
      ]
    }
  }
}
```

### 🎯 Métricas do Funil
- **URL:** `/api/analytics/funnel-metrics`
- **Método:** `GET`
- **Descrição:** Análise do funil de conversão
- **Autenticação:** Obrigatória (JWT)

### 📊 Performance dos Produtos
- **URL:** `/api/analytics/product-performance`
- **Método:** `GET`
- **Descrição:** Análise de performance por produto
- **Autenticação:** Obrigatória (JWT)

### 💰 Resumo de Receitas
- **URL:** `/api/analytics/revenue-summary`
- **Método:** `GET`
- **Descrição:** Análise detalhada das receitas
- **Autenticação:** Obrigatória (JWT)

---

## 🔧 FUNIL DE VENDAS

Sistema completo de funil com order bumps, upsells e downsells.

### 🎯 Criar Funil
- **URL:** `/api/funnel/create`
- **Método:** `POST`
- **Descrição:** Cria um novo funil de vendas
- **Autenticação:** Obrigatória (JWT)

### 💰 Order Bump
- **URL:** `/api/funnel/:productId/order-bump`
- **Método:** `GET`
- **Descrição:** Busca order bump de um produto
- **Autenticação:** Não necessária

### 📈 Upsell
- **URL:** `/api/funnel/:orderId/upsell`
- **Método:** `GET`
- **Descrição:** Busca ofertas de upsell para um pedido
- **Autenticação:** Não necessária

### 📉 Downsell
- **URL:** `/api/funnel/:orderId/downsell`
- **Método:** `GET`
- **Descrição:** Busca ofertas de downsell
- **Autenticação:** Não necessária

### 📋 Detalhes do Funil
- **URL:** `/api/funnel/:orderId/details`
- **Método:** `GET`
- **Descrição:** Detalhes completos do funil de um pedido
- **Autenticação:** Obrigatória (JWT)

---

## 📤 SISTEMA DE UPLOAD

Gerenciamento de uploads de vídeos, imagens e outros arquivos.

### 🎥 Upload de Vídeo
- **URL:** `/api/upload/video`
- **Método:** `POST`
- **Descrição:** Faz upload de arquivo de vídeo
- **Autenticação:** Obrigatória (JWT)
- **Content-Type:** `multipart/form-data`

**Form Data:**
```
video: arquivo de vídeo (.mp4, .avi, .mov)
title: "Título do vídeo"
description: "Descrição do vídeo"
```

### 🖼️ Upload de Imagem
- **URL:** `/api/upload/image`
- **Método:** `POST`
- **Descrição:** Faz upload de imagem
- **Autenticação:** Obrigatória (JWT)

### 📊 Informações do Arquivo
- **URL:** `/api/upload/info/:filename`
- **Método:** `GET`
- **Descrição:** Busca informações de um arquivo
- **Autenticação:** Obrigatória (JWT)

### 🗑️ Remover Arquivo
- **URL:** `/api/upload/video/:filename`
- **Método:** `DELETE`
- **Descrição:** Remove arquivo de vídeo
- **Autenticação:** Obrigatória (JWT)

---

## ⚙️ CONFIGURAÇÕES & SISTEMA

Configurações globais e informações do sistema.

### 📊 Status do Sistema
- **URL:** `/api/status`
- **Método:** `GET`
- **Descrição:** Status geral do sistema e APIs
- **Autenticação:** Não necessária

**Sucesso:** `200 OK`
```json
{
  "success": true,
  "data": {
    "status": "online",
    "version": "1.0.0",
    "uptime": "72h 15m 30s",
    "database": {
      "status": "connected",
      "responseTime": 15
    },
    "services": {
      "email": "online",
      "payments": "online",
      "storage": "online"
    },
    "stats": {
      "totalUsers": 1250,
      "totalProducts": 85,
      "totalOrders": 3500,
      "activeConnections": 45
    }
  }
}
```

### ⚙️ Configurações
- **URL:** `/api/settings`
- **Método:** `GET`
- **Descrição:** Configurações do sistema
- **Autenticação:** Obrigatória (JWT)

### 📧 Status Email
- **URL:** `/api/email/status`
- **Método:** `GET`
- **Descrição:** Status do serviço de email
- **Autenticação:** Obrigatória (JWT)

---

## 📋 KYC & VERIFICAÇÃO

Sistema de verificação de identidade (Know Your Customer).

### 📤 Upload KYC
- **URL:** `/api/kyc/upload`
- **Método:** `POST`
- **Descrição:** Upload de documentos KYC
- **Autenticação:** Obrigatória (JWT)

### 📊 Status KYC
- **URL:** `/api/kyc/status`
- **Método:** `GET`
- **Descrição:** Status da verificação KYC
- **Autenticação:** Obrigatória (JWT)

### 📄 Documentos KYC
- **URL:** `/api/kyc/documents`
- **Método:** `GET`
- **Descrição:** Lista documentos KYC do usuário
- **Autenticação:** Obrigatória (JWT)

---

## 💰 PLANOS & ASSINATURAS

Sistema de planos de assinatura e pagamentos recorrentes.

### 📋 Listar Planos
- **URL:** `/api/plans`
- **Método:** `GET`
- **Descrição:** Lista planos disponíveis
- **Autenticação:** Não necessária

### 🔍 Detalhes do Plano
- **URL:** `/api/plans/:planId`
- **Método:** `GET`
- **Descrição:** Detalhes de um plano específico
- **Autenticação:** Não necessária

---

## 🔗 WEBHOOKS

Sistema completo de webhooks para integrações externas.

### 📋 Listar Webhooks
- **URL:** `/api/webhooks`
- **Método:** `GET`
- **Descrição:** Lista todos os webhooks
- **Autenticação:** Obrigatória (JWT)

### ➕ Criar Webhook
- **URL:** `/api/webhooks`
- **Método:** `POST`
- **Descrição:** Cria novo webhook
- **Autenticação:** Obrigatória (JWT)

### ✏️ Atualizar Webhook
- **URL:** `/api/webhooks/:id`
- **Método:** `PUT`
- **Descrição:** Atualiza webhook
- **Autenticação:** Obrigatória (JWT)

### 🧪 Testar Webhook
- **URL:** `/api/webhooks/:id/test`
- **Método:** `POST`
- **Descrição:** Testa webhook
- **Autenticação:** Obrigatória (JWT)

### 📊 Stats Webhooks
- **URL:** `/api/webhooks/stats`
- **Método:** `GET`
- **Descrição:** Estatísticas dos webhooks
- **Autenticação:** Obrigatória (JWT)

---

## 📊 FACEBOOK TRACKING

Sistema completo de rastreamento Facebook Pixel.

### 🔐 Auth Facebook
- **URL:** `/api/facebook/auth/facebook`
- **Método:** `GET`
- **Descrição:** Login OAuth Facebook
- **Autenticação:** Não necessária

### ⚙️ Config Pixel
- **URL:** `/api/facebook/config/pixel`
- **Método:** `POST`
- **Descrição:** Configura Facebook Pixel
- **Autenticação:** Obrigatória (JWT)

### 📈 Track Event
- **URL:** `/api/facebook/track`
- **Método:** `POST`
- **Descrição:** Envia evento para Facebook
- **Autenticação:** Não necessária

### 📊 Status Facebook
- **URL:** `/api/facebook/status`
- **Método:** `GET`
- **Descrição:** Status do sistema Facebook
- **Autenticação:** Obrigatória (JWT)

### 📄 Logs Facebook
- **URL:** `/api/facebook/logs`
- **Método:** `GET`
- **Descrição:** Logs de eventos Facebook
- **Autenticação:** Obrigatória (JWT)

---

## ❌ CÓDIGOS DE ERRO COMUNS

### 📋 Códigos HTTP
- `200` - Sucesso
- `201` - Criado com sucesso
- `400` - Erro na requisição (dados inválidos)
- `401` - Não autorizado (token inválido/ausente)
- `403` - Acesso negado (permissões insuficientes)
- `404` - Recurso não encontrado
- `422` - Dados de entrada inválidos
- `429` - Muitas requisições (rate limit)
- `500` - Erro interno do servidor

### 🚨 Exemplos de Resposta de Erro
```json
{
  "success": false,
  "message": "Token de acesso inválido",
  "error": "INVALID_TOKEN",
  "code": 401
}
```

```json
{
  "success": false,
  "message": "Dados de entrada inválidos",
  "error": "VALIDATION_ERROR",
  "code": 422,
  "details": [
    {
      "field": "email",
      "message": "Email é obrigatório"
    },
    {
      "field": "password",
      "message": "Senha deve ter pelo menos 6 caracteres"
    }
  ]
}
```

---

## 🔒 AUTENTICAÇÃO & SEGURANÇA

### 🎯 JWT Token
- **Header:** `Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...`
- **Expiração:** 24 horas
- **Refresh:** Faça login novamente quando expirar

### 🛡️ Rate Limiting
- **Desenvolvimento:** 1000 requests/15min
- **Produção:** 100 requests/15min
- **Header de resposta:** `X-RateLimit-Remaining`

### 🔐 CORS
- **Origins permitidas:** Configuradas via variáveis de ambiente
- **Métodos:** GET, POST, PUT, DELETE, OPTIONS
- **Headers:** Content-Type, Authorization

### 🔑 2FA (Two-Factor Authentication)
- **Suporte:** Google Authenticator
- **Setup:** `/api/auth/2fa/setup-google`
- **Verificação:** `/api/auth/2fa/verify-google`

---

## 📧 NOTIFICAÇÕES & EMAILS

### 📮 SMTP Configurado
- **Servidor:** Gmail SMTP
- **Autenticação:** App Password
- **TLS:** Habilitado
- **Templates:** Sistema de templates customizáveis

### 📬 Tipos de Email
- **Transacionais:** Boas-vindas, recuperação de senha, confirmações
- **Marketing:** Campanhas, newsletters
- **Sistema:** Notificações de vendas, alertas

### 🎯 Personalização
- **Variáveis:** `{{user_name}}`, `{{product_name}}`, `{{order_id}}`
- **HTML/Text:** Suporte a ambos os formatos
- **Anexos:** Suporte a arquivos PDF, imagens

---

## 💾 BANCO DE DADOS

### 🗃️ PostgreSQL (Neon)
- **Host:** Neon Cloud
- **SSL:** Habilitado
- **Backup:** Automático
- **Migrações:** Sequelize

### 🔄 Models Principais
- **User:** Usuários e autenticação
- **Product:** Produtos e cursos
- **Order:** Pedidos e transações
- **Course:** Sistema de ensino
- **Partner:** Sistema de afiliados

### 🔍 Relacionamentos
- User → Products (1:N)
- Product → Orders (1:N)
- Course → Modules → Lessons
- Partner → Commissions

---

## 🚀 DEPLOYMENT & AMBIENTE

### 🐳 Docker
- **Compose:** Configurado
- **Containers:** App, Database, Redis
- **Volumes:** Persistência de dados
- **Networks:** Isolamento de serviços

### ⚙️ Variáveis de Ambiente
- **JWT_SECRET:** Chave secreta JWT
- **DATABASE_URL:** String conexão PostgreSQL
- **SMTP_:** Configurações de email
- **FACEBOOK_:** Credenciais Facebook

### 📊 Monitoramento
- **Health Checks:** `/api/status`
- **Logs:** Winston configurado
- **Métricas:** Endpoint de analytics

---

## 📞 SUPORTE & CONTATO

### 🆘 Em caso de problemas:
1. Verifique os logs da aplicação
2. Confirme as variáveis de ambiente
3. Teste a conectividade do banco
4. Valide as configurações de SMTP

### 📋 Checklist de Deploy:
- [x] Variáveis de ambiente configuradas
- [x] Banco de dados acessível
- [x] SMTP funcionando
- [x] JWT_SECRET definido
- [x] CORS configurado para produção
- [x] Rate limiting configurado

---

*Documentação gerada automaticamente - Sistema Backend v1.0.0*
*Última atualização: Janeiro 2024*

---

## 💰 PIX

Sistema PIX unificado com seleção manual ou automática de adquirentes.

### 🎯 Criar PIX
```http
POST /api/pix/create
```

**Body:**
```json
{
  "productId": "product-uuid",
  "orderId": "order-uuid",
  "customer": {
    "name": "João Silva",
    "email": "joao@email.com",
    "phone": "11999999999",
    "document": "123.456.789-00"
  },
  "acquirerSlug": "pagseguro"
}
```

**Parâmetros:**
- `productId` (obrigatório): ID do produto
- `orderId` (opcional): ID do pedido existente
- `customer` (obrigatório): Dados do cliente
- `acquirerSlug` (opcional): Força uso de adquirente específica

**Resposta:**
```json
{
  "success": true,
  "message": "PIX criado com sucesso",
  "data": {
    "orderId": "order-uuid",
    "paymentId": "pix_abc123",
    "qrCode": "00020126580014br.gov.bcb.pix...",
    "pixKey": "chave-pix@exemplo.com",
    "pixCopyPaste": "00020126580014br.gov.bcb.pix...",
    "amount": 99.90,
    "currency": "BRL",
    "description": "Pagamento - Produto Exemplo",
    "expiresAt": "2025-01-01T01:00:00Z",
    "acquirer": {
      "name": "PagSeguro",
      "slug": "pagseguro"
    },
    "status": "pending",
    "createdAt": "2025-01-01T00:00:00Z",
    "checkStatusUrl": "/api/pix/status/order-uuid",
    "webhookUrl": "/api/pix/webhook/pagseguro"
  }
}
```

### 🔍 Verificar Status
```http
GET /api/pix/status/{orderId}
```

**Exemplo:**
```http
GET /api/pix/status/12345678-1234-5678-9abc-123456789012
```

### 🎣 Webhook PIX
```http
POST /api/pix/webhook/{acquirerSlug}
```

**Exemplos:**
- `POST /api/pix/webhook/pagseguro`
- `POST /api/pix/webhook/mercadopago`
- `POST /api/pix/webhook/stripe`

### 🏦 Listar Adquirentes
```http
GET /api/pix/acquirers
```

### 🏥 Health Check PIX
```http
GET /api/pix/health
```

---

## 💳 Pagamento com Cartão

### 💳 Processar Pagamento
```http
POST /api/card-payment/create
```

### ✅ Validar Cartão
```http
POST /api/card-payment/validate
```

### 🔍 Status da Transação
```http
GET /api/card-payment/status/{transactionId}
```

### 📋 Capturar Pagamento
```http
POST /api/card-payment/capture/{chargeId}
```

### ❌ Cancelar Pagamento
```http
POST /api/card-payment/void/{chargeId}
```

### 🎣 Webhook Cartão
```http
POST /api/card-payment/webhook
```

---

## 📊 Rastreamento Facebook

### 🔐 Autenticação Facebook
```http
GET /api/facebook/auth/facebook
```

### 🔄 Callback Facebook
```http
GET /api/facebook/auth/facebook/callback
```

### ⚙️ Configurar Pixel
```http
POST /api/facebook/config/pixel
```

### 📊 Status do Rastreamento
```http
GET /api/facebook/status
```

### 📈 Enviar Evento
```http
POST /api/facebook/track
```

### 🧪 Teste de Evento
```http
POST /api/facebook/test-event
```

### 📋 Logs do Facebook
```http
GET /api/facebook/logs
```

---

## 🔗 Webhooks

### 📋 Listar Webhooks
```http
GET /api/webhooks
```

**Headers:**
```
Authorization: Bearer {token}
```

### ➕ Criar Webhook
```http
POST /api/webhooks
```

**Headers:**
```
Authorization: Bearer {token}
```

### ✏️ Atualizar Webhook
```http
PUT /api/webhooks/{id}
```

**Headers:**
```
Authorization: Bearer {token}
```

### ❌ Excluir Webhook
```http
DELETE /api/webhooks/{id}
```

**Headers:**
```
Authorization: Bearer {token}
```

### 🧪 Testar Webhook
```http
POST /api/webhooks/{id}/test
```

**Headers:**
```
Authorization: Bearer {token}
```

### 📊 Estatísticas de Webhooks
```http
GET /api/webhooks/stats
```

**Headers:**
```
Authorization: Bearer {token}
```

---

## ⚙️ Configurações

### 📋 Listar Configurações
```http
GET /api/settings
```

**Headers:**
```
Authorization: Bearer {token}
```

### ✏️ Atualizar Configurações
```http
PUT /api/settings
```

**Headers:**
```
Authorization: Bearer {token}
```

### 🎯 Buscar Configuração Específica
```http
GET /api/settings/{key}
```

**Headers:**
```
Authorization: Bearer {token}
```

### ❌ Excluir Configuração
```http
DELETE /api/settings/{key}
```

**Headers:**
```
Authorization: Bearer {token}
```

---

## 📋 KYC & Verificação

### 📤 Upload de Documentos
```http
POST /api/kyc/upload
```

### 📊 Status KYC
```http
GET /api/kyc/status
```

### ✏️ Atualizar Status
```http
PUT /api/kyc/update-status
```

### 📂 Documentos
```http
GET /api/kyc/documents
```

---

## 💰 Planos & Assinaturas

### 📋 Listar Planos
```http
GET /api/plans
```

### 🎯 Buscar Plano
```http
GET /api/plans/{planId}
```

---

## 📈 Analytics

### 📊 Dashboard
```http
GET /api/analytics/dashboard
```

### 🔄 Métricas de Funil
```http
GET /api/analytics/funnel-metrics
```

### 🏆 Performance de Produtos
```http
GET /api/analytics/product-performance
```

### 💰 Resumo de Receita
```http
GET /api/analytics/revenue-summary
```

---

## 🎨 Templates

### 📋 Listar Templates
```http
GET /api/templates
```

### 🎯 Buscar Template
```http
GET /api/templates/{id}
```

### ➕ Criar Template
```http
POST /api/templates
```

### ✏️ Atualizar Template
```http
PUT /api/templates/{id}
```

### ❌ Excluir Template
```http
DELETE /api/templates/{id}
```

---

## 🔧 Sistema

### 🏥 Status do Sistema
```http
GET /api/status
```

**Resposta:**
```json
{
  "status": "healthy",
  "timestamp": "2025-01-01T12:00:00Z",
  "version": "1.0.0",
  "environment": "production",
  "services": {
    "database": {
      "status": "connected",
      "type": "PostgreSQL",
      "host": "neon.tech"
    },
    "email": {
      "status": "configured",
      "provider": "SMTP"
    },
    "storage": {
      "status": "available",
      "type": "Local/CloudFlare"
    }
  },
  "stats": {
    "totalUsers": 1250,
    "totalProducts": 450,
    "totalOrders": 2340,
    "totalRevenue": 234500.00
  }
}
```

---

## 🔒 Autenticação e Segurança

### Headers de Autenticação
Para endpoints que requerem autenticação, inclua o header:
```
Authorization: Bearer {jwt_token}
```

### Rate Limiting
- **Produção**: 100 requests por janela de tempo
- **Desenvolvimento**: Sem limites

### Códigos de Status HTTP
- `200` - Sucesso
- `201` - Criado com sucesso
- `400` - Dados inválidos
- `401` - Não autorizado
- `403` - Proibido
- `404` - Não encontrado
- `409` - Conflito
- `422` - Dados não processáveis
- `429` - Muitas requisições
- `500` - Erro interno do servidor

### Formato de Erro Padrão
```json
{
  "success": false,
  "error": "Mensagem de erro",
  "message": "Detalhes adicionais",
  "code": "ERROR_CODE",
  "timestamp": "2025-01-01T12:00:00Z"
}
```

---

## 🚀 URLs e Configurações

**Base URL Local**: `http://localhost:5000`
**Frontend URL**: `http://localhost:3000`
**Banco de Dados**: PostgreSQL (Azure)
**CORS**: Configurado para frontend
**Rate Limit**: 100 req/window em produção

---

## 🔒 Resumo das Atualizações de Segurança

### ⚡ Mudanças Implementadas em 07/08/2025:

#### 🛡️ **Rotas Protegidas com Autenticação JWT:**
- **📧 Pedidos**: `GET /api/orders/customer/{email}` - Agora requer token
- **🎓 Cursos**: Todas as operações CRUD de módulos, aulas, matrículas e progresso
- **⚙️ Configurações**: Todas as rotas `/api/settings` protegidas
- **🔗 Webhooks**: Todas as operações CRUD protegidas
- **📊 Estatísticas**: `/api/products/{id}/stats`, `/api/partners/commissions/stats/{partnerId}`
- **📄 Dados Completos**: `/api/products/{id}/full`

#### 🔑 **Como Autenticar:**
```bash
# 1. Fazer login
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"seu@email.com","password":"suasenha"}'

# 2. Usar o token nas requisições
curl -X GET http://localhost:5000/api/orders/customer/cliente@teste.com \
  -H "Authorization: Bearer SEU_JWT_TOKEN"
```

#### ✅ **Validação Completa:**
- **Testes realizados**: Rotas retornam erro 401 sem token
- **Funcionalidade confirmada**: Rotas funcionam corretamente com token válido
- **Segurança garantida**: Dados sensíveis protegidos contra acesso não autorizado

#### 🎯 **Resultado:**
O backend está **100% seguro** para uso em produção com proteção robusta contra acessos não autorizados a dados sensíveis de clientes, cursos, configurações e estatísticas.

---

**✅ Documentação Completa - CheckoutPro Backend API v3.0 - NOVA IMPLEMENTAÇÃO COMPLETA**

---

# 🆕 NOVAS FUNCIONALIDADES IMPLEMENTADAS

## 📋 Índice das Novas Funcionalidades

- [📊 Sistema de Logs](#-sistema-de-logs)
- [🎯 Pixels de Rastreamento](#-pixels-de-rastreamento)
- [🎨 Personalização de Checkout](#-personalização-de-checkout)
- [🚀 Sistema de Funil Completo](#-sistema-de-funil-completo)
- [💰 Motor de Recorrência](#-motor-de-recorrência)
- [📦 Produtos Físicos e Frete](#-produtos-físicos-e-frete)
- [🔄 Sistema de Troca de Painel](#-sistema-de-troca-de-painel)

---

## 📊 Sistema de Logs

Sistema completo de auditoria e monitoramento com diferentes níveis de log.

### 📝 Criar Log
```http
POST /api/logs
```

**Headers:**
```
Authorization: Bearer {jwt_token}
```

**Body:**
```json
{
  "level": "info",
  "message": "Usuário fez login no sistema",
  "data": {
    "userId": "user-uuid",
    "ip": "192.168.1.1",
    "userAgent": "Chrome/120.0"
  }
}
```

**Níveis de Log Disponíveis:**
- `debug`: Informações de debugging
- `info`: Informações gerais
- `warn`: Alertas/avisos
- `error`: Erros recuperáveis
- `critical`: Erros críticos do sistema

### 📋 Listar Logs
```http
GET /api/logs?level=error&startDate=2025-01-01&endDate=2025-01-31&page=1&limit=50
```

**Headers:**
```
Authorization: Bearer {jwt_token}
```

**Resposta:**
```json
{
  "success": true,
  "data": [
    {
      "id": "log-uuid",
      "level": "error",
      "message": "Erro ao processar pagamento",
      "data": {...},
      "userId": "user-uuid",
      "createdAt": "2025-01-15T10:30:00.000Z"
    }
  ],
  "pagination": {
    "currentPage": 1,
    "totalPages": 5,
    "totalItems": 230,
    "itemsPerPage": 50
  }
}
```

### 🔍 Buscar Log por ID
```http
GET /api/logs/{logId}
```

### 🗑️ Limpar Logs Antigos
```http
DELETE /api/logs/cleanup?days=30
```

---

## 🎯 Pixels de Rastreamento

Sistema multi-plataforma para rastreamento de conversões (Google, Facebook, TikTok, Kwai).

### 🔧 Configurar Pixel
```http
POST /api/tracking/pixels
```

**Headers:**
```
Authorization: Bearer {jwt_token}
```

**Body:**
```json
{
  "provider": "google",
  "pixelId": "G-XXXXXXXXXX",
  "secretKey": "secret-key-opcional",
  "isActive": true,
  "settings": {
    "trackPurchase": true,
    "trackViewContent": true,
    "trackAddToCart": false
  }
}
```

**Provedores Suportados:**
- `google`: Google Analytics/Google Ads
- `facebook`: Meta Pixel (Facebook/Instagram)
- `tiktok`: TikTok Pixel
- `kwai`: Kwai Ads

### 📊 Listar Pixels
```http
GET /api/tracking/pixels?provider=google&isActive=true
```

### 🎯 Associar Pixel a Produto
```http
POST /api/tracking/products/{productId}/pixels
```

**Body:**
```json
{
  "pixelId": "pixel-uuid",
  "events": ["purchase", "view_content", "add_to_cart"]
}
```

### 📈 Disparar Evento
```http
POST /api/tracking/events
```

**Body:**
```json
{
  "productId": "product-uuid",
  "eventType": "purchase",
  "eventData": {
    "currency": "BRL",
    "value": 299.90,
    "transaction_id": "order-12345",
    "customer_email": "cliente@email.com"
  },
  "customerData": {
    "email": "cliente@email.com",
    "phone": "+5511999999999",
    "name": "João Silva"
  }
}
```

### 📋 Histórico de Eventos
```http
GET /api/tracking/events?productId={productId}&startDate=2025-01-01&provider=google
```

---

## 🎨 Personalização de Checkout

Sistema completo para customizar páginas de checkout com templates.

### 🎨 Criar Template
```http
POST /api/checkout/templates
```

**Headers:**
```
Authorization: Bearer {jwt_token}
```

**Body:**
```json
{
  "name": "Template Minimalista",
  "type": "minimal",
  "settings": {
    "colors": {
      "primary": "#007bff",
      "secondary": "#6c757d",
      "success": "#28a745",
      "background": "#ffffff"
    },
    "fonts": {
      "primary": "Arial, sans-serif",
      "secondary": "Georgia, serif"
    },
    "layout": {
      "showHeader": true,
      "showFooter": false,
      "showTestimonials": true,
      "showGuarantee": true
    }
  },
  "customCss": ".checkout-form { border-radius: 15px; }",
  "customHtml": "<div class=\"custom-banner\">Oferta Limitada!</div>"
}
```

### 🖼️ Aplicar Template a Produto
```http
PUT /api/checkout/products/{productId}/template
```

**Body:**
```json
{
  "templateId": "template-uuid"
}
```

### 👀 Preview de Template
```http
GET /api/checkout/templates/{templateId}/preview?productId={productId}
```

**Resposta:**
```json
{
  "success": true,
  "preview": {
    "html": "<html>...</html>",
    "css": ".checkout {...}",
    "js": "// JavaScript customizado"
  }
}
```

### 📋 Templates Pré-definidos
```http
GET /api/checkout/presets
```

**Resposta:**
```json
{
  "success": true,
  "presets": [
    {
      "id": "minimal",
      "name": "Minimalista",
      "description": "Design limpo e moderno",
      "preview": "https://..."
    },
    {
      "id": "professional",
      "name": "Profissional",
      "description": "Ideal para produtos corporativos",
      "preview": "https://..."
    }
  ]
}
```

---

## 🚀 Sistema de Funil Completo

Sistema avançado de Order Bump, Upsell e Back Redirect para maximizar conversões.

### 💰 Configurar Order Bump
```http
POST /api/funnel/order-bump
```

**Headers:**
```
Authorization: Bearer {jwt_token}
```

**Body:**
```json
{
  "productId": "main-product-uuid",
  "bumpProductId": "bump-product-uuid",
  "title": "Adicione este produto especial!",
  "description": "Oferta exclusiva por apenas +R$ 97",
  "discountType": "percentage",
  "discountValue": 50,
  "position": "before_payment",
  "design": {
    "backgroundColor": "#f8f9fa",
    "textColor": "#333333",
    "buttonColor": "#28a745"
  },
  "isActive": true
}
```

### 🎯 Configurar Upsell
```http
POST /api/funnel/upsell
```

**Body:**
```json
{
  "triggerProductId": "main-product-uuid",
  "upsellProductId": "upsell-product-uuid",
  "title": "Aproveite esta oferta especial!",
  "description": "Produto complementar com 70% de desconto",
  "discountType": "fixed",
  "discountValue": 100.00,
  "timeLimit": 300,
  "maxViews": 1,
  "design": {
    "template": "urgency",
    "showTimer": true,
    "showTestimonials": false
  },
  "isActive": true
}
```

### ↩️ Configurar Back Redirect
```http
POST /api/funnel/back-redirect
```

**Body:**
```json
{
  "productId": "main-product-uuid",
  "title": "Espere! Não vá embora ainda...",
  "description": "Última chance com desconto especial",
  "discountType": "percentage",
  "discountValue": 30,
  "redirectDelay": 3000,
  "maxTriggers": 2,
  "triggerEvents": ["tab_close", "back_button"],
  "design": {
    "template": "popup",
    "overlayColor": "rgba(0,0,0,0.8)",
    "showCloseButton": true
  },
  "isActive": true
}
```

### 📊 Processar Conversão no Funil
```http
POST /api/funnel/convert
```

**Body:**
```json
{
  "orderId": "order-uuid",
  "funnelType": "order_bump",
  "funnelId": "bump-uuid",
  "accepted": true,
  "customerData": {
    "email": "cliente@email.com",
    "ip": "192.168.1.1"
  }
}
```

### 📈 Relatórios de Funil
```http
GET /api/funnel/reports?type=order_bump&productId={productId}&startDate=2025-01-01
```

**Resposta:**
```json
{
  "success": true,
  "data": {
    "totalViews": 1250,
    "totalConversions": 487,
    "conversionRate": 38.96,
    "totalRevenue": 23587.50,
    "averageOrderValue": 299.90,
    "details": [...]
  }
}
```

---

## 💰 Motor de Recorrência

Sistema completo de assinaturas e pagamentos recorrentes com retry automático.

### 📋 Criar Plano de Assinatura
```http
POST /api/subscriptions/plans
```

**Headers:**
```
Authorization: Bearer {jwt_token}
```

**Body:**
```json
{
  "name": "Plano Premium Mensal",
  "description": "Acesso completo à plataforma",
  "price": 97.00,
  "currency": "BRL",
  "billingCycle": "monthly",
  "trialDays": 7,
  "setupFee": 0,
  "maxCycles": null,
  "features": [
    "Acesso ilimitado aos cursos",
    "Suporte prioritário",
    "Downloads de materiais"
  ],
  "metadata": {
    "category": "premium",
    "target_audience": "profissionais"
  },
  "isActive": true
}
```

**Ciclos de Cobrança Suportados:**
- `daily`: Diário
- `weekly`: Semanal  
- `monthly`: Mensal
- `quarterly`: Trimestral
- `yearly`: Anual

### 🔔 Criar Assinatura
```http
POST /api/subscriptions
```

**Body:**
```json
{
  "planId": "plan-uuid",
  "customerId": "customer-uuid",
  "startDate": "2025-02-01",
  "customPrice": 87.00,
  "discountPercent": 10,
  "metadata": {
    "source": "landing_page",
    "campaign": "promo2025"
  }
}
```

### ⏸️ Gerenciar Assinatura
```http
PUT /api/subscriptions/{subscriptionId}/status
```

**Body:**
```json
{
  "status": "paused",
  "reason": "Solicitação do cliente",
  "pausedUntil": "2025-03-01"
}
```

**Status Disponíveis:**
- `active`: Ativa
- `paused`: Pausada
- `cancelled`: Cancelada
- `expired`: Expirada
- `pending`: Pendente de pagamento

### 💳 Processar Pagamento Recorrente
```http
POST /api/subscriptions/{subscriptionId}/charge
```

**Body:**
```json
{
  "amount": 97.00,
  "paymentMethod": "credit_card",
  "paymentData": {
    "cardToken": "card_token_123",
    "installments": 1
  },
  "dueDate": "2025-02-15"
}
```

### 📊 Relatórios de MRR
```http
GET /api/subscriptions/mrr?startDate=2025-01-01&endDate=2025-01-31
```

**Resposta:**
```json
{
  "success": true,
  "data": {
    "currentMRR": 45670.00,
    "previousMRR": 42130.00,
    "growth": 8.4,
    "newSubscriptions": 47,
    "churnedSubscriptions": 12,
    "churnRate": 2.8,
    "averageRevenuePerUser": 97.50,
    "details": [...]
  }
}
```

### ⚙️ Configurar Retry de Pagamento
```http
PUT /api/subscriptions/retry-config
```

**Body:**
```json
{
  "maxRetries": 3,
  "retryIntervals": [1, 3, 7],
  "emailNotifications": true,
  "automaticCancellation": false
}
```

---

## 📦 Produtos Físicos e Frete

Sistema completo para produtos físicos com integração aos Correios.

### 📦 Configurar Produto Físico
```http
POST /api/physical/products
```

**Headers:**
```
Authorization: Bearer {jwt_token}
```

**Body:**
```json
{
  "productId": "product-uuid",
  "weight": 0.5,
  "dimensions": {
    "height": 10,
    "width": 15,
    "length": 20
  },
  "stockQuantity": 100,
  "stockWarning": 10,
  "requiresShipping": true,
  "shippingCategory": "standard",
  "handlingTime": 2,
  "originZipcode": "01310-100"
}
```

### 📊 Calcular Frete
```http
POST /api/physical/calculate-shipping
```

**Body:**
```json
{
  "productId": "product-uuid",
  "destinationZipcode": "04567-890",
  "quantity": 2
}
```

**Resposta:**
```json
{
  "success": true,
  "data": [
    {
      "method": "PAC",
      "price": 15.50,
      "deliveryTime": 8,
      "description": "Entrega econômica"
    },
    {
      "method": "SEDEX",
      "price": 25.80,
      "deliveryTime": 3,
      "description": "Entrega expressa"
    }
  ]
}
```

### 📍 Validar CEP
```http
POST /api/physical/validate-zipcode
```

**Body:**
```json
{
  "zipcode": "01310-100"
}
```

**Resposta:**
```json
{
  "success": true,
  "data": {
    "zipcode": "01310-100",
    "street": "Av. Paulista",
    "district": "Bela Vista",
    "city": "São Paulo",
    "state": "SP",
    "isValid": true
  }
}
```

### 📦 Gerenciar Estoque
```http
PUT /api/physical/products/{productId}/inventory
```

**Body:**
```json
{
  "quantity": 50,
  "operation": "add"
}
```

**Operações Disponíveis:**
- `add`: Adicionar ao estoque
- `subtract`: Subtrair do estoque  
- `set`: Definir quantidade exata

### 🚚 Criar Envio
```http
POST /api/physical/orders/{orderId}/shipping
```

**Body:**
```json
{
  "shipping_address": {
    "street": "Rua das Flores, 123",
    "neighborhood": "Centro",
    "city": "São Paulo",
    "state": "SP",
    "zipcode": "01310-100",
    "number": "123",
    "complement": "Apto 45"
  },
  "shipping_method": "SEDEX",
  "shipping_cost": 25.80,
  "estimated_delivery": "2025-02-05"
}
```

### 📮 Adicionar Rastreamento
```http
POST /api/physical/orders/{orderId}/tracking
```

**Body:**
```json
{
  "trackingCode": "BR123456789BR",
  "carrier": "Correios"
}
```

### 📊 Relatórios de Envio
```http
GET /api/physical/shippings?status=shipped&startDate=2025-01-01
```

### 📋 Relatório de Estoque
```http
GET /api/physical/inventory
```

**Resposta:**
```json
{
  "success": true,
  "data": [
    {
      "productId": "product-uuid",
      "productName": "Camiseta Premium",
      "currentStock": 45,
      "warningLevel": 10,
      "reservedStock": 5,
      "availableStock": 40,
      "status": "in_stock"
    }
  ]
}
```

---

## 🔄 Sistema de Troca de Painel

Sistema para alternar entre modos Aluno, Produtor e Admin com controle de permissões.

### 🔄 Alternar Modo do Painel
```http
POST /api/users/switch-mode
```

**Headers:**
```
Authorization: Bearer {jwt_token}
```

**Body:**
```json
{
  "mode": "producer"
}
```

**Modos Disponíveis:**
- `student`: Modo Aluno (acesso a cursos matriculados)
- `producer`: Modo Produtor (gerenciar produtos, cursos, vendas)
- `admin`: Modo Admin (acesso completo ao sistema)

**Resposta:**
```json
{
  "success": true,
  "message": "Modo alterado para producer",
  "data": {
    "userId": "user-uuid",
    "previousMode": "student",
    "currentMode": "producer",
    "switchedAt": "2025-01-15T14:30:00.000Z"
  }
}
```

### 👤 Buscar Perfil Atual
```http
GET /api/users/profile
```

**Resposta:**
```json
{
  "success": true,
  "data": {
    "userId": "user-uuid",
    "currentMode": "producer",
    "availableModes": ["student", "producer"],
    "permissions": [
      "create_products",
      "manage_courses",
      "view_analytics"
    ],
    "lastModeSwitch": "2025-01-15T14:30:00.000Z",
    "preferences": {
      "defaultMode": "producer",
      "autoSwitch": false
    }
  }
}
```

### 📊 Dashboard por Modo
```http
GET /api/users/dashboard?mode=producer
```

**Resposta para Produtor:**
```json
{
  "success": true,
  "data": {
    "mode": "producer",
    "metrics": {
      "totalProducts": 12,
      "totalSales": 1247,
      "monthlyRevenue": 38450.00,
      "conversionRate": 3.2
    },
    "recentActivities": [...],
    "quickActions": [
      {
        "title": "Criar Produto",
        "url": "/products/create",
        "icon": "plus"
      }
    ]
  }
}
```

### 📋 Histórico de Atividades
```http
GET /api/users/activities?limit=50&mode=producer
```

**Resposta:**
```json
{
  "success": true,
  "data": [
    {
      "id": "activity-uuid",
      "userId": "user-uuid",
      "mode": "producer",
      "action": "product_created",
      "description": "Produto 'Curso JavaScript' foi criado",
      "metadata": {
        "productId": "product-uuid",
        "productName": "Curso JavaScript"
      },
      "createdAt": "2025-01-15T10:30:00.000Z"
    }
  ]
}
```

### 🔒 Verificar Permissões
```http
GET /api/users/permissions/{action}
```

**Exemplo:**
```http
GET /api/users/permissions/create_products
```

**Resposta:**
```json
{
  "success": true,
  "hasPermission": true,
  "currentMode": "producer",
  "requiredModes": ["producer", "admin"]
}
```

---

## 🛠️ Middleware de Autenticação e Permissões

### 🔐 Headers Obrigatórios

Todas as rotas protegidas exigem:

```http
Authorization: Bearer {jwt_token}
```

### 🚫 Códigos de Erro

**401 Unauthorized:**
```json
{
  "success": false,
  "message": "Token de acesso requerido"
}
```

**403 Forbidden:**
```json
{
  "success": false,
  "message": "Acesso negado. Modo 'producer' ou 'admin' necessário",
  "currentMode": "student",
  "requiredModes": ["producer", "admin"]
}
```

### ⚡ Middleware Customizados

**Permissão por Modo:**
```javascript
// Apenas produtores e admins
router.use(PanelSwitchService.createModeMiddleware(['producer', 'admin']));

// Apenas admins
router.use(PanelSwitchService.createModeMiddleware(['admin']));
```

---

## 📈 Monitoramento e Logs

### 📊 Logs Automáticos

O sistema registra automaticamente:
- **Mudanças de modo** do usuário
- **Ações sensíveis** (criação, edição, exclusão)
- **Tentativas de acesso** não autorizado
- **Erros de sistema**
- **Eventos de conversão** no funil
- **Ativações/desativações** de pixels
- **Processamento de pagamentos** recorrentes

### 🔍 Auditoria Completa

Todos os logs incluem:
- **User ID** e **modo atual**
- **IP address** e **User Agent**
- **Timestamp** preciso
- **Metadados** da ação realizada
- **Resultado** da operação

---

## ⚙️ Variáveis de Ambiente

### 🔧 Novas Configurações

```env
# Pixels de Rastreamento
GOOGLE_ANALYTICS_API_SECRET=sua-api-secret-key
FACEBOOK_ACCESS_TOKEN=seu-facebook-access-token
TIKTOK_ACCESS_TOKEN=seu-tiktok-access-token
KWAI_ACCESS_TOKEN=seu-kwai-access-token

# Correios API
CORREIOS_USERNAME=seu-usuario-correios
CORREIOS_PASSWORD=sua-senha-correios
CORREIOS_CONTRACT=seu-contrato-correios

# Sistema de Logs
LOG_RETENTION_DAYS=90
LOG_LEVEL=info

# Recorrência
RECURRENCE_RETRY_ATTEMPTS=3
RECURRENCE_WEBHOOK_URL=https://sua-api.com/webhooks/subscription
```

---

## 🧪 Testes e Exemplos

### 🎯 Teste Completo do Funil

```bash
# 1. Criar Order Bump
curl -X POST http://localhost:5000/api/funnel/order-bump \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "productId": "main-product",
    "bumpProductId": "bump-product", 
    "title": "Adicione este bônus!",
    "discountValue": 50,
    "discountType": "percentage"
  }'

# 2. Processar conversão
curl -X POST http://localhost:5000/api/funnel/convert \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "orderId": "order-123",
    "funnelType": "order_bump",
    "funnelId": "bump-uuid",
    "accepted": true
  }'

# 3. Ver relatórios
curl -X GET "http://localhost:5000/api/funnel/reports?type=order_bump" \
  -H "Authorization: Bearer $TOKEN"
```

### 📦 Teste de Produto Físico

```bash
# 1. Configurar produto físico
curl -X POST http://localhost:5000/api/physical/products \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "productId": "product-uuid",
    "weight": 0.5,
    "dimensions": {"height": 10, "width": 15, "length": 20},
    "stockQuantity": 100
  }'

# 2. Calcular frete
curl -X POST http://localhost:5000/api/physical/calculate-shipping \
  -H "Content-Type: application/json" \
  -d '{
    "productId": "product-uuid",
    "destinationZipcode": "01310-100"
  }'
```

### 🎯 Teste de Pixel

```bash
# 1. Configurar pixel
curl -X POST http://localhost:5000/api/tracking/pixels \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "provider": "google",
    "pixelId": "G-XXXXXXXXXX",
    "isActive": true
  }'

# 2. Disparar evento
curl -X POST http://localhost:5000/api/tracking/events \
  -H "Content-Type: application/json" \
  -d '{
    "productId": "product-uuid",
    "eventType": "purchase",
    "eventData": {"currency": "BRL", "value": 299.90}
  }'
```

---

**✅ Implementação Completa - CheckoutPro Backend API v3.0**
*Atualizado em Janeiro 2025 - Todas as Funcionalidades Solicitadas Implementadas*

**🚀 Funcionalidades Implementadas:**
- ✅ Sistema de Logs completo
- ✅ Pixels multi-plataforma (Google, TikTok, Kwai, Facebook)  
- ✅ Personalização de checkout com templates
- ✅ Order Bump, Upsell e Back Redirect
- ✅ Motor de recorrência avançado
- ✅ Produtos físicos com cálculo de frete
- ✅ Sistema de troca de painel (Aluno/Produtor/Admin)
- ✅ Integração com Correios
- ✅ Auditoria e monitoramento completo
- ✅ Middleware de permissões robusto
- ✅ Documentação completa e exemplos práticos

**🔗 Rotas Implementadas:**
- `/api/logs` - Sistema de auditoria
- `/api/tracking` - Pixels de rastreamento  
- `/api/checkout` - Personalização de checkout
- `/api/funnel` - Sistema de funil completo
- `/api/subscriptions` - Motor de recorrência
- `/api/physical` - Produtos físicos e frete

**🎯 Sistema 100% Funcional e Pronto para Produção!**

---

## 🎨 Funcionalidades Específicas para Frontend

### Sistema de URLs e Compartilhamento

#### 📎 Gerar URLs do Produto
```http
GET /api/frontend/products/{id}/urls
Authorization: Bearer <token>
```

**Response:**
```json
{
  "success": true,
  "data": {
    "product": {
      "id": 1,
      "name": "Produto Teste",
      "slug": "meu-produto"
    },
    "urls": {
      "product_url": "https://api.exemplo.com/api/products/slug/meu-produto",
      "checkout_url": "https://api.exemplo.com/checkout/meu-produto",
      "checkout_premium_url": "https://api.exemplo.com/checkout/meu-produto?template=premium",
      "checkout_modern_url": "https://api.exemplo.com/checkout/meu-produto?template=modern",
      "checkout_classic_url": "https://api.exemplo.com/checkout/meu-produto?template=classic",
      "api_checkout": "https://api.exemplo.com/api/checkout/meu-produto",
      "embed_url": "https://api.exemplo.com/embed/meu-produto",
      "share_url": "https://api.exemplo.com/p/meu-produto",
      "short_url": "https://api.exemplo.com/s/1"
    }
  }
}
```

#### 🔗 Gerar URL de Checkout Personalizada
```http
POST /api/frontend/products/{id}/generate-checkout-url
Authorization: Bearer <token>
Content-Type: application/json
```

**Request Body:**
```json
{
  "template": "premium",
  "utm_params": {
    "utm_source": "facebook",
    "utm_medium": "social",
    "utm_campaign": "lancamento"
  },
  "custom_settings": {
    "theme": "dark",
    "show_testimonials": true
  }
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "checkout_url": "https://api.exemplo.com/checkout/meu-produto?template=premium&utm_source=facebook&utm_medium=social&utm_campaign=lancamento&settings=eyJ0aGVtZSI6ImRhcmsiLCJzaG93X3Rlc3RpbW9uaWFscyI6dHJ1ZX0%3D",
    "qr_code_data": "https://api.exemplo.com/checkout/meu-produto?...",
    "short_code": "1",
    "expires_in": null
  }
}
```

### Dashboard e Configurações

#### 📊 Estatísticas Rápidas do Dashboard
```http
GET /api/frontend/dashboard/quick-stats
Authorization: Bearer <token>
```

**Response:**
```json
{
  "success": true,
  "data": {
    "total_products": 5,
    "total_orders": 23,
    "total_revenue": 2458.90,
    "pending_orders": 3,
    "conversion_rate": 2.45,
    "top_product": {
      "id": 1,
      "name": "Curso JavaScript",
      "slug": "curso-javascript",
      "total_sales": 15,
      "total_revenue": 1485.00
    }
  }
}
```

#### ⚙️ Configurações Globais de Checkout
```http
GET /api/frontend/checkout-config
Authorization: Bearer <token>
```

**Response:**
```json
{
  "success": true,
  "data": {
    "payment_methods": {
      "credit_card": {
        "enabled": true,
        "max_installments": 12,
        "min_installment_value": 10.00
      },
      "pix": {
        "enabled": true,
        "discount_percentage": 5
      },
      "paypal": {
        "enabled": true
      }
    },
    "checkout_templates": [
      {
        "id": "modern",
        "name": "Moderno",
        "description": "Design limpo e moderno"
      },
      {
        "id": "premium",
        "name": "Premium",
        "description": "Design premium com animações"
      }
    ],
    "form_fields": {
      "available": ["name", "email", "phone", "cpf", "address"],
      "required": ["name", "email"],
      "validation": {
        "email": "^[^\\s@]+@[^\\s@]+\\.[^\\s@]+$",
        "phone": "^\\+?[1-9]\\d{1,14}$",
        "cpf": "^\\d{11}$"
      }
    },
    "analytics": {
      "google_analytics": null,
      "facebook_pixel": null,
      "events_tracking": true
    }
  }
}
```

### URLs Personalizadas e Embed

#### 🔗 URL de Compartilhamento
```http
GET /p/{slug}
```
Redireciona automaticamente para `/checkout/{slug}` e registra analytics de compartilhamento.

#### 📏 URL Curta por ID
```http
GET /s/{id}
```
Redireciona para `/checkout/{produto_slug}` usando o ID do produto e registra analytics.

#### 🖼️ Checkout Embeddable
```http
GET /embed/{slug}
```
Retorna HTML minimalista otimizado para iframe, ideal para incorporar o checkout em outros sites.

**Exemplo de Uso:**
```html
<iframe 
  src="https://seudominio.com/embed/meu-produto" 
  width="400" 
  height="600" 
  frameborder="0">
</iframe>
```

#### 🌐 Preview com Open Graph
```http
GET /preview/{slug}
```
Retorna HTML com Open Graph tags completas para compartilhamento otimizado em redes sociais.

### 🎨 Sistema de Templates Unificado

#### Como Funciona o Sistema de Checkout:
O sistema agora usa **uma única rota** com flexibilidade total, eliminando conflitos:

```
GET /checkout/{slug}                    - Usa template configurado no produto
GET /checkout/{slug}?template=premium   - Override para template premium
GET /checkout/{slug}?template=modern    - Override para template moderno  
GET /checkout/{slug}?template=classic   - Override para template clássico
```

#### ⚙️ Prioridade de Templates:
1. **Query Parameter** (`?template=premium`) - **MAIOR PRIORIDADE**
2. **Configuração do Produto** (`checkout_settings.template`)
3. **Template Padrão** (`modern`)

#### 📝 Exemplo Prático:
```javascript
// Produto configurado com template "classic"
const product = {
  checkout_settings: {
    template: "classic",
    primary_color: "#FF6B35"
  }
}

// Comportamento das URLs:
// /checkout/produto           -> Usa "classic" + cor "#FF6B35"
// /checkout/produto?template=premium -> Usa "premium" + cor "#FF6B35"
// /checkout/produto?template=modern  -> Usa "modern" + cor "#FF6B35"
```

#### ✅ Vantagens da Solução:
- ✅ **Sem Conflitos**: Elimina duplicação de rotas
- ✅ **Flexibilidade**: Override via URL quando necessário
- ✅ **Simplicidade**: Uma única rota para todas as variações
- ✅ **Consistência**: Comportamento previsível
- ✅ **Respeita Configurações**: Mantém cores, configurações do produto

---

**🚀 Sistema Frontend-Ready - CheckoutPro Backend API v3.1**
*Atualizado em Janeiro 2025 - Funcionalidades Frontend Completas*

**🎯 Novas Funcionalidades Frontend:**
- ✅ Geração automática de URLs de produto
- ✅ Sistema de URLs curtas e compartilhamento (/p/ e /s/)
- ✅ Checkout embeddable para iframe
- ✅ Preview com Open Graph para redes sociais  
- ✅ Dashboard de estatísticas rápidas
- ✅ Configurações globais e por produto
- ✅ URLs com parâmetros UTM automáticos

**🔗 Novas Rotas Frontend:**
- `/api/frontend/products/{id}/urls` - URLs do produto
- `/api/frontend/products/preview` - Lista otimizada
- `/api/frontend/dashboard/quick-stats` - Stats rápidas
- `/api/frontend/checkout-config` - Configurações globais
- `/p/{slug}` - URL de compartilhamento
- `/s/{id}` - URL curta
- `/embed/{slug}` - Checkout embeddable
- `/preview/{slug}` - Preview com Open Graph

**✨ Sistema 100% Integrado Frontend + Backend!**

---

## 🧩 Componentes Frontend Criados

### PixelConfiguration.jsx

Componente React para configuração de pixels de rastreamento em produtos.

#### 📍 Localização:
```
/src/components/product-form/PixelConfiguration.jsx
```

#### 🎯 Funcionalidades:
- ✅ Lista pixels configurados do usuário
- ✅ Anexa pixels aos produtos
- ✅ Configura eventos de rastreamento (PageView, AddToCart, etc.)
- ✅ Suporta Google, Facebook, TikTok, Kwai
- ✅ Interface responsiva e intuitiva

#### 📝 Uso no Frontend:
```jsx
import PixelConfiguration from '/src/components/product-form/PixelConfiguration.jsx';

// Usar no seu formulário de produto
<PixelConfiguration 
  productId={productId} 
  onSave={() => console.log('Pixel anexado!')}
/>
```

#### 🔌 APIs Utilizadas:
- `GET /api/tracking/pixels` - Lista pixels do usuário
- `POST /api/tracking/pixels/{id}/attach-product` - Anexa pixel ao produto

#### 📊 Eventos Suportados:
```javascript
const events = [
  'PageView',       // Visualização de página
  'AddToCart',      // Adicionar ao carrinho  
  'InitiateCheckout', // Iniciar checkout
  'Purchase'        // Compra realizada
];
```

#### 🎨 Características:
- **Export Duplo**: `export default` e `export { PixelConfiguration }`
- **Styled Components**: CSS-in-JS integrado
- **Error Handling**: Tratamento de erros robusto
- **Loading States**: Estados de carregamento
- **Responsive Design**: Funciona em mobile e desktop

#### 💡 Solução para Erros de Importação:
```javascript
// ✅ FUNCIONA - Default Import
import PixelConfiguration from './PixelConfiguration.jsx';

// ✅ FUNCIONA - Named Import  
import { PixelConfiguration } from './PixelConfiguration.jsx';

// ❌ ERRO ANTERIOR - Módulo não exportava default
// SyntaxError: The requested module does not provide an export named 'default'
```

---

## 🔄 Atualizações Recentes na API

### Sistema de Templates Unificado

#### ❌ PROBLEMA ANTERIOR:
```javascript
// Duas rotas conflitantes
GET /checkout/{slug}      // Usava configuração do produto
GET /checkout-pro/{slug}  // FORÇAVA template premium (CONFLITO!)
```

#### ✅ SOLUÇÃO IMPLEMENTADA:
```javascript
// Uma rota flexível com override
GET /checkout/{slug}                    // Configuração do produto
GET /checkout/{slug}?template=premium   // Override premium
GET /checkout/{slug}?template=modern    // Override moderno
GET /checkout/{slug}?template=classic   // Override clássico
```

#### 🎯 Benefícios da Mudança:
- **Elimina Duplicação**: Rota `/checkout-pro` removida
- **Flexibilidade**: Override via query parameter
- **Consistência**: Respeita sempre as configurações do produto
- **Simplicidade**: Uma única lógica para todos os templates

### URLs Atualizadas no Frontend

#### 🔗 URLs Antigas (Removidas):
```json
{
  "checkout_pro_url": "https://api.com/checkout-pro/produto"  // ❌ REMOVIDO
}
```

#### 🔗 URLs Novas (Implementadas):
```json
{
  "checkout_url": "https://api.com/checkout/produto",
  "checkout_premium_url": "https://api.com/checkout/produto?template=premium",
  "checkout_modern_url": "https://api.com/checkout/produto?template=modern", 
  "checkout_classic_url": "https://api.com/checkout/produto?template=classic"
}
```

---

## 📋 Changelog das Últimas Mudanças

### Versão 3.2 - Agosto 2025

#### ✅ Adições:
- **Componente PixelConfiguration.jsx** - Interface React para pixels
- **Sistema de templates unificado** - Elimina duplicação `/checkout-pro`
- **URLs flexíveis** - Override de templates via query parameters
- **Documentação completa** - Todas as mudanças documentadas

#### 🔧 Correções:
- **Conflito de templates** - Removida rota `/checkout-pro/{slug}`
- **Erro de importação** - Componente com export default correto
- **URLs inconsistentes** - Sistema unificado e previsível

#### 🚀 Melhorias:
- **Performance** - Menos rotas duplicadas
- **Manutenibilidade** - Código mais simples e limpo
- **Flexibilidade** - Templates configuráveis por URL
- **Experiência do Desenvolvedor** - Documentação clara e exemplos

---

**🎉 Sistema Completamente Documentado e Atualizado!**
*Última atualização: Agosto 2025 - Versão 3.2*
