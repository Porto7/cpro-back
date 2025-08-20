# APIs do Backend Necessárias para o Sistema Completo de Cursos

## 🎓 CRIAÇÃO E GESTÃO DE CURSOS

### 1. Criar Curso
```
POST /api/courses
Headers: Authorization: Bearer {token}
Body: {
  "title": "JavaScript Avançado",
  "description": "Curso completo de JavaScript",
  "shortDescription": "JavaScript do zero ao avançado",
  "thumbnailUrl": "https://cdn.exemplo.com/thumb.jpg",
  "price": 199.90,
  "category": "Programação",
  "level": "intermediate",
  "language": "pt-BR",
  "tags": ["javascript", "programação"],
  "requirements": ["Conhecimento básico de HTML"],
  "whatYouWillLearn": ["Criar aplicações web", "Usar frameworks"],
  "status": "draft",
  "hasLifetimeAccess": true,
  "allowDownloads": true,
  "hasCertificate": false,
  "maxStudents": null,
  "useCustomCheckout": false,
  "checkoutTheme": "default",
  "orderBumpEnabled": false,
  "upsellEnabled": false,
  "seoTitle": "Curso JavaScript Avançado",
  "seoDescription": "Aprenda JavaScript do zero",
  "seoKeywords": ["javascript", "curso"],
  "slug": "javascript-avancado"
}
```

**Resposta de Sucesso:**
```json
{
  "success": true,
  "course": {
    "id": "course_123",
    "title": "JavaScript Avançado",
    "slug": "javascript-avancado",
    "status": "draft",
    "createdAt": "2024-01-15T10:30:00Z",
    "modules": []
  }
}
```

### 2. Atualizar Curso
```
PUT /api/courses/{courseId}
Headers: Authorization: Bearer {token}
Body: {Mesma estrutura do POST}
```

### 3. Buscar Curso Específico
```
GET /api/courses/{courseId}
Headers: Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "id": "course_123",
  "title": "JavaScript Avançado",
  "description": "Curso completo...",
  "modules": [
    {
      "id": "module_456",
      "title": "Módulo 1: Introdução",
      "description": "Conceitos básicos",
      "order": 0,
      "lessons": [
        {
          "id": "lesson_789",
          "title": "Aula 1: Variáveis",
          "description": "Tipos de variáveis",
          "type": "video",
          "order": 0,
          "duration": 900,
          "videoUrl": "https://cdn.exemplo.com/video.mp4",
          "materials": [
            {
              "id": "material_101",
              "name": "Slides da aula",
              "url": "https://cdn.exemplo.com/slides.pdf",
              "type": "pdf"
            }
          ],
          "isPreview": false,
          "isPublished": true
        }
      ],
      "isPublished": true
    }
  ],
  "instructor": {
    "id": "user_123",
    "name": "João Silva",
    "email": "joao@exemplo.com"
  },
  "stats": {
    "totalEnrollments": 150,
    "averageRating": 4.5,
    "totalRevenue": 29985.00
  }
}
```

### 4. Listar Cursos do Instrutor
```
GET /api/courses?instructor_id={userId}
Headers: Authorization: Bearer {token}
Query: ?page=1&limit=10&status=published&category=programacao
```

### 5. Deletar Curso
```
DELETE /api/courses/{courseId}
Headers: Authorization: Bearer {token}
```

## 📂 UPLOAD DE ARQUIVOS

### 6. Upload de Arquivos para Curso
```
POST /api/courses/upload
Headers: Authorization: Bearer {token}, Content-Type: multipart/form-data
Body: FormData {
  file: File,
  type: "thumbnail" | "video" | "material",
  courseId: "course_123"
}
```

**Resposta:**
```json
{
  "success": true,
  "url": "https://cdn.exemplo.com/uploads/video_456.mp4",
  "type": "video",
  "duration": 1800,
  "size": 157286400,
  "filename": "aula-01.mp4"
}
```

## 📝 GERENCIAMENTO DE MÓDULOS

### 7. Criar Módulo
```
POST /api/courses/{courseId}/modules
Headers: Authorization: Bearer {token}
Body: {
  "title": "Módulo 1: Introdução",
  "description": "Conceitos fundamentais",
  "order": 0
}
```

### 8. Atualizar Módulo
```
PUT /api/courses/{courseId}/modules/{moduleId}
Headers: Authorization: Bearer {token}
Body: {
  "title": "Módulo Atualizado",
  "description": "Nova descrição",
  "isPublished": true
}
```

### 9. Deletar Módulo
```
DELETE /api/courses/{courseId}/modules/{moduleId}
Headers: Authorization: Bearer {token}
```

## 🎬 GERENCIAMENTO DE AULAS

### 10. Criar Aula
```
POST /api/courses/{courseId}/modules/{moduleId}/lessons
Headers: Authorization: Bearer {token}
Body: {
  "title": "Aula 1: Variáveis",
  "description": "Tipos de variáveis em JavaScript",
  "type": "video",
  "order": 0,
  "videoUrl": "https://cdn.exemplo.com/video.mp4",
  "duration": 900,
  "isPreview": false
}
```

### 11. Atualizar Aula
```
PUT /api/courses/{courseId}/modules/{moduleId}/lessons/{lessonId}
Headers: Authorization: Bearer {token}
Body: {Mesma estrutura do POST}
```

### 12. Deletar Aula
```
DELETE /api/courses/{courseId}/modules/{moduleId}/lessons/{lessonId}
Headers: Authorization: Bearer {token}
```

## 🎓 SISTEMA DE MATRÍCULAS (Mantido do documento anterior)

### 13. Verificar Status de Matrícula
```
GET /api/courses/{courseId}/enrollment/status
Headers: Authorization: Bearer {token}
```

### 14. Realizar Matrícula
```
POST /api/courses/{courseId}/enroll
Headers: Authorization: Bearer {token}
Body: {
  "paymentMethod": "checkout" | "direct",
  "productId": "prod_123"
}
```

### 15. Webhook de Pagamento Aprovado
```
POST /api/webhooks/payment/success
Headers: X-Webhook-Secret: {secret}
Body: {
  "paymentId": "pay_123456",
  "userId": "user_789",
  "productId": "prod_course_123",
  "courseId": "course_123",
  "amount": 199.90,
  "currency": "BRL"
}
```

## 🔐 ACESSO AO CONTEÚDO

### 16. Verificar Acesso a Aula
```
GET /api/courses/{courseId}/lessons/{lessonId}/access
Headers: Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "hasAccess": true,
  "accessType": "full",
  "downloadAllowed": true,
  "streamUrl": "https://stream.exemplo.com/secure/lesson_789",
  "lesson": {
    "id": "lesson_789",
    "title": "Aula 1: Variáveis",
    "description": "Tipos de variáveis em JavaScript",
    "type": "video",
    "duration": 900,
    "isCompleted": false,
    "watchedPercentage": 45,
    "lastPosition": 405,
    "materials": [
      {
        "id": "material_101",
        "name": "Slides da aula",
        "url": "https://cdn.exemplo.com/materials/slides.pdf",
        "type": "pdf",
        "size": 2048576
      }
    ]
  },
  "nextLesson": {
    "id": "lesson_790",
    "title": "Aula 2: Funções",
    "hasAccess": true
  },
  "prevLesson": {
    "id": "lesson_788",
    "title": "Introdução",
    "hasAccess": true
  }
}
```

### 17. Obter URL de Stream/Download
```
GET /api/courses/{courseId}/lessons/{lessonId}/media?type=stream
Headers: Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "url": "https://stream.exemplo.com/secure/xyz123?token=abc456",
  "expiresAt": "2024-01-15T12:00:00Z",
  "type": "stream",
  "quality": "1080p",
  "watermark": true,
  "allowDownload": false
}
```

