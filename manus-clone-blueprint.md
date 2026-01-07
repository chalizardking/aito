# Manus.im Clone - Complete Development Blueprint

## 1. PROJECT OVERVIEW

### Mission
Build a collaborative AI workspace that enables teams to work together on AI-powered tasks through a shared canvas interface, with real-time collaboration, autonomous AI agents, and interactive visual editing capabilities.

### Value Proposition
- **Shared Canvas**: Single source of truth for team collaboration
- **Real-time Collaboration**: Multiple users working simultaneously without version conflicts
- **AI Agent Execution**: Autonomous task completion with transparent reasoning
- **Interactive Design View**: Canvas-based image creation and editing
- **Task Memory**: Persistent context across sessions

### Target Users
- Teams collaborating on AI-powered work
- Designers and creatives using AI tools
- Developers building with AI assistance
- Researchers conducting AI-driven analysis

### Success Metrics
- Task completion rate
- Collaboration session duration
- User satisfaction scores
- AI agent success rate
- Canvas interaction frequency

## 2. TECHNICAL ARCHITECTURE

```mermaid
graph TB
    subgraph "Frontend Layer"
        A[Next.js 14 App]
        B[Canvas Engine]
        C[Real-time UI]
        D[State Management]
    end
    
    subgraph "Backend Services"
        E[API Server]
        F[WebSocket Server]
        G[Task Orchestrator]
        H[AI Agent Manager]
    end
    
    subgraph "AI Layer"
        I[LLM Integration]
        J[Tool Execution]
        K[Memory System]
        L[Image Generation]
    end
    
    subgraph "Data Layer"
        M[(PostgreSQL)]
        N[(Redis)]
        O[S3 Storage]
    end
    
    A --> E
    A --> F
    B --> C
    C --> D
    E --> G
    F --> C
    G --> H
    H --> I
    H --> J
    I --> K
    J --> L
    E --> M
    F --> N
    G --> O
    
    classDef frontend fill:#e1f5ff
    classDef backend fill:#fff4e1
    classDef ai fill:#ffe1f5
    classDef data fill:#e1ffe1
    
    class A,B,C,D frontend
    class E,F,G,H backend
    class I,J,K,L ai
    class M,N,O data
```

### Architecture Explanation

**Frontend Layer**
- Next.js 14 with App Router for server-side rendering and routing
- Canvas Engine using Fabric.js for interactive canvas manipulation
- Real-time UI with WebSocket connections for live updates
- Zustand for global state management

**Backend Services**
- Express.js API server for RESTful endpoints
- Socket.io WebSocket server for real-time collaboration
- Task Orchestrator for managing AI agent workflows
- AI Agent Manager for executing autonomous tasks

**AI Layer**
- LLM Integration with Anthropic Claude, OpenAI GPT-4, or local models
- Tool Execution framework for running AI-powered tools
- Memory System for persistent context and learning
- Image Generation using Stable Diffusion or DALL-E

**Data Layer**
- PostgreSQL for structured data (users, tasks, sessions)
- Redis for real-time state and WebSocket pub/sub
- S3-compatible storage for artifacts and media

## 3. TECHNOLOGY STACK

### Frontend
```json
{
  "framework": "next@14.2.0",
  "react": "18.3.0",
  "typescript": "5.4.0",
  "canvas": "fabric@6.0.0",
  "state": "zustand@4.5.0",
  "realtime": "socket.io-client@4.7.0",
  "ui": "@radix-ui/react@1.1.0",
  "styling": "tailwindcss@3.4.0",
  "forms": "react-hook-form@7.51.0",
  "validation": "zod@3.22.0",
  "animations": "framer-motion@11.0.0"
}
```

### Backend
```json
{
  "runtime": "node@20.11.0",
  "framework": "express@4.18.0",
  "websocket": "socket.io@4.7.0",
  "database": "pg@8.11.0",
  "orm": "prisma@5.11.0",
  "cache": "ioredis@5.3.0",
  "storage": "@aws-sdk/client-s3@3.540.0",
  "ai": "@anthropic-ai/sdk@0.20.0",
  "openai": "openai@4.29.0",
  "queue": "bullmq@5.4.0",
  "auth": "jsonwebtoken@9.0.2"
}
```

### Infrastructure
```yaml
hosting: Vercel / AWS
database: PostgreSQL 16
cache: Redis 7
storage: S3 / R2
cdn: Cloudflare
monitoring: Sentry
```

## 4. FILE STRUCTURE

```
manus-clone/
├── package.json
├── tsconfig.json
├── next.config.js
├── tailwind.config.ts
├── prisma/
│   ├── schema.prisma
│   └── migrations/
├── src/
│   ├── app/
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   ├── app/
│   │   │   ├── layout.tsx
│   │   │   ├── page.tsx
│   │   │   └── [sessionId]/
│   │   │       └── page.tsx
│   │   └── api/
│   │       ├── sessions/
│   │       │   └── route.ts
│   │       ├── tasks/
│   │       │   └── route.ts
│   │       └── ai/
│   │           └── route.ts
│   ├── components/
│   │   ├── canvas/
│   │   │   ├── Canvas.tsx
│   │   │   ├── CanvasToolbar.tsx
│   │   │   └── CanvasObjects.tsx
│   │   ├── collaboration/
│   │   │   ├── CollaboratorCursors.tsx
│   │   │   ├── CollaboratorList.tsx
│   │   │   └── ShareDialog.tsx
│   │   ├── sidebar/
│   │   │   ├── Sidebar.tsx
│   │   │   ├── SessionList.tsx
│   │   │   └── SessionItem.tsx
│   │   ├── chat/
│   │   │   ├── ChatInterface.tsx
│   │   │   ├── MessageList.tsx
│   │   │   └── MessageInput.tsx
│   │   └── ui/
│   │       ├── Button.tsx
│   │       ├── Input.tsx
│   │       └── Dialog.tsx
│   ├── lib/
│   │   ├── ai/
│   │   │   ├── agent.ts
│   │   │   ├── tools.ts
│   │   │   └── memory.ts
│   │   ├── canvas/
│   │   │   ├── engine.ts
│   │   │   └── objects.ts
│   │   ├── realtime/
│   │   │   ├── socket.ts
│   │   │   └── sync.ts
│   │   └── utils/
│   │       ├── api.ts
│   │       └── validation.ts
│   ├── hooks/
│   │   ├── useCanvas.ts
│   │   ├── useCollaboration.ts
│   │   └── useAI.ts
│   ├── store/
│   │   ├── canvas.ts
│   │   ├── session.ts
│   │   └── user.ts
│   └── types/
│       ├── canvas.ts
│       ├── session.ts
│       └── ai.ts
├── server/
│   ├── index.ts
│   ├── socket.ts
│   ├── api/
│   │   ├── sessions.ts
│   │   ├── tasks.ts
│   │   └── ai.ts
│   ├── services/
│   │   ├── ai-agent.ts
│   │   ├── task-orchestrator.ts
│   │   └── storage.ts
│   └── utils/
│       ├── redis.ts
│       └── db.ts
└── tests/
    ├── components/
    ├── hooks/
    └── lib/
```

