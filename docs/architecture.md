# Express.js Backend Architecture: Layered Architecture Pattern (REST API & TypeScript Reference)

This document defines a comprehensive architecture and folder structure for Express.js backend applications, employing the "Layered Architecture" pattern. It emphasizes REST API design, database integration, security best practices, and leverages TypeScript for robust type safety and maintainability.

---

## Core Express.js Architecture Principles

This layered structure adheres to fundamental backend architectural principles:

* **Separation of Concerns:** Clear boundaries between presentation (routes), business logic (services), data access (models/repositories), and infrastructure (middleware/config).

* **Single Responsibility Principle:** Each layer and module has a well-defined purpose and responsibility.

* **Dependency Inversion:** Higher layers depend on abstractions, not concrete implementations, facilitating testing and modularity.

* **Scalability:** Architecture supports horizontal and vertical scaling through modular design and stateless services.

* **Security by Design:** Built-in security layers including authentication, authorization, input validation, and data sanitization.

* **Type Safety (with TypeScript):** Static type checking reduces runtime errors, improves code documentation, and enhances developer experience.

---

## Mandatory Folder Structure

The root directory organizes the application following industry best practices for enterprise-grade Express.js applications.

```
project-root/
├── src/
│   ├── config/
│   ├── controllers/
│   ├── middleware/
│   ├── models/
│   ├── routes/
│   ├── services/
│   ├── repositories/
│   ├── utils/
│   ├── types/
│   ├── validators/
│   ├── jobs/
│   └── app.ts
├── tests/
├── docs/
├── migrations/
├── seeds/
├── uploads/
├── logs/
├── .env.example
├── .gitignore
├── server.ts
└── package.json
```

---

### 1. `config/`

* **Purpose:** Centralizes all application configuration, environment variables, and external service connections. Provides a single source of truth for application settings.

* **Content:**
    * `database.ts`: Database connection configuration (MongoDB, PostgreSQL, MySQL).
    * `redis.ts`: Redis cache configuration and connection.
    * `cloudinary.ts`: File upload service configuration.
    * `email.ts`: Email service configuration (SendGrid, Nodemailer).
    * `index.ts`: Main configuration export combining all settings.
    * `cors.ts`: CORS policy configuration.
    * `rate-limit.ts`: Rate limiting configuration.

* **Architectural Role:** Enforces **configuration management** best practices, enables environment-specific settings, and centralizes external service integrations.

* **TypeScript Consideration:** Interfaces define configuration objects for type safety and IDE support.

* **Example:**
    ```typescript
    src/config/
    ├── database.ts      // Database connection & options
    ├── redis.ts         // Cache configuration
    ├── email.ts         // Email service settings
    ├── cloudinary.ts    // File upload configuration
    └── index.ts         // Combined config export
    ```

---

### 2. `controllers/`

* **Purpose:** Contains HTTP request handlers that orchestrate business logic execution. Controllers are the entry point for HTTP requests, handling request/response transformation and delegating business logic to services.

* **Content:**
    * `auth.controller.ts`: Authentication endpoints (login, register, password reset).
    * `user.controller.ts`: User management endpoints (CRUD operations).
    * `product.controller.ts`: Product-related endpoints.
    * `file.controller.ts`: File upload/download endpoints.
    * Each controller focuses on a specific domain or resource.

* **Architectural Role:** Implements the **presentation layer** of the application. Handles HTTP-specific concerns like status codes, headers, and request/response formatting while delegating business logic to services.

* **TypeScript Consideration:** Request/Response interfaces ensure type safety for API contracts and middleware integration.

* **Example:**
    ```typescript
    src/controllers/
    ├── auth.controller.ts    // POST /auth/login, /auth/register
    ├── user.controller.ts    // GET/PUT/DELETE /users/:id
    ├── product.controller.ts // CRUD operations for products
    └── file.controller.ts    // POST /upload, GET /files/:id
    ```

---

### 3. `middleware/`

* **Purpose:** Houses reusable middleware functions that process requests before they reach controllers. Implements cross-cutting concerns like authentication, logging, validation, and error handling.

* **Content:**
    * `auth.middleware.ts`: JWT verification, session management, role-based access control.
    * `validation.middleware.ts`: Request validation using Joi, express-validator, or similar.
    * `error.middleware.ts`: Global error handling and formatting.
    * `upload.middleware.ts`: File upload processing with Multer.
    * `rate-limit.middleware.ts`: Request rate limiting and throttling.
    * `cors.middleware.ts`: Cross-origin resource sharing configuration.
    * `security.middleware.ts`: Security headers, helmet integration.

