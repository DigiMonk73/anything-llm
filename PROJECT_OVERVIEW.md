# AnythingLLM Project Overview

## Project Summary

AnythingLLM is a full-stack, open-source application that transforms private documents into an intelligent chat interface using Large Language Models (LLMs). It enables users to create their own private ChatGPT-like experience by uploading documents, which are then processed, embedded, and made available for conversational AI interactions.

**Core Value Proposition**: Turn any document collection into a private, intelligent knowledge base that can be queried through natural language conversations.

## Architecture Overview

### System Components

The application consists of three main microservices:

1. **Frontend** (`frontend/`) - React + Vite application
   - User interface for document management, chat, and settings
   - Built with React 18, TailwindCSS, and Vite
   - Runs on port 3000 in development

2. **Server** (`server/`) - Node.js Express API server
   - Handles all business logic, LLM interactions, and vector database operations
   - Manages user authentication, workspaces, and chat sessions
   - Runs on port 3001

3. **Collector** (`collector/`) - Document processing service
   - Processes and parses uploaded documents into searchable chunks
   - Supports 50+ file formats including PDF, DOCX, TXT, images, audio, video
   - Runs on port 8888

### Data Flow Architecture

```
User Upload → Collector (Process) → Server (Embed & Store) → Vector DB
     ↓
Frontend Chat → Server (Query) → Vector DB (Search) → LLM (Generate) → Response
```

## Key Technical Concepts

### 1. Document Processing Pipeline

**Text Splitting**: Documents are chunked using LangChain's RecursiveCharacterTextSplitter
- Configurable chunk size (default: 1000 tokens)
- Configurable overlap (default: 20 tokens)
- Metadata preservation per chunk

**Embedding Generation**: Text chunks are converted to vector embeddings
- Supports 12+ embedding providers (OpenAI, Cohere, Ollama, etc.)
- Default: Native embedding engine (runs locally)
- Embeddings stored in vector database

### 2. Vector Database Integration

**Supported Vector DBs**:
- LanceDB (default, local file-based)
- Pinecone, Weaviate, Qdrant, Chroma, Milvus, Zilliz, Astra DB, PGVector

**Vector Operations**:
- Similarity search with configurable thresholds
- Top-N retrieval (default: 4 chunks)
- Metadata filtering and querying

### 3. LLM Provider Abstraction

**Supported LLM Providers** (30+ total):
- **Cloud**: OpenAI, Anthropic, Google Gemini, Azure OpenAI, AWS Bedrock
- **Local**: Ollama, LM Studio, LocalAI, KoboldCPP, Text Generation WebUI
- **Specialized**: Together AI, Perplexity, Groq, Mistral, Cohere, DeepSeek

**Provider Interface**: Unified API for all LLM interactions
- Standardized chat completion format
- Provider-specific configuration management
- Fallback and error handling

### 4. Workspace System

**Workspace Concept**: 
- Isolated document collections with separate chat contexts
- Each workspace has its own vector database namespace
- Configurable LLM settings per workspace
- User permissions and access control

**Workspace Features**:
- Document pinning and watching
- Custom system prompts
- Temperature and model configuration
- Chat history and thread management

## Database Schema (Prisma)

### Core Entities

**Users** (`users`):
- Authentication and authorization
- Role-based access (admin, default)
- Profile management and settings

**Workspaces** (`workspaces`):
- Document containers with isolated contexts
- LLM configuration and settings
- Vector database namespace management

**Workspace Documents** (`workspace_documents`):
- Document metadata and file paths
- Pinning and watching status
- Relationship to workspaces

**Workspace Chats** (`workspace_chats`):
- Chat history and conversations
- User attribution and threading
- Feedback and scoring

**Document Vectors** (`document_vectors`):
- Vector embedding references
- Chunk-level metadata tracking

### Additional Features

**Agent System**:
- AI agents for web browsing, document analysis
- Plugin-based architecture using AIbitat framework
- MCP (Model Context Protocol) compatibility

**Embedding Management**:
- Configurable embedding providers per workspace
- Embedding model selection and configuration
- Vector database provider abstraction

## API Architecture

### Server Endpoints (`server/endpoints/`)

**Core APIs**:
- `/api/system` - System settings and health
- `/api/workspaces` - Workspace management
- `/api/chat` - Chat interactions
- `/api/document` - Document operations
- `/api/admin` - Administrative functions

**Specialized APIs**:
- `/api/embed` - Embedding widget endpoints
- `/api/agent` - AI agent interactions
- `/api/extensions` - Extension system
- `/api/experimental` - Experimental features

### Collector Endpoints (`collector/`)