## 5. COMPLETE IMPLEMENTATION

### 5.1 Configuration Files

#### package.json
```json
{
  "name": "manus-clone",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "concurrently \"next dev\" \"tsx watch server/index.ts\"",
    "build": "next build && tsc --project tsconfig.server.json",
    "start": "concurrently \"next start\" \"node dist/server/index.js\"",
    "lint": "next lint",
    "test": "jest",
    "test:watch": "jest --watch",
    "db:push": "prisma db push",
    "db:studio": "prisma studio",
    "db:generate": "prisma generate"
  },
  "dependencies": {
    "next": "14.2.0",
    "react": "18.3.0",
    "react-dom": "18.3.0",
    "typescript": "5.4.0",
    "@anthropic-ai/sdk": "0.20.0",
    "openai": "4.29.0",
    "@radix-ui/react-dialog": "1.0.5",
    "@radix-ui/react-dropdown-menu": "2.0.6",
    "@radix-ui/react-slot": "1.0.2",
    "@radix-ui/react-toast": "1.1.5",
    "fabric": "6.0.0",
    "socket.io": "4.7.0",
    "socket.io-client": "4.7.0",
    "zustand": "4.5.0",
    "express": "4.18.0",
    "cors": "2.8.5",
    "helmet": "7.1.0",
    "@prisma/client": "5.11.0",
    "prisma": "5.11.0",
    "pg": "8.11.0",
    "ioredis": "5.3.0",
    "@aws-sdk/client-s3": "3.540.0",
    "@aws-sdk/s3-request-presigner": "3.540.0",
    "bullmq": "5.4.0",
    "jsonwebtoken": "9.0.2",
    "bcryptjs": "2.4.3",
    "zod": "3.22.0",
    "react-hook-form": "7.51.0",
    "@hookform/resolvers": "3.3.0",
    "framer-motion": "11.0.0",
    "tailwindcss": "3.4.0",
    "tailwind-merge": "2.2.0",
    "clsx": "2.1.0",
    "class-variance-authority": "0.7.0",
    "lucide-react": "0.358.0",
    "nanoid": "5.0.0",
    "date-fns": "3.3.0",
    "sharp": "0.33.0"
  },
  "devDependencies": {
    "@types/node": "20.11.0",
    "@types/react": "18.3.0",
    "@types/react-dom": "18.3.0",
    "@types/express": "4.17.0",
    "@types/cors": "2.8.0",
    "@types/bcryptjs": "2.4.0",
    "@types/jsonwebtoken": "9.0.0",
    "tsx": "4.7.0",
    "concurrently": "8.2.0",
    "eslint": "8.57.0",
    "eslint-config-next": "14.2.0",
    "jest": "29.7.0",
    "@testing-library/react": "14.2.0",
    "@testing-library/jest-dom": "6.4.0",
    "postcss": "8.4.0",
    "autoprefixer": "10.4.0"
  }
}
```

#### tsconfig.json
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "lib": ["ES2020", "dom", "dom.iterable"],
    "jsx": "preserve",
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "allowJs": true,
    "checkJs": false,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "incremental": true,
    "paths": {
      "@/*": ["./src/*"],
      "@/components/*": ["./src/components/*"],
      "@/lib/*": ["./src/lib/*"],
      "@/hooks/*": ["./src/hooks/*"],
      "@/store/*": ["./src/store/*"],
      "@/types/*": ["./src/types/*"]
    },
    "plugins": [
      {
        "name": "next"
      }
    ]
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules", "dist", "server"]
}
```

#### tsconfig.server.json
```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "module": "commonjs",
    "outDir": "./dist",
    "noEmit": false,
    "isolatedModules": false
  },
  "include": ["server/**/*.ts"],
  "exclude": ["node_modules", "src", "tests"]
}
```

#### next.config.js
```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  images: {
    domains: ['files.manuscdn.com', 'localhost'],
    remotePatterns: [
      {
        protocol: 'https',
        hostname: '**.s3.amazonaws.com',
      },
      {
        protocol: 'https',
        hostname: '**.r2.cloudflarestorage.com',
      },
    ],
  },
  webpack: (config) => {
    config.externals.push({
      'utf-8-validate': 'commonjs utf-8-validate',
      'bufferutil': 'commonjs bufferutil',
    });
    return config;
  },
  experimental: {
    serverActions: {
      bodySizeLimit: '10mb',
    },
  },
};

module.exports = nextConfig;
```

#### tailwind.config.ts
```typescript
import type { Config } from 'tailwindcss';

const config: Config = {
  content: [
    './src/pages/**/*.{js,ts,jsx,tsx,mdx}',
    './src/components/**/*.{js,ts,jsx,tsx,mdx}',
    './src/app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        border: 'hsl(var(--border))',
        input: 'hsl(var(--input))',
        ring: 'hsl(var(--ring))',
        background: 'hsl(var(--background))',
        foreground: 'hsl(var(--foreground))',
        primary: {
          DEFAULT: 'hsl(var(--primary))',
          foreground: 'hsl(var(--primary-foreground))',
        },
        secondary: {
          DEFAULT: 'hsl(var(--secondary))',
          foreground: 'hsl(var(--secondary-foreground))',
        },
        destructive: {
          DEFAULT: 'hsl(var(--destructive))',
          foreground: 'hsl(var(--destructive-foreground))',
        },
        muted: {
          DEFAULT: 'hsl(var(--muted))',
          foreground: 'hsl(var(--muted-foreground))',
        },
        accent: {
          DEFAULT: 'hsl(var(--accent))',
          foreground: 'hsl(var(--accent-foreground))',
        },
        canvas: {
          bg: 'var(--background-gray-main)',
          nav: 'var(--background-nav)',
        },
      },
      borderRadius: {
        lg: 'var(--radius)',
        md: 'calc(var(--radius) - 2px)',
        sm: 'calc(var(--radius) - 4px)',
      },
      animation: {
        'pulse-slow': 'pulse 3s cubic-bezier(0.4, 0, 0.6, 1) infinite',
      },
    },
  },
  plugins: [require('tailwindcss-animate')],
};

