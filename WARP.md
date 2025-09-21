# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Development Commands

### Core Development
- **Start development server**: `npm run dev` (uses Node.js --watch for auto-reload)
- **Run linting**: `npm run lint`
- **Fix lint issues**: `npm run lint:fix`
- **Format code**: `npm run format`
- **Check formatting**: `npm run format:check`

### Database Operations
- **Generate migrations**: `npm run db:generate`
- **Run migrations**: `npm run db:migrate`
- **Open database studio**: `npm run db:studio`

## Architecture Overview

### Technology Stack
- **Runtime**: Node.js with ES modules
- **Framework**: Express.js 5.x
- **Database**: PostgreSQL with Neon serverless
- **ORM**: Drizzle ORM with Drizzle Kit
- **Validation**: Zod schemas
- **Authentication**: JWT with httpOnly cookies
- **Logging**: Winston with file and console transports
- **Security**: Helmet, CORS, bcrypt for password hashing

### Project Structure
The codebase follows a layered MVC architecture with ES6 modules and path mapping:

```
src/
├── config/          # Configuration modules (database, logger)
├── controllers/     # Request handlers and business logic orchestration
├── models/          # Drizzle ORM database schema definitions
├── routes/          # Express route definitions and middleware
├── services/        # Business logic and data access layer
├── utils/           # Shared utilities (JWT, cookies, formatting)
├── validations/     # Zod schema validation definitions
├── app.js           # Express app configuration and middleware setup
├── server.js        # Server startup and port binding
└── index.js         # Application entry point
```

### Path Mapping System
Uses Node.js subpath imports for clean internal module resolution:
- `#config/*` → `./src/config/*`
- `#controllers/*` → `./src/controllers/*`
- `#middleware/*` → `./src/middleware/*`
- `#models/*` → `./src/models/*`
- `#routes/*` → `./src/routes/*`
- `#services/*` → `./src/services/*`
- `#utils/*` → `./src/utils/*`
- `#validations/*` → `./src/validations/*`

### Database Architecture
- **ORM**: Drizzle with PostgreSQL dialect
- **Connection**: Neon serverless driver (`@neondatabase/serverless`)
- **Migrations**: Located in `drizzle/` directory, managed by Drizzle Kit
- **Models**: Defined using Drizzle's pgTable schema in `src/models/`

Current schema includes:
- `users` table with authentication fields (id, name, email, password, role, timestamps)

### Authentication Flow
- JWT-based authentication with httpOnly cookies for security
- Password hashing with bcrypt (salt rounds: 10)
- Role-based access control (user/admin roles)
- Cookie configuration adapts to environment (secure in production)
- 15-minute cookie expiration with 1-day JWT expiration

### Logging Strategy
Winston logger with structured JSON logging:
- **Development**: Console output with colors + file logging
- **Production**: File-only logging (error.lg for errors, combined.log for all)
- **HTTP requests**: Morgan integration with Winston stream
- **Service**: Tagged as 'acquisitions-api'

### Code Standards
ESLint configuration enforces:
- ES2022 features with module syntax
- 2-space indentation with switch case indentation
- Single quotes, semicolons required
- Prefer const, no var, object shorthand, arrow callbacks
- Unix line endings
- Unused variable detection with underscore prefix exception

## Environment Requirements

### Required Environment Variables
- `DATABASE_URL`: PostgreSQL connection string for Neon database
- `JWT_SECRET`: Secret key for JWT token signing (defaults to development key)
- `PORT`: Server port (defaults to 3000)
- `LOG_LEVEL`: Winston log level (defaults to 'info')
- `NODE_ENV`: Environment setting (affects cookie security and logging)

### Development Setup
1. Install dependencies: `npm install`
2. Configure environment variables in `.env`
3. Generate and run database migrations: `npm run db:generate && npm run db:migrate`
4. Start development server: `npm run dev`

## API Structure

### Available Endpoints
- `GET /` - Welcome message
- `GET /health` - Health check with uptime
- `GET /api` - API status
- `POST /api/auth/sign-up` - User registration (implemented)
- `POST /api/auth/sign-in` - User login (placeholder)
- `POST /api/auth/sign-out` - User logout (placeholder)

### Request/Response Patterns
- **Validation**: Zod schemas with detailed error formatting
- **Error Handling**: Structured error responses with appropriate HTTP status codes
- **Authentication**: JWT tokens in httpOnly cookies
- **Logging**: All operations logged with Winston
- **Security**: Helmet middleware, CORS enabled, input sanitization

## Development Notes

### Module System
- Uses ES6 modules throughout (`"type": "module"` in package.json)
- Import statements use file extensions (.js)
- Path mapping enables clean internal imports

### Database Development
- Drizzle Studio available for database inspection (`npm run db:studio`)
- Schema changes require regenerating migrations
- Models are co-located with migration generation

### Authentication Implementation Status
- User registration (sign-up) fully implemented
- Sign-in and sign-out endpoints exist but need implementation
- JWT utilities and cookie management are complete
- Password comparison function exists but is commented out