# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Frontend Development
```bash
# Install dependencies
npm install

# Start development server (default port 5173)
npm run dev

# Start development server on port 5050
npm run dev:5050

# Build for production
npm run build

# Build and watch for changes
npm run build:watch

# Preview production build
npm run preview

# Run type checking
npm run check

# Run type checking in watch mode
npm run check:watch

# Run all linting (frontend, types, backend)
npm run lint

# Format frontend code
npm run format

# Run frontend tests
npm run test:frontend

# Run Cypress tests
npm run cy:open

# Parse and format internationalization files
npm run i18n:parse
```

### Backend Development
```bash
# Install Python dependencies (requires Python 3.11-3.12)
pip install -e .

# Start the application
open-webui

# Start backend in development mode (with reload)
cd backend && PORT=8080 uvicorn open_webui.main:app --port 8080 --host 0.0.0.0 --forwarded-allow-ips '*' --reload

# Format backend code
npm run format:backend

# Lint backend code
npm run lint:backend
```

### Docker Development
```bash
# Run with Docker
docker run -d -p 3000:8080 --name open-webui ghcr.io/open-webui/open-webui:main

# Run with GPU support
docker run -d -p 3000:8080 --gpus all --name open-webui ghcr.io/open-webui/open-webui:cuda

# Run with Ollama
docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data --name open-webui \
  ghcr.io/open-webui/open-webui:main
```

### Docker Compose Commands
```bash
# Install and start with docker-compose
make install

# Start containers
make start

# Stop containers
make stop

# Build and start containers
make startAndBuild

# Update application and containers
make update

# Remove containers (with confirmation)
make remove
```

## Architecture Overview

### Frontend (SvelteKit)
- **Location**: `/src/`
- **Framework**: SvelteKit with TypeScript
- **Key Components**:
  - `/src/routes/` - Page routes and layouts
  - `/src/lib/components/` - Reusable Svelte components
  - `/src/lib/apis/` - API client functions
  - `/src/lib/stores/` - Svelte stores for state management
  - `/src/lib/utils/` - Utility functions

### Backend (FastAPI)
- **Location**: `/backend/open_webui/`
- **Framework**: FastAPI with Python
- **Key Modules**:
  - `main.py` - Application entry point and middleware setup
  - `/routers/` - API endpoints organized by feature
  - `/models/` - SQLAlchemy database models
  - `/utils/` - Backend utilities and helpers
  - `/retrieval/` - RAG and document processing
  - `/socket/` - WebSocket handling for real-time features

### Key Features Architecture

#### Chat System
- Frontend: `/src/lib/components/chat/` - Chat UI components
- Backend: `/backend/open_webui/routers/chats.py` - Chat API endpoints
- Models stored in `/backend/open_webui/models/chats.py`
- Real-time updates via Socket.IO

#### Pipeline System
- Frontend: `/src/lib/components/admin/Settings/Pipelines.svelte`
- Backend: `/backend/open_webui/routers/pipelines.py`
- Pipelines act as middleware for request/response processing
- Support for filter and pipe types

#### RAG (Retrieval Augmented Generation)
- Document processing: `/backend/open_webui/retrieval/`
- Vector stores: Chroma, Milvus, Qdrant, etc.
- Web search integration for enhanced context

#### Authentication & Authorization
- RBAC system with user groups
- OAuth support (Google, Microsoft, etc.)
- Backend: `/backend/open_webui/routers/auths.py`

### Database
- SQLAlchemy with Alembic migrations
- Models in `/backend/open_webui/models/`
- Migrations in `/backend/open_webui/migrations/`
- Migration commands handled automatically by the application

### Configuration
- Environment variables documented in `.env.example`
- Frontend config: `vite.config.ts`, `svelte.config.js`, `tailwind.config.js`
- Backend config: `/backend/open_webui/config.py`
- Package configuration: `package.json` (frontend), `pyproject.toml` (backend)

## Development Environment Setup

### Prerequisites
- **Node.js**: Version 18.13.0 - 22.x.x
- **Python**: Version 3.11 - 3.12
- **npm**: Version 6.0.0 or higher

### Local Development Workflow
1. **Frontend development**: Run `npm run dev` for hot-reload development server
2. **Backend development**: Use `cd backend && uvicorn open_webui.main:app --reload` for auto-reload
3. **Full stack development**: Run both frontend and backend simultaneously on different ports
4. **Production build**: Use `npm run build` to create optimized frontend build

### Environment Variables
Key environment variables (see `.env.example` for full list):
- `OLLAMA_BASE_URL`: Backend Ollama connection URL
- `OPENAI_API_BASE_URL`: OpenAI API base URL
- `OPENAI_API_KEY`: OpenAI API key
- `PORT`: Backend server port (default: 8080)
- `HOST`: Backend server host (default: 0.0.0.0)

## Important Patterns

### API Communication
- Frontend uses `/src/lib/apis/index.ts` for API calls
- All API routes prefixed with `/api/v1/`
- WebSocket connections for real-time features via Socket.IO
- Streaming responses for chat interactions

### State Management
- Svelte stores in `/src/lib/stores/`
- Key stores: `user`, `settings`, `models`, `chats`
- Reactive state updates across components

### Component Structure
- Components are modular and located in `/src/lib/components/`
- Icons in `/src/lib/components/icons/`
- Common UI elements in `/src/lib/components/common/`
- Page routes follow SvelteKit conventions in `/src/routes/`

### Error Handling
- Frontend: Toast notifications via `svelte-sonner`
- Backend: FastAPI exception handlers
- Consistent error response format

### Data Flow Architecture
- **Request Flow**: Frontend → API Router → Business Logic → Database Models
- **Real-time Updates**: WebSocket connections for live chat updates
- **File Processing**: Upload → Storage Provider → Document Processing → Vector Store
- **Authentication**: JWT tokens with role-based access control

### Code Organization Principles
- **Backend**: Feature-based organization in `/routers/` with corresponding models
- **Frontend**: Component-based architecture with shared utilities
- **API Design**: RESTful endpoints with WebSocket for real-time features
- **Database**: SQLAlchemy ORM with automatic migrations

## Testing
- Frontend tests with Vitest (run `npm run test:frontend`)
- E2E tests with Cypress (run `npm run cy:open`)
- Test files colocated with source files
- Backend tests in `/backend/open_webui/test/`

## Build and Deployment
- **Frontend**: SvelteKit builds to static files or Node.js server
- **Backend**: Python package with FastAPI application
- **Docker**: Multi-stage builds with frontend and backend combined
- **Dependencies**: Frontend deps via npm, backend deps via pip/pyproject.toml