export default config;
```

#### .env.example
```bash
# Database
DATABASE_URL="postgresql://user:password@localhost:5432/manus"

# Redis
REDIS_URL="redis://localhost:6379"

# S3 Storage
S3_ENDPOINT="https://s3.amazonaws.com"
S3_BUCKET="manus-storage"
S3_ACCESS_KEY=""
S3_SECRET_KEY=""
S3_REGION="us-east-1"

# AI Providers
ANTHROPIC_API_KEY=""
OPENAI_API_KEY=""

# JWT
JWT_SECRET="your-secret-key-here"
JWT_EXPIRES_IN="7d"

# WebSocket
SOCKET_PORT=3001

# Environment
NODE_ENV="development"
NEXT_PUBLIC_API_URL="http://localhost:3000/api"
NEXT_PUBLIC_WS_URL="http://localhost:3001"
```

#### prisma/schema.prisma
```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id            String    @id @default(cuid())
  email         String    @unique
  name          String?
  passwordHash  String
  avatarUrl     String?
  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt
  
  sessions      Session[]
  messages      Message[]
  collaborations SessionCollaborator[]
  
  @@index([email])
}

model Session {
  id          String   @id @default(cuid())
  title       String   @default("New Session")
  userId      String
  canvasState Json?
  memory      Json?
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
  
  user          User      @relation(fields: [userId], references: [id], onDelete: Cascade)
  messages      Message[]
  collaborators SessionCollaborator[]
  artifacts     Artifact[]
  
  @@index([userId])
  @@index([createdAt])
}

model SessionCollaborator {
  id        String   @id @default(cuid())
  sessionId String
  userId    String
  role      String   @default("editor") // owner, editor, viewer
  joinedAt  DateTime @default(now())
  
  session Session @relation(fields: [sessionId], references: [id], onDelete: Cascade)
  user    User    @relation(fields: [userId], references: [id], onDelete: Cascade)
  
  @@unique([sessionId, userId])
  @@index([sessionId])
  @@index([userId])
}

model Message {
  id        String   @id @default(cuid())
  sessionId String
  userId    String?
  role      String   // user, assistant, system
  content   String
  metadata  Json?
  createdAt DateTime @default(now())
  
  session Session @relation(fields: [sessionId], references: [id], onDelete: Cascade)
  user    User?   @relation(fields: [userId], references: [id], onDelete: SetNull)
  
  @@index([sessionId])
  @@index([createdAt])
}

model Artifact {
  id         String   @id @default(cuid())
  sessionId  String
  type       String   // image, code, document, data
  title      String
  content    String
  metadata   Json?
  storageUrl String?
  createdAt  DateTime @default(now())
  
  session Session @relation(fields: [sessionId], references: [id], onDelete: Cascade)
  
  @@index([sessionId])
  @@index([type])
}

model Task {
  id          String   @id @default(cuid())
  sessionId   String?
  status      String   @default("pending") // pending, running, completed, failed
  type        String
  input       Json
  output      Json?
  error       String?
  startedAt   DateTime?
  completedAt DateTime?
  createdAt   DateTime @default(now())
  
  @@index([status])
  @@index([sessionId])
}
```

### 5.2 Source Code

#### src/app/layout.tsx
```typescript
import type { Metadata } from 'next';
import { Inter } from 'next/font/google';
import './globals.css';
import { Providers } from '@/components/providers/Providers';
import { Toaster } from '@/components/ui/toaster';

const inter = Inter({ subsets: ['latin'] });

export const metadata: Metadata = {
  title: 'Manus Clone - Collaborative AI Workspace',
  description: 'Real-time collaborative AI workspace with canvas-based interaction',
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en" suppressHydrationWarning>
      <body className={inter.className}>
        <Providers>
          {children}
          <Toaster />
        </Providers>
      </body>
    </html>
  );
}
```

#### src/app/globals.css
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;
    --card: 0 0% 100%;
    --card-foreground: 222.2 84% 4.9%;
    --popover: 0 0% 100%;
    --popover-foreground: 222.2 84% 4.9%;
    --primary: 222.2 47.4% 11.2%;
    --primary-foreground: 210 40% 98%;
    --secondary: 210 40% 96.1%;
    --secondary-foreground: 222.2 47.4% 11.2%;
    --muted: 210 40% 96.1%;
    --muted-foreground: 215.4 16.3% 46.9%;
    --accent: 210 40% 96.1%;
    --accent-foreground: 222.2 47.4% 11.2%;
    --destructive: 0 84.2% 60.2%;
    --destructive-foreground: 210 40% 98%;
    --border: 214.3 31.8% 91.4%;
    --input: 214.3 31.8% 91.4%;
    --ring: 222.2 84% 4.9%;
    --radius: 0.5rem;
    --background-gray-main: 0 0% 97%;
    --background-nav: 0 0% 98%;
    --fill-tsp-white-light: 0 0% 95%;
    --fill-tsp-white-main: 0 0% 93%;
  }

  .dark {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;
    --card: 222.2 84% 4.9%;
    --card-foreground: 210 40% 98%;
    --popover: 222.2 84% 4.9%;
    --popover-foreground: 210 40% 98%;
    --primary: 210 40% 98%;
    --primary-foreground: 222.2 47.4% 11.2%;
    --secondary: 217.2 32.6% 17.5%;
    --secondary-foreground: 210 40% 98%;
    --muted: 217.2 32.6% 17.5%;
    --muted-foreground: 215 20.2% 65.1%;
    --accent: 217.2 32.6% 17.5%;
    --accent-foreground: 210 40% 98%;
    --destructive: 0 62.8% 30.6%;
    --destructive-foreground: 210 40% 98%;
    --border: 217.2 32.6% 17.5%;
    --input: 217.2 32.6% 17.5%;
    --ring: 212.7 26.8% 83.9%;
  }
}

@layer base {
  * {
    @apply border-border;
  }
  body {
    @apply bg-background text-foreground;
  }
}

.canvas-container {
  width: 100%;
  height: 100%;
  position: relative;
}

.collaborator-cursor {
  position: absolute;
  pointer-events: none;
  transition: transform 0.1s ease-out;
  z-index: 1000;
}

.collaborator-cursor-label {
  position: absolute;
  top: 20px;
  left: 0;
  padding: 2px 8px;
  border-radius: 4px;
  font-size: 12px;
  white-space: nowrap;
  color: white;
}
```

