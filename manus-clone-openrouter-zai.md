# Manus.im Clone - OpenRouter + Z.ai Integration Blueprint

## Updated Technology Stack Changes

### AI Provider Changes

**Removed:**
- `@anthropic-ai/sdk@0.20.0`
- `openai@4.29.0`

**Added:**
- `axios@1.6.0` - For HTTP requests to OpenRouter and Z.ai
- `@google/generative-ai@0.1.0` - Gemini SDK
- No SDK needed for Claude Code (CLI-based)

### Updated package.json Dependencies

```json
{
  "dependencies": {
    "axios": "1.6.0",
    "@google/generative-ai": "0.1.0",
    "eventsource-parser": "1.1.0"
  }
}
```

### Updated Environment Variables

```bash
# .env.example

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
OPENROUTER_API_KEY=""
OPENROUTER_API_URL="https://openrouter.ai/api/v1"

ZAI_API_KEY="ee059c8c537a46e99ab614b8fdde8384.MBCC7QS9uSSIer8z"
ZAI_API_URL="https://api.z.ai/api/coding/paas/v4"

GEMINI_API_KEY=""

# Claude Code CLI (installed globally via npm/pip)
CLAUDE_CODE_ENABLED="true"

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

## Updated AI Service Implementation

### server/services/ai-agent.ts (Complete Rewrite)

```typescript
import axios, { AxiosInstance } from 'axios';
import { GoogleGenerativeAI } from '@google/generative-ai';
import { spawn } from 'child_process';
import { EventSourceParserStream } from 'eventsource-parser/stream';

interface ChatOptions {
  sessionId: string;
  message: string;
  history: any[];
  memory: any;
  provider?: 'openrouter' | 'zai' | 'gemini' | 'claude-code';
  model?: string;
}

interface ChatResponse {
  content: string;
  metadata?: any;
  memory?: any;
}

interface ImageOptions {
  prompt: string;
  width: number;
  height: number;
  provider?: 'openrouter' | 'zai';
}

interface ImageResponse {
  url: string;
  metadata?: any;
}

interface CodeExecutionOptions {
  code: string;
  language: string;
  context?: string;
}

interface CodeExecutionResponse {
  output: string;
  error?: string;
  metadata?: any;
}

export class AIAgent {
  private openRouterClient: AxiosInstance;
  private zaiClient: AxiosInstance;
  private geminiClient: GoogleGenerativeAI;

  constructor() {
    // Initialize OpenRouter client
    this.openRouterClient = axios.create({
      baseURL: process.env.OPENROUTER_API_URL || 'https://openrouter.ai/api/v1',
      headers: {
        'Authorization': `Bearer ${process.env.OPENROUTER_API_KEY}`,
        'HTTP-Referer': 'https://manus-clone.app',
        'X-Title': 'Manus Clone',
        'Content-Type': 'application/json',
      },
    });

    // Initialize Z.ai client
    this.zaiClient = axios.create({
      baseURL: process.env.ZAI_API_URL || 'https://api.z.ai/api/coding/paas/v4',
      headers: {
        'Authorization': `Bearer ${process.env.ZAI_API_KEY}`,
        'Content-Type': 'application/json',
      },
    });

    // Initialize Gemini client
    this.geminiClient = new GoogleGenerativeAI(process.env.GEMINI_API_KEY || '');
  }

  async chat(options: ChatOptions): Promise<ChatResponse> {
    const { message, history, memory, provider = 'openrouter', model } = options;

    switch (provider) {
      case 'openrouter':
        return this.chatOpenRouter(message, history, memory, model);
      case 'zai':
        return this.chatZAI(message, history, memory);
      case 'gemini':
        return this.chatGemini(message, history, memory, model);
      case 'claude-code':
        return this.chatClaudeCode(message, history, memory);
      default:
        throw new Error(`Unknown provider: ${provider}`);
    }
  }

