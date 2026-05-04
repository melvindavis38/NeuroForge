<div align="center">

# 🧠 NeuroForge AI Studio

### The AI-Powered Full-Stack App Builder

*Build production-ready web applications using natural language — entirely in your browser.*

[![MIT License](https://img.shields.io/badge/License-MIT-purple.svg)](LICENSE)
[![Node.js](https://img.shields.io/badge/Node.js-18+-green.svg)](https://nodejs.org)
[![pnpm](https://img.shields.io/badge/pnpm-9.x-blue.svg)](https://pnpm.io)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.7-blue.svg)](https://www.typescriptlang.org)
[![Remix](https://img.shields.io/badge/Remix-2.15-black.svg)](https://remix.run)

</div>

---

## 📖 Table of Contents

- [What is NeuroForge?](#-what-is-neuroforge)
- [Key Features](#-key-features)
- [System Architecture](#-system-architecture)
- [Tech Stack](#-tech-stack)
- [Getting Started](#-getting-started)
- [Supported AI Models](#-supported-ai-models)
- [Project Structure](#-project-structure)
- [Configuration Guide](#-configuration-guide)
- [How It Works](#-how-it-works)
- [Adding More AI Providers](#-adding-more-ai-providers)
- [Deployment](#-deployment)
- [Contributing](#-contributing)
- [License](#-license)

---

## ✨ What is NeuroForge?

NeuroForge AI Studio is a **browser-based, AI-powered development environment** that transforms natural language descriptions into fully functional web applications. It combines the power of large language models with an in-browser runtime to provide a complete development workflow — from idea to deployment — without ever leaving your browser.

### The Problem It Solves

Traditional web development requires:
- Setting up local development environments
- Writing boilerplate code manually
- Switching between editors, terminals, and browsers
- Managing dependencies and build tools

**NeuroForge eliminates all of this.** You describe what you want, and the AI builds it for you in real-time with a live preview.

---

## 🎯 Key Features

| Feature | Description |
|---------|-------------|
| 🤖 **AI Code Generation** | Describe your app in plain English → get production-quality code |
| 👁️ **Live Preview** | See your app running in real-time as the AI writes code |
| 📝 **Code Editor** | Full CodeMirror editor with syntax highlighting, search, and diff view |
| 🖥️ **Integrated Terminal** | Complete terminal access powered by Xterm.js |
| 📁 **File Manager** | Browse, create, edit, and delete project files |
| 🔀 **Git Integration** | Clone repos, push to GitHub/GitLab directly from the UI |
| 🚀 **One-Click Deploy** | Deploy to Vercel, Netlify, or any platform |
| 💬 **Chat History** | All conversations are saved locally with export/import support |
| 🎨 **Design Schemes** | Choose color schemes and design preferences for generated apps |
| 🔒 **File Locking** | Lock files to prevent the AI from modifying them |
| 🌙 **Dark/Light Theme** | Full theme support with system preference detection |
| 🆓 **100% Free** | Powered by HuggingFace's free inference API — no paid subscriptions |

---

## 🏗️ System Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        BROWSER (Client)                         │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌───────────────┐  │
│  │  Chat UI  │  │  Code    │  │ Terminal  │  │  Live Preview │  │
│  │  (React)  │  │  Editor  │  │ (Xterm)   │  │ (WebContainer)│  │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └───────┬───────┘  │
│       │              │             │                │           │
│  ┌────┴──────────────┴─────────────┴────────────────┴───────┐  │
│  │                    Remix Framework                        │  │
│  │              (Client-Side Routing & State)                │  │
│  └──────────────────────┬────────────────────────────────────┘  │
└─────────────────────────┼───────────────────────────────────────┘
                          │ HTTP/WebSocket
┌─────────────────────────┼───────────────────────────────────────┐
│                    SERVER (Remix + Vite)                         │
│  ┌──────────────────────┴────────────────────────────────────┐  │
│  │                    API Routes Layer                        │  │
│  │  /api/chat  /api/models  /api/github-*  /api/deploy-*     │  │
│  └──────────────────────┬────────────────────────────────────┘  │
│  ┌──────────────────────┴────────────────────────────────────┐  │
│  │                   LLM Manager                             │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐                │  │
│  │  │ Provider  │  │  Stream  │  │  System  │                │  │
│  │  │ Registry  │  │  Engine  │  │  Prompts │                │  │
│  │  └────┬─────┘  └────┬─────┘  └──────────┘                │  │
│  └───────┼──────────────┼────────────────────────────────────┘  │
└──────────┼──────────────┼───────────────────────────────────────┘
           │              │
┌──────────┼──────────────┼───────────────────────────────────────┐
│          │    EXTERNAL SERVICES                                  │
│  ┌───────┴──────┐  ┌───┴────────┐  ┌─────────────┐             │
│  │  HuggingFace │  │   GitHub   │  │   Vercel/   │             │
│  │  Router API  │  │   API      │  │   Netlify   │             │
│  │  (AI Models) │  │  (Git Ops) │  │  (Deploy)   │             │
│  └──────────────┘  └────────────┘  └─────────────┘             │
└─────────────────────────────────────────────────────────────────┘
```

### AI Processing Pipeline

```
User Message
    │
    ▼
┌─────────────────┐
│ Extract Model &  │
│ Provider Info    │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Build System     │    ┌──────────────────────┐
│ Prompt + Context │◄───│ File Context Buffer   │
│ + Design Scheme  │    │ Chat History Summary  │
└────────┬────────┘    └──────────────────────┘
         │
         ▼
┌─────────────────┐
│ Stream to LLM    │
│ (HuggingFace)   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Parse Response   │    Extracts:
│ (Message Parser) │───▶ • <boltArtifact> tags
└────────┬────────┘    • <boltAction> tags
         │              • File operations
         ▼              • Shell commands
┌─────────────────┐
│ Execute Actions  │
│ in WebContainer  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Live Preview     │
│ Updates          │
└─────────────────┘
```

### WebContainer Runtime

NeuroForge uses [WebContainers](https://webcontainers.io) by StackBlitz — a browser-native Node.js runtime that enables:

- **npm/pnpm install** directly in the browser
- **Dev server execution** (Vite, Next.js, etc.)
- **File system operations** without a backend
- **Hot module replacement** for instant preview updates

---

## 🛠️ Tech Stack

### Frontend
| Technology | Purpose |
|-----------|---------|
| [React 18](https://react.dev) | UI component library |
| [Remix 2.15](https://remix.run) | Full-stack web framework |
| [CodeMirror 6](https://codemirror.net) | Code editor with syntax highlighting |
| [Xterm.js](https://xtermjs.org) | Terminal emulator |
| [UnoCSS](https://unocss.dev) | Atomic CSS engine |
| [Framer Motion](https://www.framer.com/motion) | Animations |
| [Nanostores](https://github.com/nanostores/nanostores) | State management |

### Backend
| Technology | Purpose |
|-----------|---------|
| [Vite 5](https://vitejs.dev) | Build tool & dev server |
| [Vercel AI SDK](https://sdk.vercel.ai) | LLM integration & streaming |
| [WebContainers](https://webcontainers.io) | In-browser Node.js runtime |
| [isomorphic-git](https://isomorphic-git.org) | Git operations in JavaScript |

### AI / LLM
| Technology | Purpose |
|-----------|---------|
| [HuggingFace Inference](https://huggingface.co/inference-api) | Free AI model hosting |
| Qwen2.5-Coder-32B | Primary code generation model |
| Qwen2.5-Coder-7B | Fast code generation model |

---

## 🚀 Getting Started

### Prerequisites

| Requirement | Version |
|-------------|---------|
| [Node.js](https://nodejs.org) | 18.18.0+ |
| [pnpm](https://pnpm.io) | 9.x |
| Modern Browser | Chrome, Edge, or Firefox |

### Step 1: Clone & Install

```bash
git clone <your-repo-url>
cd AI-APP-BUILDER
pnpm install
```

### Step 2: Configure Environment

Create or edit `.env.local` in the project root:

```env
# ═══════════════════════════════════════════
#        NEUROFORGE CONFIGURATION
# ═══════════════════════════════════════════

# Required: HuggingFace API Key (FREE)
# Get yours at: https://huggingface.co/settings/tokens
HuggingFace_API_KEY=hf_your_token_here

# Optional: Google Gemini (free tier available)
# Get from: https://aistudio.google.com/apikey
GOOGLE_GENERATIVE_AI_API_KEY=your_key_here

# Optional: GitHub Models
# Get from: https://github.com/settings/tokens
GITHUB_API_KEY=your_github_pat_here
```

### Step 3: Start Development Server

```bash
pnpm run dev
```

You'll see:
```
★═══════════════════════════════════════★
        N E U R O F O R G E
       🧠  AI App Builder  🧠
★═══════════════════════════════════════★
```

### Step 4: Open in Browser

Navigate to **http://localhost:5173** — Select a model and start building! 🎉

---

## 🤖 Supported AI Models

### Currently Enabled (Free)

| Model | Parameters | Max Tokens | Speed | Quality | Best For |
|-------|-----------|------------|-------|---------|----------|
| **Qwen2.5-Coder-32B** | 32B | 32,768 | ⚡⚡ | ⭐⭐⭐⭐⭐ | Complex multi-file apps |
| **Qwen2.5-Coder-7B** | 7B | 32,768 | ⚡⚡⚡⚡ | ⭐⭐⭐⭐ | Simple apps, quick prototypes |

### Why These Models?

- ✅ **Free** — No API costs via HuggingFace Inference
- ✅ **Code-optimized** — Trained specifically for code generation
- ✅ **Tool-calling support** — Required by NeuroForge's artifact system
- ✅ **Large context** — Can handle complex prompts with file context

### Available (Requires API Keys)

Additional providers can be enabled by uncommenting imports in `registry.ts`:

| Provider | Models Available | API Key Required |
|----------|-----------------|-----------------|
| Google Gemini | Gemini 2.0 Flash, Gemini 1.5 Pro | Yes (free tier) |
| OpenAI | GPT-4o, GPT-4o-mini | Yes (paid) |
| Anthropic | Claude 4 Sonnet, Claude 4 Opus | Yes (paid) |
| Groq | Llama 3.3, Mixtral | Yes (free tier) |
| DeepSeek | DeepSeek Coder V2 | Yes (paid) |

---

## 📁 Project Structure

```
NeuroForge/
├── app/
│   ├── components/                 # React UI Components
│   │   ├── chat/                   # Chat interface
│   │   │   ├── BaseChat.tsx        # Main chat layout
│   │   │   ├── ChatBox.tsx         # Message input
│   │   │   ├── ChatAlert.tsx       # Error alerts
│   │   │   └── ExamplePrompts.tsx  # Starter prompts
│   │   ├── header/                 # Top navigation bar
│   │   │   └── Header.tsx          # NeuroForge logo & nav
│   │   ├── sidebar/                # Left sidebar menu
│   │   ├── workbench/              # Code editor & preview
│   │   │   ├── EditorPanel.tsx     # CodeMirror editor
│   │   │   ├── Preview.tsx         # Live app preview
│   │   │   └── terminal/           # Terminal component
│   │   ├── deploy/                 # GitHub/Vercel deployment
│   │   └── @settings/              # Settings panel
│   │
│   ├── lib/                        # Core Business Logic
│   │   ├── .server/                # Server-only code
│   │   │   └── llm/
│   │   │       ├── stream-text.ts  # AI streaming engine
│   │   │       └── constants.ts    # Token limits & config
│   │   ├── modules/                # Feature modules
│   │   │   └── llm/
│   │   │       ├── manager.ts      # LLM provider manager
│   │   │       ├── registry.ts     # Provider registration
│   │   │       └── providers/      # AI provider configs
│   │   │           └── huggingface.ts
│   │   ├── runtime/                # WebContainer runtime
│   │   │   ├── action-runner.ts    # Execute AI actions
│   │   │   └── message-parser.ts   # Parse AI responses
│   │   ├── stores/                 # Nanostores state
│   │   └── common/                 # Shared utilities
│   │       └── prompts/            # System prompts
│   │
│   ├── routes/                     # Remix Routes & API
│   │   ├── _index.tsx              # Home page
│   │   ├── api.chat.ts             # Chat API endpoint
│   │   ├── api.models.ts           # Model list endpoint
│   │   └── api.github-*.ts         # GitHub integration
│   │
│   └── styles/                     # Global CSS
│
├── public/                         # Static assets
├── .env.local                      # API keys (gitignored)
├── package.json                    # Dependencies
├── vite.config.ts                  # Vite configuration
├── PROJECT.md                      # Project documentation
├── LICENSE                         # MIT License
└── README.md                       # This file
```

---

## ⚙️ Configuration Guide

### Environment Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `HuggingFace_API_KEY` | ✅ | — | HuggingFace token for AI inference |
| `GOOGLE_GENERATIVE_AI_API_KEY` | ❌ | — | Google Gemini API key |
| `GITHUB_API_KEY` | ❌ | — | GitHub Models API access |
| `OPENAI_API_KEY` | ❌ | — | OpenAI API key |
| `ANTHROPIC_API_KEY` | ❌ | — | Anthropic Claude key |
| `GROQ_API_KEY` | ❌ | — | Groq inference key |

### Token Limits

Token limits control how much code the AI can generate per response:

| Setting | File | Default |
|---------|------|---------|
| HuggingFace completion limit | `constants.ts` | 16,384 tokens |
| Model max token allowed | `huggingface.ts` | 32,768 tokens |
| Global max tokens | `constants.ts` | 128,000 tokens |

---

## ⚡ How It Works

### 1. User Sends a Prompt
The user types a natural language request like *"Build a todo app with React and Tailwind"*.

### 2. System Prompt Injection
NeuroForge wraps the user's message with a detailed system prompt that instructs the AI to:
- Output code inside `<boltArtifact>` XML tags
- Use `<boltAction type="file">` for file creation
- Use `<boltAction type="shell">` for terminal commands
- Follow specific formatting rules for the parser

### 3. AI Model Generates Response
The message is streamed to HuggingFace's Qwen Coder model, which generates:
- Project structure and files
- Package.json with dependencies
- Application code (React, HTML, CSS, etc.)
- Shell commands (npm install, dev server start)

### 4. Response Parsing
The `MessageParser` processes the streamed response in real-time:
- Extracts file contents from `<boltAction type="file">` tags
- Extracts shell commands from `<boltAction type="shell">` tags
- Writes files to the WebContainer filesystem

### 5. WebContainer Execution
Commands are executed in the browser-native Node.js runtime:
- `npm install` installs dependencies
- `npm run dev` starts the development server
- The preview iframe connects to the dev server

### 6. Live Preview
The user sees the running app in the preview panel, updated in real-time.

---

## 🔌 Adding More AI Providers

To enable additional AI providers:

1. Open `app/lib/modules/llm/registry.ts`
2. Uncomment the desired provider import and export:

```typescript
// Uncomment to enable Google Gemini:
import GoogleProvider from './providers/google';

export {
  HuggingFaceProvider,
  GoogleProvider,  // Add this
};
```

3. Add the API key to `.env.local`:
```env
GOOGLE_GENERATIVE_AI_API_KEY=your_key_here
```

4. Restart the dev server

---

## 🚢 Deployment

### Docker

```bash
# Build production image
docker build -t neuroforge:latest --target bolt-ai-production .

# Run container
docker run -it -p 5173:5173 --env-file .env.local neuroforge:latest
```

### Cloudflare Pages

```bash
pnpm run build
npx wrangler pages deploy ./build/client
```

### Vercel / Netlify

Use the built-in deployment feature in NeuroForge's UI — click the deploy button and follow the prompts.

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📜 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- Built on the open-source [bolt.diy](https://github.com/stackblitz-labs/bolt.diy) framework by StackBlitz
- AI models provided by [HuggingFace](https://huggingface.co)
- In-browser runtime powered by [WebContainers](https://webcontainers.io)

---

<div align="center">

### 🧠 NeuroForge AI Studio

**Build apps with AI. No limits.**

*Made with ❤️ and 🤖*

</div>