#### src/app/page.tsx
```typescript
import { redirect } from 'next/navigation';
import { nanoid } from 'nanoid';

export default function HomePage() {
  // Create new session and redirect
  const sessionId = nanoid();
  redirect(`/app/${sessionId}`);
}
```

#### src/app/app/layout.tsx
```typescript
import { Sidebar } from '@/components/sidebar/Sidebar';

export default function AppLayout({ children }: { children: React.ReactNode }) {
  return (
    <div className="flex w-full h-screen overflow-hidden">
      <Sidebar />
      <main className="flex-1 min-w-0 h-full">
        {children}
      </main>
    </div>
  );
}
```

#### src/app/app/page.tsx
```typescript
'use client';

import { useEffect } from 'react';
import { useRouter } from 'next/navigation';
import { nanoid } from 'nanoid';

export default function AppHomePage() {
  const router = useRouter();

  useEffect(() => {
    // Create new session and redirect
    const sessionId = nanoid();
    router.push(`/app/${sessionId}`);
  }, [router]);

  return (
    <div className="flex items-center justify-center h-full">
      <div className="text-center">
        <div className="animate-pulse-slow">Creating new session...</div>
      </div>
    </div>
  );
}
```

#### src/app/app/[sessionId]/page.tsx
```typescript
'use client';

import { use, useEffect } from 'react';
import { Canvas } from '@/components/canvas/Canvas';
import { ChatInterface } from '@/components/chat/ChatInterface';
import { CollaboratorList } from '@/components/collaboration/CollaboratorList';
import { useSession } from '@/hooks/useSession';
import { useCollaboration } from '@/hooks/useCollaboration';

export default function SessionPage({ 
  params 
}: { 
  params: Promise<{ sessionId: string }> 
}) {
  const { sessionId } = use(params);
  const { session, loading } = useSession(sessionId);
  const { collaborators, connect } = useCollaboration(sessionId);

  useEffect(() => {
    connect();
  }, [connect, sessionId]);

  if (loading) {
    return (
      <div className="flex h-full items-center justify-center">
        <div className="animate-pulse">Loading session...</div>
      </div>
    );
  }

  return (
    <div className="flex h-full bg-canvas-bg">
      <div className="flex-1 flex flex-col">
        {/* Header */}
        <div className="flex h-12 items-center justify-between px-6 py-3 border-b">
          <h1 className="text-lg font-semibold">{session?.title || 'Untitled'}</h1>
          <CollaboratorList collaborators={collaborators} />
        </div>

        {/* Canvas Area */}
        <div className="flex-1 overflow-hidden">
          <Canvas sessionId={sessionId} />
        </div>

        {/* Chat Interface */}
        <div className="h-64 border-t">
          <ChatInterface sessionId={sessionId} />
        </div>
      </div>
    </div>
  );
}
```

#### src/components/canvas/Canvas.tsx
```typescript
'use client';

import { useEffect, useRef } from 'react';
import { fabric } from 'fabric';
import { useCanvas } from '@/hooks/useCanvas';
import { CollaboratorCursors } from '@/components/collaboration/CollaboratorCursors';
import { CanvasToolbar } from './CanvasToolbar';

interface CanvasProps {
  sessionId: string;
}

export function Canvas({ sessionId }: CanvasProps) {
  const canvasRef = useRef<HTMLCanvasElement>(null);
  const {
    canvas,
    initCanvas,
    addObject,
    updateObject,
    deleteObject,
    cursors,
  } = useCanvas(sessionId);

  useEffect(() => {
    if (!canvasRef.current) return;

    const fabricCanvas = new fabric.Canvas(canvasRef.current, {
      width: canvasRef.current.parentElement?.clientWidth || 800,
      height: canvasRef.current.parentElement?.clientHeight || 600,
      backgroundColor: '#ffffff',
    });

    initCanvas(fabricCanvas);

    // Handle window resize
    const handleResize = () => {
      if (!canvasRef.current?.parentElement) return;
      fabricCanvas.setDimensions({
        width: canvasRef.current.parentElement.clientWidth,
        height: canvasRef.current.parentElement.clientHeight,
      });
      fabricCanvas.renderAll();
    };

    window.addEventListener('resize', handleResize);

    return () => {
      window.removeEventListener('resize', handleResize);
      fabricCanvas.dispose();
    };
  }, [initCanvas]);

  return (
    <div className="relative w-full h-full">
      <CanvasToolbar 
        onAddRect={() => addObject('rect', { fill: '#3b82f6' })}
        onAddCircle={() => addObject('circle', { fill: '#ef4444' })}
        onAddText={() => addObject('text', { text: 'Text' })}
      />
      <div className="canvas-container">
        <canvas ref={canvasRef} />
        <CollaboratorCursors cursors={cursors} />
      </div>
    </div>
  );
}
```

