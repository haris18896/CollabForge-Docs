# CollabForge-Docs

## Phase 1: Backend Foundation

**Goal:** Create a clean production-ready backend base.

### Steps

1. Create project folder and initialize Node project
2. Add TypeScript setup
3. Install Fastify and core dependencies
4. Create clean folder structure
5. Add environment configuration
6. Validate `.env` using Zod
7. Create Fastify app bootstrap
8. Create server entry file
9. Add health-check route
10. Add Docker Compose for MongoDB
11. Add basic test setup with Vitest
12. Test `/health` route
13. Add linting/formatting setup
14. Confirm build works

---

## Phase 2: MongoDB Connection & Database Layer

**Goal:** Connect backend to MongoDB professionally.

### Steps

1. Create MongoDB client plugin
2. Add database connection lifecycle
3. Add graceful shutdown
4. Create database constants
5. Create collection names
6. Create base repository pattern
7. Create health check for database
8. Add indexes setup
9. Add database integration test
10. Verify MongoDB is working from backend

---

## Phase 3: User Module & Authentication

**Goal:** Add secure user registration and login.

### Steps

1. Create user module folder
2. Define user schema/model
3. Add password hashing with bcrypt
4. Create user repository
5. Create auth service
6. Create signup route
7. Create login route
8. Generate JWT token
9. Add auth middleware/plugin
10. Add protected test route
11. Add validation for auth payloads
12. Add auth unit tests
13. Add auth integration tests
14. Test with Postman/Thunder Client

---

## Phase 4: Document Metadata API

**Goal:** Build normal REST APIs before real-time editing.

### Steps

1. Create document module
2. Define document schema
3. Design MongoDB document model
4. Add owner field
5. Add permissions/sharing field
6. Create document repository
7. Create document service
8. Create document routes
9. Add create document API
10. Add list my documents API
11. Add get document by ID API
12. Add update title API
13. Add delete/archive document API
14. Add authorization checks
15. Add document API tests

---

## Phase 5: WebSocket Foundation

**Goal:** Add WebSocket support without Yjs first.

### Steps

1. Install WebSocket dependency
2. Decide Fastify WebSocket plugin vs standalone `ws` server
3. Create WebSocket module
4. Add connection endpoint
5. Add connection logging
6. Add authentication during WebSocket connection
7. Add document authorization check
8. Add room concept using `documentId`
9. Add basic join/leave events
10. Add ping/pong heartbeat
11. Test WebSocket connection manually
12. Add WebSocket integration test

---

## Phase 6: Yjs Real-time Collaboration

**Goal:** Sync document content between multiple users.

### Steps

1. Install Yjs dependencies
2. Understand CRDT document structure
3. Create Yjs document manager
4. Create document room map
5. Bind Yjs document to WebSocket room
6. Handle Yjs sync messages
7. Open same document in two clients
8. Verify real-time text sync
9. Add cleanup when users disconnect
10. Add logging for sync lifecycle
11. Add basic Yjs sync test

---

## Phase 7: Yjs Persistence in MongoDB

**Goal:** Save collaborative edits permanently.

### Steps

1. Decide persistence strategy
2. Store Yjs updates as binary updates
3. Create `document_updates` collection
4. Add update append logic
5. Restore Yjs document from saved updates
6. Add snapshot strategy
7. Compact old updates into snapshots
8. Add document version metadata
9. Test server restart restore
10. Add persistence integration tests
11. Add failure-handling logic

---

## Phase 8: Cursor Presence & Awareness

**Goal:** Show active users, cursor position, and selections.

### Steps

1. Understand Yjs Awareness protocol
2. Define presence payload
3. Add user identity to WebSocket session
4. Broadcast user join/leave
5. Track cursor position
6. Track selection range
7. Track active username/color
8. Avoid persisting presence data
9. Clean presence on disconnect
10. Test multiple users in same document

---

## Phase 9: Offline Editing & Sync

**Goal:** Allow users to edit offline and merge later.

### Steps

1. Understand offline-first syncing
2. Explain why CRDT helps offline edits
3. Store local Yjs updates on client side
4. Reconnect WebSocket after offline period
5. Sync missing updates
6. Resolve concurrent edits automatically
7. Handle deleted/archived documents
8. Add conflict examples
9. Test offline edit scenario
10. Test two users editing same paragraph offline

---

## Phase 10: Access Control & Sharing

**Goal:** Build Google Docs-like permission model.

### Steps

1. Define roles: owner, editor, viewer
2. Add permissions schema
3. Add invite/share API
4. Add revoke access API
5. Add role update API
6. Restrict REST APIs by permission
7. Restrict WebSocket editing by permission
8. Allow viewers to connect read-only
9. Prevent unauthorized Yjs updates
10. Add authorization tests

---

## Phase 11: Testing Strategy

**Goal:** Make the project reliable.

### Steps

1. Unit test services
2. Unit test repositories with mocks
3. Integration test MongoDB repositories
4. E2E test REST APIs
5. WebSocket connection tests
6. Yjs sync tests
7. Auth permission tests
8. Offline sync tests
9. Multi-user editing tests
10. Server restart persistence tests
11. Test coverage setup

---

## Phase 12: Security Hardening

**Goal:** Protect the backend properly.

### Steps

1. Add Helmet security headers
2. Configure CORS correctly
3. Add rate limiting
4. Add request payload limits
5. Add JWT expiration
6. Add refresh token strategy
7. Sanitize user input
8. Add document access checks everywhere
9. Prevent WebSocket abuse
10. Add audit logs for document sharing
11. Add production error handling

---

## Phase 13: Observability & Logging

**Goal:** Make debugging production issues easier.

### Steps

1. Add request IDs
2. Add structured logs
3. Log WebSocket lifecycle
4. Log document sync errors
5. Log persistence failures
6. Add health route
7. Add readiness route
8. Add database health check
9. Add metrics discussion
10. Add production monitoring plan

---

## Phase 14: Multi-instance Scaling

**Goal:** Prepare for real production deployment.

### Steps

1. Understand why single-instance WebSocket is limited
2. Explain sticky sessions
3. Explain load balancer behavior
4. Add Redis pub/sub design
5. Sync document updates across instances
6. Handle user presence across instances
7. Run two backend instances locally
8. Test two users connected to different instances
9. Add scaling limitations
10. Decide when to split collaboration server from API server

---

## Phase 15: Deployment

**Goal:** Deploy backend professionally.

### Steps

1. Add Dockerfile
2. Add `.dockerignore`
3. Add production environment config
4. Add build script
5. Add start script
6. Add MongoDB Atlas option
7. Add deployment architecture
8. Add CI pipeline
9. Add test step in CI
10. Add build step in CI
11. Add deploy step
12. Add smoke test after deployment

---