## 📈 PROGRESSO E ANALYTICS

### 18. Progresso do Curso
```
GET /api/courses/{courseId}/progress
Headers: Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "course": {
    "id": "course_123",
    "title": "JavaScript Avançado",
    "description": "Curso completo de JavaScript",
    "thumbnailUrl": "https://cdn.exemplo.com/thumb.jpg",
    "instructor": {
      "name": "João Silva",
      "avatar": "https://cdn.exemplo.com/avatar.jpg"
    },
    "totalDuration": 18000,
    "certificateAvailable": true
  },
  "enrollment": {
    "enrolledAt": "2024-01-15T10:30:00Z",
    "expiresAt": "2024-12-31T23:59:59Z",
    "status": "active"
  },
  "progress": {
    "totalLessons": 20,
    "completedLessons": 13,
    "progressPercentage": 65,
    "timeSpent": 7200,
    "estimatedTimeToComplete": 3600,
    "lastAccessed": "2024-01-20T15:45:00Z",
    "completionDate": null,
    "certificateEarned": false
  },
  "modules": [
    {
      "id": "module_456",
      "title": "Módulo 1: Introdução",
      "description": "Conceitos básicos",
      "order": 0,
      "totalLessons": 5,
      "completedLessons": 5,
      "isCompleted": true,
      "lessons": [
        {
          "id": "lesson_789",
          "title": "Aula 1: Variáveis",
          "type": "video",
          "duration": 900,
          "order": 0,
          "isCompleted": true,
          "completedAt": "2024-01-15T11:00:00Z",
          "timeSpent": 900,
          "watchedPercentage": 100,
          "isPreview": false,
          "hasAccess": true
        }
      ]
    }
  ],
  "currentLesson": {
    "id": "lesson_790",
    "moduleId": "module_457",
    "title": "Aula 6: Loops",
    "watchedPercentage": 45
  },
  "nextLesson": {
    "id": "lesson_791",
    "moduleId": "module_457",
    "title": "Aula 7: Arrays"
  }
}
```

### 19. Atualizar Progresso da Aula
```
POST /api/courses/{courseId}/lessons/{lessonId}/progress
Headers: Authorization: Bearer {token}
Body: {
  "completed": true,
  "timeSpent": 900,
  "watchedPercentage": 95,
  "lastPosition": 855
}
```

**Resposta:**
```json
{
  "success": true,
  "lesson": {
    "id": "lesson_789",
    "completed": true,
    "completedAt": "2024-01-20T16:30:00Z",
    "timeSpent": 900,
    "watchedPercentage": 95
  },
  "course": {
    "progressPercentage": 70,
    "completedLessons": 14,
    "totalLessons": 20
  },
  "achievements": [
    {
      "type": "lesson_completed",
      "message": "Parabéns! Você completou mais uma aula!"
    }
  ],
  "nextLesson": {
    "id": "lesson_790",
    "title": "Aula 2: Funções",
    "hasAccess": true
  }
}
```

### 20. Listar Meus Cursos (Aluno)
```
GET /api/courses/my-courses
Headers: Authorization: Bearer {token}
Query: ?status=all|in_progress|completed&page=1&limit=10
```

**Resposta:**
```json
{
  "courses": [
    {
      "id": "course_123",
      "title": "JavaScript Avançado",
      "description": "Curso completo de JavaScript",
      "thumbnailUrl": "https://cdn.exemplo.com/thumb.jpg",
      "instructor": {
        "name": "João Silva",
        "avatar": "https://cdn.exemplo.com/avatar.jpg"
      },
      "enrollment": {
        "enrolledAt": "2024-01-15T10:30:00Z",
        "expiresAt": "2024-12-31T23:59:59Z",
        "status": "active"
      },
      "progress": {
        "percentage": 65,
        "completedLessons": 13,
        "totalLessons": 20,
        "lastAccessed": "2024-01-20T15:45:00Z",
        "timeSpent": 7200,
        "isCompleted": false
      },
      "currentLesson": {
        "id": "lesson_790",
        "title": "Aula 6: Loops",
        "watchedPercentage": 45
      },
      "certificateAvailable": true,
      "downloadAllowed": true,
      "communityAccess": true
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 5,
    "totalPages": 1
  },
  "stats": {
    "totalCourses": 5,
    "completedCourses": 2,
    "inProgressCourses": 3,
    "totalTimeSpent": 25200,
    "certificatesEarned": 2
  }
}
```

## 👨‍🏫 ADMINISTRAÇÃO

### 21. Listar Matriculados em um Curso
```
GET /api/admin/courses/{courseId}/enrollments
Headers: Authorization: Bearer {admin_token}
```

### 22. Gerenciar Acesso Manual
```
POST /api/admin/courses/{courseId}/enrollments
Headers: Authorization: Bearer {admin_token}
Body: {
  "userId": "user_123",
  "action": "grant" | "revoke",
  "expirationDate": "2024-12-31T23:59:59Z",
  "reason": "Cortesia"
}
```

## 🎯 ÁREA DE MEMBROS - INTERFACE DO ALUNO

### 25. Dashboard do Aluno
```
GET /api/student/dashboard
Headers: Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "student": {
    "id": "user_123",
    "name": "Maria Silva",
    "email": "maria@email.com",
    "avatar": "https://cdn.exemplo.com/avatar.jpg",
    "memberSince": "2024-01-15T10:30:00Z"
  },
  "stats": {
    "totalCourses": 5,
    "completedCourses": 2,
    "inProgressCourses": 3,
    "totalTimeSpent": 25200,
    "certificatesEarned": 2,
    "currentStreak": 7,
    "longestStreak": 15
  },
  "recentActivity": [
    {
      "type": "lesson_completed",
      "courseId": "course_123",
      "courseTitle": "JavaScript Avançado",
      "lessonId": "lesson_789",
      "lessonTitle": "Aula 1: Variáveis",
      "timestamp": "2024-01-20T15:45:00Z"
    }
  ],
  "continueWatching": [
    {
      "courseId": "course_123",
      "courseTitle": "JavaScript Avançado",
      "courseThumbnail": "https://cdn.exemplo.com/thumb.jpg",
      "currentLesson": {
        "id": "lesson_790",
        "title": "Aula 6: Loops",
        "watchedPercentage": 45,
        "lastPosition": 405
      },
      "progress": 65
    }
  ],
  "achievements": [
    {
      "id": "first_course",
      "title": "Primeiro Curso",
      "description": "Completou seu primeiro curso",
      "icon": "🎓",
      "earnedAt": "2024-01-18T10:00:00Z"
    }
  ]
}
```

