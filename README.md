
# TypeScript CRUD API with Node.js, Express & MySQL

Tools Needed: Node.js, MySQL, VS Code, Postman (or curl, vscode extensions like postman, thunderclient or EchoAPI)

Quick Discussion: Why TypeScript?

> Student: "We've been using JavaScript this whole time. Why switch to TypeScript now?"

> Instructor: "Great question! TypeScript adds type safety to your code. Instead of guessing if a variable is a string or number, TypeScript tells you at compile time. This catches bugs early, improves code documentation, and makes working in teams much smoother. Think of it as JavaScript with superpowers!"

## What You'll Learn

By the end of this activity, you will be able to:
- ✅ Set up a TypeScript project with Node.js and Express
- ✅ Define interfaces and types for your API models
- ✅ Build a CRUD API with MySQL using Sequelize and TypeScript
- ✅ Implement JWT authentication with typed middleware
- ✅ Test your API endpoints using Postman or curl
- ✅ Understand the TypeScript compilation workflow

### ![check icon](page_1_image_1_v2.jpg) Prerequisites Checklist

Before you start, make sure you can:
- [ ] Run basic Express routes in JavaScript
- [ ] Use `npm` to install packages
- [ ] Write simple SQL queries or understand database concepts
- [ ] Use Postman or curl to test HTTP endpoints

> 🔔 Don't worry if you're new to TypeScript! We'll walk through the setup together.

---

💬 Discussion: API-First Development

> Student: "Why are we removing the front end for this activity?"

> Instructor: "Because APIs are the backbone of modern applications. Whether your frontend is React, Angular, mobile, or even another backend service, they all talk to your API. By focusing on the API first, you learn to build reliable, well-documented endpoints that any client can consume. Plus, testing APIs directly helps you debug faster!"

---

🧩 What We're Building

A TypeScript-based REST API that manages user records with:


<table>
  <thead>
    <tr>
        <th>Feature</th>
        <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>🔐 Authentication</td>
        <td>JWT-based login/register with bcrypt password hashing</td>
    </tr>
    <tr>
        <td>👥 Role-Based Access</td>
        <td><mark>Admin</mark> and <mark>User</mark> roles with server-side enforcement</td>
    </tr>
    <tr>
        <td>🗄️ Database</td>
        <td>MySQL with Sequelize ORM + TypeScript models</td>
    </tr>
    <tr>
        <td>🧪 Testing</td>
        <td>Test all endpoints with Postman/curl (no frontend needed)</td>
    </tr>
    <tr>
        <td>📦 Structure</td>
        <td>Clean, typed, feature-based architecture</td>
    </tr>
  </tbody>
</table>

🎯 Final Result: A fully typed, tested CRUD API ready for any frontend to consume.

---

🗂️ Project Structure (TypeScript Edition)

```text
 1  typescript-crud-api/
 2  ├── config.json             # Database credentials
 3  ├── tsconfig.json           # TypeScript compiler settings ⭐ NEW
 4  ├── package.json
 5  │
 6  ├── src/
 7  │   ├── server.ts           # Entry point (was server.js) ⭐ NEW
 8  │   │
 9  │   ├── _helpers/
10  │   │   ├── db.ts           # MySQL + Sequelize setup ⭐ NEW
11  │   │   └── role.ts         # Role enum (now typed!) ⭐ NEW
12  │   │
13  │   ├── _middleware/
14  │   │   ├── errorHandler.ts    # Global error handler ⭐ NEW
15  │   │   └── validateRequest.ts # Joi validation wrapper ⭐ NEW
16  │   │
17  │   └── users/
18  │       ├── user.model.ts       # Sequelize User model (typed) ⭐ NEW
19  │       ├── user.service.ts     # Business logic (typed methods) ⭐ NEW
20  │       └── users.controller.ts # Route handlers (typed) ⭐ NEW
21  │
22  └── tests/                  # ⭐ NEW: API test scripts
23      └── users.test.ts       # Example: test CRUD endpoints
```

