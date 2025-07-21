# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a Model Context Protocol (MCP) server implementation for interacting with Salesforce through its REST API using jsforce. The server exposes Salesforce operations as MCP tools that can be called by Claude and other AI assistants.

## Common Development Commands

### Setup
```bash
npm install
cp .env.example .env  # Then configure with your Salesforce credentials
```

### Build and Run
```bash
npm run build     # Compile TypeScript to JavaScript
npm start         # Run the compiled server
npm run dev       # Development mode with file watching
```

### Evaluation/Testing
```bash
# Run evaluations (requires OPENAI_API_KEY environment variable)
OPENAI_API_KEY=your-key npx mcp-eval src/evals/evals.ts src/tools.ts
```

## Architecture

### Core Components

- **`src/index.ts`**: Main server entry point implementing the MCP protocol server class
- **`src/tools.ts`**: Tool definitions and handlers for Salesforce operations
- **`src/types.ts`**: TypeScript interfaces and type guards for tool arguments
- **`src/evals/evals.ts`**: Evaluation functions for testing tool functionality

### MCP Server Structure

The server follows the standard MCP pattern:
1. **SalesforceServer class** extends MCP Server with Salesforce-specific capabilities
2. **Tool registration** via `ListToolsRequestSchema` handler
3. **Tool execution** via `CallToolRequestSchema` handler with jsforce connection management
4. **Authentication** happens on each tool call using environment variables

### Available Tools

- **`query`**: Execute SOQL queries against Salesforce data
- **`tooling-query`**: Execute queries against the Salesforce Tooling API 
- **`describe-object`**: Get detailed metadata about Salesforce objects
- **`metadata-retrieve`**: Retrieve metadata components (Flows, Apex classes, etc.)

### Environment Configuration

Required environment variables (set in `.env` file):
- `SF_LOGIN_URL`: Salesforce login URL (defaults to https://login.salesforce.com)
- `SF_USERNAME`: Salesforce username
- `SF_PASSWORD`: Salesforce password
- `SF_SECURITY_TOKEN`: Salesforce security token

### Type Safety

All tool arguments use TypeScript interfaces with runtime type guards for validation. The `types.ts` file provides:
- Interface definitions for each tool's arguments
- Type guard functions (e.g., `isValidQueryArgs`) for runtime validation
- Metadata type validation using jsforce's MetadataType

### Evaluation Framework

Uses the `mcp-evals` package for testing tool functionality:
- Each tool has corresponding evaluation functions
- Evaluations use GPT-4 to test tool behavior
- Run evaluations during development to ensure tools work correctly

## Development Notes

- The server uses jsforce version 3.6.3 with TypeScript definitions
- Authentication is handled per-request rather than maintaining persistent sessions
- All tool responses are JSON-stringified for MCP protocol compatibility
- Error handling includes both Salesforce API errors and MCP protocol errors
- The server runs on stdio transport for MCP communication