### 26. Página do Curso (Área de Membros)
```
GET /api/student/courses/{courseId}
Headers: Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "course": {
    "id": "course_123",
    "title": "JavaScript Avançado",
    "description": "Curso completo de JavaScript do básico ao avançado",
    "thumbnailUrl": "https://cdn.exemplo.com/thumb.jpg",
    "instructor": {
      "name": "João Silva",
      "avatar": "https://cdn.exemplo.com/instructor.jpg",
      "bio": "Desenvolvedor Full Stack com 10 anos de experiência"
    },
    "settings": {
      "allowDownloads": true,
      "hasCertificate": true,
      "hasLifetimeAccess": true,
      "communityEnabled": true,
      "whatsappGroupUrl": "https://chat.whatsapp.com/xyz"
    }
  },
  "enrollment": {
    "enrolledAt": "2024-01-15T10:30:00Z",
    "expiresAt": "2024-12-31T23:59:59Z",
    "status": "active"
  },
  "progress": {
    "totalLessons": 20,
    "completedLessons": 13,
    "progressPercentage": 65,
    "timeSpent": 7200,
    "estimatedTimeToComplete": 3600,
    "lastAccessed": "2024-01-20T15:45:00Z",
    "certificateEarned": false
  },
  "modules": [
    {
      "id": "module_456",
      "title": "Módulo 1: Introdução",
      "description": "Conceitos básicos de JavaScript",
      "order": 0,
      "totalLessons": 5,
      "completedLessons": 5,
      "isCompleted": true,
      "isLocked": false,
      "lessons": [
        {
          "id": "lesson_789",
          "title": "Aula 1: Variáveis",
          "description": "Tipos de variáveis em JavaScript",
          "type": "video",
          "duration": 900,
          "order": 0,
          "isCompleted": true,
          "completedAt": "2024-01-15T11:00:00Z",
          "timeSpent": 900,
          "watchedPercentage": 100,
          "isPreview": false,
          "isLocked": false,
          "hasAccess": true,
          "thumbnailUrl": "https://cdn.exemplo.com/lesson-thumb.jpg"
        }
      ]
    }
  ],
  "currentLesson": {
    "id": "lesson_790",
    "moduleId": "module_457",
    "title": "Aula 6: Loops",
    "watchedPercentage": 45,
    "lastPosition": 405
  },
  "nextLesson": {
    "id": "lesson_791",
    "moduleId": "module_457",
    "title": "Aula 7: Arrays",
    "isLocked": false
  },
  "communityInfo": {
    "whatsappMembers": 1250,
    "forumPosts": 450,
    "supportEmail": "suporte@exemplo.com"
  }
}
```

### 27. Player de Vídeo com Controles
```
GET /api/student/courses/{courseId}/lessons/{lessonId}/player
Headers: Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "lesson": {
    "id": "lesson_789",
    "title": "Aula 1: Variáveis",
    "description": "Nesta aula você aprenderá sobre os diferentes tipos de variáveis em JavaScript",
    "duration": 900,
    "thumbnailUrl": "https://cdn.exemplo.com/lesson-thumb.jpg"
  },
  "video": {
    "streamUrl": "https://stream.exemplo.com/secure/xyz123?token=abc456",
    "downloadUrl": "https://cdn.exemplo.com/download/xyz123?token=def789",
    "subtitlesUrl": "https://cdn.exemplo.com/subtitles/pt-br.vtt",
    "quality": ["1080p", "720p", "480p", "360p"],
    "defaultQuality": "720p",
    "watermark": {
      "enabled": true,
      "text": "© João Silva - JavaScript Avançado",
      "position": "bottom-right"
    }
  },
  "materials": [
    {
      "id": "material_101",
      "name": "Slides da Aula 1",
      "type": "pdf",
      "url": "https://cdn.exemplo.com/materials/aula1-slides.pdf",
      "size": 2048576,
      "downloadAllowed": true
    },
    {
      "id": "material_102",
      "name": "Código da Aula",
      "type": "zip",
      "url": "https://cdn.exemplo.com/materials/aula1-codigo.zip",
      "size": 1024000,
      "downloadAllowed": true
    }
  ],
  "progress": {
    "isCompleted": false,
    "watchedPercentage": 45,
    "lastPosition": 405,
    "timeSpent": 405
  },
  "navigation": {
    "prevLesson": {
      "id": "lesson_788",
      "title": "Introdução ao Curso",
      "hasAccess": true
    },
    "nextLesson": {
      "id": "lesson_790",
      "title": "Aula 2: Funções",
      "hasAccess": true,
      "isLocked": false
    },
    "currentModule": {
      "id": "module_456",
      "title": "Módulo 1: Introdução",
      "totalLessons": 5,
      "completedLessons": 3
    }
  },
  "settings": {
    "autoPlay": true,
    "playbackSpeed": [0.5, 0.75, 1, 1.25, 1.5, 2],
    "defaultSpeed": 1,
    "allowDownload": true,
    "blockScreenshot": true,
    "sessionTimeout": 3600
  }
}
```

### 28. Marcar Aula como Concluída
```
POST /api/student/courses/{courseId}/lessons/{lessonId}/complete
Headers: Authorization: Bearer {token}
Body: {
  "timeSpent": 900,
  "watchedPercentage": 95,
  "finalPosition": 855,
  "rating": 5,
  "feedback": "Excelente aula!"
}
```

**Resposta:**
```json
{
  "success": true,
  "lesson": {
    "id": "lesson_789",
    "completed": true,
    "completedAt": "2024-01-20T16:30:00Z",
    "timeSpent": 900,
    "watchedPercentage": 95
  },
  "course": {
    "progressPercentage": 70,
    "completedLessons": 14,
    "totalLessons": 20
  },
  "achievements": [
    {
      "type": "lesson_completed",
      "title": "Aula Concluída!",
      "message": "Parabéns! Você completou mais uma aula!",
      "points": 10
    },
    {
      "type": "module_completed",
      "title": "Módulo Finalizado!",
      "message": "Você completou o Módulo 1: Introdução",
      "points": 50
    }
  ],
  "nextRecommendation": {
    "lessonId": "lesson_790",
    "title": "Aula 2: Funções",
    "autoRedirect": true,
    "redirectDelay": 5
  },
  "unlockedContent": [
    {
      "type": "lesson",
      "id": "lesson_795",
      "title": "Aula Bônus: Dicas Avançadas"
    }
  ]
}
```

### 29. Download de Materiais
```
GET /api/student/courses/{courseId}/lessons/{lessonId}/materials/{materialId}/download
Headers: Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "downloadUrl": "https://cdn.exemplo.com/secure-download/material_101?token=xyz789&expires=1234567890",
  "filename": "aula1-slides.pdf",
  "size": 2048576,
  "expiresAt": "2024-01-20T17:00:00Z",
  "downloadCount": 1,
  "maxDownloads": 5
}
```

### 30. Pesquisar no Curso
```
GET /api/student/courses/{courseId}/search
Headers: Authorization: Bearer {token}
Query: ?q=variáveis&type=lessons|materials|transcripts
```

**Resposta:**
```json
{
  "query": "variáveis",
  "results": {
    "lessons": [
      {
        "id": "lesson_789",
        "title": "Aula 1: Variáveis",
        "moduleTitle": "Módulo 1: Introdução",
        "description": "Tipos de variáveis em JavaScript",
        "duration": 900,
        "thumbnailUrl": "https://cdn.exemplo.com/thumb.jpg",
        "relevance": 0.95
      }
    ],
    "materials": [
      {
        "id": "material_101",
        "name": "Guia de Variáveis",
        "lessonTitle": "Aula 1: Variáveis",
        "type": "pdf",
        "relevance": 0.88
      }
    ],
    "transcripts": [
      {
        "lessonId": "lesson_789",
        "lessonTitle": "Aula 1: Variáveis",
        "snippet": "...existem três tipos principais de variáveis em JavaScript...",
        "timestamp": 120,
        "relevance": 0.92
      }
    ]
  },
  "total": 3,
  "suggestions": ["funções", "arrays", "objetos"]
}
```

### 31. Notas Pessoais
```
POST /api/student/courses/{courseId}/lessons/{lessonId}/notes
Headers: Authorization: Bearer {token}
Body: {
  "content": "Lembrar de revisar os tipos de variáveis var, let e const",
  "timestamp": 120,
  "isPrivate": true
}
```

```
GET /api/student/courses/{courseId}/notes
Headers: Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "notes": [
    {
      "id": "note_123",
      "lessonId": "lesson_789",
      "lessonTitle": "Aula 1: Variáveis",
      "content": "Lembrar de revisar os tipos de variáveis var, let e const",
      "timestamp": 120,
      "createdAt": "2024-01-20T15:30:00Z",
      "isPrivate": true
    }
  ],
  "total": 15
}
```