> 💡 Key Change: All `.js` files become `.ts`, and we add `tsconfig.json` to configure the TypeScript compiler.

---

💬 Discussion: TypeScript vs. JavaScript Compilation

> Student: "Do I need to compile TypeScript every time I make a change?"

> Instructor: "Good catch! During development, we use `ts-node` or `nodemon` + `ts-node` to run TypeScript directly without manual compilation. For production, we compile to JavaScript using `tsc`. We'll set up both workflows so you can focus on coding, not compiling!"

# 🚀 Step-by-Step Implementation

## 🔹 Step 1: Initialize TypeScript Project

# 1. Create project folder
```bash
mkdir typescript-crud-api
cd typescript-crud-api
```

# 2. Initialize Node.js project
```bash
npm init -y
```

# 3. Install runtime dependencies
```bash
npm install express mysql2 sequelize bcryptjs jsonwebtoken cors joi rootpath
```

# 4. Install TypeScript + dev dependencies
```bash
npm install --save-dev typescript ts-node @types/node @types/express @types/cors @types/bcryptjs @types/jsonwebtoken nodemon
```

# 5. Generate tsconfig.json
```bash
npx tsc --init
```

✅ What this does: Sets up TypeScript with sensible defaults for Node.js development.

---

## 🔹 Step 2: Configure TypeScript (`tsconfig.json`)

Update your `tsconfig.json` to:

# json

```json
1 {
2    "compilerOptions": {
3     "target": "ES2020",
4      "module": "commonjs",
5     "lib": ["ES2020"],
6      "outDir": "./dist",
7      "rootDir": "./src",
8     "strict": true,
9     "esModuleInterop": true,
10     "skipLibCheck": true,
11     "forceConsistentCasingInFileNames": true,
12     "resolveJsonModule": true,
13     "baseUrl": "./src",
14     "paths": {
15       "_helpers/*": ["_helpers/*"],
16       "_middleware/*": ["_middleware/*"],
17       "users/*": ["users/*"]
18     }
19    },
20    "include": ["src/**/*"],
21    "exclude": ["node_modules", "dist"]
22 }
```

![checkmark icon](page_5_image_1_v2.jpg) **Key settings explained:**

* `strict: true` → Enables full type checking (catches more bugs!)

* `baseUrl` + `paths` → Allows clean imports like `import { db } from '_helpers/db'`

* `resolveJsonModule: true` → Lets you import `config.json` directly

---

* Step 3: Update `package.json` Scripts

```json
1 "scripts": {
2  "build": "tsc",
3  "start": "node dist/server.js",
4  "start:dev": "nodemon --exec ts-node src/server.ts",
5  "test": "ts-node tests/users.test.ts"
6 }
```

✅ Now you can:

- `npm run start:dev` → Run with auto-reload during development

- `npm run build` → Compile to JavaScript for production

- `npm run test` → Run API tests

### Discussion: Why `ts-node`?

> Student: "What does `ts-node` actually do?"

> Instructor: "`ts-node` is a TypeScript execution engine. It compiles your `.ts` files in memory and runs them with Node.js—no manual `tsc` step needed during development. It's like having a translator that works in real-time!"

#### Step 4: Configure Database (`config.json`)

Create `config.json` in project root:

```json
1 {
2   "database": {
3     "host": "localhost",
4     "port": 3306,
5     "user": "root",
6     "password": "",
7     "database": "typescript_crud_api"
8   },
9   "jwtSecret": "change-this-in-production-123!"
10 }
```

> ⚠️ Security Note: In production, use environment variables (`process.env.JWT_SECRET`) instead of hardcoding secrets.

***

* Step 5: Database Helper (`src/_helpers/db.ts`)

