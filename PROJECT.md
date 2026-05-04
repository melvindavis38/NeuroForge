# NeuroForge AI Studio — Technical Project Documentation

## 🧠 Project Overview

**NeuroForge AI Studio** is an AI-powered, browser-based integrated development environment (IDE) that transforms natural language prompts into fully functional web applications. It leverages large language models (LLMs) via the HuggingFace Inference API, executes generated code in WebContainers (a browser-native Node.js runtime), and renders live previews — all without requiring a backend server for code execution.

| Property | Value |
|----------|-------|
| **Project Type** | Full-Stack Web Application |
| **Framework** | Remix 2.15 + Vite 5 |
| **Language** | TypeScript 5.7 |
| **Package Manager** | pnpm 9.x |
| **Node.js** | 18.18.0+ |
| **License** | MIT |

---

## 🏗️ System Architecture

### Layer Architecture

```
┌──────────────────────────────────────────────────────────────────────────┐
│                         PRESENTATION LAYER                                │
│                                                                          │
│  ┌─────────────┐  ┌─────────────┐  ┌──────────┐  ┌─────────────────┐   │
│  │   Chat UI    │  │ Code Editor │  │ Terminal  │  │  Live Preview   │   │
│  │  (BaseChat)  │  │ (CodeMirror)│  │ (Xterm)   │  │ (WebContainer)  │   │
│  │  - Messages  │  │ - Syntax HL │  │ - Bash    │  │ - iframe        │   │
│  │  - Prompts   │  │ - Diff View │  │ - npm     │  │ - HMR           │   │
│  │  - Alerts    │  │ - Search    │  │ - git     │  │ - Port Mapping  │   │
│  └──────┬──────┘  └──────┬──────┘  └────┬─────┘  └────────┬────────┘   │
│         └─────────────┬──┴──────────────┴──────────────────┘            │
├───────────────────────┼─────────────────────────────────────────────────┤
│                       │      APPLICATION LAYER                           │
│                       ▼                                                  │
│  ┌────────────────────────────────────────────────────────────────────┐  │
│  │                    Remix Framework (v2.15)                         │  │
│  │  ┌────────────┐  ┌────────────┐  ┌──────────────┐                │  │
│  │  │   Routing   │  │   Loaders  │  │   Actions    │                │  │
│  │  │  _index.tsx │  │ (SSR Data) │  │ (Mutations)  │                │  │
│  │  │  git.tsx    │  │            │  │              │                │  │
│  │  └────────────┘  └────────────┘  └──────────────┘                │  │
│  │                                                                    │  │
│  │  ┌─────────────────────────────────────────────────────────────┐  │  │
│  │  │                    State Management                         │  │  │
│  │  │  Nanostores: chatStore, workbenchStore, themeStore, etc.    │  │  │
│  │  └─────────────────────────────────────────────────────────────┘  │  │
│  └────────────────────────────┬───────────────────────────────────────┘  │
├───────────────────────────────┼─────────────────────────────────────────┤
│                               │      SERVICE LAYER                       │
│                               ▼                                          │
│  ┌──────────────────────────────────────────────────────────────────┐    │
│  │                       API Routes                                  │    │
│  │                                                                    │    │
│  │  POST /api/chat          → Stream AI responses                    │    │
│  │  GET  /api/models        → List available AI models               │    │
│  │  GET  /api/models/:prov  → Provider-specific models               │    │
│  │  POST /api/bug-report    → Submit bug reports                     │    │
│  │  GET  /api/github-user   → GitHub authentication                  │    │
│  │  POST /api/github-*      → GitHub operations                      │    │
│  │  POST /api/gitlab-*      → GitLab operations                      │    │
│  │  GET  /api/system/*      → System info & health                   │    │
│  └──────────────────────────────┬───────────────────────────────────┘    │
├─────────────────────────────────┼───────────────────────────────────────┤
│                                 │      AI ENGINE LAYER                   │
│                                 ▼                                        │
│  ┌──────────────────────────────────────────────────────────────────┐    │
│  │                      LLM Manager                                  │    │
│  │                                                                    │    │
│  │  ┌──────────────┐   ┌──────────────┐   ┌──────────────────────┐  │    │
│  │  │   Provider   │   │   Stream     │   │    System Prompt     │  │    │
│  │  │   Registry   │   │   Engine     │   │    Builder           │  │    │
│  │  │              │   │              │   │                      │  │    │
│  │  │ • HuggingFace│   │ • Token mgmt │   │ • Base instructions  │  │    │
│  │  │ • (Google)   │   │ • Streaming  │   │ • File context       │  │    │
│  │  │ • (OpenAI)   │   │ • Error hdlg │   │ • Chat history       │  │    │
│  │  │ • (Anthropic)│   │ • Reasoning  │   │ • Design scheme      │  │    │
│  │  │ • (20+ more) │   │   model det. │   │ • Locked files       │  │    │
│  │  └──────────────┘   └──────────────┘   └──────────────────────┘  │    │
│  └──────────────────────────────────────────────────────────────────┘    │
├─────────────────────────────────────────────────────────────────────────┤
│                           RUNTIME LAYER                                  │
│                                                                          │
│  ┌──────────────────────────────────────────────────────────────────┐    │
│  │                     WebContainer Runtime                          │    │
│  │                                                                    │    │
│  │  ┌──────────────┐   ┌──────────────┐   ┌──────────────────────┐  │    │
│  │  │  Action      │   │  Message     │   │    File System       │  │    │
│  │  │  Runner      │   │  Parser      │   │    (Virtual)         │  │    │
│  │  │              │   │              │   │                      │  │    │
│  │  │ • File write │   │ • XML parse  │   │ • In-memory FS       │  │    │
│  │  │ • Shell exec │   │ • Artifact   │   │ • File watchers      │  │    │
│  │  │ • npm install│   │   extraction │   │ • Binary support     │  │    │
│  │  │ • Dev server │   │ • Action     │   │ • Lock mechanism     │  │    │
│  │  │   start      │   │   dispatch   │   │                      │  │    │
│  │  └──────────────┘   └──────────────┘   └──────────────────────┘  │    │
│  └──────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## ⚡ Core Data Flows

### 1. Chat Request Flow

```
┌──────────┐     ┌────────────┐     ┌──────────────┐     ┌─────────────┐
│  User    │────▶│ ChatBox    │────▶│ /api/chat    │────▶│ stream-text │
│  Input   │     │ Component  │     │ (POST)       │     │ .ts         │
└──────────┘     └────────────┘     └──────────────┘     └──────┬──────┘
                                                                │
                 ┌────────────────────────────────────────────────┘
                 ▼
         ┌──────────────┐     ┌─────────────────┐     ┌──────────────┐
         │ Build System │────▶│ HuggingFace     │────▶│ Stream       │
         │ Prompt       │     │ Router API      │     │ Response     │
         │              │     │ (OpenAI compat) │     │ (SSE)        │
         └──────────────┘     └─────────────────┘     └──────┬───────┘
                                                              │
         ┌────────────────────────────────────────────────────┘
         ▼
  ┌──────────────┐     ┌──────────────┐     ┌──────────────────┐
  │ Message      │────▶│ Action       │────▶│ WebContainer     │
  │ Parser       │     │ Runner       │     │ (file write,     │
  │ (XML tags)   │     │ (execute)    │     │  npm install,    │
  └──────────────┘     └──────────────┘     │  dev server)     │
                                            └────────┬─────────┘
                                                     │
                                            ┌────────▼─────────┐
                                            │  Live Preview    │
                                            │  (iframe)        │
                                            └──────────────────┘