### 32. Certificado de Conclusão
```
GET /api/student/courses/{courseId}/certificate
Headers: Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "certificate": {
    "id": "cert_123456",
    "courseId": "course_123",
    "courseTitle": "JavaScript Avançado",
    "studentName": "Maria Silva",
    "completedAt": "2024-01-25T14:30:00Z",
    "issueDate": "2024-01-25T14:30:00Z",
    "certificateUrl": "https://cdn.exemplo.com/certificates/cert_123456.pdf",
    "verificationCode": "JS-ADV-2024-123456",
    "verificationUrl": "https://seusite.com/verify/cert_123456",
    "instructorName": "João Silva",
    "instructorSignature": "https://cdn.exemplo.com/signatures/joao.png",
    "courseDuration": "20 horas",
    "grade": "A+",
    "skills": ["JavaScript", "Programming", "Web Development"]
  },
  "isEligible": true,
  "requirements": {
    "minProgress": 100,
    "currentProgress": 100,
    "minTimeSpent": 18000,
    "currentTimeSpent": 19800,
    "allLessonsCompleted": true
  }
}
```

### 33. Avaliação do Curso
```
POST /api/student/courses/{courseId}/review
Headers: Authorization: Bearer {token}
Body: {
  "rating": 5,
  "comment": "Excelente curso! Aprendi muito sobre JavaScript.",
  "wouldRecommend": true,
  "aspects": {
    "content": 5,
    "instructor": 5,
    "difficulty": 4,
    "support": 5
  }
}
```

### 34. Fórum/Discussões
```
GET /api/student/courses/{courseId}/discussions
Headers: Authorization: Bearer {token}
Query: ?lesson_id=lesson_789&page=1&limit=10
```

```
POST /api/student/courses/{courseId}/discussions
Headers: Authorization: Bearer {token}
Body: {
  "lessonId": "lesson_789",
  "title": "Dúvida sobre hoisting",
  "content": "Não entendi bem o conceito de hoisting com var...",
  "isQuestion": true
}
```

## 📱 CONFIGURAÇÕES DA ÁREA DE MEMBROS

### 35. Configurações do Player
```
GET /api/student/player-settings
Headers: Authorization: Bearer {token}
```

```
PUT /api/student/player-settings
Headers: Authorization: Bearer {token}
Body: {
  "autoPlay": true,
  "defaultQuality": "720p",
  "playbackSpeed": 1.25,
  "subtitlesEnabled": true,
  "subtitlesLanguage": "pt-BR",
  "rememberPosition": true,
  "skipIntro": false,
  "darkMode": true
}
```

## 🛒 INTEGRAÇÃO COM CHECKOUT

### 23. Configurações de Checkout do Curso
```
PUT /api/courses/{courseId}/checkout-settings
Headers: Authorization: Bearer {token}
Body: {
  "useCustomCheckout": true,
  "checkoutTheme": "modern",
  "orderBumpEnabled": true,
  "orderBumpProduct": "prod_456",
  "orderBumpText": "Adicione este bônus...",
  "orderBumpDiscount": 20,
  "upsellEnabled": true,
  "upsellProducts": ["prod_789", "prod_101"],
  "successUrl": "/courses/{id}/welcome",
  "cancelUrl": "/courses/{id}",
  "conversionPixel": "FB_PIXEL_123",
  "customTrackingCode": "<!-- código -->"
}
```

### 24. Vincular Curso a Produto
```
POST /api/products/{productId}/link-course
Headers: Authorization: Bearer {token}
Body: {
  "courseId": "course_123",
  "autoEnroll": true
}
```

## 📊 MODELOS DE DADOS

### Courses (Tabela Principal)
```sql
CREATE TABLE courses (
  id VARCHAR(50) PRIMARY KEY,
  title VARCHAR(255) NOT NULL,
  description TEXT,
  short_description VARCHAR(500),
  thumbnail_url VARCHAR(500),
  price DECIMAL(10,2) DEFAULT 0,
  category VARCHAR(100),
  level ENUM('beginner', 'intermediate', 'advanced', 'all') DEFAULT 'beginner',
  language VARCHAR(10) DEFAULT 'pt-BR',
  tags JSON,
  requirements JSON,
  what_you_will_learn JSON,
  status ENUM('draft', 'published', 'private', 'archived') DEFAULT 'draft',
  has_lifetime_access BOOLEAN DEFAULT TRUE,
  allow_downloads BOOLEAN DEFAULT TRUE,
  has_certificate BOOLEAN DEFAULT FALSE,
  max_students INT NULL,
  instructor_id VARCHAR(50) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  
  -- SEO
  seo_title VARCHAR(255),
  seo_description VARCHAR(500),
  seo_keywords JSON,
  slug VARCHAR(255) UNIQUE,
  
  -- Checkout
  use_custom_checkout BOOLEAN DEFAULT FALSE,
  checkout_theme VARCHAR(50) DEFAULT 'default',
  order_bump_enabled BOOLEAN DEFAULT FALSE,
  order_bump_product_id VARCHAR(50),
  order_bump_text TEXT,
  order_bump_discount INT DEFAULT 0,
  upsell_enabled BOOLEAN DEFAULT FALSE,
  upsell_products JSON,
  success_url VARCHAR(500),
  cancel_url VARCHAR(500),
  conversion_pixel VARCHAR(255),
  custom_tracking_code TEXT,
  
  -- Configurações Avançadas
  anti_piracy_enabled BOOLEAN DEFAULT FALSE,
  watermark_enabled BOOLEAN DEFAULT FALSE,
  watermark_text VARCHAR(100),
  block_simultaneous_sessions BOOLEAN DEFAULT FALSE,
  device_limit INT NULL,
  welcome_email_enabled BOOLEAN DEFAULT TRUE,
  progress_reminder_enabled BOOLEAN DEFAULT FALSE,
  certificate_email_enabled BOOLEAN DEFAULT TRUE,
  instructor_email VARCHAR(255),
  detailed_tracking_enabled BOOLEAN DEFAULT TRUE,
  weekly_reports_enabled BOOLEAN DEFAULT FALSE,
  google_analytics_id VARCHAR(50),
  facebook_pixel_id VARCHAR(50),
  forum_enabled BOOLEAN DEFAULT FALSE,
  reviews_enabled BOOLEAN DEFAULT TRUE,
  whatsapp_group_enabled BOOLEAN DEFAULT FALSE,
  whatsapp_group_url VARCHAR(500),
  community_url VARCHAR(500),
  support_message TEXT,
  
  INDEX idx_instructor (instructor_id),
  INDEX idx_status (status),
  INDEX idx_category (category),
  INDEX idx_slug (slug)
);
```

### Course_Modules
```sql
CREATE TABLE course_modules (
  id VARCHAR(50) PRIMARY KEY,
  course_id VARCHAR(50) NOT NULL,
  title VARCHAR(255) NOT NULL,
  description TEXT,
  order_index INT NOT NULL,
  is_published BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (course_id) REFERENCES courses(id) ON DELETE CASCADE,
  INDEX idx_course_order (course_id, order_index)
);
```

### Course_Lessons
```sql
CREATE TABLE course_lessons (
  id VARCHAR(50) PRIMARY KEY,
  module_id VARCHAR(50) NOT NULL,
  title VARCHAR(255) NOT NULL,
  description TEXT,
  type ENUM('video', 'text', 'quiz', 'assignment') DEFAULT 'video',
  order_index INT NOT NULL,
  duration INT DEFAULT 0,
  video_url VARCHAR(500),
  content TEXT,
  materials JSON,
  is_preview BOOLEAN DEFAULT FALSE,
  is_published BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (module_id) REFERENCES course_modules(id) ON DELETE CASCADE,
  INDEX idx_module_order (module_id, order_index)
);
```