  private async chatOpenRouter(
    message: string,
    history: any[],
    memory: any,
    model?: string
  ): Promise<ChatResponse> {
    // Build conversation history
    const messages = history.slice(-10).map((msg) => ({
      role: msg.role === 'user' ? 'user' : 'assistant',
      content: msg.content,
    }));

    // Add system message with memory
    messages.unshift({
      role: 'system',
      content: this.buildSystemPrompt(memory),
    });

    // Add current message
    messages.push({
      role: 'user',
      content: message,
    });

    try {
      const response = await this.openRouterClient.post('/chat/completions', {
        model: model || 'anthropic/claude-3.5-sonnet', // Default model
        messages,
        max_tokens: 4096,
        temperature: 0.7,
        stream: false,
      });

      const content = response.data.choices[0].message.content;

      return {
        content,
        metadata: {
          provider: 'openrouter',
          model: response.data.model,
          usage: response.data.usage,
        },
        memory: this.extractMemory(content, memory),
      };
    } catch (error) {
      console.error('OpenRouter API error:', error);
      throw new Error('Failed to generate response from OpenRouter');
    }
  }

  private async chatZAI(
    message: string,
    history: any[],
    memory: any
  ): Promise<ChatResponse> {
    // Z.ai API expects a different format for coding tasks
    const context = history.slice(-5).map((msg) => 
      `${msg.role}: ${msg.content}`
    ).join('\n\n');

    try {
      const response = await this.zaiClient.post('/complete', {
        prompt: message,
        context: context,
        memory: memory ? JSON.stringify(memory) : '',
        max_tokens: 4096,
        temperature: 0.7,
      });

      const content = response.data.completion || response.data.result;

      return {
        content,
        metadata: {
          provider: 'zai',
          tokens_used: response.data.tokens_used,
        },
        memory: this.extractMemory(content, memory),
      };
    } catch (error) {
      console.error('Z.ai API error:', error);
      throw new Error('Failed to generate response from Z.ai');
    }
  }

  private async chatGemini(
    message: string,
    history: any[],
    memory: any,
    modelName?: string
  ): Promise<ChatResponse> {
    try {
      const model = this.geminiClient.getGenerativeModel({ 
        model: modelName || 'gemini-2.0-flash-exp'
      });

      // Build conversation history for Gemini
      const chatHistory = history.slice(-10).map((msg) => ({
        role: msg.role === 'user' ? 'user' : 'model',
        parts: [{ text: msg.content }],
      }));

      const chat = model.startChat({
        history: chatHistory,
        systemInstruction: this.buildSystemPrompt(memory),
      });

      const result = await chat.sendMessage(message);
      const content = result.response.text();

      return {
        content,
        metadata: {
          provider: 'gemini',
          model: modelName || 'gemini-2.0-flash-exp',
        },
        memory: this.extractMemory(content, memory),
      };
    } catch (error) {
      console.error('Gemini API error:', error);
      throw new Error('Failed to generate response from Gemini');
    }
  }

  private async chatClaudeCode(
    message: string,
    history: any[],
    memory: any
  ): Promise<ChatResponse> {
    return new Promise((resolve, reject) => {
      // Build context from history
      const context = history.slice(-5).map((msg) => 
        `${msg.role}: ${msg.content}`
      ).join('\n\n');

      const fullPrompt = `${context}\n\nuser: ${message}`;

      // Spawn Claude Code CLI process
      const claudeCode = spawn('claude', ['code', '--prompt', fullPrompt], {
        env: { ...process.env },
        shell: true,
      });

      let output = '';
      let errorOutput = '';

      claudeCode.stdout.on('data', (data) => {
        output += data.toString();
      });

      claudeCode.stderr.on('data', (data) => {
        errorOutput += data.toString();
      });

      claudeCode.on('close', (code) => {
        if (code !== 0 && errorOutput) {
          reject(new Error(`Claude Code failed: ${errorOutput}`));
          return;
        }

        resolve({
          content: output,
          metadata: {
            provider: 'claude-code',
            exit_code: code,
          },
          memory: this.extractMemory(output, memory),
        });
      });

      claudeCode.on('error', (error) => {
        reject(new Error(`Failed to spawn Claude Code: ${error.message}`));
      });
    });
  }