**Processing APIs**:
- `/process` - Single file processing
- `/process-link` - URL/webpage processing
- `/process-raw-text` - Raw text processing
- `/util/get-link` - Link content extraction

## File Processing Capabilities

### Supported Formats

**Documents**: PDF, DOCX, TXT, RTF, ODT, EPUB, MOBI
**Spreadsheets**: XLSX, CSV, TSV
**Presentations**: PPTX, PPT
**Images**: PNG, JPG, JPEG, GIF, BMP, TIFF, WEBP (with OCR)
**Audio**: MP3, WAV, M4A, FLAC, OGG (with transcription)
**Video**: MP4, AVI, MOV, MKV (with transcription)
**Web**: HTML, URLs, RSS feeds
**Code**: Various programming language files
**Archives**: ZIP, RAR, 7Z

### Processing Features

**OCR Integration**: Tesseract.js for image text extraction
**Audio Transcription**: OpenAI Whisper or local alternatives
**Web Scraping**: Puppeteer-based webpage processing
**Metadata Extraction**: File properties, creation dates, authors
**Content Parsing**: Structured data extraction from various formats

## Security & Authentication

### Authentication System
- JWT-based authentication
- Password hashing with bcrypt
- Multi-factor authentication support
- API key management for integrations

### Data Security
- Local storage by default (no cloud dependencies)
- Encrypted sensitive data
- User permission system
- Workspace-level access control

## Development & Deployment

### Development Setup
```bash
# Install dependencies
yarn setup

# Start services (separate terminals)
yarn dev:server    # Backend API
yarn dev:frontend  # Frontend UI
yarn dev:collector # Document processor
```

### Production Deployment
**Docker (Recommended)**:
```bash
docker run -d -p 3001:3001 \
  --cap-add SYS_ADMIN \
  -v /path/to/storage:/app/server/storage \
  mintplexlabs/anythingllm
```

**Bare Metal**:
- Node.js v18+ required
- Build frontend and serve from server/public
- Run server and collector as separate processes

### Environment Configuration

**Key Environment Variables**:
- `STORAGE_DIR` - Application data storage path
- `JWT_SECRET` - Authentication secret
- `LLM_PROVIDER` - Default LLM provider
- `VECTOR_DB` - Vector database provider
- `EMBEDDING_ENGINE` - Embedding provider

## Integration Capabilities

### External Integrations
- **Browser Extension**: Chrome extension for web page processing
- **Embed Widget**: Embeddable chat widget for websites
- **API Access**: RESTful API for custom integrations
- **Webhooks**: Event-driven integrations

### Community Features
- **Multi-user Support**: User management and permissions
- **Invitation System**: Workspace sharing and collaboration
- **Community Hub**: Plugin and extension marketplace

## Performance Considerations

### Optimization Features
- **Vector Caching**: Cached embeddings for faster retrieval
- **Chunking Strategies**: Configurable text splitting for optimal context
- **Database Indexing**: Optimized vector search performance
- **Memory Management**: Efficient handling of large documents

### Scalability
- **Horizontal Scaling**: Stateless API design
- **Database Scaling**: Support for distributed vector databases
- **Load Balancing**: Multiple collector instances
- **Caching Layers**: Redis support for session management

## Monitoring & Observability

### Logging
- Structured logging with Winston
- Request/response tracking
- Error monitoring and alerting
- Performance metrics collection

### Health Checks
- Service health endpoints
- Database connectivity monitoring
- LLM provider status checks
- Storage capacity monitoring

## Future Roadmap

### Planned Features
- **Advanced Agents**: More sophisticated AI agent capabilities
- **Enhanced MCP Support**: Expanded Model Context Protocol integration
- **Multi-modal LLMs**: Better image and audio understanding
- **Advanced Analytics**: Usage analytics and insights
- **Enterprise Features**: SSO, LDAP, advanced security

### Technical Improvements
- **Performance Optimization**: Faster document processing
- **Memory Efficiency**: Reduced resource usage
- **Scalability Enhancements**: Better multi-user support
- **API Enhancements**: More comprehensive REST API

## Contributing Guidelines

### Development Workflow
1. Fork the repository
2. Create feature branch (`<issue>-<description>`)
3. Follow coding standards (ESLint, Prettier)
4. Add tests for new functionality
5. Submit pull request

### Code Standards
- **JavaScript**: ES6+ with async/await
- **React**: Functional components with hooks
- **API**: RESTful design with consistent error handling
- **Database**: Prisma ORM with migrations
- **Testing**: Jest for unit and integration tests

This overview provides the essential context for understanding the AnythingLLM codebase, its architecture, and how to work with it effectively. 