### Course_Enrollments (Atualizada)
```sql
CREATE TABLE course_enrollments (
  id VARCHAR(50) PRIMARY KEY,
  user_id VARCHAR(50) NOT NULL,
  course_id VARCHAR(50) NOT NULL,
  enrolled_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  expires_at TIMESTAMP NULL,
  status ENUM('active', 'expired', 'cancelled') DEFAULT 'active',
  payment_id VARCHAR(50) NULL,
  progress_percentage DECIMAL(5,2) DEFAULT 0,
  completed_at TIMESTAMP NULL,
  certificate_issued BOOLEAN DEFAULT FALSE,
  created_by ENUM('payment', 'admin', 'system') DEFAULT 'payment',
  FOREIGN KEY (course_id) REFERENCES courses(id) ON DELETE CASCADE,
  UNIQUE KEY unique_enrollment (user_id, course_id)
);
```

### Lesson_Progress (Atualizada)
```sql
CREATE TABLE lesson_progress (
  id VARCHAR(50) PRIMARY KEY,
  enrollment_id VARCHAR(50) NOT NULL,
  lesson_id VARCHAR(50) NOT NULL,
  completed BOOLEAN DEFAULT FALSE,
  completed_at TIMESTAMP NULL,
  time_spent INT DEFAULT 0,
  watched_percentage DECIMAL(5,2) DEFAULT 0,
  last_position INT DEFAULT 0,
  rating INT NULL,
  feedback TEXT NULL,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  FOREIGN KEY (enrollment_id) REFERENCES course_enrollments(id) ON DELETE CASCADE,
  FOREIGN KEY (lesson_id) REFERENCES course_lessons(id) ON DELETE CASCADE,
  UNIQUE KEY unique_progress (enrollment_id, lesson_id)
);
```

### Course_Notes
```sql
CREATE TABLE course_notes (
  id VARCHAR(50) PRIMARY KEY,
  user_id VARCHAR(50) NOT NULL,
  course_id VARCHAR(50) NOT NULL,
  lesson_id VARCHAR(50) NOT NULL,
  content TEXT NOT NULL,
  timestamp INT DEFAULT 0,
  is_private BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  FOREIGN KEY (course_id) REFERENCES courses(id) ON DELETE CASCADE,
  FOREIGN KEY (lesson_id) REFERENCES course_lessons(id) ON DELETE CASCADE,
  INDEX idx_user_course (user_id, course_id)
);
```

### Course_Reviews
```sql
CREATE TABLE course_reviews (
  id VARCHAR(50) PRIMARY KEY,
  user_id VARCHAR(50) NOT NULL,
  course_id VARCHAR(50) NOT NULL,
  rating INT NOT NULL CHECK (rating >= 1 AND rating <= 5),
  comment TEXT,
  would_recommend BOOLEAN DEFAULT TRUE,
  content_rating INT DEFAULT 5,
  instructor_rating INT DEFAULT 5,
  difficulty_rating INT DEFAULT 5,
  support_rating INT DEFAULT 5,
  is_verified BOOLEAN DEFAULT FALSE,
  is_featured BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  FOREIGN KEY (course_id) REFERENCES courses(id) ON DELETE CASCADE,
  UNIQUE KEY unique_review (user_id, course_id),
  INDEX idx_course_rating (course_id, rating)
);
```

### Course_Certificates
```sql
CREATE TABLE course_certificates (
  id VARCHAR(50) PRIMARY KEY,
  enrollment_id VARCHAR(50) NOT NULL,
  certificate_url VARCHAR(500) NOT NULL,
  verification_code VARCHAR(100) UNIQUE NOT NULL,
  issue_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  student_name VARCHAR(255) NOT NULL,
  course_title VARCHAR(255) NOT NULL,
  instructor_name VARCHAR(255) NOT NULL,
  course_duration VARCHAR(50),
  grade VARCHAR(10) DEFAULT 'A',
  skills JSON,
  is_active BOOLEAN DEFAULT TRUE,
  FOREIGN KEY (enrollment_id) REFERENCES course_enrollments(id) ON DELETE CASCADE,
  INDEX idx_verification (verification_code)
);
```

### Course_Discussions
```sql
CREATE TABLE course_discussions (
  id VARCHAR(50) PRIMARY KEY,
  course_id VARCHAR(50) NOT NULL,
  lesson_id VARCHAR(50) NULL,
  user_id VARCHAR(50) NOT NULL,
  parent_id VARCHAR(50) NULL,
  title VARCHAR(255),
  content TEXT NOT NULL,
  is_question BOOLEAN DEFAULT FALSE,
  is_answered BOOLEAN DEFAULT FALSE,
  is_pinned BOOLEAN DEFAULT FALSE,
  votes_up INT DEFAULT 0,
  votes_down INT DEFAULT 0,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  FOREIGN KEY (course_id) REFERENCES courses(id) ON DELETE CASCADE,
  FOREIGN KEY (lesson_id) REFERENCES course_lessons(id) ON DELETE CASCADE,
  FOREIGN KEY (parent_id) REFERENCES course_discussions(id) ON DELETE CASCADE,
  INDEX idx_course_lesson (course_id, lesson_id),
  INDEX idx_parent (parent_id)
);
```

### User_Achievements
```sql
CREATE TABLE user_achievements (
  id VARCHAR(50) PRIMARY KEY,
  user_id VARCHAR(50) NOT NULL,
  course_id VARCHAR(50) NULL,
  achievement_type ENUM('first_lesson', 'first_course', 'course_completed', 'streak_7', 'streak_30', 'fast_learner', 'note_taker', 'community_helper') NOT NULL,
  title VARCHAR(255) NOT NULL,
  description TEXT,
  icon VARCHAR(50),
  points INT DEFAULT 0,
  earned_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (course_id) REFERENCES courses(id) ON DELETE SET NULL,
  UNIQUE KEY unique_achievement (user_id, course_id, achievement_type),
  INDEX idx_user (user_id)
);
```

### Student_Settings
```sql
CREATE TABLE student_settings (
  user_id VARCHAR(50) PRIMARY KEY,
  
  -- Player Settings
  auto_play BOOLEAN DEFAULT TRUE,
  default_quality VARCHAR(10) DEFAULT '720p',
  playback_speed DECIMAL(3,2) DEFAULT 1.00,
  subtitles_enabled BOOLEAN DEFAULT FALSE,
  subtitles_language VARCHAR(10) DEFAULT 'pt-BR',
  remember_position BOOLEAN DEFAULT TRUE,
  skip_intro BOOLEAN DEFAULT FALSE,
  dark_mode BOOLEAN DEFAULT FALSE,
  
  -- Notification Settings
  email_new_lessons BOOLEAN DEFAULT TRUE,
  email_progress_reminder BOOLEAN DEFAULT TRUE,
  email_certificate_ready BOOLEAN DEFAULT TRUE,
  email_community_messages BOOLEAN DEFAULT FALSE,
  push_study_reminder BOOLEAN DEFAULT TRUE,
  push_new_content BOOLEAN DEFAULT TRUE,
  push_achievements BOOLEAN DEFAULT TRUE,
  notification_frequency ENUM('daily', 'weekly', 'never') DEFAULT 'daily',
  
  -- Study Settings
  daily_goal_minutes INT DEFAULT 30,
  reminder_time TIME DEFAULT '19:00:00',
  timezone VARCHAR(50) DEFAULT 'America/Sao_Paulo',
  
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

### Course_Materials
```sql
CREATE TABLE course_materials (
  id VARCHAR(50) PRIMARY KEY,
  lesson_id VARCHAR(50) NOT NULL,
  name VARCHAR(255) NOT NULL,
  type VARCHAR(50) NOT NULL,
  url VARCHAR(500) NOT NULL,
  size BIGINT DEFAULT 0,
  download_count INT DEFAULT 0,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (lesson_id) REFERENCES course_lessons(id) ON DELETE CASCADE
);
```

## ⚙️ CONFIGURAÇÕES NECESSÁRIAS

### Variáveis de Ambiente (Atualizada)
```env
# Upload de Arquivos
UPLOAD_MAX_SIZE=500MB
UPLOAD_ALLOWED_TYPES=mp4,mov,avi,pdf,doc,docx,zip,jpg,png
CDN_BASE_URL=https://cdn.seudominio.com
VIDEO_PROCESSING_ENABLED=true
THUMBNAIL_GENERATION=true