  async generateImage(options: ImageOptions): Promise<ImageResponse> {
    const { prompt, width, height, provider = 'openrouter' } = options;

    if (provider === 'openrouter') {
      return this.generateImageOpenRouter(prompt, width, height);
    } else if (provider === 'zai') {
      return this.generateImageZAI(prompt, width, height);
    }

    throw new Error(`Image generation not supported for provider: ${provider}`);
  }

  private async generateImageOpenRouter(
    prompt: string,
    width: number,
    height: number
  ): Promise<ImageResponse> {
    try {
      const response = await this.openRouterClient.post('/images/generations', {
        model: 'black-forest-labs/flux-1.1-pro', // or other image models
        prompt,
        n: 1,
        size: `${width}x${height}`,
      });

      const imageUrl = response.data.data[0].url;

      return {
        url: imageUrl,
        metadata: {
          provider: 'openrouter',
          model: 'flux-1.1-pro',
        },
      };
    } catch (error) {
      console.error('OpenRouter image generation error:', error);
      throw new Error('Failed to generate image from OpenRouter');
    }
  }

  private async generateImageZAI(
    prompt: string,
    width: number,
    height: number
  ): Promise<ImageResponse> {
    try {
      const response = await this.zaiClient.post('/image/generate', {
        prompt,
        width,
        height,
      });

      return {
        url: response.data.image_url,
        metadata: {
          provider: 'zai',
        },
      };
    } catch (error) {
      console.error('Z.ai image generation error:', error);
      throw new Error('Failed to generate image from Z.ai');
    }
  }

  async executeCode(options: CodeExecutionOptions): Promise<CodeExecutionResponse> {
    const { code, language, context } = options;

    // Use Z.ai for code execution
    try {
      const response = await this.zaiClient.post('/code/execute', {
        code,
        language,
        context,
      });

      return {
        output: response.data.output,
        error: response.data.error,
        metadata: {
          provider: 'zai',
          execution_time: response.data.execution_time,
        },
      };
    } catch (error) {
      console.error('Z.ai code execution error:', error);
      throw new Error('Failed to execute code');
    }
  }

  async executeGeminiCLI(prompt: string): Promise<string> {
    return new Promise((resolve, reject) => {
      const gemini = spawn('gemini', ['cli', '--prompt', prompt], {
        env: { ...process.env, GEMINI_API_KEY: process.env.GEMINI_API_KEY },
        shell: true,
      });

      let output = '';
      let errorOutput = '';

      gemini.stdout.on('data', (data) => {
        output += data.toString();
      });

      gemini.stderr.on('data', (data) => {
        errorOutput += data.toString();
      });

      gemini.on('close', (code) => {
        if (code !== 0 && errorOutput) {
          reject(new Error(`Gemini CLI failed: ${errorOutput}`));
          return;
        }

        resolve(output);
      });

      gemini.on('error', (error) => {
        reject(new Error(`Failed to spawn Gemini CLI: ${error.message}`));
      });
    });
  }

  private buildSystemPrompt(memory: any): string {
    let prompt = `You are Manus, an AI assistant for collaborative canvas-based work. You help users with tasks, answer questions, and generate content.

Core capabilities:
- Canvas manipulation and object creation
- Image generation and editing
- Code generation and execution
- Data analysis and visualization
- Document creation and editing

You work in a collaborative environment where multiple users can interact simultaneously.`;

    if (memory && Object.keys(memory).length > 0) {
      prompt += `\n\nContext from previous conversations:\n${JSON.stringify(memory, null, 2)}`;
    }

    return prompt;
  }