* **Architectural Role:** Implements **aspect-oriented programming** for cross-cutting concerns. Ensures security, validation, and logging are consistently applied across the application.

* **TypeScript Consideration:** Middleware interfaces define request/response extensions and next function types.

* **Example:**
    ```typescript
    src/middleware/
    ├── auth.middleware.ts      // JWT verification, role checking
    ├── validation.middleware.ts // Request schema validation
    ├── error.middleware.ts     // Global error handler
    ├── upload.middleware.ts    // File upload processing
    └── security.middleware.ts  // Security headers, CORS
    ```

---

### 4. `models/`

* **Purpose:** Defines data models, schemas, and database entity definitions. For MongoDB/Mongoose, contains schema definitions. For SQL databases, contains ORM model definitions (Sequelize, TypeORM, Prisma).

* **Content:**
    * `User.model.ts`: User entity with validation, relationships, and methods.
    * `Product.model.ts`: Product schema with business rules.
    * `Order.model.ts`: Order entity with complex relationships.
    * `BaseModel.ts`: Common model functionality (timestamps, soft delete).
    * Database-specific configurations and relationships.

* **Architectural Role:** Implements the **data layer** abstraction. Defines data structure, validation rules, and database relationships while abstracting database-specific implementation details.

* **TypeScript Consideration:** Model interfaces define data shapes, ensuring type safety between database and application layers.

* **Example:**
    ```typescript
    src/models/
    ├── User.model.ts     // User schema with validation
    ├── Product.model.ts  // Product entity definition
    ├── Order.model.ts    // Order with relationships
    └── BaseModel.ts      // Common model functionality
    ```

---

### 5. `routes/`

* **Purpose:** Defines API routing structure, mapping HTTP methods and paths to controller functions. Organizes endpoints logically and applies route-specific middleware.

* **Content:**
    * `auth.routes.ts`: Authentication routes (`/auth/login`, `/auth/register`).
    * `user.routes.ts`: User management routes (`/users`, `/users/:id`).
    * `product.routes.ts`: Product CRUD routes.
    * `api.routes.ts`: Main API router combining all route modules.
    * `index.ts`: Route aggregation and middleware application.

* **Architectural Role:** Implements **API contract definition** and request routing. Centralizes endpoint documentation and applies appropriate middleware chains for each route group.

* **TypeScript Consideration:** Route parameter interfaces and middleware type definitions ensure compile-time route validation.

* **Example:**
    ```typescript
    src/routes/
    ├── auth.routes.ts    // Authentication endpoints
    ├── user.routes.ts    // User management routes
    ├── product.routes.ts // Product CRUD operations
    ├── api.routes.ts     // Main API router
    └── index.ts          // Route aggregation
    ```

---

### 6. `services/`

* **Purpose:** **Core business logic layer.** Contains domain-specific business rules, complex operations, and orchestration between different data sources. Services are called by controllers and can interact with repositories, external APIs, and other services.

* **Content:**
    * `auth.service.ts`: Authentication logic, password hashing, token generation.
    * `user.service.ts`: User business logic, profile management, user relationships.
    * `email.service.ts`: Email sending, template processing, notification logic.
    * `file.service.ts`: File processing, storage management, image manipulation.
    * `payment.service.ts`: Payment processing, transaction management.
    * `notification.service.ts`: Push notifications, real-time updates.

* **Architectural Role:** Implements the **business logic layer**. Encapsulates domain expertise, ensures business rule consistency, and provides reusable business operations across different presentation layers.

* **TypeScript Consideration:** Service interfaces define business operation contracts, ensuring consistent business logic implementation.

* **Example:**
    ```typescript
    src/services/
    ├── auth.service.ts         // Login, registration, password reset
    ├── user.service.ts         // User profile, preferences, relationships  
    ├── email.service.ts        // Email templates, sending logic
    ├── file.service.ts         // Upload, processing, storage
    └── notification.service.ts // Push notifications, alerts
    ```

---

### 7. `repositories/`

* **Purpose:** Implements the **data access layer** following the Repository pattern. Abstracts database operations and provides a consistent interface for data persistence regardless of the underlying database technology.

