# Node.js Application

Modern Node.js backend application.

## Tech Stack

- **Runtime**: Node.js 20+ (LTS)
- **Language**: TypeScript
- **Framework**: Express / Fastify / Hono
- **Database**: PostgreSQL + Prisma / Drizzle
- **Testing**: Vitest / Jest
- **Validation**: Zod

## Project Structure

- `src/` - Source code
  - `routes/` - Route handlers
  - `services/` - Business logic
  - `repositories/` - Data access
  - `middleware/` - Express middleware
  - `utils/` - Utility functions
  - `types/` - TypeScript types
  - `config/` - Configuration
  - `index.ts` - Entry point
- `tests/` - Test files
- `prisma/` - Database schema

## Development Commands

- **Dev**: `npm run dev`
- **Build**: `npm run build`
- **Start**: `npm start`
- **Test**: `npm test`
- **Lint**: `npm run lint`

## Coding Guidelines

### Project Setup

```typescript
// src/index.ts
import express from 'express';
import cors from 'cors';
import helmet from 'helmet';
import { errorHandler } from './middleware/error';
import { userRoutes } from './routes/users';

const app = express();

app.use(helmet());
app.use(cors());
app.use(express.json());

app.use('/api/users', userRoutes);
app.use(errorHandler);

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

### Routes

```typescript
// src/routes/users.ts
import { Router } from 'express';
import { UserController } from '../controllers/user';
import { validate } from '../middleware/validate';
import { createUserSchema, updateUserSchema } from '../schemas/user';

const router = Router();
const controller = new UserController();

router.get('/', controller.getAll);
router.get('/:id', controller.getById);
router.post('/', validate(createUserSchema), controller.create);
router.put('/:id', validate(updateUserSchema), controller.update);
router.delete('/:id', controller.delete);

export { router as userRoutes };
```

### Controllers

```typescript
// src/controllers/user.ts
import { Request, Response, NextFunction } from 'express';
import { UserService } from '../services/user';

export class UserController {
  private userService = new UserService();

  getAll = async (req: Request, res: Response, next: NextFunction) => {
    try {
      const users = await this.userService.findAll();
      res.json(users);
    } catch (error) {
      next(error);
    }
  };

  getById = async (req: Request, res: Response, next: NextFunction) => {
    try {
      const user = await this.userService.findById(req.params.id);
      if (!user) {
        return res.status(404).json({ message: 'User not found' });
      }
      res.json(user);
    } catch (error) {
      next(error);
    }
  };

  create = async (req: Request, res: Response, next: NextFunction) => {
    try {
      const user = await this.userService.create(req.body);
      res.status(201).json(user);
    } catch (error) {
      next(error);
    }
  };
}
```

### Services

```typescript
// src/services/user.ts
import { UserRepository } from '../repositories/user';
import { CreateUserDto, UpdateUserDto } from '../types/user';

export class UserService {
  private repository = new UserRepository();

  async findAll() {
    return this.repository.findAll();
  }

  async findById(id: string) {
    return this.repository.findById(id);
  }

  async create(data: CreateUserDto) {
    // Business logic here
    return this.repository.create(data);
  }

  async update(id: string, data: UpdateUserDto) {
    const existing = await this.repository.findById(id);
    if (!existing) {
      throw new NotFoundError('User not found');
    }
    return this.repository.update(id, data);
  }
}
```

### Validation with Zod

```typescript
// src/schemas/user.ts
import { z } from 'zod';

export const createUserSchema = z.object({
  body: z.object({
    name: z.string().min(2).max(100),
    email: z.string().email(),
    password: z.string().min(8),
  }),
});

export const updateUserSchema = z.object({
  body: z.object({
    name: z.string().min(2).max(100).optional(),
    email: z.string().email().optional(),
  }),
  params: z.object({
    id: z.string().uuid(),
  }),
});

// Middleware
export const validate = (schema: z.ZodSchema) => {
  return (req: Request, res: Response, next: NextFunction) => {
    try {
      schema.parse({
        body: req.body,
        query: req.query,
        params: req.params,
      });
      next();
    } catch (error) {
      if (error instanceof z.ZodError) {
        return res.status(400).json({ errors: error.errors });
      }
      next(error);
    }
  };
};
```

### Error Handling

```typescript
// src/middleware/error.ts
import { Request, Response, NextFunction } from 'express';

export class AppError extends Error {
  constructor(
    public statusCode: number,
    message: string
  ) {
    super(message);
  }
}

export class NotFoundError extends AppError {
  constructor(message = 'Not found') {
    super(404, message);
  }
}

export const errorHandler = (
  err: Error,
  req: Request,
  res: Response,
  next: NextFunction
) => {
  console.error(err);

  if (err instanceof AppError) {
    return res.status(err.statusCode).json({ message: err.message });
  }

  res.status(500).json({ message: 'Internal server error' });
};
```

### Configuration

```typescript
// src/config/index.ts
import { z } from 'zod';

const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'production', 'test']),
  PORT: z.string().transform(Number).default('3000'),
  DATABASE_URL: z.string(),
  JWT_SECRET: z.string(),
});

export const config = envSchema.parse(process.env);
```

### Testing

```typescript
import { describe, it, expect, beforeAll, afterAll } from 'vitest';
import request from 'supertest';
import { app } from '../src/app';

describe('Users API', () => {
  it('GET /api/users returns users', async () => {
    const response = await request(app)
      .get('/api/users')
      .expect(200);

    expect(response.body).toBeInstanceOf(Array);
  });

  it('POST /api/users creates user', async () => {
    const response = await request(app)
      .post('/api/users')
      .send({ name: 'John', email: 'john@example.com' })
      .expect(201);

    expect(response.body.name).toBe('John');
  });
});
```

## Key Guidelines

1. Use async/await with proper error handling
2. Validate all input with Zod
3. Separate concerns (routes, services, repositories)
4. Use environment variables for configuration
5. Implement proper logging and monitoring