  private extractMemory(text: string, existingMemory: any): any {
    const memory = { ...existingMemory };

    // Simple memory extraction patterns
    const patterns = [
      { regex: /my name is (\w+)/i, key: 'user_name' },
      { regex: /i work at ([\w\s]+)/i, key: 'workplace' },
      { regex: /i'm interested in ([\w\s,]+)/i, key: 'interests' },
    ];

    for (const pattern of patterns) {
      const match = text.match(pattern.regex);
      if (match) {
        memory[pattern.key] = match[1].trim();
      }
    }

    return memory;
  }

  // Multi-provider routing
  async chatWithAutoRouting(
    message: string,
    history: any[],
    memory: any,
    taskType?: 'coding' | 'image' | 'general'
  ): Promise<ChatResponse> {
    // Route based on task type
    if (taskType === 'coding') {
      // Try Z.ai first, fallback to Claude Code
      try {
        return await this.chat({ message, history, memory, provider: 'zai', sessionId: '' });
      } catch (error) {
        console.log('Z.ai failed, falling back to Claude Code');
        return await this.chat({ message, history, memory, provider: 'claude-code', sessionId: '' });
      }
    } else if (taskType === 'image') {
      // Use Gemini for image understanding/analysis
      return await this.chat({ message, history, memory, provider: 'gemini', sessionId: '' });
    } else {
      // General queries go to OpenRouter
      return await this.chat({ message, history, memory, provider: 'openrouter', sessionId: '' });
    }
  }
}
```

### server/api/ai.ts (Updated)

```typescript
import { Router } from 'express';
import { PrismaClient } from '@prisma/client';
import { AIAgent } from '../services/ai-agent';
import { z } from 'zod';

const router = Router();
const prisma = new PrismaClient();
const aiAgent = new AIAgent();

const chatSchema = z.object({
  sessionId: z.string(),
  message: z.string(),
  provider: z.enum(['openrouter', 'zai', 'gemini', 'claude-code']).optional(),
  model: z.string().optional(),
  taskType: z.enum(['coding', 'image', 'general']).optional(),
});

// Chat endpoint with provider selection
router.post('/chat', async (req, res) => {
  try {
    const { sessionId, message, provider, model, taskType } = chatSchema.parse(req.body);

    // Save user message
    const userMessage = await prisma.message.create({
      data: {
        sessionId,
        role: 'user',
        content: message,
      },
    });

    // Get session context
    const session = await prisma.session.findUnique({
      where: { id: sessionId },
      include: {
        messages: {
          orderBy: { createdAt: 'asc' },
          take: 10,
        },
      },
    });

    if (!session) {
      return res.status(404).json({ error: 'Session not found' });
    }

    // Generate AI response
    let response;
    if (taskType) {
      // Auto-route based on task type
      response = await aiAgent.chatWithAutoRouting(
        message,
        session.messages,
        session.memory as any,
        taskType
      );
    } else {
      // Use specified provider or default
      response = await aiAgent.chat({
        sessionId,
        message,
        history: session.messages,
        memory: session.memory as any,
        provider,
        model,
      });
    }

    // Save assistant message
    const assistantMessage = await prisma.message.create({
      data: {
        sessionId,
        role: 'assistant',
        content: response.content,
        metadata: response.metadata,
      },
    });

    // Update session memory
    if (response.memory) {
      await prisma.session.update({
        where: { id: sessionId },
        data: { memory: response.memory },
      });
    }

    res.json(assistantMessage);
  } catch (error) {
    if (error instanceof z.ZodError) {
      return res.status(400).json({ error: error.errors });
    }
    console.error('Error in chat:', error);
    res.status(500).json({ error: 'Internal server error' });
  }
});

// Generate image with provider selection
router.post('/image/generate', async (req, res) => {
  try {
    const { prompt, sessionId, provider, width = 1024, height = 1024 } = req.body;

    const result = await aiAgent.generateImage({
      prompt,
      width,
      height,
      provider: provider || 'openrouter',
    });

    // Save artifact
    const artifact = await prisma.artifact.create({
      data: {
        sessionId,
        type: 'image',
        title: 'Generated Image',
        content: prompt,
        storageUrl: result.url,
        metadata: result.metadata,
      },
    });

    res.json(artifact);
  } catch (error) {
    console.error('Error generating image:', error);
    res.status(500).json({ error: 'Internal server error' });
  }
});

// Execute code via Z.ai
router.post('/code/execute', async (req, res) => {
  try {
    const { code, language, context, sessionId } = req.body;

    const result = await aiAgent.executeCode({
      code,
      language,
      context,
    });

    // Save artifact
    const artifact = await prisma.artifact.create({
      data: {
        sessionId,
        type: 'code',
        title: 'Code Execution',
        content: code,
        metadata: {
          language,
          output: result.output,
          error: result.error,
          ...result.metadata,
        },
      },
    });

    res.json(artifact);
  } catch (error) {
    console.error('Error executing code:', error);
    res.status(500).json({ error: 'Internal server error' });
  }
});

// Gemini CLI endpoint
router.post('/gemini/cli', async (req, res) => {
  try {
    const { prompt } = req.body;

    const output = await aiAgent.executeGeminiCLI(prompt);

    res.json({ output });
  } catch (error) {
    console.error('Error executing Gemini CLI:', error);
    res.status(500).json({ error: 'Internal server error' });
  }
});

// Claude Code CLI endpoint
router.post('/claude-code/execute', async (req, res) => {
  try {
    const { message, sessionId } = req.body;

    const session = await prisma.session.findUnique({
      where: { id: sessionId },
      include: {
        messages: {
          orderBy: { createdAt: 'asc' },
          take: 10,
        },
      },
    });

    if (!session) {
      return res.status(404).json({ error: 'Session not found' });
    }

    const response = await aiAgent.chat({
      sessionId,
      message,
      history: session.messages,
      memory: session.memory as any,
      provider: 'claude-code',
    });

    const assistantMessage = await prisma.message.create({
      data: {
        sessionId,
        role: 'assistant',
        content: response.content,
        metadata: response.metadata,
      },
    });

    res.json(assistantMessage);
  } catch (error) {
    console.error('Error executing Claude Code:', error);
    res.status(500).json({ error: 'Internal server error' });
  }
});

export { router as aiRouter };
```

## Frontend Provider Selector Component

### src/components/chat/ProviderSelector.tsx

```typescript
'use client';

import { useState } from 'react';
import { Button } from '@/components/ui/button';
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuLabel,
  DropdownMenuSeparator,
  DropdownMenuTrigger,
} from '@/components/ui/dropdown-menu';
import { ChevronDown, Sparkles, Code, Image, Zap } from 'lucide-react';