* **Content:**
    * `user.repository.ts`: User data access operations (CRUD, queries, aggregations).
    * `product.repository.ts`: Product data persistence and retrieval.
    * `base.repository.ts`: Common repository functionality and interfaces.
    * Database-specific implementations for different storage systems.

* **Architectural Role:** Provides **data persistence abstraction**. Enables database technology changes without affecting business logic, supports testing with mock repositories, and centralizes query optimization.

* **TypeScript Consideration:** Repository interfaces define data access contracts, enabling dependency injection and testing.

* **Example:**
    ```typescript
    src/repositories/
    ├── user.repository.ts    // User data access layer
    ├── product.repository.ts // Product persistence operations  
    └── base.repository.ts    // Common repository interface
    ```

---

### 8. `utils/`

* **Purpose:** Contains pure utility functions, helper classes, constants, and shared functionality used across the application. These are framework-agnostic, reusable components.

* **Content:**
    * `logger.ts`: Logging configuration and utilities (Winston, Pino).
    * `crypto.ts`: Encryption, hashing, and security utilities.
    * `date.ts`: Date manipulation and formatting functions.
    * `validation.ts`: Common validation functions and patterns.
    * `constants.ts`: Application-wide constants and enums.
    * `response.ts`: Standardized API response formatting.

* **Architectural Role:** Promotes **code reusability** and **DRY principles**. Centralizes common functionality and provides consistent utility interfaces across the application.

* **TypeScript Consideration:** Utility function types ensure correct usage and return values.

* **Example:**
    ```typescript
    src/utils/
    ├── logger.ts      // Logging configuration
    ├── crypto.ts      // Encryption, hashing utilities
    ├── validation.ts  // Common validation functions
    ├── constants.ts   // Global constants, enums
    └── response.ts    // API response formatting
    ```

---

### 9. `types/`

* **Purpose:** Centralizes TypeScript type definitions, interfaces, enums, and custom types used throughout the application. Provides type safety and documentation for data structures.

* **Content:**
    * `common.ts`: Shared types across multiple domains.
    * `user.ts`: User-related interfaces and types.
    * `product.ts`: Product domain types and enums.
    * `api.ts`: API request/response types and error interfaces.
    * `database.ts`: Database-specific type definitions.
    * `auth.ts`: Authentication and authorization types.

* **Architectural Role:** Ensures **type consistency** across layers and provides **compile-time safety**. Facilitates refactoring and improves code documentation.

* **TypeScript Consideration:** Essential for maintaining type safety in a large codebase with multiple contributors.

* **Example:**
    ```typescript
    src/types/
    ├── common.ts     // ApiResponse, PaginationOptions, etc.
    ├── user.ts       // User, UserProfile, UserRole interfaces
    ├── product.ts    // Product, Category, ProductStatus types
    ├── api.ts        // Request/Response interfaces
    └── auth.ts       // JwtPayload, AuthRequest types
    ```

---

### 10. `validators/`

* **Purpose:** Contains input validation schemas and rules for request validation. Separates validation logic from controllers and middleware for better maintainability.

* **Content:**
    * `user.validator.ts`: User input validation schemas (registration, profile updates).
    * `auth.validator.ts`: Authentication request validation.
    * `product.validator.ts`: Product creation and update validation.
    * `common.validator.ts`: Shared validation rules and patterns.

* **Architectural Role:** Implements **input validation** as a separate concern. Ensures data integrity and security by validating all incoming requests against defined schemas.

* **TypeScript Consideration:** Validation schemas can generate TypeScript types for compile-time validation.

* **Example:**
    ```typescript
    src/validators/
    ├── user.validator.ts    // User input validation
    ├── auth.validator.ts    // Login, registration validation
    ├── product.validator.ts // Product schema validation
    └── common.validator.ts  // Shared validation rules
    ```

---

### 11. `jobs/`

* **Purpose:** Contains background job definitions, scheduled tasks, and queue processing logic. Handles asynchronous operations that don't need immediate response.

* **Content:**
    * `email.jobs.ts`: Email sending background jobs.
    * `cleanup.jobs.ts`: Database cleanup and maintenance tasks.
    * `report.jobs.ts`: Report generation and processing.
    * `scheduler.ts`: Job scheduling configuration.

* **Architectural Role:** Implements **asynchronous processing** and **task scheduling**. Improves application performance by offloading time-consuming operations.

