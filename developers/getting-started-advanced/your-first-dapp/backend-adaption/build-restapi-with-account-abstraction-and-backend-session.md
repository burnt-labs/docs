# Account Abstraction for RESTful API with Backend Session

In this guide, we will walk through building a RESTful API app with session management using the [Abstraxion Backend library](https://www.npmjs.com/package/@burnt-labs/abstraxion-backend), demonstrating how to create a web2 user experience without any web3 interactive behavior in frontend. It also includes the Abstraxion account and gasless transaction experience for users, but all the blockchain related implementation will be handled in the backend.

To better understand Account Abstraction you can visit the[Introduction to Account Abstraction](https://docs.burnt.com/xion/developers/learn/intro-to-account-abstraction) page.

A fully functional demo of this app is also available in the [Xion.js](https://github.com/burnt-labs/xion.js/tree/main/apps/backend-session) repository.

## Requirements

Before getting started, ensure you have the following installed:

* [**Node.js**](https://nodejs.org/) (LTS version recommended) – Required for running the development environment and installing dependencies.
* [**pnpm**](https://pnpm.io/) – Package manager for efficient dependency management.
* [**Git**](https://git-scm.com/) – Version control system.

## Project Setup

### 1. Initialize the Project

Create a new Next.js project with TypeScript:

```bash
npx create-next-app@latest backend-session-app --typescript --tailwind --eslint --app --src-dir --import-alias "@/*"
cd backend-session-app
```

### 2. Install Dependencies

Install the required dependencies for the backend session management:

```bash
# Core dependencies
pnpm add @burnt-labs/abstraxion-backend @burnt-labs/abstraxion-core @burnt-labs/constants
pnpm add @prisma/client prisma zod
pnpm add next-auth @auth/prisma-adapter bcryptjs

# Development dependencies
pnpm add -D @types/bcryptjs @types/node tsx jest @testing-library/jest-dom
```

### 3. Project Structure

You can adjust your project structure like this:

```text
src/
├── app/
│   ├── api/
│   │   ├── auth/              # Authentication endpoints
│   │   ├── callback/          # OAuth callback handlers
│   │   └── wallet/            # Wallet management endpoints
│   ├── globals.css
│   ├── layout.tsx
│   └── page.tsx
├── lib/
│   ├── xion/
│   │   ├── abstraxion-backend.ts  # AbstraxionBackend configuration
│   │   └── database.ts            # Database adapter
│   ├── auth.ts                # NextAuth configuration
│   └── validation.ts          # Request validation schemas
└── __tests__/                 # Test files
```

## Environment Configuration

### 1. Environment Variables

Create a `.env.local` file in your project root:

```env
# Database
DATABASE_URL="file:./dev.db"

# XION Configuration
XION_RPC_URL="https://rpc.xion-testnet-2.burnt.com/"
XION_REST_URL="https://api.xion-testnet-2.burnt.com/"
XION_REDIRECT_URL="http://localhost:3000/api/callback/grant_session"
XION_TREASURY="xion1..." # Your treasury address

# NextAuth Configuration
NEXTAUTH_URL="http://localhost:3000"
NEXTAUTH_SECRET="your-nextauth-secret-key-here"

# Security
ENCRYPTION_KEY="your-base64-encoded-aes-256-key-here"

# Session Configuration
SESSION_KEY_EXPIRY_MS=864000000 # 10 days
REFRESH_THRESHOLD_MS=3600000    # 1 hour
```

### 2. Generate Encryption Key

Create a script to generate a secure encryption key:

```typescript
// scripts/generate-key.ts
import crypto from 'crypto';

const key = crypto.randomBytes(32).toString('base64');
console.log('Generated encryption key:', key);
console.log('Add this to your .env.local file as ENCRYPTION_KEY');
```

Run the script:

```bash
npx tsx scripts/generate-key.ts
```

## Database Setup

### 1. Prisma Configuration

Create a `prisma/schema.prisma` file:

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "sqlite"
  url      = env("DATABASE_URL")
}

model User {
  id       String  @id @default(cuid())
  username String  @unique
  email    String? @unique
  password String

  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  // Relations
  sessionKeys SessionKey[]
  accounts    Account[]
  sessions    Session[]

  @@index([username])
  @@index([email])
  @@map("users")
}

model Account {
  id                String  @id @default(cuid())
  userId            String
  type              String
  provider          String
  providerAccountId String
  refresh_token     String?
  access_token      String?
  expires_at        Int?
  token_type        String?
  scope             String?
  id_token          String?
  session_state     String?

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@unique([provider, providerAccountId])
  @@map("accounts")
}

model Session {
  id           String   @id @default(cuid())
  sessionToken String   @unique
  userId       String
  expires      DateTime
  user         User     @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@map("sessions")
}

model SessionKey {
  id     String @id @default(cuid())
  userId String

  sessionKeyAddress  String   @unique
  sessionKeyMaterial String
  sessionKeyExpiry   DateTime
  sessionPermissions String   @default("{}")
  sessionState       String   @default("PENDING")
  metaAccountAddress String

  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@index([userId, sessionKeyAddress])
  @@index([userId, sessionState])
  @@index([userId, sessionKeyExpiry])
  @@index([userId, metaAccountAddress])
  @@index([metaAccountAddress])
  @@map("session_keys")
}

```

### 2. Initialize Database

```bash
# Generate Prisma client
npx prisma generate

# Push schema to database
npx prisma db push
```

## AbstraxionBackend Integration

### 1. Database Adapter

Create the database adapter that implements the `BaseDatabaseAdapter` interface:

```typescript
// src/lib/xion/database.ts
import { PrismaClient, type Prisma } from "@prisma/client";
import type {
  SessionKeyInfo,
  Permissions,
} from "@burnt-labs/abstraxion-backend";
import {
  BaseDatabaseAdapter,
  SessionState,
} from "@burnt-labs/abstraxion-backend";

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined;
};

export const prisma = globalForPrisma.prisma ?? new PrismaClient();

if (process.env.NODE_ENV !== "production") globalForPrisma.prisma = prisma;

export class PrismaDatabaseAdapter extends BaseDatabaseAdapter {
  constructor(private prisma: PrismaClient) {
    super();
  }

  private parseSessionKeyInfo(
    sessionKeyInfo: Prisma.SessionKeyGetPayload<{}>,
  ): SessionKeyInfo {
    return {
      userId: sessionKeyInfo.userId,
      sessionKeyAddress: sessionKeyInfo.sessionKeyAddress,
      sessionKeyMaterial: sessionKeyInfo.sessionKeyMaterial,
      sessionKeyExpiry: sessionKeyInfo.sessionKeyExpiry,
      sessionPermissions: JSON.parse(
        sessionKeyInfo.sessionPermissions,
      ) as Permissions,
      sessionState: sessionKeyInfo.sessionState as SessionState,
      metaAccountAddress: sessionKeyInfo.metaAccountAddress,
      createdAt: sessionKeyInfo.createdAt,
      updatedAt: sessionKeyInfo.updatedAt,
    };
  }

  async getLastSessionKey(userId: string): Promise<SessionKeyInfo | null> {
    const sessionKey = await this.prisma.sessionKey.findFirst({
      where: { userId },
      orderBy: { createdAt: "desc" },
    });

    if (!sessionKey) {
      return null;
    }

    return this.parseSessionKeyInfo(sessionKey);
  }

  async getSessionKey(
    userId: string,
    sessionKeyAddress: string,
  ): Promise<SessionKeyInfo | null> {
    const sessionKey = await this.prisma.sessionKey.findUnique({
      where: {
        userId,
        sessionKeyAddress,
      },
    });

    if (!sessionKey) {
      return null;
    }

    return this.parseSessionKeyInfo(sessionKey);
  }

  async getActiveSessionKeys(userId: string): Promise<SessionKeyInfo[]> {
    const sessionKeys = await this.prisma.sessionKey.findMany({
      where: {
        userId,
        sessionState: SessionState.ACTIVE,
      },
    });

    if (!sessionKeys) {
      return [];
    }

    return sessionKeys.map((sessionKey) =>
      this.parseSessionKeyInfo(sessionKey),
    );
  }

  async revokeSessionKey(
    userId: string,
    sessionKeyAddress: string,
  ): Promise<boolean> {
    const result = await this.prisma.sessionKey.update({
      where: {
        userId,
        sessionKeyAddress,
      },
      data: {
        sessionState: SessionState.REVOKED,
      },
    });
    return result !== null;
  }

  async revokeActiveSessionKeys(userId: string): Promise<void> {
    await this.prisma.sessionKey.updateMany({
      where: {
        userId,
        sessionState: SessionState.ACTIVE,
      },
      data: {
        sessionState: SessionState.REVOKED,
      },
    });
  }

  async addNewSessionKey(
    userId: string,
    updates: Pick<
      SessionKeyInfo,
      "sessionKeyAddress" | "sessionKeyMaterial" | "sessionKeyExpiry"
    >,
    activeState?: Pick<
      SessionKeyInfo,
      "metaAccountAddress" | "sessionPermissions"
    >,
  ): Promise<void> {
    const updateData: Prisma.SessionKeyCreateInput = {
      user: {
        connect: {
          id: userId,
        },
      },
      sessionKeyAddress: updates.sessionKeyAddress,
      sessionKeyMaterial: updates.sessionKeyMaterial,
      sessionKeyExpiry: updates.sessionKeyExpiry,
      sessionState: SessionState.PENDING,
      sessionPermissions: JSON.stringify({}),
      metaAccountAddress: "",
    };
    
    if (activeState) {
      updateData.sessionState = SessionState.ACTIVE;
      updateData.metaAccountAddress = activeState.metaAccountAddress;
      updateData.sessionPermissions = JSON.stringify(
        activeState.sessionPermissions,
      );
    }
    
    await this.prisma.sessionKey.create({
      data: updateData,
    });
  }

  async updateSessionKeyWithParams(
    userId: string,
    sessionKeyAddress: string,
    updates: Partial<
      Pick<
        SessionKeyInfo,
        "sessionState" | "sessionPermissions" | "metaAccountAddress"
      >
    >,
  ): Promise<void> {
    const updateData: Prisma.SessionKeyUpdateInput = {};
    if (updates.sessionPermissions) {
      updateData.sessionPermissions = JSON.stringify(
        updates.sessionPermissions,
      );
    }
    if (updates.sessionState) {
      updateData.sessionState = updates.sessionState;
    }
    if (updates.metaAccountAddress) {
      updateData.metaAccountAddress = updates.metaAccountAddress;
    }

    await this.prisma.sessionKey.update({
      where: {
        userId,
        sessionKeyAddress,
      },
      data: updateData,
    });
  }

  async close(): Promise<void> {
    await this.prisma.$disconnect();
  }
}
```

### 2. AbstraxionBackend Configuration

Create the main configuration file:

```typescript
// src/lib/xion/abstraxion-backend.ts
import { AbstraxionBackend } from "@burnt-labs/abstraxion-backend";
import { PrismaDatabaseAdapter, prisma } from "./database";

const globalForAbstraxion = globalThis as unknown as {
  abstraxionBackend: AbstraxionBackend | undefined;
};

export function getAbstraxionBackend(): AbstraxionBackend {
  if (globalForAbstraxion.abstraxionBackend) {
    return globalForAbstraxion.abstraxionBackend;
  }

  // Ensure all environment variables are set
  if (!process.env.XION_RPC_URL) {
    throw new Error("XION_RPC_URL is not set");
  }
  if (!process.env.XION_REDIRECT_URL) {
    throw new Error("XION_REDIRECT_URL is not set");
  }
  if (!process.env.XION_TREASURY) {
    throw new Error("XION_TREASURY is not set");
  }
  if (!process.env.ENCRYPTION_KEY) {
    throw new Error("ENCRYPTION_KEY is not set");
  }

  const databaseAdapter = new PrismaDatabaseAdapter(prisma);

  const config = {
    rpcUrl: process.env.XION_RPC_URL,
    redirectUrl: process.env.XION_REDIRECT_URL,
    treasury: process.env.XION_TREASURY,
    encryptionKey: process.env.ENCRYPTION_KEY,
    databaseAdapter,
    sessionKeyExpiryMs: parseInt(
      process.env.SESSION_KEY_EXPIRY_MS || "86400000",
    ),
    refreshThresholdMs: parseInt(process.env.REFRESH_THRESHOLD_MS || "3600000"),
  };

  globalForAbstraxion.abstraxionBackend = new AbstraxionBackend(config);
  return globalForAbstraxion.abstraxionBackend;
}
```

## Authentication Configuration

### NextAuth Setup

Set up NextAuth for user authentication:

```typescript
// src/lib/auth.ts
import type { NextAuthOptions } from "next-auth";
import CredentialsProvider from "next-auth/providers/credentials";
import bcrypt from "bcryptjs";
import { z } from "zod";
import { prisma } from "@/lib/xion/database";

const loginSchema = z.object({
  username: z.string().min(1, "Username is required"),
  password: z.string().min(6, "Password must be at least 6 characters"),
});

const registerSchema = z.object({
  username: z.string().min(3, "Username must be at least 3 characters"),
  email: z.string().email("Invalid email address").optional(),
  password: z.string().min(6, "Password must be at least 6 characters"),
});

export const authOptions: NextAuthOptions = {
  providers: [
    CredentialsProvider({
      name: "credentials",
      credentials: {
        username: { label: "Username", type: "text" },
        password: { label: "Password", type: "password" },
      },
      async authorize(credentials) {
        if (!credentials?.username || !credentials.password) {
          return null;
        }

        try {
          // Validate input
          const { username, password } = loginSchema.parse(credentials);

          // Find user by username
          const user = await prisma.user.findUnique({
            where: { username },
          });

          if (!user) {
            return null;
          }

          // Verify password
          const isValidPassword = await bcrypt.compare(
            password,
            (user as any).password,
          );
          if (!isValidPassword) {
            return null;
          }

          // Return user object (without password)
          return {
            id: user.id,
            username: user.username,
            email: user.email,
          };
        } catch (error) {
          console.error("Auth error:", error);
          return null;
        }
      },
    }),
  ],
  session: {
    strategy: "jwt",
  },
  callbacks: {
    async jwt({ token, user }) {
      if (user) {
        token.id = user.id;
        token.username = user.username;
      }
      return token;
    },
    async session({ session, token }) {
      if (token) {
        session.user.id = token.id;
        session.user.username = token.username;
      }
      return session;
    },
  },
  pages: {
    signIn: "/auth/signin",
  },
  secret: process.env.NEXTAUTH_SECRET,
};

// Helper function to hash passwords
export async function hashPassword(password: string): Promise<string> {
  return bcrypt.hash(password, 12);
}

// Export the schemas for use in API routes
export { loginSchema, registerSchema };

// Helper function to validate registration data
export function validateRegistration(data: unknown) {
  return registerSchema.parse(data);
}

// Helper function to validate login data
export function validateLogin(data: unknown) {
  return loginSchema.parse(data);
}
```

## Request Validation

The demo uses Zod for request validation. For detailed validation schemas and implementation, refer to the [validation.ts](https://github.com/burnt-labs/xion.js/blob/main/apps/backend-session/src/lib/validation.ts) file in the demo repository.

## RESTful API Endpoints

### API Utilities

Before implementing the API endpoints, it's important to understand the utility functions used throughout the implementation:

* **`createApiWrapper`** - A wrapper function that encapsulates Next.js API routes with common functionality including request validation, rate limiting, error handling, and response formatting. For detailed implementation, refer to the [api-wrapper.ts](https://github.com/burnt-labs/xion.js/blob/main/apps/backend-session/src/lib/api-wrapper.ts) file in the demo repository.

* **`requireAuth`** - A NextAuth.js middleware function that handles user authentication and session validation for API routes. It ensures that only authenticated users can access protected endpoints. For detailed implementation, refer to the [auth-middleware.ts](https://github.com/burnt-labs/xion.js/blob/main/apps/backend-session/src/lib/auth-middleware.ts) file in the demo repository.

### 1. Wallet Connection Endpoint

Create the wallet connection API:

```typescript
// src/app/api/wallet/connect/route.ts
import { getAbstraxionBackend } from "@/lib/xion/abstraxion-backend";
import { connectWalletSchema } from "@/lib/validation";
import { createApiWrapper } from "@/lib/api-wrapper";
import { requireAuth } from "@/lib/auth-middleware";

export const dynamic = "force-dynamic";

export const POST = createApiWrapper(
  async (context) => {
    const { validatedData } = context;
    const { permissions, grantedRedirectUrl } = validatedData;

    // Get authenticated user from session
    const authContext = await requireAuth(context.request);
    const { user } = authContext;

    // Get AbstraxionBackend instance
    const abstraxionBackend = getAbstraxionBackend();

    // Initiate wallet connection
    const result = await abstraxionBackend.connectInit(
      user.id,
      permissions,
      grantedRedirectUrl,
    );

    return result;
  },
  {
    schema: connectWalletSchema,
    schemaType: "body",
    rateLimit: "strict",
    allowedMethods: ["POST"],
  },
);
```

### 2. Wallet Status Endpoint

Create the wallet status check API:

```typescript
// src/app/api/wallet/status/route.ts
import { getAbstraxionBackend } from "@/lib/xion/abstraxion-backend";
import { createApiWrapper } from "@/lib/api-wrapper";
import { requireAuth } from "@/lib/auth-middleware";

export const dynamic = "force-dynamic";

export const GET = createApiWrapper(
  async (context) => {
    // Get authenticated user from session
    const authContext = await requireAuth(context.request);
    const { user } = authContext;

    // Get AbstraxionBackend instance
    const abstraxionBackend = getAbstraxionBackend();

    // Check status
    const result = await abstraxionBackend.checkStatus(user.id);

    return result;
  },
  {
    rateLimit: "normal",
    allowedMethods: ["GET"],
  },
);
```

### 3. Wallet Disconnect Endpoint

Create the wallet disconnect API:

```typescript
// src/app/api/wallet/disconnect/route.ts
import { getAbstraxionBackend } from "@/lib/xion/abstraxion-backend";
import { createApiWrapper } from "@/lib/api-wrapper";
import { requireAuth } from "@/lib/auth-middleware";
import { ApiException } from "@/lib/api-response";

export const dynamic = "force-dynamic";

export const DELETE = createApiWrapper(
  async (context) => {
    // Get authenticated user from session
    const authContext = await requireAuth(context.request);
    const { user } = authContext;

    // Get AbstraxionBackend instance
    const abstraxionBackend = getAbstraxionBackend();

    // Disconnect wallet
    const result = await abstraxionBackend.disconnect(user.id);

    if (!result.success) {
      throw new ApiException(
        result.error || "Disconnect failed",
        400,
        "DISCONNECT_FAILED",
      );
    }

    return result;
  },
  {
    rateLimit: "normal",
    allowedMethods: ["DELETE"],
  },
);
```

### 4. XION MetaAccount Authentication Callback Handler

Create the OAuth callback handler:

```typescript
// src/app/api/callback/grant_session/route.ts
import { getAbstraxionBackend } from "@/lib/xion/abstraxion-backend";
import { grantSessionCallbackSchema } from "@/lib/validation";
import { createApiWrapper, handleRedirectResponse } from "@/lib/api-wrapper";
import { ApiException } from "@/lib/api-response";

export const dynamic = "force-dynamic";

export const GET = createApiWrapper(
  async (context) => {
    const { validatedData } = context;
    const { granted, granter, state } = validatedData;

    // Get AbstraxionBackend instance
    const abstraxionBackend = getAbstraxionBackend();

    // Handle callback
    const result = await abstraxionBackend.handleCallback({
      granted,
      granter,
      state,
    });

    if (!result.success) {
      throw new ApiException(
        result.error || "Callback failed",
        400,
        "CALLBACK_FAILED",
      );
    }

    // Use the generic redirect handler
    return handleRedirectResponse(result, result.grantedRedirectUrl);
  },
  {
    schema: grantSessionCallbackSchema,
    schemaType: "query",
    rateLimit: "strict",
    allowedMethods: ["GET"],
  },
);
```

### 5. Transaction Endpoint

Create a transaction sending endpoint:

```typescript
// src/app/api/wallet/transaction/send/route.ts
import { getAbstraxionBackend } from "@/lib/xion/abstraxion-backend";
import { createApiWrapper } from "@/lib/api-wrapper";
import { requireAuth } from "@/lib/auth-middleware";
import {
  sendTransactionSchema,
  SendTransactionRequest,
} from "@/lib/validation";
import { MsgSend } from "cosmjs-types/cosmos/bank/v1beta1/tx";

export const dynamic = "force-dynamic";

export const POST = createApiWrapper(
  async (context) => {
    const { validatedData } = context;
    const { to, amount, denom } = validatedData as SendTransactionRequest;

    // Get authenticated user from session
    const authContext = await requireAuth(context.request);
    const { user } = authContext;

    // Get AbstraxionBackend instance
    const abstraxionBackend = getAbstraxionBackend();

    // Check if user has an active wallet connection
    const status = await abstraxionBackend.checkStatus(user.id);

    if (!status.connected || !status.metaAccountAddress) {
      throw new Error("No active wallet connection found");
    }

    try {
      // Start AbstraxionAuth to get the signing client
      const abstraxionAuth = await abstraxionBackend.startAbstraxionBackendAuth(
        user.id,
        context.request as any,
      );
      const signer = await abstraxionAuth.getSigner(
        abstraxionBackend.gasPriceDefault,
      );

      // Convert amount to micro units
      const amountNum = parseFloat(amount);
      const microAmount = Math.floor(amountNum * 1_000_000).toString();
      const denomMicro = denom === "XION" ? "uxion" : "uusdc";

      // Create the bank send message
      const msgSend: MsgSend = {
        fromAddress: status.metaAccountAddress,
        toAddress: to,
        amount: [
          {
            denom: denomMicro,
            amount: microAmount,
          },
        ],
      };

      // Sign and broadcast the transaction
      const result = await signer.signAndBroadcast(
        status.metaAccountAddress,
        [
          {
            typeUrl: "/cosmos.bank.v1beta1.MsgSend",
            value: msgSend,
          },
        ],
        "auto",
      );

      return {
        transactionHash: result.transactionHash,
        fromAddress: status.metaAccountAddress,
        toAddress: to,
        amount: amount,
        denom: denom,
        gasUsed: result.gasUsed?.toString(),
        gasWanted: result.gasWanted?.toString(),
      };
    } catch (error) {
      console.error("Error sending transaction:", error);
      throw new Error(
        error instanceof Error ? error.message : "Failed to send transaction",
      );
    }
  },
  {
    schema: sendTransactionSchema,
    schemaType: "body",
    rateLimit: "normal",
    allowedMethods: ["POST"],
  },
);
```

## Testing Your API

### Basic API Testing

You can test your API endpoints using curl or any HTTP client:

```bash
# Test wallet connection
curl -X POST http://localhost:3000/api/wallet/connect \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{
    "permissions": {
      "contracts": ["contract1"],
      "bank": [{"denom": "XION", "amount": "1000"}]
    }
  }'

# Test wallet status
curl -X GET http://localhost:3000/api/wallet/status \
  -H "Authorization: Bearer <token>"

# Test transaction
curl -X POST http://localhost:3000/api/wallet/transaction/send \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{
    "to": "xion1...",
    "amount": "100",
    "denom": "XION"
  }'
```

## Deployment

### Production Environment Variables

Create production environment variables:

```env
# Production Database
DATABASE_URL="postgresql://user:password@host:port/database"

# XION Configuration
XION_RPC_URL="https://rpc.xion-mainnet.burnt.com/"
XION_REST_URL="https://api.xion-mainnet.burnt.com/"
XION_REDIRECT_URL="https://yourdomain.com/api/callback/grant_session"
XION_TREASURY="xion1..."

# Security
NEXTAUTH_SECRET="your-production-secret"
ENCRYPTION_KEY="your-production-encryption-key"

# Session Configuration
SESSION_KEY_EXPIRY_MS=864000000
REFRESH_THRESHOLD_MS=3600000
```

### Build and Deploy

```bash
# Build the application
pnpm build

# Start production server
pnpm start
```

## API Documentation

### Endpoints Summary

| Method | Endpoint | Description | Authentication |
|--------|----------|-------------|----------------|
| `POST` | `/api/wallet/connect` | Initiate wallet connection | Required |
| `GET` | `/api/wallet/status` | Check wallet status | Required |
| `DELETE` | `/api/wallet/disconnect` | Disconnect wallet | Required |
| `POST` | `/api/wallet/transaction/send` | Send transaction | Required |
| `GET` | `/api/callback/grant_session` | OAuth callback handler | None |

### Response Format

All API responses follow this format:

```json
{
  "success": boolean,
  "data": object | null,
  "error": string | null
}
```

## Security Considerations

### Encryption

* All sensitive data (private keys) is encrypted using AES-256-CBC
* Encryption keys are generated securely and stored in environment variables
* Each encryption operation uses a unique IV for security

### Session Management

* Automatic key rotation before expiry
* Configurable refresh threshold
* Background monitoring service for expired sessions

## Troubleshooting

### Common Issues

1. **Database Connection Errors**
   * Ensure `DATABASE_URL` is correctly set
   * Check database server is running
   * Verify database permissions

2. **Encryption Key Issues**
   * Ensure `ENCRYPTION_KEY` is base64 encoded
   * Key must be exactly 32 bytes (256 bits)
   * Use the provided script to generate keys

3. **XION Network Issues**
   * Verify `XION_RPC_URL` and `XION_REST_URL` are correct
   * Check network connectivity
   * Ensure treasury address is valid

4. **Session Key Problems**
   * Check `SESSION_KEY_EXPIRY_MS` configuration
   * Verify `REFRESH_THRESHOLD_MS` settings

## Next Steps

Now that you have a fully functional RESTful API with account abstraction and backend session management, you can:

1. **Extend the API** with additional endpoints for specific use cases
2. **Add a frontend** to interact with your API
3. **Implement additional security features** like 2FA or biometric authentication
4. **Scale the application** with load balancers and multiple instances

This implementation provides a solid foundation for building Web3 applications with Web2 user experience, leveraging XION's account abstraction capabilities while maintaining security and scalability.

For more detailed implementation examples, comprehensive error handling, advanced features, and complete source code, please refer to the [backend-session demo repository](https://github.com/burnt-labs/xion.js/tree/main/apps/backend-session).