const providers = [
  { id: 'openrouter', name: 'OpenRouter', icon: Sparkles, color: 'text-purple-500' },
  { id: 'zai', name: 'Z.ai Coding', icon: Code, color: 'text-blue-500' },
  { id: 'gemini', name: 'Gemini', icon: Zap, color: 'text-green-500' },
  { id: 'claude-code', name: 'Claude Code', icon: Code, color: 'text-orange-500' },
] as const;

const models = {
  openrouter: [
    'anthropic/claude-3.5-sonnet',
    'anthropic/claude-3-opus',
    'openai/gpt-4-turbo',
    'google/gemini-pro',
    'meta-llama/llama-3.1-70b-instruct',
  ],
  gemini: [
    'gemini-2.0-flash-exp',
    'gemini-1.5-pro',
    'gemini-1.5-flash',
  ],
};

interface ProviderSelectorProps {
  onProviderChange: (provider: string, model?: string) => void;
}

export function ProviderSelector({ onProviderChange }: ProviderSelectorProps) {
  const [selectedProvider, setSelectedProvider] = useState<string>('openrouter');
  const [selectedModel, setSelectedModel] = useState<string | undefined>(
    'anthropic/claude-3.5-sonnet'
  );

  const currentProvider = providers.find((p) => p.id === selectedProvider);
  const CurrentIcon = currentProvider?.icon || Sparkles;

  const handleProviderChange = (providerId: string) => {
    setSelectedProvider(providerId);
    
    // Set default model for provider
    if (providerId === 'openrouter') {
      setSelectedModel('anthropic/claude-3.5-sonnet');
      onProviderChange(providerId, 'anthropic/claude-3.5-sonnet');
    } else if (providerId === 'gemini') {
      setSelectedModel('gemini-2.0-flash-exp');
      onProviderChange(providerId, 'gemini-2.0-flash-exp');
    } else {
      setSelectedModel(undefined);
      onProviderChange(providerId);
    }
  };

  const handleModelChange = (model: string) => {
    setSelectedModel(model);
    onProviderChange(selectedProvider, model);
  };

  return (
    <div className="flex gap-2">
      <DropdownMenu>
        <DropdownMenuTrigger asChild>
          <Button variant="outline" size="sm" className="gap-2">
            <CurrentIcon className={`h-4 w-4 ${currentProvider?.color}`} />
            <span>{currentProvider?.name}</span>
            <ChevronDown className="h-4 w-4" />
          </Button>
        </DropdownMenuTrigger>
        <DropdownMenuContent align="end">
          <DropdownMenuLabel>AI Provider</DropdownMenuLabel>
          <DropdownMenuSeparator />
          {providers.map((provider) => {
            const Icon = provider.icon;
            return (
              <DropdownMenuItem
                key={provider.id}
                onClick={() => handleProviderChange(provider.id)}
              >
                <Icon className={`mr-2 h-4 w-4 ${provider.color}`} />
                {provider.name}
              </DropdownMenuItem>
            );
          })}
        </DropdownMenuContent>
      </DropdownMenu>

      {selectedModel && (
        <DropdownMenu>
          <DropdownMenuTrigger asChild>
            <Button variant="outline" size="sm" className="gap-2">
              <span className="text-xs">{selectedModel.split('/').pop()}</span>
              <ChevronDown className="h-4 w-4" />
            </Button>
          </DropdownMenuTrigger>
          <DropdownMenuContent align="end">
            <DropdownMenuLabel>Model</DropdownMenuLabel>
            <DropdownMenuSeparator />
            {(selectedProvider === 'openrouter' 
              ? models.openrouter 
              : selectedProvider === 'gemini'
              ? models.gemini
              : []
            ).map((model) => (
              <DropdownMenuItem
                key={model}
                onClick={() => handleModelChange(model)}
              >
                {model}
              </DropdownMenuItem>
            ))}
          </DropdownMenuContent>
        </DropdownMenu>
      )}
    </div>
  );
}
```

### Updated ChatInterface with Provider Selection

```typescript
'use client';