* **Example:**
    ```typescript
    src/jobs/
    ├── email.jobs.ts    // Background email processing
    ├── cleanup.jobs.ts  // Database maintenance
    └── scheduler.ts     // Job scheduling setup
    ```

---

### 12. `app.ts`

* **Purpose:** Main application configuration and middleware setup. Configures Express.js app, applies global middleware, sets up error handling, and integrates all routes.

* **Content:**
    * Express app initialization and configuration.
    * Global middleware application (CORS, helmet, morgan).
    * Route integration and API versioning.
    * Error handling middleware setup.
    * Database connection initialization.

* **Architectural Role:** **Application bootstrap** and configuration. Orchestrates all application components and defines the request processing pipeline.

* **TypeScript Consideration:** Extends Express Request/Response interfaces for custom properties.

---

### 13. `server.ts`

* **Purpose:** Application entry point. Starts the HTTP server, handles graceful shutdown, and manages process-level concerns.

* **Content:**
    * Server startup and port binding.
    * Graceful shutdown handling.
    * Process signal management (SIGTERM, SIGINT).
    * Uncaught exception handling.

* **Architectural Role:** **Process management** and server lifecycle control.

---

## Additional Directories

### `tests/`
```
tests/
├── unit/           # Unit tests for individual functions/classes
├── integration/    # Integration tests for API endpoints
├── fixtures/       # Test data and mock objects
└── setup/         # Test configuration and helpers
```

### `docs/`
```
docs/
├── api/           # API documentation (OpenAPI/Swagger)
├── database/      # Database schema documentation
└── deployment/    # Deployment guides and configurations
```

### `migrations/` & `seeds/`
```
migrations/        # Database migration files
seeds/            # Database seed data for development/testing
```

---

## Benefits of Layered Architecture Pattern

* **Separation of Concerns:** Clear boundaries between different application responsibilities.

* **Testability:** Each layer can be tested independently with mock dependencies.

* **Maintainability:** Changes in one layer don't affect others, reducing coupling.

* **Scalability:** Different layers can be scaled independently based on load patterns.

* **Security:** Security concerns are handled at appropriate layers with defense in depth.

* **Type Safety:** TypeScript provides compile-time error checking and better developer experience.

* **Team Collaboration:** Clear structure enables multiple developers to work on different layers simultaneously.

---

## Best Practices and Considerations

### Code Organization
* **Naming Conventions:** Use consistent naming (PascalCase for classes, camelCase for functions, kebab-case for files).
* **File Structure:** Group related functionality and maintain consistent file organization.
* **Index Files:** Use `index.ts` files for clean imports and module organization.

### Security
* **Input Validation:** Validate all inputs at the controller/middleware level.
* **Authentication:** Implement JWT-based authentication with refresh tokens.
* **Authorization:** Use role-based access control (RBAC) in middleware.
* **Data Sanitization:** Sanitize inputs to prevent injection attacks.

### Performance
* **Caching:** Implement Redis caching for frequently accessed data.
* **Database Optimization:** Use database indexing and query optimization.
* **Response Compression:** Enable gzip compression for API responses.
* **Rate Limiting:** Implement rate limiting to prevent abuse.

### Error Handling
* **Global Error Handler:** Centralized error handling middleware.
* **Custom Errors:** Define custom error classes for different error types.
* **Logging:** Comprehensive logging for debugging and monitoring.
* **Graceful Degradation:** Handle service failures gracefully.

### Development Workflow
* **Environment Configuration:** Use environment-specific configurations.
* **Database Migrations:** Version control database schema changes.
* **API Documentation:** Maintain up-to-date API documentation.
* **Testing Strategy:** Implement unit, integration, and end-to-end tests.

### Scalability Considerations
* **Stateless Design:** Keep services stateless for horizontal scaling.
* **Database Connection Pooling:** Manage database connections efficiently.
* **Microservices Ready:** Architecture supports extraction to microservices.
* **Load Balancing:** Design for load balancer compatibility.

---

## TypeScript Configuration

Ensure proper `tsconfig.json` setup:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "baseUrl": "./",
    "paths": {
      "@/*": ["src/*"],
      "@/config/*": ["src/config/*"],
      "@/controllers/*": ["src/controllers/*"],
      "@/services/*": ["src/services/*"],
      "@/models/*": ["src/models/*"],
      "@/types/*": ["src/types/*"]
    }
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "tests"]
}
```