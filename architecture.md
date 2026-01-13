## Core Features

### 1.1 User Authentication (Custom)

**Features**

- User registration
- User login
- Password hashing
- JWT-based authentication
- Protected routes

**Requirements**

- Email must be unique
- Password must be hashed (bcrypt)
- JWT stored on frontend (localStorage)
- Refresh tokens optional (advanced)

---

### 1.2 User Profiles

**Features**

- View profile (username, bio, joined date)
- Update profile
- View user’s public posts

**Permissions**

- Users can edit **only their own profile**

---

### 1.3 Blog Posts

**Features**

- Create post
- Read post
- Update post
- Delete post
- Draft / Published state
- Public / Private visibility

**Post Fields**

- title
- content
- visibility (PUBLIC | PRIVATE)
- publishedAt
- authorId

**Permissions**

- Only author can update/delete
- Private posts visible only to author

---

### 1.4 Comment System

**Features**

- Add comment to post
- View comments on post
- Delete own comment

**Permissions**

- Only logged-in users can comment
- Only comment author or post author can delete

---

### 1.5 Search Functionality

**Search Scope**

- Post title
- Post content
- Author username

**Rules**

- Only PUBLIC posts appear in search
- Case-insensitive search
- Pagination required

---

## User Stories

### Authentication

- As a user, I want to register so I can create posts
- As a user, I want to log in securely
- As a user, I want protected routes

### Blogging

- As a user, I want to write a post
- As a user, I want to save a post as private
- As a user, I want to edit my post

### Comments

- As a user, I want to comment on posts
- As a user, I want to delete my comment

---

## Data Models (High Level)

### User

- id
- email
- username
- passwordHash
- bio
- createdAt

### Post

- id
- title
- content
- visibility
- publishedAt
- authorId

### Comment

- id
- content
- authorId
- postId
- createdAt

---

## 6. Validation Strategy (Zod)

- Request body validation
- Query param validation
- Auth payload validation
- Centralized validation middleware

---

## 7. Security Requirements

- Password hashing (bcrypt)
- JWT expiry
- Route-level authorization
- SQL injection prevention (Prisma)
- Input sanitization (Zod)

---

## 8. API Success Metrics

- Clear error messages
- Consistent HTTP status codes
- Proper ownership checks
- Clean separation of layers

---

# High Level Design

## 1. High-Level Architecture

```
Client (React + TS)
        |
        | HTTP (REST API)
        v
Backend (Node + Express + TS)
        |
        | Prisma ORM
        v
PostgreSQL (Supabase)
```

---

## 2. Backend Architecture (Node + Express + TypeScript)

### 2.1 Folder Structure

```
backend/
├── src/
│   ├── app.ts
│   ├── server.ts
│   ├── config/
│   │   ├── env.ts
│   │   └── jwt.ts
│   ├── prisma/
│   │   └── client.ts
│   ├── modules/
│   │   ├── auth/
│   │   │   ├── auth.controller.ts
│   │   │   ├── auth.service.ts
│   │   │   ├── auth.routes.ts
│   │   │   └── auth.schema.ts
│   │   ├── users/
│   │   ├── posts/
│   │   └── comments/
│   ├── middlewares/
│   │   ├── auth.middleware.ts
│   │   ├── error.middleware.ts
│   │   └── validate.middleware.ts
│   ├── utils/
│   │   ├── hash.ts
│   │   └── jwt.ts
│   └── types/
│       └── express.d.ts
├── prisma/
│   ├── schema.prisma
│   └── migrations/
└── tsconfig.json
```

---

### 2.2 Backend Layer Responsibilities

#### Controllers

- Handle HTTP request/response
- Call services
- Return standardized responses

#### Services

- Business logic
- Authorization rules
- Data manipulation

#### Prisma

- Database access only
- No business logic

#### Middleware

- Auth verification
- Validation
- Error handling

---

### 2.3 Authentication Flow

```
POST /auth/register
→ validate input (Zod)
→ hash password
→ save user
→ return success

POST /auth/login
→ validate input
→ compare password
→ generate JWT
→ return token
```

---

### 2.4 Authorization Strategy

- JWT middleware decodes token
- `req.user` injected
- Ownership checks inside services

Example:

```
if (post.authorId !== user.id) throw ForbiddenError
```

---

### 2.5 Prisma Usage Rules

- One Prisma client instance
- Relations defined in schema
- Use transactions for deletes
- No raw SQL (beginner-friendly)

---

## 3. Frontend Architecture (React + TypeScript)

### 3.1 Folder Structure

```
frontend/
├── src/
│   ├── api/
│   │   ├── client.ts
│   │   ├── auth.api.ts
│   │   ├── posts.api.ts
│   ├── pages/
│   │   ├── Login.tsx
│   │   ├── Register.tsx
│   │   ├── Feed.tsx
│   │   ├── Post.tsx
│   │   └── Profile.tsx
│   ├── components/
│   ├── hooks/
│   │   └── useAuth.ts
│   ├── context/
│   │   └── AuthContext.tsx
│   └── types/
│       └── api.ts
```

---

### 3.2 Frontend Responsibilities

- Render UI
- Store JWT
- Call backend APIs
- Protect routes client-side
- Minimal business logic

---

### 3.3 API Communication

- Axios or Fetch
- Authorization header:

```
Authorization: Bearer <token>
```