#### src/components/canvas/CanvasToolbar.tsx
```typescript
'use client';

import { Button } from '@/components/ui/button';
import { Square, Circle, Type, Image, Trash2 } from 'lucide-react';

interface CanvasToolbarProps {
  onAddRect: () => void;
  onAddCircle: () => void;
  onAddText: () => void;
}

export function CanvasToolbar({
  onAddRect,
  onAddCircle,
  onAddText,
}: CanvasToolbarProps) {
  return (
    <div className="absolute top-4 left-4 z-10 flex gap-2 bg-white rounded-lg shadow-lg p-2">
      <Button
        variant="ghost"
        size="icon"
        onClick={onAddRect}
        title="Add Rectangle"
      >
        <Square className="h-4 w-4" />
      </Button>
      <Button
        variant="ghost"
        size="icon"
        onClick={onAddCircle}
        title="Add Circle"
      >
        <Circle className="h-4 w-4" />
      </Button>
      <Button
        variant="ghost"
        size="icon"
        onClick={onAddText}
        title="Add Text"
      >
        <Type className="h-4 w-4" />
      </Button>
      <Button
        variant="ghost"
        size="icon"
        title="Add Image"
      >
        <Image className="h-4 w-4" />
      </Button>
      <div className="w-px bg-border mx-1" />
      <Button
        variant="ghost"
        size="icon"
        title="Delete Selected"
      >
        <Trash2 className="h-4 w-4" />
      </Button>
    </div>
  );
}
```

#### src/components/collaboration/CollaboratorCursors.tsx
```typescript
'use client';

import { motion, AnimatePresence } from 'framer-motion';

interface Cursor {
  userId: string;
  userName: string;
  color: string;
  x: number;
  y: number;
}

interface CollaboratorCursorsProps {
  cursors: Cursor[];
}

export function CollaboratorCursors({ cursors }: CollaboratorCursorsProps) {
  return (
    <AnimatePresence>
      {cursors.map((cursor) => (
        <motion.div
          key={cursor.userId}
          className="collaborator-cursor"
          initial={{ opacity: 0, scale: 0 }}
          animate={{ opacity: 1, scale: 1, x: cursor.x, y: cursor.y }}
          exit={{ opacity: 0, scale: 0 }}
          transition={{ type: 'spring', damping: 30, stiffness: 500 }}
        >
          <svg width="24" height="24" viewBox="0 0 24 24" fill="none">
            <path
              d="M5.65376 12.3673L14.6867 3.334C15.6813 2.33947 17.2984 2.96556 17.4203 4.36546L18.7422 19.0799C18.8484 20.3124 17.4676 21.1269 16.4277 20.4086L10.8485 16.5036C10.5856 16.3127 10.2515 16.2477 9.93 16.3242L3.78695 17.7314C2.49471 18.0544 1.41713 16.7069 2.00141 15.4942L5.65376 12.3673Z"
              fill={cursor.color}
              stroke="white"
              strokeWidth="1.5"
            />
          </svg>
          <div
            className="collaborator-cursor-label"
            style={{ backgroundColor: cursor.color }}
          >
            {cursor.userName}
          </div>
        </motion.div>
      ))}
    </AnimatePresence>
  );
}
```

#### src/components/collaboration/CollaboratorList.tsx
```typescript
'use client';

import { Users } from 'lucide-react';
import { Button } from '@/components/ui/button';
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuLabel,
  DropdownMenuSeparator,
  DropdownMenuTrigger,
} from '@/components/ui/dropdown-menu';
import { ShareDialog } from './ShareDialog';
import { useState } from 'react';

interface Collaborator {
  id: string;
  name: string;
  email: string;
  avatarUrl?: string;
  role: string;
}

interface CollaboratorListProps {
  collaborators: Collaborator[];
}

export function CollaboratorList({ collaborators }: CollaboratorListProps) {
  const [shareOpen, setShareOpen] = useState(false);

  return (
    <>
      <DropdownMenu>
        <DropdownMenuTrigger asChild>
          <Button variant="outline" size="sm" className="gap-2">
            <Users className="h-4 w-4" />
            <span>{collaborators.length}</span>
          </Button>
        </DropdownMenuTrigger>
        <DropdownMenuContent align="end" className="w-56">
          <DropdownMenuLabel>Collaborators</DropdownMenuLabel>
          <DropdownMenuSeparator />
          {collaborators.map((collab) => (
            <DropdownMenuItem key={collab.id} className="flex items-center gap-2">
              <div className="flex h-8 w-8 items-center justify-center rounded-full bg-primary text-primary-foreground">
                {collab.name.charAt(0).toUpperCase()}
              </div>
              <div className="flex flex-col">
                <span className="text-sm font-medium">{collab.name}</span>
                <span className="text-xs text-muted-foreground">{collab.role}</span>
              </div>
            </DropdownMenuItem>
          ))}
          <DropdownMenuSeparator />
          <DropdownMenuItem onClick={() => setShareOpen(true)}>
            Invite collaborators
          </DropdownMenuItem>
        </DropdownMenuContent>
      </DropdownMenu>

      <ShareDialog open={shareOpen} onOpenChange={setShareOpen} />
    </>
  );
}
```

#### src/components/collaboration/ShareDialog.tsx
```typescript
'use client';

import { useState } from 'react';
import { Button } from '@/components/ui/button';
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogTitle,
} from '@/components/ui/dialog';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import { useToast } from '@/hooks/useToast';

interface ShareDialogProps {
  open: boolean;
  onOpenChange: (open: boolean) => void;
}

export function ShareDialog({ open, onOpenChange }: ShareDialogProps) {
  const [email, setEmail] = useState('');
  const [loading, setLoading] = useState(false);
  const { toast } = useToast();

  const handleShare = async () => {
    if (!email) return;

    setLoading(true);
    try {
      // API call to invite collaborator
      await fetch('/api/sessions/invite', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ email }),
      });

      toast({
        title: 'Invitation sent',
        description: `Invited ${email} to collaborate`,
      });

      setEmail('');
      onOpenChange(false);
    } catch (error) {
      toast({
        title: 'Error',
        description: 'Failed to send invitation',
        variant: 'destructive',
      });
    } finally {
      setLoading(false);
    }
  };

  return (
    <Dialog open={open} onOpenChange={onOpenChange}>
      <DialogContent>
        <DialogHeader>
          <DialogTitle>Invite Collaborator</DialogTitle>
          <DialogDescription>
            Invite team members to collaborate on this session
          </DialogDescription>
        </DialogHeader>
        <div className="grid gap-4 py-4">
          <div className="grid gap-2">
            <Label htmlFor="email">Email address</Label>
            <Input
              id="email"
              type="email"
              placeholder="colleague@example.com"
              value={email}
              onChange={(e) => setEmail(e.target.value)}
            />
          </div>
        </div>
        <DialogFooter>
          <Button
            variant="outline"
            onClick={() => onOpenChange(false)}
          >
            Cancel
          </Button>
          <Button onClick={handleShare} disabled={loading || !email}>
            {loading ? 'Sending...' : 'Send Invitation'}
          </Button>
        </DialogFooter>
      </DialogContent>
    </Dialog>
  );
}
```