```

### 2. Provider Registration Flow

```typescript
// registry.ts → manager.ts → stream-text.ts

registry.ts exports providers
    ↓
LLMManager._registerProvidersFromDirectory()
    ↓ iterates Object.values(providers)
    ↓ checks instanceof BaseProvider
    ↓
LLMManager.registerProvider(provider)
    ↓ adds to _providers Map
    ↓ merges staticModels into _modelList
    ↓
/api/models endpoint returns modelList
    ↓
UI renders provider/model dropdowns
```

### 3. AI Response Artifact Format

The AI model outputs code in a structured XML format that the message parser understands:

```xml
<boltArtifact id="counter-app" title="Simple Counter">
  <boltAction type="file" filePath="index.html">
    <!DOCTYPE html>
    <html>
      <head><title>Counter</title></head>
      <body>
        <h1 id="count">0</h1>
        <button onclick="increment()">+</button>
        <button onclick="decrement()">-</button>
        <script>
          let count = 0;
          const el = document.getElementById('count');
          function increment() { el.textContent = ++count; }
          function decrement() { el.textContent = --count; }
        </script>
      </body>
    </html>
  </boltAction>
  <boltAction type="shell">
    echo "No build step needed for static HTML"
  </boltAction>
</boltArtifact>
```

**Parsed by:** `app/lib/runtime/message-parser.ts`
**Executed by:** `app/lib/runtime/action-runner.ts`

---

## 🔧 Key Module Deep Dives

### LLM Manager (`app/lib/modules/llm/manager.ts`)

The LLM Manager is a **singleton** that handles provider registration, model discovery, and instance creation.

```typescript
class LLMManager {
  private _providers: Map<string, BaseProvider>  // Registered providers
  private _modelList: ModelInfo[]                // Aggregated model list
  private _env: Record<string, string>           // Server environment