# Segurança e Anti-Piracy
WATERMARK_DEFAULT_TEXT=© Seu Nome
ANTI_PIRACY_ENABLED=true
MAX_SIMULTANEOUS_SESSIONS=1
DEVICE_LIMIT_DEFAULT=3
SESSION_TIMEOUT=3600
DOWNLOAD_TOKEN_EXPIRY=3600
VIDEO_STREAM_SECURITY=true

# Checkout Integration
CHECKOUT_WEBHOOK_SECRET=seu_webhook_secret
CHECKOUT_SUCCESS_URL=https://seusite.com/courses/{courseId}/welcome
CHECKOUT_CANCEL_URL=https://seusite.com/courses/{courseId}

# Email Templates
WELCOME_EMAIL_TEMPLATE=course_welcome
PROGRESS_REMINDER_TEMPLATE=course_progress
CERTIFICATE_EMAIL_TEMPLATE=course_certificate
NEW_LESSON_EMAIL_TEMPLATE=new_lesson_available

# Analytics e Tracking
DEFAULT_TRACKING_ENABLED=true
REPORTS_FREQUENCY=weekly
GOOGLE_ANALYTICS_ENABLED=true
FACEBOOK_PIXEL_ENABLED=true

# Área de Membros
MEMBER_AREA_URL=https://seusite.com/membros
CERTIFICATE_VERIFICATION_URL=https://seusite.com/verify
FORUM_ENABLED=true
COMMUNITY_FEATURES=true
WHATSAPP_INTEGRATION=true

# Player e Streaming
VIDEO_QUALITY_LEVELS=360p,480p,720p,1080p
DEFAULT_VIDEO_QUALITY=720p
SUBTITLES_SUPPORTED=pt-BR,en,es
AUTO_PLAY_DEFAULT=true
PLAYBACK_SPEEDS=0.5,0.75,1,1.25,1.5,2

# Certificados
CERTIFICATE_TEMPLATE=default
CERTIFICATE_SIGNATURE_ENABLED=true
VERIFICATION_CODE_LENGTH=12
```

### Permissions e Roles (Atualizada)
```sql
-- Permissões específicas para área de membros
INSERT INTO permissions (name, description) VALUES
-- Curso Creation (mantidas)
('course.create', 'Criar cursos'),
('course.edit.own', 'Editar próprios cursos'),
('course.edit.all', 'Editar todos os cursos'),
('course.delete.own', 'Deletar próprios cursos'),
('course.delete.all', 'Deletar todos os cursos'),
('course.view.analytics', 'Ver analytics de cursos'),
('course.manage.enrollments', 'Gerenciar matrículas'),
('course.upload.files', 'Upload de arquivos para cursos'),

-- Módulos e Aulas (mantidas)
('module.create', 'Criar módulos'),
('module.edit', 'Editar módulos'),
('module.delete', 'Deletar módulos'),
('lesson.create', 'Criar aulas'),
('lesson.edit', 'Editar aulas'),
('lesson.delete', 'Deletar aulas'),
('lesson.upload.video', 'Upload de vídeos'),

-- Área de Membros - Aluno
('student.access.courses', 'Acessar cursos matriculados'),
('student.watch.videos', 'Assistir vídeos das aulas'),
('student.download.materials', 'Baixar materiais das aulas'),
('student.take.notes', 'Criar notas pessoais'),
('student.participate.discussions', 'Participar de discussões'),
('student.rate.courses', 'Avaliar cursos'),
('student.earn.certificates', 'Obter certificados'),
('student.view.progress', 'Ver próprio progresso'),
('student.update.settings', 'Atualizar configurações pessoais'),

-- Certificados
('certificate.generate', 'Gerar certificados'),
('certificate.verify', 'Verificar certificados'),
('certificate.revoke', 'Revogar certificados'),

-- Comunidade e Discussões
('discussion.create', 'Criar tópicos de discussão'),
('discussion.reply', 'Responder discussões'),
('discussion.moderate', 'Moderar discussões'),
('discussion.pin', 'Fixar discussões importantes'),

-- Analytics Estudante
('analytics.view.own', 'Ver próprias estatísticas'),
('analytics.view.all', 'Ver todas as estatísticas'),

-- Matrículas (mantidas)
('enrollment.create', 'Criar matrículas'),
('enrollment.cancel', 'Cancelar matrículas'),
('content.access', 'Acessar conteúdo de cursos');

-- Roles atualizados
UPDATE roles SET permissions = JSON_ARRAY(
  'student.access.courses',
  'student.watch.videos', 
  'student.download.materials',
  'student.take.notes',
  'student.participate.discussions',
  'student.rate.courses',
  'student.earn.certificates',
  'student.view.progress',
  'student.update.settings',
  'discussion.create',
  'discussion.reply',
  'analytics.view.own',
  'content.access'
) WHERE name = 'student';

UPDATE roles SET permissions = JSON_ARRAY(
  'course.create',
  'course.edit.own',
  'course.delete.own',
  'course.view.analytics',
  'course.manage.enrollments',
  'course.upload.files',
  'module.create',
  'module.edit', 
  'module.delete',
  'lesson.create',
  'lesson.edit',
  'lesson.delete',
  'lesson.upload.video',
  'enrollment.create',
  'enrollment.cancel',
  'certificate.generate',
  'discussion.moderate',
  'analytics.view.own'
) WHERE name = 'instructor';
```

## 🔄 FLUXOS DE INTEGRAÇÃO

### 1. Criação Completa de Curso
```mermaid
graph TD
    A[Criar Curso] --> B[Upload Thumbnail]
    B --> C[Criar Módulos]
    C --> D[Criar Aulas]
    D --> E[Upload Vídeos]
    E --> F[Upload Materiais]
    F --> G[Configurar Checkout]
    G --> H[Publicar Curso]
```

### 2. Compra e Acesso
```mermaid
graph TD
    A[Cliente vê curso] --> B[Clica comprar]
    B --> C[Checkout personalizado]
    C --> D[Pagamento aprovado]
    D --> E[Webhook recebido]
    E --> F[Matrícula criada]
    F --> G[Email de boas-vindas]
    G --> H[Acesso liberado]
```

### 3. Experiência do Aluno na Área de Membros
```mermaid
graph TD
    A[Login na área de membros] --> B[Dashboard pessoal]
    B --> C[Seleciona curso]
    C --> D[Visualiza estrutura]
    D --> E[Assiste aula]
    E --> F[Progresso salvo]
    F --> G[Próxima aula ou certificado]
    G --> H[Avaliação do curso]
```

### 4. Fluxo de Progresso e Certificação
```mermaid
graph TD
    A[Aula iniciada] --> B[Progresso trackado]
    B --> C[Aula concluída]
    C --> D[Verifica pré-requisitos]
    D --> E{Curso 100% completo?}
    E -->|Sim| F[Gera certificado]
    E -->|Não| G[Próxima aula]
    F --> H[Email com certificado]
    G --> I[Continua estudando]