import { useState } from 'react';
import { MessageList } from './MessageList';
import { MessageInput } from './MessageInput';
import { ProviderSelector } from './ProviderSelector';
import { useMessages } from '@/hooks/useMessages';

interface ChatInterfaceProps {
  sessionId: string;
}

export function ChatInterface({ sessionId }: ChatInterfaceProps) {
  const [provider, setProvider] = useState<string>('openrouter');
  const [model, setModel] = useState<string | undefined>('anthropic/claude-3.5-sonnet');
  const { messages, sendMessage, loading } = useMessages(sessionId);

  const handleProviderChange = (newProvider: string, newModel?: string) => {
    setProvider(newProvider);
    setModel(newModel);
  };

  const handleSendMessage = (message: string) => {
    sendMessage(message, { provider, model });
  };

  return (
    <div className="flex flex-col h-full">
      <div className="flex items-center justify-between p-3 border-b">
        <h3 className="text-sm font-medium">Chat</h3>
        <ProviderSelector onProviderChange={handleProviderChange} />
      </div>
      <div className="flex-1 overflow-y-auto">
        <MessageList messages={messages} />
      </div>
      <div className="border-t p-4">
        <MessageInput 
          onSend={handleSendMessage} 
          disabled={loading}
        />
      </div>
    </div>
  );
}
```

### Updated useMessages Hook

```typescript
'use client';

import { useState, useEffect, useCallback } from 'react';
import { useSocket } from './useSocket';

interface Message {
  id: string;
  role: 'user' | 'assistant' | 'system';
  content: string;
  createdAt: string;
  metadata?: any;
}