  // Key methods:
  registerProvider(provider)      // Add a provider to the registry
  getProvider(name)              // Get provider by name
  getAllProviders()              // List all registered providers
  getModelList()                 // Get combined static + dynamic models
  updateModelList(options)       // Refresh dynamic models from APIs
  getDefaultProvider()           // First registered provider
}
```

**Design Decisions:**
- Singleton pattern ensures one instance across all requests
- Static models (hardcoded) vs dynamic models (fetched from API) are merged
- Dynamic models are cached to avoid excessive API calls

### Stream Engine (`app/lib/.server/llm/stream-text.ts`)

The streaming engine handles the complete lifecycle of an AI request:

```
1. Extract model & provider from user message
2. Find model details (token limits, capabilities)
3. Build system prompt with:
   - Base instructions (artifact format, rules)
   - File context buffer (existing project files)
   - Chat history summary (for context optimization)
   - Design scheme preferences
   - Locked file restrictions
4. Detect reasoning models (o1, GPT-5) → use maxCompletionTokens
5. Filter unsupported parameters for reasoning models
6. Call Vercel AI SDK's streamText() with OpenAI-compatible provider
7. Return SSE stream to client
```

**Token Management:**
```
Priority Order for completion token limits:
1. model.maxCompletionTokens (if set)
2. PROVIDER_COMPLETION_LIMITS[provider] (provider default)
3. Math.min(MAX_TOKENS, 16384) (global fallback)
```

### Message Parser (`app/lib/runtime/message-parser.ts`)

Parses the streaming AI response in real-time:

```
Stream chunk arrives
    ↓
Check for <boltArtifact> opening tag → emit ArtifactOpen event
    ↓
Check for <boltAction type="file"> → emit ActionOpen (file) event
    ↓
Buffer file content line by line
    ↓
Check for </boltAction> → emit ActionClose event → write file
    ↓
Check for <boltAction type="shell"> → emit ActionOpen (shell) event
    ↓
Buffer command → execute in WebContainer terminal
    ↓
Check for </boltArtifact> → emit ArtifactClose event
```

### HuggingFace Provider (`app/lib/modules/llm/providers/huggingface.ts`)

```typescript
class HuggingFaceProvider extends BaseProvider {
  name = 'HuggingFace';
  
  staticModels = [
    { name: 'Qwen/Qwen2.5-Coder-32B-Instruct', maxTokenAllowed: 32768 },
    { name: 'Qwen/Qwen2.5-Coder-7B-Instruct',  maxTokenAllowed: 32768 },
  ];