#### src/components/chat/ChatInterface.tsx
```typescript
'use client';

import { useState } from 'react';
import { MessageList } from './MessageList';
import { MessageInput } from './MessageInput';
import { useMessages } from '@/hooks/useMessages';

interface ChatInterfaceProps {
  sessionId: string;
}

export function ChatInterface({ sessionId }: ChatInterfaceProps) {
  const { messages, sendMessage, loading } = useMessages(sessionId);

  return (
    <div className="flex flex-col h-full">
      <div className="flex-1 overflow-y-auto">
        <MessageList messages={messages} />
      </div>
      <div className="border-t p-4">
        <MessageInput 
          onSend={sendMessage} 
          disabled={loading}
        />
      </div>
    </div>
  );
}
```

#### src/components/chat/MessageList.tsx
```typescript
'use client';

import { useEffect, useRef } from 'react';
import { motion } from 'framer-motion';

interface Message {
  id: string;
  role: 'user' | 'assistant' | 'system';
  content: string;
  createdAt: string;
}

interface MessageListProps {
  messages: Message[];
}

export function MessageList({ messages }: MessageListProps) {
  const endRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    endRef.current?.scrollIntoView({ behavior: 'smooth' });
  }, [messages]);

  return (
    <div className="space-y-4 p-4">
      {messages.map((message) => (
        <motion.div
          key={message.id}
          initial={{ opacity: 0, y: 20 }}
          animate={{ opacity: 1, y: 0 }}
          className={`flex ${message.role === 'user' ? 'justify-end' : 'justify-start'}`}
        >
          <div
            className={`max-w-[80%] rounded-lg px-4 py-2 ${
              message.role === 'user'
                ? 'bg-primary text-primary-foreground'
                : 'bg-muted'
            }`}
          >
            <p className="text-sm whitespace-pre-wrap">{message.content}</p>
            <p className="text-xs opacity-50 mt-1">
              {new Date(message.createdAt).toLocaleTimeString()}
            </p>
          </div>
        </motion.div>
      ))}
      <div ref={endRef} />
    </div>
  );
}
```

#### src/components/chat/MessageInput.tsx
```typescript
'use client';

import { useState, KeyboardEvent } from 'react';
import { Button } from '@/components/ui/button';
import { Textarea } from '@/components/ui/textarea';
import { Send } from 'lucide-react';

interface MessageInputProps {
  onSend: (message: string) => void;
  disabled?: boolean;
}

export function MessageInput({ onSend, disabled }: MessageInputProps) {
  const [message, setMessage] = useState('');

  const handleSend = () => {
    if (!message.trim() || disabled) return;
    onSend(message);
    setMessage('');
  };

  const handleKeyDown = (e: KeyboardEvent<HTMLTextAreaElement>) => {
    if (e.key === 'Enter' && !e.shiftKey) {
      e.preventDefault();
      handleSend();
    }
  };

  return (
    <div className="flex gap-2">
      <Textarea
        value={message}
        onChange={(e) => setMessage(e.target.value)}
        onKeyDown={handleKeyDown}
        placeholder="Type a message..."
        className="min-h-[60px] resize-none"
        disabled={disabled}
      />
      <Button
        onClick={handleSend}
        disabled={!message.trim() || disabled}
        size="icon"
        className="h-[60px] w-[60px]"
      >
        <Send className="h-4 w-4" />
      </Button>
    </div>
  );
}
```

### 5.3 Hooks

