# mcp-server-generator

Auto-generate MCP (Model Context Protocol) servers from OpenAPI specifications. One command turns any OpenAPI/Swagger spec into a production-ready MCP server.

## Quick Start

### Installation

```bash
npm install
npm run build
```

### Usage

```bash
npx mcp-generate https://api.example.com/openapi.json
# or
npx mcp-generate ./my-spec.yaml --output ./my-mcp-server
```

### Output

The generator creates a complete project with:

- TypeScript source code
- MCP tool definitions (one per endpoint, intelligently grouped)
- Authentication integration (API key, Bearer, OAuth2, Basic)
- HTTP client with retry/timeout logic
- Docker + docker-compose templates
- Comprehensive README
- Quick-start examples

## Architecture

### Pipeline Stages

1. **Parser** (`src/pipeline/parser/`)
   - Fetches spec from URL/file/stdin
   - Dereferences `$ref` chains
   - Normalizes Swagger 2.0 → OpenAPI 3.x
   - Extracts endpoints, schemas, auth schemes

2. **Analyzer** (`src/pipeline/analyzer/`)
   - Filters deprecated/internal endpoints
   - Profiles API structure
   - Suggests grouping strategies

3. **Mapper** (`src/pipeline/mapper/`)
   - Converts endpoints → MCP tools
   - Generates input schemas (Zod compatible)
   - Names tools consistently

4. **Optimizer** (`src/pipeline/optimizer/`)
   - Compresses descriptions for LLM efficiency
   - Disambiguates similar tools
   - Tracks token budgets

5. **Emitter** (`src/pipeline/emitter/`)
   - Generates TypeScript source files
   - Creates package.json / tsconfig.json
   - Generates Docker / README / .env templates

### Key Modules

- `src/auth/` — Authentication injection (API key, Bearer, OAuth2, Basic)
- `src/http/` — HTTP client with retry logic
- `src/cli/` — Command-line interface

## Testing

```bash
npm run test           # Watch mode
npm run test:run       # Single run
npm run test:ui        # UI dashboard
```

Test fixtures in `test/fixtures/`. Integration tests in `test/integration/`.

## Development

```bash
npm run build          # Compile TypeScript
npm run lint           # Check code quality
npm run dev -- <spec>  # Run generator in dev mode
```

## Project Structure

```
src/
├── cli/               # Command-line interface
├── pipeline/          # Five-stage generation pipeline
│   ├── parser/        # OpenAPI parsing + normalization
│   ├── analyzer/      # Semantic analysis
│   ├── mapper/        # Endpoint → tool conversion
│   ├── optimizer/     # Description optimization
│   └── emitter/       # Code generation
├── auth/              # Auth injection modules
├── http/              # HTTP client
└── utils/             # Utilities (result types, naming, etc.)

test/
├── unit/              # Parser, mapper, optimizer tests
├── integration/       # End-to-end pipeline tests
├── fixtures/          # Test OpenAPI specs
└── eval/              # LLM evaluation tests

examples/              # Pre-generated example servers
```

## Features

### OpenAPI Support

- ✅ OpenAPI 3.0.x / 3.1.x
- ✅ Swagger 2.0 (auto-converted)
- ✅ `$ref` dereferencing (local + remote)
- ✅ Circular reference detection

### Authentication

- ✅ API Key (header / query)
- ✅ Bearer / JWT
- ✅ Basic Auth
- ✅ OAuth2 (client credentials)
- ✅ OpenID Connect (detected)

### Generated Server Features

- ✅ MCP protocol compliance (stdio + HTTP/SSE transports)
- ✅ Per-tool input validation (Zod)
- ✅ Error handling (VALIDATION_ERROR / API_ERROR / AUTH_ERROR / TIMEOUT)
- ✅ Structured logging (stderr)
- ✅ Large response truncation
- ✅ Rate limit awareness

## CLI Options

```
mcp-generate <spec-source> [options]

Options:
  -o, --output <dir>          Output directory (default: ./mcp-server-output)
  --name <name>               Server name (derived from spec by default)
  --mode <mode>              Grouping: individual|resource|tag|custom (default: resource)
  --include-deprecated        Include deprecated endpoints
  --transport <type>         Transport: stdio|http|both (default: stdio)
  --port <port>              HTTP port if transport=http (default: 3000)
  --skip-auth                Generate without auth (public APIs)
```

## Example Generated Server

```typescript
// src/tools.ts (generated)
export const tools = [
  {
    name: 'list_users',
    description: 'List all users with pagination and optional filtering.',
    inputSchema: { /* ... */ },
    run: async (args) => {
      // HTTP client call + authentication + error handling
    }
  },
  // ... more tools
];
```

## Performance Targets

| API Size | Endpoints | Tools Generated | Generation Time |
|----------|-----------|-----------------|-----------------|
| Tiny | 1-10 | 1-10 | <2s |
| Small | 10-50 | 5-20 | <5s |
| Medium | 50-200 | 15-50 | <15s |
| Large | 200-500 | 30-80 | <30s |
| Massive | 500-2000 | 50-150 | <60s |

## Contributing

Issues and PRs welcome. Key areas:
- Description optimizer (token efficiency)
- Additional auth patterns
- Code generation quality
- Test coverage

## License

MIT