  getModelInstance(options) {
    // Uses createOpenAI() from @ai-sdk/openai
    // Base URL: https://router.huggingface.co/v1
    // Auth: Bearer <HuggingFace_API_KEY>
    // Returns OpenAI-compatible model instance
  }
}
```

**Why `router.huggingface.co`?**
- The old `api-inference.huggingface.co` endpoint doesn't support the OpenAI chat completions format
- The router endpoint provides OpenAI-compatible `/v1/chat/completions`
- Supports streaming, tool calling, and function calling

---

## 📊 Token Flow & Limits

```
┌─────────────────────────────────────────────────────────┐
│                    Token Budget                          │
│                                                          │
│  Model Context Window: 32,768 tokens                     │
│  ┌──────────────────────────────────────────────────┐   │
│  │  System Prompt        ≈ 3,000-5,000 tokens       │   │
│  │  File Context Buffer  ≈ 0-10,000 tokens          │   │
│  │  Chat History         ≈ 0-5,000 tokens           │   │
│  │  User Message         ≈ 50-500 tokens            │   │
│  │  ─────────────────────────────────────────        │   │
│  │  Available for Response: ≈ 16,384 tokens          │   │
│  │  (PROVIDER_COMPLETION_LIMITS['HuggingFace'])      │   │
│  └──────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

---

## 🗄️ State Management

NeuroForge uses **Nanostores** for lightweight, framework-agnostic state:

| Store | Location | Purpose |
|-------|----------|---------|
| `chatStore` | `lib/stores/chat.ts` | Chat state (started, aborted) |
| `workbenchStore` | `lib/stores/workbench.ts` | Files, editor, preview state |
| `themeStore` | `lib/stores/theme.ts` | Dark/light theme preference |
| `filesStore` | `lib/stores/files.ts` | Virtual filesystem state |
| `settingsStore` | `lib/stores/settings.ts` | User preferences |

---

## 🔐 Security Considerations

| Concern | Mitigation |
|---------|-----------|
| API Key Storage | Keys stored in `.env.local` (gitignored) or browser cookies |
| Code Execution | All code runs in WebContainers (sandboxed, browser-only) |
| XSS Prevention | Preview runs in sandboxed iframe |
| Token Exposure | Server-side API calls, tokens never reach client |
| File Access | WebContainer filesystem is isolated from host |

---

## 🧪 Testing

```bash
# Run unit tests
pnpm test

# Run tests in watch mode
pnpm test:watch

# Type checking
pnpm typecheck

# Lint
pnpm lint
```

**Test files:** `app/lib/runtime/message-parser.spec.ts`

---

## 📈 Performance Optimizations

| Optimization | Implementation |
|-------------|---------------|
| Context Optimization | Only sends relevant files to LLM, not entire project |
| Chat Summarization | Long conversations are summarized to save tokens |
| Model Caching | Dynamic model lists are cached to avoid repeated API calls |
| Lazy Loading | Components use `ClientOnly` for SSR-safe rendering |
| Virtual Scrolling | Large file lists use react-virtual for performance |
| HMR | Vite's Hot Module Replacement for instant dev updates |

---

## 🔮 Extension Points

### Adding a New AI Provider

1. Create `app/lib/modules/llm/providers/my-provider.ts`
2. Extend `BaseProvider` class
3. Define `staticModels` array
4. Implement `getModelInstance()` method
5. Register in `registry.ts`

### Adding a New Deployment Target

1. Create component in `app/components/deploy/`
2. Add API route in `app/routes/api.deploy-*.ts`
3. Register in deployment menu

### Adding a New Theme

1. Add CSS variables in `app/styles/`
2. Register in `themeStore`
3. Add toggle in settings UI

---

## 📝 Changelog

### v1.0.0 — NeuroForge Release

- ✅ Rebranded from bolt.diy to NeuroForge AI Studio
- ✅ Configured HuggingFace as primary (free) AI provider
- ✅ Optimized token limits for code generation (16K completion tokens)
- ✅ Curated model list to only tool-call-capable models
- ✅ Updated all UI text, logos, and metadata
- ✅ Disabled non-configured providers to prevent errors

---

<div align="center">

**🧠 NeuroForge AI Studio**

*Technical documentation for developers and contributors.*

</div>