#### src/hooks/useCanvas.ts
```typescript
'use client';

import { useState, useCallback, useEffect } from 'react';
import { fabric } from 'fabric';
import { useSocket } from './useSocket';
import type { Canvas as FabricCanvas } from 'fabric/fabric-impl';

interface CanvasObject {
  id: string;
  type: string;
  data: any;
}

interface Cursor {
  userId: string;
  userName: string;
  color: string;
  x: number;
  y: number;
}

export function useCanvas(sessionId: string) {
  const [canvas, setCanvas] = useState<FabricCanvas | null>(null);
  const [objects, setObjects] = useState<CanvasObject[]>([]);
  const [cursors, setCursors] = useState<Cursor[]>([]);
  const socket = useSocket();

  useEffect(() => {
    if (!socket || !sessionId) return;

    socket.emit('canvas:join', { sessionId });

    socket.on('canvas:object-added', (object: CanvasObject) => {
      if (!canvas) return;
      
      // Add object to canvas
      let fabricObject;
      if (object.type === 'rect') {
        fabricObject = new fabric.Rect(object.data);
      } else if (object.type === 'circle') {
        fabricObject = new fabric.Circle(object.data);
      } else if (object.type === 'text') {
        fabricObject = new fabric.IText(object.data.text, object.data);
      }

      if (fabricObject) {
        fabricObject.set('id', object.id);
        canvas.add(fabricObject);
        canvas.renderAll();
      }
    });

    socket.on('canvas:object-updated', (data: { id: string; changes: any }) => {
      if (!canvas) return;

      const obj = canvas.getObjects().find((o: any) => o.id === data.id);
      if (obj) {
        obj.set(data.changes);
        canvas.renderAll();
      }
    });

    socket.on('canvas:object-removed', (id: string) => {
      if (!canvas) return;

      const obj = canvas.getObjects().find((o: any) => o.id === id);
      if (obj) {
        canvas.remove(obj);
        canvas.renderAll();
      }
    });

    socket.on('canvas:cursor-moved', (cursor: Cursor) => {
      setCursors((prev) => {
        const existing = prev.find((c) => c.userId === cursor.userId);
        if (existing) {
          return prev.map((c) =>
            c.userId === cursor.userId ? cursor : c
          );
        }
        return [...prev, cursor];
      });
    });

    return () => {
      socket.emit('canvas:leave', { sessionId });
      socket.off('canvas:object-added');
      socket.off('canvas:object-updated');
      socket.off('canvas:object-removed');
      socket.off('canvas:cursor-moved');
    };
  }, [socket, canvas, sessionId]);

  const initCanvas = useCallback((fabricCanvas: FabricCanvas) => {
    setCanvas(fabricCanvas);

    // Setup canvas event listeners
    fabricCanvas.on('object:added', (e) => {
      if (!e.target) return;
      const obj = e.target as any;
      if (!obj.id) {
        obj.id = Math.random().toString(36).substr(2, 9);
        socket?.emit('canvas:add-object', {
          sessionId,
          object: {
            id: obj.id,
            type: obj.type,
            data: obj.toJSON(),
          },
        });
      }
    });

    fabricCanvas.on('object:modified', (e) => {
      if (!e.target) return;
      const obj = e.target as any;
      socket?.emit('canvas:update-object', {
        sessionId,
        id: obj.id,
        changes: obj.toJSON(),
      });
    });

    fabricCanvas.on('mouse:move', (e) => {
      if (!e.pointer) return;
      socket?.emit('canvas:cursor-move', {
        sessionId,
        x: e.pointer.x,
        y: e.pointer.y,
      });
    });
  }, [socket, sessionId]);

  const addObject = useCallback(
    (type: string, data: any) => {
      if (!canvas) return;

      let obj;
      if (type === 'rect') {
        obj = new fabric.Rect({
          left: 100,
          top: 100,
          width: 100,
          height: 100,
          ...data,
        });
      } else if (type === 'circle') {
        obj = new fabric.Circle({
          left: 100,
          top: 100,
          radius: 50,
          ...data,
        });
      } else if (type === 'text') {
        obj = new fabric.IText(data.text || 'Text', {
          left: 100,
          top: 100,
          fontSize: 20,
          ...data,
        });
      }

      if (obj) {
        canvas.add(obj);
        canvas.setActiveObject(obj);
        canvas.renderAll();
      }
    },
    [canvas]
  );

  const updateObject = useCallback(
    (id: string, changes: any) => {
      if (!canvas) return;

      const obj = canvas.getObjects().find((o: any) => o.id === id);
      if (obj) {
        obj.set(changes);
        canvas.renderAll();
      }
    },
    [canvas]
  );

  const deleteObject = useCallback(
    (id: string) => {
      if (!canvas) return;

      const obj = canvas.getObjects().find((o: any) => o.id === id);
      if (obj) {
        canvas.remove(obj);
        canvas.renderAll();
      }
    },
    [canvas]
  );

  return {
    canvas,
    objects,
    cursors,
    initCanvas,
    addObject,
    updateObject,
    deleteObject,
  };
}
```

#### src/hooks/useSocket.ts
```typescript
'use client';

import { useEffect, useState } from 'react';
import { io, Socket } from 'socket.io-client';

let socket: Socket | null = null;

export function useSocket() {
  const [isConnected, setIsConnected] = useState(false);

  useEffect(() => {
    if (!socket) {
      const wsUrl = process.env.NEXT_PUBLIC_WS_URL || 'http://localhost:3001';
      socket = io(wsUrl, {
        transports: ['websocket'],
        autoConnect: true,
      });

      socket.on('connect', () => {
        console.log('Socket connected');
        setIsConnected(true);
      });

      socket.on('disconnect', () => {
        console.log('Socket disconnected');
        setIsConnected(false);
      });
    }

    return () => {
      // Don't disconnect on unmount, keep socket alive
    };
  }, []);

  return socket;
}
```

#### src/hooks/useSession.ts
```typescript
'use client';

import { useState, useEffect } from 'react';

interface Session {
  id: string;
  title: string;
  userId: string;
  canvasState: any;
  memory: any;
  createdAt: string;
  updatedAt: string;
}

export function useSession(sessionId: string) {
  const [session, setSession] = useState<Session | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    if (!sessionId) return;

    const fetchSession = async () => {
      try {
        const response = await fetch(`/api/sessions/${sessionId}`);
        if (!response.ok) throw new Error('Failed to fetch session');
        const data = await response.json();
        setSession(data);
      } catch (err) {
        setError(err instanceof Error ? err.message : 'Unknown error');
      } finally {
        setLoading(false);
      }
    };

    fetchSession();
  }, [sessionId]);

  return { session, loading, error };
}
```

#### src/hooks/useCollaboration.ts
```typescript
'use client';

import { useState, useCallback, useEffect } from 'react';
import { useSocket } from './useSocket';

interface Collaborator {
  id: string;
  name: string;
  email: string;
  avatarUrl?: string;
  role: string;
}

export function useCollaboration(sessionId: string) {
  const [collaborators, setCollaborators] = useState<Collaborator[]>([]);
  const socket = useSocket();

  useEffect(() => {
    if (!socket || !sessionId) return;

    socket.emit('collab:join', { sessionId });

    socket.on('collab:user-joined', (user: Collaborator) => {
      setCollaborators((prev) => {
        if (prev.find((c) => c.id === user.id)) return prev;
        return [...prev, user];
      });
    });

    socket.on('collab:user-left', (userId: string) => {
      setCollaborators((prev) => prev.filter((c) => c.id !== userId));
    });

    socket.on('collab:users-list', (users: Collaborator[]) => {
      setCollaborators(users);
    });

    return () => {
      socket.emit('collab:leave', { sessionId });
      socket.off('collab:user-joined');
      socket.off('collab:user-left');
      socket.off('collab:users-list');
    };
  }, [socket, sessionId]);

  const connect = useCallback(() => {
    if (!socket || !sessionId) return;
    socket.emit('collab:join', { sessionId });
  }, [socket, sessionId]);

  return { collaborators, connect };
}
```