```

## 📋 PRIORIDADES DE IMPLEMENTAÇÃO

### Fase 1 (Essencial - Área de Membros Básica)
1. ✅ Criação de cursos (POST /api/courses)
2. ✅ Upload de arquivos (POST /api/courses/upload)  
3. ✅ Gestão de módulos e aulas
4. ✅ Sistema de matrículas básico
5. 🆕 Dashboard do aluno (GET /api/student/dashboard)
6. 🆕 Visualização do curso (GET /api/student/courses/{courseId})
7. 🆕 Player de vídeo (GET /api/student/courses/{courseId}/lessons/{lessonId}/player)
8. 🆕 Progresso básico de aulas

### Fase 2 (Importante - Funcionalidades Avançadas)
9. ✅ Verificação de acesso ao conteúdo
10. ✅ Progresso detalhado de aulas
11. ✅ Integração com checkout
12. ✅ Webhooks de pagamento
13. 🆕 Sistema de notas pessoais
14. 🆕 Download de materiais
15. 🆕 Pesquisa no curso
16. 🆕 Configurações do player

### Fase 3 (Avançado - Comunidade e Engajamento)
17. ✅ Configurações de segurança e anti-piracy
18. ✅ Analytics detalhado
19. 🆕 Sistema de certificados
20. 🆕 Avaliações e reviews
21. 🆕 Fórum/discussões
22. 🆕 Sistema de conquistas
23. 🆕 Notificações personalizadas
24. 🆕 Relatórios avançados

### Fase 4 (Premium - Recursos Especiais)
25. 🆕 Integração com WhatsApp
26. 🆕 Comunidade externa
27. 🆕 Transmissões ao vivo
28. 🆕 Mentoria 1:1
29. � API móvel nativa
30. 🆕 Gamificação avançada

### Fase 5 (Experiência Premium - Área de Membros Avançada)
31. 🆕 Navegação inteligente e breadcrumbs
32. 🆕 Layout responsivo personalizado
33. 🆕 Dashboard com estatísticas detalhadas
34. 🆕 Recomendações personalizadas por IA
35. 🆕 Sistema completo de gamificação
36. 🆕 Metas e objetivos pessoais
37. 🆕 Sistema de mensagens integrado
38. 🆕 Suporte técnico com tickets
39. 🆕 Rankings e leaderboards
40. 🆕 Challenges e desafios semanais

## 📱 ÁREA DE MEMBROS - NAVEGAÇÃO E INTERFACE

### 37. Menu de Navegação da Área de Membros
```
GET /api/member-area/navigation
Headers: Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "user": {
    "name": "Maria Silva",
    "avatar": "https://cdn.exemplo.com/avatar.jpg",
    "memberSince": "2024-01-15"
  },
  "navigation": {
    "dashboard": {
      "label": "Dashboard",
      "url": "/membros/dashboard",
      "icon": "home",
      "active": true
    },
    "myCourses": {
      "label": "Meus Cursos",
      "url": "/membros/cursos",
      "icon": "book",
      "badge": 3
    },
    "certificates": {
      "label": "Certificados", 
      "url": "/membros/certificados",
      "icon": "award",
      "badge": 2
    },
    "profile": {
      "label": "Perfil",
      "url": "/membros/perfil", 
      "icon": "user"
    },
    "support": {
      "label": "Suporte",
      "url": "/membros/suporte",
      "icon": "help"
    }
  },
  "quickActions": [
    {
      "label": "Continuar Assistindo",
      "courseId": "course_123",
      "lessonId": "lesson_790",
      "progress": 45
    }
  ]
}
```

### 38. Breadcrumb/Navegação Contextual
```
GET /api/member-area/breadcrumb?path=/cursos/{courseId}/aulas/{lessonId}
Headers: Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "breadcrumb": [
    {
      "label": "Dashboard",
      "url": "/membros/dashboard"
    },
    {
      "label": "Meus Cursos", 
      "url": "/membros/cursos"
    },
    {
      "label": "JavaScript Avançado",
      "url": "/membros/cursos/course_123"
    },
    {
      "label": "Módulo 1: Introdução",
      "url": "/membros/cursos/course_123#module_456"
    },
    {
      "label": "Aula 6: Loops",
      "url": "/membros/cursos/course_123/aulas/lesson_790",
      "current": true
    }
  ]
}
```

### 39. Layout Responsivo da Área de Membros
```
GET /api/member-area/layout-config
Headers: Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "layout": {
    "sidebar": {
      "enabled": true,
      "collapsible": true,
      "defaultCollapsed": false
    },
    "header": {
      "showProgress": true,
      "showNotifications": true,
      "showUserMenu": true
    },
    "theme": {
      "primaryColor": "#3B82F6",
      "darkMode": false,
      "customCSS": null
    }
  },
  "features": {
    "progressBar": true,
    "achievementNotifications": true,
    "autoSaveNotes": true,
    "offlineMode": false
  }
}
```

## 📊 DASHBOARD AVANÇADO

### 40. Estatísticas Detalhadas do Aluno
```
GET /api/student/detailed-stats
Headers: Authorization: Bearer {token}
Query: ?period=week|month|year&timezone=America/Sao_Paulo
```

**Resposta:**
```json
{
  "period": "month",
  "stats": {
    "studyTime": {
      "total": 25200,
      "average": 840,
      "goal": 1800,
      "percentage": 46.7
    },
    "courses": {
      "enrolled": 5,
      "completed": 2,
      "inProgress": 3,
      "completionRate": 40
    },
    "lessons": {
      "watched": 47,
      "completed": 35,
      "totalAvailable": 62,
      "completionRate": 74.5
    },
    "achievements": {
      "earned": 8,
      "points": 450,
      "rank": "Estudante Dedicado",
      "nextRank": "Expert",
      "pointsToNext": 150
    },
    "streak": {
      "current": 7,
      "longest": 15,
      "goal": 30
    }
  },
  "weeklyActivity": [
    {
      "date": "2024-01-15",
      "minutes": 120,
      "lessonsCompleted": 3
    }
  ],
  "topCategories": [
    {
      "category": "Programação",
      "timeSpent": 15600,
      "courses": 3
    }
  ]
}
```

### 41. Recomendações Personalizadas
```
GET /api/student/recommendations
Headers: Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "recommendations": {
    "continueWatching": [
      {
        "courseId": "course_123",
        "courseTitle": "JavaScript Avançado",
        "currentLesson": {
          "id": "lesson_790",
          "title": "Aula 6: Loops",
          "progress": 45,
          "estimatedTimeToComplete": 15
        },
        "priority": "high"
      }
    ],
    "similarCourses": [
      {
        "courseId": "course_456",
        "title": "React.js Fundamentals",
        "reason": "Baseado em JavaScript Avançado",
        "rating": 4.8,
        "students": 1250
      }
    ],
    "nextLessons": [
      {
        "courseId": "course_123",
        "lessonId": "lesson_791",
        "title": "Aula 7: Arrays",
        "estimatedTime": 20,
        "difficulty": "intermediate"
      }
    ],
    "skillGaps": [
      {
        "skill": "Async/Await",
        "courses": ["course_789"],
        "priority": "medium"
      }
    ]
  }
}
```

## 🎮 GAMIFICAÇÃO E ENGAJAMENTO

### 42. Sistema de Pontos e Rankings
```
GET /api/student/gamification
Headers: Authorization: Bearer {token}
```

**Resposta:**
```json
{
  "player": {
    "level": 12,
    "xp": 2450,
    "xpToNextLevel": 550,
    "title": "Estudante Dedicado",
    "avatar": "https://cdn.exemplo.com/avatar.jpg"
  },
  "points": {
    "total": 2450,
    "thisWeek": 180,
    "breakdown": {
      "lessonsCompleted": 1200,
      "coursesFinished": 800,
      "dailyStreak": 300,
      "communityParticipation": 150
    }
  },
  "badges": [
    {
      "id": "first_course",
      "name": "Primeiro Curso",
      "description": "Completou o primeiro curso",
      "icon": "🎓",
      "earnedAt": "2024-01-18T10:00:00Z",
      "rarity": "common"
    },
    {
      "id": "speed_learner",
      "name": "Aprendiz Veloz",
      "description": "Completou 5 aulas em um dia",
      "icon": "⚡",
      "earnedAt": "2024-01-22T15:30:00Z",
      "rarity": "rare"
    }
  ],
  "leaderboard": {
    "position": 45,
    "totalUsers": 1250,
    "top3": [
      {
        "position": 1,
        "name": "João Silva",
        "points": 8900,
        "avatar": "https://cdn.exemplo.com/user1.jpg"
      }
    ]
  },
  "challenges": [
    {
      "id": "weekly_goal",
      "title": "Meta Semanal",
      "description": "Complete 10 aulas esta semana",
      "progress": 7,
      "target": 10,
      "reward": "50 XP",
      "expiresAt": "2024-01-28T23:59:59Z"
    }
  ]
}
```

### 43. Metas e Objetivos Pessoais
```
GET /api/student/goals
Headers: Authorization: Bearer {token}
```

```
POST /api/student/goals
Headers: Authorization: Bearer {token}
Body: {
  "type": "daily_study_time",
  "target": 60,
  "period": "daily",
  "startDate": "2024-01-20",
  "endDate": "2024-02-20"
}
```

**Resposta:**
```json
{
  "goals": [
    {
      "id": "goal_123",
      "type": "daily_study_time",
      "target": 60,
      "period": "daily",
      "currentProgress": 45,
      "streak": 7,
      "completedDays": 18,
      "totalDays": 25,
      "status": "active",
      "createdAt": "2024-01-20T10:00:00Z"
    },
    {
      "id": "goal_456", 
      "type": "course_completion",
      "target": 1,
      "period": "monthly",
      "currentProgress": 0.75,
      "status": "in_progress",
      "courseId": "course_123"
    }
  ]
}
```

## 📧 SISTEMA DE COMUNICAÇÃO

### 44. Mensagens do Sistema
```
GET /api/student/messages
Headers: Authorization: Bearer {token}
Query: ?type=system|instructor|support&unread_only=true
```

**Resposta:**
```json
{
  "messages": [
    {
      "id": "msg_123",
      "type": "instructor",
      "from": {
        "name": "João Silva",
        "avatar": "https://cdn.exemplo.com/instructor.jpg",
        "role": "instructor"
      },
      "subject": "Nova aula disponível!",
      "content": "Acabei de adicionar uma nova aula bônus sobre Async/Await...",
      "courseId": "course_123",
      "isRead": false,
      "createdAt": "2024-01-20T10:30:00Z",
      "priority": "normal"
    },
    {
      "id": "msg_124",
      "type": "system",
      "subject": "Parabéns! Certificado disponível",
      "content": "Seu certificado do curso JavaScript Avançado está pronto...",
      "courseId": "course_123",
      "isRead": true,
      "createdAt": "2024-01-19T14:20:00Z",
      "priority": "high",
      "actionButton": {
        "label": "Ver Certificado",
        "url": "/membros/certificados/cert_123"
      }
    }
  ],
  "unreadCount": 3,
  "totalCount": 15
}
```

### 45. Suporte e Tickets
```
GET /api/student/support/tickets
Headers: Authorization: Bearer {token}
```

```
POST /api/student/support/tickets
Headers: Authorization: Bearer {token}
Body: {
  "courseId": "course_123",
  "lessonId": "lesson_790",
  "type": "technical|content|billing|other",
  "subject": "Problema com o vídeo",
  "description": "O vídeo da aula 6 não está carregando...",
  "priority": "medium",
  "attachments": ["screenshot.png"]
}
```

**Resposta:**
```json
{
  "tickets": [
    {
      "id": "ticket_123",
      "subject": "Problema com o vídeo",
      "status": "open",
      "priority": "medium",
      "course": {
        "id": "course_123",
        "title": "JavaScript Avançado"
      },
      "lesson": {
        "id": "lesson_790",
        "title": "Aula 6: Loops"
      },
      "createdAt": "2024-01-20T15:30:00Z",
      "lastReply": "2024-01-20T16:45:00Z",
      "assignedTo": {
        "name": "Suporte Técnico",
        "avatar": "https://cdn.exemplo.com/support.jpg"
      }
    }
  ]
}
```

---

**🎯 RESUMO TOTAL DE APIs ATUALIZADAS:**
- **45 endpoints** documentados (9 novos para área de membros)
- **12 tabelas** de banco de dados
- **4 fluxos** de integração mapeados  
- **5 fases** de implementação definidas
- **📱 Área de Membros Completa** com navegação, dashboard avançado, gamificação e comunicação

## 🏗️ ARQUITETURA UNIFICADA

### 📚 **Sistema de Criação de Cursos** (APIs 1-24)
- Criação e gestão completa de cursos
- Upload de vídeos e materiais  
- Módulos e aulas com drag-drop
- Configurações de checkout personalizadas
- Analytics para instrutores

### 🎓 **Sistema de Matrículas** (APIs 13-15, 21-22)
- Matrícula automática via checkout
- Webhooks de pagamento
- Gestão de acessos
- Controle de expiração

### 👨‍🎓 **Área de Membros - Visualização** (APIs 16-20, 25-36)
- Dashboard personalizado do aluno
- Player de vídeo avançado
- Progresso detalhado por aula
- Sistema de notas e materiais
- Certificados automáticos
- Pesquisa e filtros

### 📱 **Área de Membros - Interface** (APIs 37-39)
- Navegação responsiva
- Breadcrumbs contextuais  
- Layout personalizável
- Tema escuro/claro

### 📊 **Analytics e Inteligência** (APIs 40-41)
- Estatísticas detalhadas
- Recomendações por IA
- Análise de comportamento
- Insights de aprendizado

### 🎮 **Gamificação** (APIs 42-43)
- Sistema de pontos e XP
- Badges e conquistas
- Rankings e leaderboards
- Metas pessoais
- Challenges semanais

### 📧 **Comunicação** (APIs 44-45)
- Mensagens do sistema
- Notificações do instrutor
- Suporte técnico integrado
- Tickets e chat

## 🔄 **COMPATIBILIDADE TOTAL**

✅ **Sistema de Criação** ↔️ **Área de Membros**
- Cursos criados aparecem automaticamente na área de membros
- Módulos e aulas são exibidos na mesma estrutura
- Configurações de checkout são respeitadas
- Analytics unificado entre criação e consumo

✅ **Checkout Existente** ↔️ **Sistema de Cursos**  
- Produtos podem ser vinculados a cursos
- Webhooks criam matrículas automaticamente
- Order bumps e upsells funcionam normalmente
- Temas de checkout personalizados

✅ **Área de Membros** ↔️ **Todas as Funcionalidades**
- Player integrado com anti-piracy
- Progresso sincronizado em tempo real
- Certificados baseados no progresso real
- Gamificação conectada com todas as ações

**📋 Este documento serve como especificação técnica completa e unificada para implementar um sistema de cursos online profissional, desde a criação até o consumo, totalmente integrado com o sistema de checkout existente.**
