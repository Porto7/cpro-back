# 📚 DOCUMENTAÇÃO COMPLETA - CHECKOUTPRO BACKEND API

## 🚀 Visão Geral

Esta é a documentação completa de todas as APIs disponíveis no CheckoutPro Backend. O sistema utiliza Node.js/Express com PostgreSQL e oferece funcionalidades completas de e-commerce, cursos online, afiliados, pagamentos e muito mais.

**Base URL**: `http://localhost:5000`
**Ambiente de Produção**: Configurável via variáveis de ambiente

---

## 📋 Índice

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
        "progress": 65.5,
        "expiresAt": "2026-01-01T00:00:00Z"
      },
      "course": {
        "id": "course-uuid",
        "title": "Curso de JavaScript Avançado",
        "description": "Aprenda JavaScript do zero ao avançado",
        "thumbnailUrl": "https://exemplo.com/thumb.jpg",
        "trailerUrl": "https://exemplo.com/trailer.mp4",
        "duration": 1200,
        "level": "intermediate",
        "instructor": {
          "id": "instructor-uuid",
          "name": "Professor João",
          "email": "professor@email.com"
        },
        "modules": [
          {
            "id": "module-uuid",
            "title": "Introdução ao JavaScript",
            "orderIndex": 1,
            "durationMinutes": 120,
            "isFree": true,
            "lessons": [
              {
                "id": "lesson-uuid",
                "title": "Variáveis e Tipos",
                "durationMinutes": 15,
                "isFree": true,
                "orderIndex": 1
              }
            ]
          }
        ]
      }
    }
  ]
}
```

### 📋 Listar Cursos (Instrutor)
```http
GET /api/courses
```

**Headers:**
```
Authorization: Bearer {token}
```

**Query Parameters:**
- `status`: active, draft, archived
- `level`: beginner, intermediate, advanced
- `page`: Página
- `limit`: Itens por página

**Resposta:**
```json
{
  "success": true,
  "courses": [
    {
      "id": "course-uuid",
      "title": "Meu Curso",
      "description": "Descrição do curso",
      "status": "active",
      "level": "beginner",
      "duration_minutes": 600,
      "enrolled_students": 45,
      "total_modules": 8,
      "total_lessons": 32,
      "instructor": {
        "id": "user-uuid",
        "name": "Instrutor"
      },
      "product": {
        "id": "product-uuid",
        "name": "Produto do Curso",
        "price": 197.00
      }
    }
  ]
}
```

### 🎯 Buscar Curso por ID
```http
GET /api/courses/{id}
```

**Resposta Completa com Módulos e Aulas:**
```json
{
  "success": true,
  "course": {
    "id": "course-uuid",
    "title": "Curso Completo",
    "description": "Descrição detalhada",
    "thumbnailUrl": "https://exemplo.com/thumb.jpg",
    "trailerUrl": "https://exemplo.com/trailer.mp4",
    "duration_minutes": 1200,
    "level": "intermediate",
    "status": "active",
    "instructor": {
      "id": "instructor-uuid",
      "name": "Professor",
      "email": "prof@email.com"
    },
    "modules": [
      {
        "id": "module-uuid",
        "title": "Módulo 1",
        "description": "Descrição do módulo",
        "order_index": 1,
        "duration_minutes": 180,
        "is_free": false,
        "lessons": [
          {
            "id": "lesson-uuid",
            "title": "Aula 1",
            "description": "Descrição da aula",
            "video_url": "https://exemplo.com/aula1.mp4",
            "duration_minutes": 25,
            "order_index": 1,
            "is_free": false,
            "transcript": "Transcrição da aula...",
            "materials": [
              {
                "name": "Material Extra.pdf",
                "url": "https://exemplo.com/material.pdf",
                "type": "pdf"
              }
            ]
          }
        ]
      }
    ],
    "stats": {
      "totalStudents": 125,
      "averageRating": 4.8,
      "totalRatings": 89,
      "completionRate": 78.5
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

**Body:**
```json
{
  "title": "Novo Curso",
  "description": "Descrição do curso",
  "level": "beginner",
  "product_id": "product-uuid",
  "thumbnail_url": "https://exemplo.com/thumb.jpg",
  "trailer_url": "https://exemplo.com/trailer.mp4",
  "prerequisites": ["Conhecimento básico em programação"],
  "learning_objectives": ["Aprender JavaScript", "Criar aplicações web"]
}
```

### ✏️ Atualizar Curso
```http
PUT /api/courses/{id}
```

### ❌ Excluir Curso
```http
DELETE /api/courses/{id}
```

### 📘 Adicionar Módulo
```http
POST /api/courses/{id}/modules
```

**Body:**
```json
{
  "title": "Novo Módulo",
  "description": "Descrição do módulo",
  "order_index": 2,
  "is_free": false
}
```

### ✏️ Atualizar Módulo
```http
PUT /api/courses/{courseId}/modules/{moduleId}
```

### ❌ Excluir Módulo
```http
DELETE /api/courses/{courseId}/modules/{moduleId}
```

### 📝 Adicionar Aula
```http
POST /api/courses/{courseId}/modules/{moduleId}/lessons
```

**Body:**
```json
{
  "title": "Nova Aula",
  "description": "Descrição da aula",
  "video_url": "https://exemplo.com/video.mp4",
  "duration_minutes": 15,
  "order_index": 1,
  "is_free": false,
  "transcript": "Transcrição...",
  "materials": [
    {
      "name": "Slides.pdf",
      "url": "https://exemplo.com/slides.pdf",
      "type": "pdf"
    }
  ]
}
```

### ✏️ Atualizar Aula
```http
PUT /api/courses/{courseId}/modules/{moduleId}/lessons/{lessonId}
```

### ❌ Excluir Aula
```http
DELETE /api/courses/{courseId}/modules/{moduleId}/lessons/{lessonId}
```

### 🎓 Matricular no Curso
```http
POST /api/courses/{id}/enroll
```

**Body:**
```json
{
  "student_email": "aluno@email.com",
  "order_id": "order-uuid"
}
```

### 📊 Atualizar Progresso
```http
PUT /api/courses/{id}/progress
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
- [ ] Variáveis de ambiente configuradas
- [ ] Banco de dados acessível
- [ ] SMTP funcionando
- [ ] JWT_SECRET definido
- [ ] CORS configurado para produção
- [ ] Rate limiting configurado

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

### ➕ Criar Webhook
```http
POST /api/webhooks
```

### ✏️ Atualizar Webhook
```http
PUT /api/webhooks/{id}
```

### ❌ Excluir Webhook
```http
DELETE /api/webhooks/{id}
```

### 🧪 Testar Webhook
```http
POST /api/webhooks/{id}/test
```

### 📊 Estatísticas de Webhooks
```http
GET /api/webhooks/stats
```

---

## ⚙️ Configurações

### 📋 Listar Configurações
```http
GET /api/settings
```

### ✏️ Atualizar Configurações
```http
PUT /api/settings
```

### 🎯 Buscar Configuração Específica
```http
GET /api/settings/{key}
```

### ❌ Excluir Configuração
```http
DELETE /api/settings/{key}
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
**Banco de Dados**: PostgreSQL (Neon)
**CORS**: Configurado para frontend
**Rate Limit**: 100 req/window em produção

---

**✅ Documentação Completa - CheckoutPro Backend API v1.0**
*Atualizado em 07/08/2025*