#### src/hooks/useMessages.ts
```typescript
'use client';

import { useState, useEffect, useCallback } from 'react';
import { useSocket } from './useSocket';

interface Message {
  id: string;
  role: 'user' | 'assistant' | 'system';
  content: string;
  createdAt: string;
}

export function useMessages(sessionId: string) {
  const [messages, setMessages] = useState<Message[]>([]);
  const [loading, setLoading] = useState(false);
  const socket = useSocket();

  useEffect(() => {
    if (!sessionId) return;

    const fetchMessages = async () => {
      try {
        const response = await fetch(`/api/sessions/${sessionId}/messages`);
        if (!response.ok) throw new Error('Failed to fetch messages');
        const data = await response.json();
        setMessages(data);
      } catch (error) {
        console.error('Error fetching messages:', error);
      }
    };

    fetchMessages();
  }, [sessionId]);

  useEffect(() => {
    if (!socket || !sessionId) return;

    socket.on('message:received', (message: Message) => {
      setMessages((prev) => [...prev, message]);
      setLoading(false);
    });

    return () => {
      socket.off('message:received');
    };
  }, [socket, sessionId]);

  const sendMessage = useCallback(
    async (content: string) => {
      if (!content.trim()) return;

      setLoading(true);

      const userMessage: Message = {
        id: Math.random().toString(36).substr(2, 9),
        role: 'user',
        content,
        createdAt: new Date().toISOString(),
      };

      setMessages((prev) => [...prev, userMessage]);

      try {
        const response = await fetch('/api/ai/chat', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({
            sessionId,
            message: content,
          }),
        });

        if (!response.ok) throw new Error('Failed to send message');
      } catch (error) {
        console.error('Error sending message:', error);
        setLoading(false);
      }
    },
    [sessionId]
  );

  return { messages, sendMessage, loading };
}
```

### 5.4 Server Implementation

#### server/index.ts
```typescript
import express from 'express';
import http from 'http';
import cors from 'cors';
import helmet from 'helmet';
import { Server as SocketIOServer } from 'socket.io';
import { setupSocketHandlers } from './socket';
import { sessionRouter } from './api/sessions';
import { taskRouter } from './api/tasks';
import { aiRouter } from './api/ai';

const app = express();
const server = http.createServer(app);
const io = new SocketIOServer(server, {
  cors: {
    origin: process.env.CORS_ORIGIN || 'http://localhost:3000',
    credentials: true,
  },
});

// Middleware
app.use(helmet());
app.use(cors());
app.use(express.json({ limit: '10mb' }));

// Routes
app.use('/api/sessions', sessionRouter);
app.use('/api/tasks', taskRouter);
app.use('/api/ai', aiRouter);

// Health check
app.get('/health', (req, res) => {
  res.json({ status: 'ok' });
});

// Setup WebSocket handlers
setupSocketHandlers(io);

const PORT = process.env.SOCKET_PORT || 3001;

server.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

#### server/socket.ts
```typescript
import { Server as SocketIOServer, Socket } from 'socket.io';
import { getRedisClient } from './utils/redis';

interface SocketData {
  userId: string;
  userName: string;
  sessionId?: string;
}

export function setupSocketHandlers(io: SocketIOServer) {
  const redis = getRedisClient();

  io.on('connection', (socket: Socket) => {
    console.log('Client connected:', socket.id);

    // Canvas collaboration
    socket.on('canvas:join', async ({ sessionId }) => {
      socket.data.sessionId = sessionId;
      await socket.join(`canvas:${sessionId}`);
      
      // Load canvas state from Redis
      const state = await redis.get(`canvas:${sessionId}:state`);
      if (state) {
        socket.emit('canvas:state', JSON.parse(state));
      }
    });

    socket.on('canvas:add-object', async ({ sessionId, object }) => {
      // Save to Redis
      await redis.lpush(
        `canvas:${sessionId}:objects`,
        JSON.stringify(object)
      );

      // Broadcast to others in room
      socket.to(`canvas:${sessionId}`).emit('canvas:object-added', object);
    });

    socket.on('canvas:update-object', async ({ sessionId, id, changes }) => {
      // Update in Redis
      const objects = await redis.lrange(`canvas:${sessionId}:objects`, 0, -1);
      const updated = objects.map((obj) => {
        const parsed = JSON.parse(obj);
        if (parsed.id === id) {
          return JSON.stringify({ ...parsed, ...changes });
        }
        return obj;
      });

      await redis.del(`canvas:${sessionId}:objects`);
      if (updated.length > 0) {
        await redis.rpush(`canvas:${sessionId}:objects`, ...updated);
      }

      socket.to(`canvas:${sessionId}`).emit('canvas:object-updated', { id, changes });
    });

    socket.on('canvas:cursor-move', ({ sessionId, x, y }) => {
      socket.to(`canvas:${sessionId}`).emit('canvas:cursor-moved', {
        userId: socket.id,
        userName: socket.data.userName || 'Anonymous',
        color: generateColor(socket.id),
        x,
        y,
      });
    });

    // Collaboration
    socket.on('collab:join', async ({ sessionId }) => {
      socket.data.sessionId = sessionId;
      await socket.join(`collab:${sessionId}`);

      // Get list of users in room
      const users = await getSessionUsers(io, sessionId);
      socket.emit('collab:users-list', users);

      // Notify others
      socket.to(`collab:${sessionId}`).emit('collab:user-joined', {
        id: socket.id,
        name: socket.data.userName || 'Anonymous',
        email: socket.data.userEmail || '',
      });
    });

    socket.on('collab:leave', async ({ sessionId }) => {
      await socket.leave(`collab:${sessionId}`);
      socket.to(`collab:${sessionId}`).emit('collab:user-left', socket.id);
    });

    socket.on('disconnect', () => {
      console.log('Client disconnected:', socket.id);
      if (socket.data.sessionId) {
        socket.to(`collab:${socket.data.sessionId}`).emit('collab:user-left', socket.id);
      }
    });
  });
}

function generateColor(id: string): string {
  const colors = [
    '#3b82f6', // blue
    '#ef4444', // red
    '#10b981', // green
    '#f59e0b', // amber
    '#8b5cf6', // purple
    '#ec4899', // pink
  ];
  const hash = id.split('').reduce((acc, char) => acc + char.charCodeAt(0), 0);
  return colors[hash % colors.length];
}

async function getSessionUsers(io: SocketIOServer, sessionId: string) {
  const sockets = await io.in(`collab:${sessionId}`).fetchSockets();
  return sockets.map((s) => ({
    id: s.id,
    name: s.data.userName || 'Anonymous',
    email: s.data.userEmail || '',
    role: 'editor',
  }));
}
```

(Continued in next message due to length...)