```typescript
// src/_helpers/db.ts
import config from '../../config.json';
import mysql from 'mysql2/promise';
import { Sequelize } from 'sequelize';

  export interface Database {
    User: any; // We'll type this properly after creating the model
  }

  export const db: Database = {} as Database;

  export async function initialize(): Promise<void> {
    const { host, port, user, password, database } = config.database;

    // Create database if it doesn't exist
    const connection = await mysql.createConnection({ host, port, user, password });
    await connection.query(`CREATE DATABASE IF NOT EXISTS \`${database}\`;`);
    await connection.end();

    // Connect to database with Sequelize
    const sequelize = new Sequelize(database, user, password, { dialect: 'mysql' });

    // Initialize models
    const { default: userModel } = await import('../users/user.model');
    db.User = userModel(sequelize);

    // Sync models with database
    await sequelize.sync({ alter: true });

    console.log('✅ Database initialized and models synced');
  }
```

![check mark icon](page_8_image_1_v2.jpg) **TypeScript highlights:**

- `export interface Database` → Defines the shape of our db object

- `Promise<void>` → Explicit return type for async function

- `as Database` → Type assertion for initial empty object

***

* Step 6: Role Enum (`src/_helpers/role.ts`)

![Screenshot of a code editor showing the file src/_helpers/role.ts defining an export enum Role with Admin and User values.](page_9_image_1_v2.jpg)
File Name: role.ts

Code:

TypeScript
// src/_helpers/role.ts
export enum Role {
    Admin = 'Admin',
    User = 'User'
}

## ✅ Why an enum?

Instead of magic strings like `'Admin'`, we use `Role.Admin`. TypeScript will catch typos at compile time!

***

💬 Discussion: Enums vs. String Literals

> Student: "Could we just use string literals instead of an enum?"

> Instructor: "Yes, but enums give you autocomplete and refactoring safety. If you rename `Role.Admin` to `Role.SuperAdmin`, TypeScript updates all references. With strings, you'd have to search-and-replace—and risk missing one!"

***

🔹 Step 7: User Model (`src/users/user.model.ts`)

typescript-crud-api > src > users > TS user.model.ts > default

```typescript
1   // src/users/user.model.ts
2   import { DataTypes, Model, Optional } from 'sequelize';
3   import type { Sequelize } from 'sequelize';
4
5   // Define the attributes interface
6   export interface UserAttributes {
7     id: number;
8     email: string;
9     passwordHash: string;
10    title: string;
11    firstName: string;
12    lastName: string;
13    role: string;
14    createdAt: Date;          // ✅ ADD THIS
15    updatedAt: Date;          // ✅ ADD THIS
16  }
17
18  // Define optional attributes for creation
19  export interface UserCreationAttributes
20    extends Optional<UserAttributes, 'id' | 'createdAt' | 'updatedAt'> {}
21
22  // Define the Sequelize model class
23  export class User
24    extends Model<UserAttributes, UserCreationAttributes>
25    implements UserAttributes {
26
27    public id!: number;
28    public email!: string;
29    public passwordHash!: string;
30    public title!: string;
31    public firstName!: string;
32    public lastName!: string;
33    public role!: string;
34    public readonly createdAt!: Date;      // ✅ ADD THIS
35    public readonly updatedAt!: Date;      // ✅ ADD THIS
36  }
```

![Screenshot of a code editor showing a TypeScript file named user.model.ts.](page_11_image_1_v2.jpg)

```typescript
37
38  // Export the model initializer function
39  export default function (sequelize: Sequelize): typeof User {
40      User.init(
41          {
42              id: {
43                  type: DataTypes.INTEGER,
44                  autoIncrement: true,
45                  primaryKey: true,
46              },
47              email: {
48                  type: DataTypes.STRING,
49                  allowNull: false,
50                  unique: true,
51              },
52              passwordHash: {
53                  type: DataTypes.STRING,
54                  allowNull: false,
55              },
56              title: {
57                  type: DataTypes.STRING,
58                  allowNull: false,
59              },
60              firstName: {
61                  type: DataTypes.STRING,
62                  allowNull: false,
63              },
64              lastName: {
65                  type: DataTypes.STRING,
66                  allowNull: false,
67              },
68              role: {
69                  type: DataTypes.STRING,
70                  allowNull: false,
71              },
```

![Screenshot of a code editor showing a TypeScript Sequelize model definition for a User.](page_12_image_1_v2.jpg)

export default function (sequelize: Sequelize): typeof User {
    createdAt: {
      type: DataTypes.DATE,
      allowNull: false,
      defaultValue: DataTypes.NOW,
    },
    updatedAt: {
      type: DataTypes.DATE,
      allowNull: false,
      defaultValue: DataTypes.NOW,
    },
  },
  {
    sequelize,
    modelName: 'User',
    tableName: 'users',
    timestamps: true, // ✅ Ensure this is true (default)
    defaultScope: {
      attributes: { exclude: ['passwordHash'] },
    },
    scopes: {
      withHash: {
        attributes: { include: ['passwordHash'] },
      },
    },
  }
);

return User;
}




## ✅ TypeScript power moves:

* `UserAttributes` interface → Defines the exact shape of a user object
* `Optional<UserAttributes, ...>` → Marks fields that aren't required on creation
* `typeof User` → Returns the model class type, not an instance

* Step 8: User Service (`src/users/user.service.ts`)

```typescript
// src/users/user.service.ts
import bcrypt from 'bcryptjs';
import { db } from '../_helpers/db';
import { Role } from '../_helpers/role';
import { User, UserCreationAttributes } from './user.model';

export const userService = {
    getAll,
    getById,
    create,
    update,
    delete: _delete,
};

async function getAll(): Promise<User[]> {
    return await db.User.findAll();
}

async function getById(id: number): Promise<User> {
    return await getUser(id);
}

async function create(params: UserCreationAttributes & { password: string }): Promise<void> {
    // Check if email already exists
    const existingUser = await db.User.findOne({ where: { email: params.email } });
    if (existingUser) {
        throw new Error(`Email "${params.email}" is already registered`);
    }

    // Hash password
    const passwordHash = await bcrypt.hash(params.password, 10);

    // Create user (exclude password from saved fields)
    await db.User.create({
        ...params,
        passwordHash,
        role: params.role || Role.User, // Default to User role
    } as UserCreationAttributes);
}
```

```typescript
// user.service.ts
typescript-crud-api > src > users > TS user.service.ts > getUser
40
41  async function update(id: number, params: Partial<UserCreationAttributes> & { password?: string }): Promise<void> {
42      const user = await getUser(id);
43
44      // Hash new password if provided
45      if (params.password) {
46          params.passwordHash = await bcrypt.hash(params.password, 10);
47          delete params.password; // Remove plain password
48      }
49
50      // Update user
51      await user.update(params as Partial<UserCreationAttributes>);
52  }
53
54  async function _delete(id: number): Promise<void> {
55      const user = await getUser(id);
56      await user.destroy();
57  }
58
59  // Helper: Get user or throw error
60  async function getUser(id: number): Promise<User> {
61      const user = await db.User.scope('withHash').findByPk(id);
62      if (!user) {
63          throw new Error('User not found');
64      }
65      return user;
66  }
```

### [x] TypeScript benefits:

- `Promise<User[]>` → Clear return type for async functions

- `Partial<UserCreationAttributes>` → Allows updating only some fields

- Type assertions (`as UserCreationAttributes`) → Safe casting when needed

***

💬 Discussion: Error Handling in TypeScript

> Student: "Why are we using `throw new Error()` instead of just `throw 'string'`?"
> Instructor: "Great observation! In JavaScript, you can throw anything—even strings. But in TypeScript with `strict` mode, it's best practice to throw `Error` objects. They include stack traces, are easier to debug, and work better with typed error handlers. Plus, our global error handler can check `err instanceof Error`!"

***

🔹 Step 9: Middleware (`src/_middleware/`)

Global Error Handler (`errorHandler.ts`)

```typescript
// src/_middleware/errorHandler.ts
import type { Request, Response, NextFunction } from 'express';

export function errorHandler(
  err: Error | string,
  req: Request,
  res: Response,
  next: NextFunction
): Response | void {
  if (typeof err === 'string') {
    // Custom application error
    const is404 = err.toLowerCase().endsWith('not found');
    const statusCode = is404 ? 404 : 400;
    return res.status(statusCode).json({ message: err });
  }

  if (err instanceof Error) {
    // Standard Error object
    return res.status(500).json({ message: err.message });
  }

  // Fallback
  return res.status(500).json({ message: 'Internal server error' });
}
```

Request Validator (`validateRequest.ts`)

```typescript
// src/_middleware/validateRequest.ts
import type { Request, NextFunction } from 'express';
import Joi from 'joi';

export function validateRequest(
  req: Request,
  next: NextFunction,
  schema: Joi.ObjectSchema
): void {
  const options = {
    abortEarly: false,
    allowUnknown: true,
    stripUnknown: true,
  };

  const { error, value } = schema.validate(req.body, options);

  if (error) {
    next(`Validation error: ${error.details.map((d) => d.message).join(', ')}`);
  } else {
    req.body = value;
    next();
  }
}
```

![check mark icon](page_17_image_1_v2.jpg) **TypeScript types used:**

* `Request, Response, NextFunction` → Express built-in types

* `Joi.ObjectSchema` → Type from Joi library

***

* Step 10: Users Controller (`src/users/users.controller.ts`)

TS users.controller.ts ×

typescript-crud-api > src > users > TS users.controller.ts > ⚙️ updateSchema

```typescript
 1   // src/users/users.controller.ts
 2   import type { Request, Response, NextFunction } from 'express';
 3   import { Router } from 'express';
 4   import Joi from 'joi';
 5   import { Role } from '../_helpers/role';
 6   import { validateRequest } from '../_middleware/validateRequest';
 7   import { userService } from './user.service';
 8
 9   const router = Router();
10
11   // 👇 ROUTES
12   router.get('/', getAll);
13   router.get('/:id', getById);
14   router.post('/', createSchema, create);
15   router.put('/:id', updateSchema, update);
16   router.delete('/:id', _delete);
17
18   export default router;
19
20   // 👇 ROUTE HANDLERS (typed)
21   function getAll(req: Request, res: Response, next: NextFunction): void {
22     userService.getAll()
23       .then((users) => res.json(users))
24       .catch(next);
25   }
26
27   function getById(req: Request, res: Response, next: NextFunction): void {
28     userService.getById(Number(req.params.id))
29       .then((user) => res.json(user))
30       .catch(next);
31   }
32
33   function create(req: Request, res: Response, next: NextFunction): void {
34     userService.create(req.body)
35       .then(() => res.json({ message: 'User created' }))
36       .catch(next);
37   }
```

```typescript
TS users.controller.ts ×
typescript-crud-api > src > users > TS users.controller.ts > ...
38
39  function update(req: Request, res: Response, next: NextFunction): void {
40      userService.update(Number(req.params.id), req.body)
41          .then(() => res.json({ message: 'User updated' }))
42          .catch(next);
43  }
44
45  function _delete(req: Request, res: Response, next: NextFunction): void {
46      userService.delete(Number(req.params.id))
47          .then(() => res.json({ message: 'User deleted' }))
48          .catch(next);
49  }
50
51  // 👇 VALIDATION SCHEMAS
52  function createSchema(req: Request, res: Response, next: NextFunction): void {
53      const schema = Joi.object({
54          title: Joi.string().required(),
55          firstName: Joi.string().required(),
56          lastName: Joi.string().required(),
57          role: Joi.string().valid(Role.Admin, Role.User).default(Role.User),
58          email: Joi.string().email().required(),
59          password: Joi.string().min(6).required(),
60          confirmPassword: Joi.string().valid(Joi.ref('password')).required(),
61      });
62      validateRequest(req, next, schema);
63  }
64
65  function updateSchema(req: Request, res: Response, next: NextFunction): void {
66      const schema = Joi.object({
67          title: Joi.string().empty(''),
68          firstName: Joi.string().empty(''),
69          lastName: Joi.string().empty(''),
70          role: Joi.string().valid(Role.Admin, Role.User).empty(''),
71          email: Joi.string().email().empty(''),
72          password: Joi.string().min(6).empty(''),
73          confirmPassword: Joi.string().valid(Joi.ref('password')).empty(''),
74      }).with('password', 'confirmPassword');
75      validateRequest(req, next, schema);
76  }
```

![Checkmark icon](page_19_image_1_v2.jpg) Key TypeScript patterns:

- `type { Request, Response, NextFunction } from 'express'` → Import only types (not values)
- `Number(req.params.id)` → Explicit conversion with type safety
- Route handlers all follow the same typed signature

***

* Step 11: Server Entry Point (`src/server.ts`)

```typescript
// src/server.ts
import express, { Application } from 'express';
import cors from 'cors';
import { errorHandler } from './_middleware/errorHandler';
import { initialize } from './_helpers/db';
import usersController from './users/users.controller';

const app: Application = express();

// Middleware
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.use(cors());

// API Routes
app.use('/users', usersController);

// Global Error Handler (must be last)
app.use(errorHandler);

// Start server + initialize database
const PORT = process.env.PORT || 4000;

initialize()
    .then(() => {
        app.listen(PORT, () => {
            console.log(`✅ Server running on http://localhost:${PORT}`);
            console.log(`🔐 Test with: POST /users with { email, password, ... }`);
        });
    })
    .catch((err) => {
        console.error('❌ Failed to initialize database:', err);
        process.exit(1);
    });
```

### ![check mark icon](page_21_image_1_v2.jpg) TypeScript highlights:

* `Application` type → Typed Express app instance

* `initialize().then()` → Proper async initialization before starting server

- `process.exit(1)` → Exit with error code if DB fails

---

🧪 API Testing Guide (No Frontend Needed!)

> 💬 Discussion: Why Test APIs Directly?
> Student: "Can't we just test with a frontend?"
> Instructor: "You could, but testing APIs directly with Postman/curl is faster and more reliable. You isolate backend bugs from frontend issues, test edge cases easily, and document expected request/response formats. Plus, it's how professional backend devs work!"

---

🔹 Testing Setup

1. Start your API:

```bash
1 npm run start:dev
```

You should see: `✅ Server running on http://localhost:4000`

2. Open Postman (or use curl in terminal)

---

Test 1: Create a User (POST /users)

Request:

```http
1 POST http://localhost:4000/users
2 Content-Type: application/json
3
4 {
5  "title": "Mr",
6  "firstName": "Jane",
7  "lastName": "Smith",
8  "email": "jane@example.com",
9  "password": "secret123",
10 "confirmPassword": "secret123",
11 "role": "User"
12 }
```

Expected Response (200 OK):

```json
1 {
2  "message": "User created"
3 }
```

![Screenshot of EchoAPI for VS Code showing a POST request to create a new user and the successful JSON response.](page_23_image_1_v2.jpg)

```sql
mysql> select * from users;
Empty set (0.00 sec)

mysql> select * from users;
+----+-----------------------+--------------------------------------------------------------+
| id | email                 | passwordHash                                                 |
|    | title | firstName     | lastName | role | createdAt           | updatedAt            |
+----+-----------------------+--------------------------------------------------------------+
|  2 | jane@example.com      | $2b$10$UvKLNQ6zOZOn/BUaRJ4yCuSmloRvkHJlWk2KR8UXsFWmPp1       |
| 2UYQpq | Mr | Jane         | Smith    | User | 2026-02-18 05:28:54 | 2026-02-18           |
| 05:28:54 |                 |          |      |                     |                      |
+----+-----------------------+--------------------------------------------------------------+
1 row in set (0.00 sec)
```

✅ TypeScript validation: If you send `role: "SuperAdmin"`, Joi + TypeScript will reject it because `Role` enum only allows `"Admin"` or `"User"`.

***

### Test 2: Get All Users (GET /users)

#### Request:

```http
http

1 GET http://localhost:4000/users
```

Expected Response (200 OK):

```json
[
  {
    "id": 1,
    "email": "jane@example.com",
    "title": "Mr",
    "firstName": "Jane",
    "lastName": "Smith",
    "role": "User",
    "createdAt": "2026-02-18T10:00:00.000Z",
    "updatedAt": "2026-02-18T10:00:00.000Z"
  }
]
```

![Screenshot of EchoAPI for VS Code showing a GET request to http://localhost:4000/users and the JSON response containing user data.](page_25_image_1_v2.jpg)

⚠️ Note: `passwordHash` is excluded by default (thanks to Sequelize `defaultScope`)!

---

Test 3: Get User by ID (GET /users/:id)

Request:

```http
1 GET http://localhost:4000/users/1
```

Expected Response (200 OK):

```json
1 {
2   "id": 1,
3   "email": "jane@example.com",
4   ...
5 }
```

Error Case (404 Not Found):

```http
1 GET http://localhost:4000/users/999
```

```json
1 {
2   "message": "User not found"
3 }
```

***

Test 4: Update User (PUT /users/:id)

Request:

```http
1 PUT http://localhost:4000/users/1
2 Content-Type: application/json
3 
4 {
5   "firstName": "Janet",
6   "password": "newsecret456",
7   "confirmPassword": "newsecret456"
8 }
```

Expected Response (200 OK):

```json
1 {
2   "message": "User updated"
3 }
```

![checkmark icon](page_27_image_1_v2.jpg) TypeScript safety: The `updateSchema` uses `.empty('')` to make fields optional—TypeScript enforces this at compile time!

---

* Test 5: Delete User (DELETE /users/:id)

Request:

```http
1 DELETE http://localhost:4000/users/1
```

Expected Response (200 OK):

```json
1 {
2  "message": "User deleted"
3 }
```

## Test 6: Validation Errors (Bad Request)

Request (missing required field):

```http
1 POST http://localhost:4000/users
2 Content-Type: application/json
3
4 {
5  "firstName": "Bob"
6  // Missing email, password, etc.
7 }
```

Expected Response (400 Bad Request):

```json
1 {
2  "message": "Validation error: email is required, password is required, ..."
3 }
```

![Checkmark icon](page_28_image_1_v2.jpg) **TypeScript + Joi combo:** TypeScript catches type errors at compile time; Joi catches validation errors at runtime. Double protection!

### ![Wrench and hammer icon](page_28_image_2_v2.jpg) Common Issues & Troubleshooting

<table>
  <thead>
    <tr>
        <th>Problem</th>
        <th>Likely Cause</th>
        <th>TypeScript-Specific Fix</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>❌ "Cannot find module"</td>
        <td>Wrong import path</td>
        <td>Check `tsconfig.json` `paths` config</td>
    </tr>
    <tr>
        <td>❌ "Property 'X' does not exist on type"</td>
        <td>Missing type definition</td>
        <td>Add interface or use `as Type` assertion</td>
    </tr>
    <tr>
        <td>❌ "TS2345: Argument of type 'string' is not assignable"</td>
        <td>Type mismatch</td>
        <td>Check function signatures match Express types</td>
    </tr>
    <tr>
        <td>❌ "Cannot find name 'require'"</td>
        <td>Using CommonJS in ESModule</td>
        <td>Ensure `module: "commonjs"` in `tsconfig.json`</td>
    </tr>
    <tr>
        <td>❌ API not starting</td>
        <td>DB connection failed</td>
        <td>Verify MySQL is running + `config.json` credentials</td>
    </tr>
  </tbody>
</table>

> 💡 Debug tip: Add `console.log(typeof variable)` to check runtime types during development.

***

## Deliverables (Submit These)

1. Code:
 - Complete TypeScript backend (`src/`, `tsconfig.json`, `package.json`)
 - All files properly typed with interfaces/enums
 - `README.md` with setup + testing instructions

2. API Test Evidence (screenshots or curl logs):
 - [x] Successful user creation (POST /users)
 - [x] Get all users (GET /users) showing typed response
 - [x] Validation error for missing field (400 response)
 - [x] Delete user (DELETE /users/:id)

3. Short Reflection (3–5 sentences):
 > "How did TypeScript change your development experience? Did you catch any errors at compile time that would have been runtime bugs in JavaScript?"

***

### 🚀 Extension Challenges (Optional)

Try these if you finish early:

1. 🔐 Add JWT Authentication: Create `/auth/login` and `/auth/register` endpoints with typed request/response interfaces
2. 🧪 Write Automated Tests: Use Jest + Supertest to test endpoints programmatically
3. 📚 Add Swagger Documentation: Use `swagger-jsdoc` + `swagger-ui-express` to auto-generate API docs from TypeScript comments
4. ♻️ Add Pagination: `GET /users?page=2&limit=10` with typed query params
5. 🌐 Add Environment Config: Use `dotenv` + typed environment variables with `zod`

***

💬 Final Discussion: TypeScript in Production

> Student: "Is TypeScript worth the extra setup for real projects?"
> Instructor: "Absolutely! Companies like Microsoft, Slack, and Airbnb use TypeScript because it:
> - 🐛 Catches bugs before deployment
> - 📖 Serves as living documentation
> - 👥 Makes onboarding new devs easier
> - 🔄 Enables safer refactoring

> The 10 minutes you spend setting up `tsconfig.json` can save hours of debugging later. Plus, your future self will thank you!"

***

![books icon](page_30_image_1_v2.jpg) Helpful Resources


<table>
  <thead>
    <tr>
        <th>Resource</th>
        <th>Link</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td><img src="page_30_image_3_v2.jpg" alt="handbook icon"> TypeScript Handbook</td>
        <td>https://www.typescriptlang.org/docs/</td>
    </tr>
    <tr>
        <td><img src="page_30_image_2_v2.jpg" alt="sequelize icon"> Sequelize + TypeScript</td>
        <td>https://sequelize.org/master/manual/typescript.html</td>
    </tr>
    <tr>
        <td><img src="page_30_image_4_v2.jpg" alt="postman icon"> Postman</td>
        <td>https://www.postman.com</td>
    </tr>
    <tr>
        <td><img src="page_30_image_5_v2.jpg" alt="mysql icon"> MySQL Download</td>
        <td>https://dev.mysql.com/downloads/</td>
    </tr>
    <tr>
        <td><img src="page_30_image_6_v2.jpg" alt="express icon"> Express Types</td>
        <td>https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/express</td>
    </tr>
  </tbody>
</table>

***

> ![thinking emoji](page_31_image_1_v2.jpg) Stuck?
> > 1. Run `npx tsc --noEmit` to check for TypeScript errors without compiling
> > 2. Check VS Code's Problems panel for real-time type errors
> > 3. Ask your instructor or a classmate!

***

✅ You've leveled up! You now have a production-ready, typed CRUD API that any frontend can consume. In the next activity, you'll build a React/Angular frontend to connect to this API. Keep coding! ![flexed biceps emoji](page_31_image_2_v2.jpg)

Handout v1.0 | © 2026 Integrative Programming and Technologies | For educational use only