interface SendOptions {
  provider?: string;
  model?: string;
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
    async (content: string, options?: SendOptions) => {
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
            provider: options?.provider,
            model: options?.model,
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

## Installation and Setup

### Phase 1: Install Dependencies

```bash
# Remove old AI SDKs if present
npm uninstall @anthropic-ai/sdk openai

# Install new dependencies
npm install axios@1.6.0 @google/generative-ai@0.1.0 eventsource-parser@1.1.0

# Install CLI tools globally
npm install -g @anthropic-ai/claude-code
pip install google-generativeai
```

### Phase 2: Configure Environment

```bash
# Update .env with API keys
echo "OPENROUTER_API_KEY=your_openrouter_key" >> .env
echo "ZAI_API_KEY=ee059c8c537a46e99ab614b8fdde8384.MBCC7QS9uSSIer8z" >> .env
echo "GEMINI_API_KEY=your_gemini_key" >> .env
echo "CLAUDE_CODE_ENABLED=true" >> .env
```

### Phase 3: Test AI Providers

```bash
# Test OpenRouter
curl -X POST https://openrouter.ai/api/v1/chat/completions \
  -H "Authorization: Bearer $OPENROUTER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"anthropic/claude-3.5-sonnet","messages":[{"role":"user","content":"Hello"}]}'

# Test Z.ai
curl -X POST https://api.z.ai/api/coding/paas/v4/complete \
  -H "Authorization: Bearer ee059c8c537a46e99ab614b8fdde8384.MBCC7QS9uSSIer8z" \
  -H "Content-Type: application/json" \
  -d '{"prompt":"Write a hello world function"}'

# Test Claude Code CLI
claude code --prompt "Write a hello world function in TypeScript"

# Test Gemini CLI
gemini cli --prompt "Explain TypeScript interfaces"
```

## API Usage Examples

### OpenRouter Example

```typescript
// Using OpenRouter with Claude 3.5 Sonnet
const response = await fetch('/api/ai/chat', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    sessionId: 'session-123',
    message: 'Explain React hooks',
    provider: 'openrouter',
    model: 'anthropic/claude-3.5-sonnet',
  }),
});
```

### Z.ai Example

```typescript
// Using Z.ai for coding tasks
const response = await fetch('/api/ai/chat', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    sessionId: 'session-123',
    message: 'Write a sorting algorithm in Python',
    provider: 'zai',
    taskType: 'coding',
  }),
});
```

### Gemini Example

```typescript
// Using Gemini for general queries
const response = await fetch('/api/ai/chat', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    sessionId: 'session-123',
    message: 'What are the benefits of TypeScript?',
    provider: 'gemini',
    model: 'gemini-2.0-flash-exp',
  }),
});
```

### Claude Code Example

```typescript
// Using Claude Code CLI
const response = await fetch('/api/claude-code/execute', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    sessionId: 'session-123',
    message: 'Refactor this component to use hooks',
  }),
});
```

### Auto-Routing Example

```typescript
// Let the system choose the best provider
const response = await fetch('/api/ai/chat', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    sessionId: 'session-123',
    message: 'Debug this JavaScript code',
    taskType: 'coding', // Auto-routes to Z.ai or Claude Code
  }),
});
```

## Testing Strategy

```bash
# Test all providers
npm run test:providers

# Test OpenRouter
npm run test:openrouter

# Test Z.ai
npm run test:zai

# Test Gemini
npm run test:gemini

# Test Claude Code
npm run test:claude-code
```

## Summary of Changes

**Removed:**
- ❌ `@anthropic-ai/sdk`
- ❌ `openai`
- ❌ Direct Anthropic/OpenAI API calls

**Added:**
- ✅ OpenRouter integration with multiple models
- ✅ Z.ai coding API integration
- ✅ Gemini SDK integration
- ✅ Claude Code CLI integration
- ✅ Gemini CLI integration
- ✅ Provider selector UI
- ✅ Auto-routing based on task type
- ✅ Multi-provider fallback system

**Benefits:**
- Access to 200+ AI models via OpenRouter
- Specialized coding assistance via Z.ai
- CLI-based tools (Claude Code, Gemini)
- Cost optimization through provider selection
- Redundancy and fallback options
- Single API key management via OpenRouter
