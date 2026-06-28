# collaborative-document-editor

```
Client Editor
   |
   | REST APIs
   v
Fastify API Server
   |
   | Auth / Users / Documents / Sharing / Audit Logs
   v
MongoDB

Client Editor
   |
   | WebSocket / Yjs sync
   v
Collaboration Gateway
   |
   | Redis Pub/Sub for multi-server sync
   v
Other Collaboration Instances

Background Worker
   |
   | Snapshots / Email / Cleanup / Versioning
   v
MongoDB + Redis
```

## `Phase 1: Backend Foundation`

**Goal:** Create a clean production-ready Fastify backend base.

### Steps

1. Create project folder and initialize Node project ✅
2. Add TypeScript setup ✅
3. Install Fastify and core dependencies ✅
4. Create clean modular folder structure ✅
5. Add environment configuration
6. Validate `.env` using Zod
7. Create Fastify app bootstrap
8. Create server entry file
9. Add centralized error handling
10. Add standard API response format
11. Add health-check route
12. Add Docker Compose for MongoDB and Redis
13. Add basic test setup with Vitest
14. Test `/health` route
15. Add linting and formatting setup
16. Confirm build works

### `Phase 1 - Step 1 - Create Folder & Initilize Project`

```sh
cd collaborative-document-editor
# Create Node project
npm init -y

# Install Typescript
yarn add -D typescript tsx @types/node
```

### `Phase 1 - Step 2 - Add Typescript setup`

```
# Initialize TypeScript
npx tsc --init

# Create the Soruce Directory
mkdir src
```

- Replace the generated `tsconfig.json` file with what we have now in the `tsconfig.json` its been updated
- replace the `package.json` script with the below

```json
"scripts": {
  "dev": "tsx watch src/server.ts",
  "build": "tsc",
  "start": "node dist/server.js",
  "type-check": "tsc --noEmit"
}
```

- add `server.ts` to src
- Verification `yarn type-check`

### `Phase 1 - Step 3 - Install Runtime Dependencies`

```sh
yarn add \
fastify \
@fastify/env \
@fastify/sensible
```

### Why Aren't We Installing Everything?

You might see projects installing 20–30 packages on day one.

We'll avoid that.

For example:

```md
- Authentication → install when building Auth
- MongoDB → install in Database phase
- Redis → install in Redis phase
- Swagger → install in API Docs phase
- WebSockets → install in Collaboration phase
- Yjs → install in CRDT phase
```

This keeps dependencies intentional and easier to maintain.

### Verify Installed Packages

```sh
yarn why fastify
```

### Discuss the Architecture Before Writing Code

This is an important design decision.

### ❌ Beginner Structure

This works for small demos but becomes difficult to scale.

```md
src/
├── server.ts
├── routes.ts
├── db.ts
└── app.ts
```

### ❌ Express-Style Structure

This is common but tends to become layer-centric rather than feature-centric. As projects grow, related code gets scattered across many folders.

```md
src/
├── controllers/
├── services/
├── routes/
├── middleware/
└── models/
```

### ✅ Our Structure (Feature-Based + Shared Infrastructure)

We'll gradually build toward:

```md
src/
├── app/
│ ├── plugins/
│ ├── config/
│ ├── hooks/
│ ├── middleware/
│ ├── errors/
│ ├── utils/
│ └── server.ts
│
├── modules/
│ ├── auth/
│ ├── users/
│ ├── documents/
│ ├── collaboration/
│ └── ...
│
├── shared/
│
└── index.ts
```

### Why this approach?

It gives us:

```
- High cohesion (related code stays together)
- Low coupling (modules don't depend heavily on each other)
- Easier onboarding for new developers
- Better scalability as modules grow
- Cleaner testing boundaries
```

- ✅ This aligns well with `Domain-Driven Design (DDD)` and `modular monolith` principles, making it easier to evolve into `microservices` later if needed.

### Fastify Instance Design

There are two common approaches.

Option 1 — Single File

```ts
const app = Fastify();
```

Suitable for small projects, but difficult to test and extend.

Option 2 — Factory Function ✅ (Recommended)

```ts
export function buildServer() {
  return Fastify();
}
```

Why choose this?

Because later we can:

```
- Inject different configurations for testing
- Register plugins conditionally
- Reuse the server in integration tests
- Keep startup logic separate from application construction
```

This pattern is widely used in production Fastify applications.

## `Phase 1 - Step 4 - Create clean modular folder structure`

- We are using a modular monolith structure:

```
src/
├── app/
├── modules/
└── shared/
```

This gives us clean separation between:

```
- application infrastructure
- business modules
- shared reusable utilities
```

### 4.1 Create Folders

```sh
mkdir -p src/app/{config,plugins,hooks,middleware,errors,utils}
mkdir -p src/modules
mkdir -p src/shared/{constants,types,utils}
```

- `src/app` Application-level setup.
- This should not contain business logic.
- Use this for:

```
- Fastify bootstrap
- config loading
- plugins
- global hooks
- error handling
- middleware
- server utilities
```

- `src/modules` Business features. later we will build

```
- modules/auth
- modules/users
- modules/documents
- modules/collaboration
- modules/sharing
- modules/comments
- modules/notifications
```

- Each module will eventually contain its own:

```
routes
service
repository
schemas
types
tests
```

- `src/shared` Reusable cross-module utilities.
- Use this only for things that are genuinely shared.

```
- pagination helpers
- common constants
- shared types
- date utilities
- response helpers
```

### 4.2 — Add .gitkeep Files

- Git does not track empty folders, so add placeholders:

```sh
find src -type d -empty -exec touch {}/.gitkeep \;
```

### 4.3 — Update Temporary server.ts

- For now, keep src/server.ts only as a temporary TypeScript entry point:

```ts
console.log("CollabForge Docs backend initialized");
```

Verify

```sh
yarn type-check
yarn build
# expected dist/server.js (generated)
```

### Production Note

This structure intentionally avoids `controllers/`, `services/`, and `repositories/` at the global level.
We’ll keep those inside each module.

```
src/modules/documents/
├── documents.routes.ts
├── documents.service.ts
├── documents.repository.ts
├── documents.schema.ts
├── documents.types.ts
└── documents.test.ts
```

---

## `Phase 2: MongoDB Connection & Database Layer`

**Goal:** Connect backend to MongoDB professionally.

### Steps

1. Create MongoDB plugin
2. Add database connection lifecycle
3. Add graceful shutdown
4. Create database constants
5. Create collection names
6. Create base repository pattern
7. Add pagination helper
8. Add database health check
9. Add indexes setup strategy
10. Add database integration test
11. Verify MongoDB is working from backend

---

## `Phase 3: User Module & Authentication`

**Goal:** Build secure authentication and user foundation.

### Steps

1. Create user module folder
2. Define user schema/model
3. Add password hashing with Argon2 or bcrypt
4. Create user repository
5. Create auth service
6. Create signup route
7. Create login route
8. Generate access token
9. Generate refresh token
10. Store hashed refresh token
11. Add refresh token rotation
12. Add logout flow
13. Add auth middleware/plugin
14. Add protected `/me` route
15. Add profile update route
16. Add soft delete/deactivate user flow
17. Add validation for auth payloads
18. Add auth unit tests
19. Add auth integration tests
20. Test with Postman/Thunder Client

---

## `Phase 4: RBAC, Permissions Foundation & Audit Logs`

**Goal:** Add security rules before building business modules.

### Steps

1. Define system roles: `user`, `admin`, `super_admin`
2. Define permission constants
3. Create RBAC guard/plugin
4. Create ownership guard utility
5. Create audit log collection
6. Create audit log service
7. Log login/logout/security actions
8. Add admin user management routes
9. Add activate/deactivate user route
10. Add role update route
11. Add authorization tests

---

## `Phase 5: Document Metadata API`

**Goal:** Build REST APIs before real-time editing.

### Steps

1. Create document module
2. Define document schema
3. Design MongoDB document model
4. Add owner field
5. Add document status: `active`, `archived`, `deleted`
6. Add visibility: `private`, `shared`, `public_link`
7. Create document repository
8. Create document service
9. Create document routes
10. Add create document API
11. Add list my documents API
12. Add get document by ID API
13. Add update title API
14. Add archive document API
15. Add restore document API
16. Add soft delete document API
17. Add pagination/search/filter/sort
18. Add ownership checks
19. Add document audit logs
20. Add document API tests

---

## `Phase 6: Sharing & Document Access Control`

**Goal:** Build Google Docs-like permission model.

### Steps

1. Define document roles: `owner`, `editor`, `viewer`
2. Add collaborator collection
3. Add invite collection
4. Create invite/share API
5. Add accept invite API
6. Add reject invite API
7. Add revoke access API
8. Add collaborator role update API
9. Restrict REST APIs by permission
10. Add document permission guard
11. Add audit logs for sharing actions
12. Add authorization tests

---

## `Phase 7: WebSocket Foundation`

**Goal:** Add WebSocket support without Yjs first.

### Steps

1. Install WebSocket dependency
2. Decide Fastify WebSocket plugin vs standalone `ws`
3. Create WebSocket module
4. Add connection endpoint
5. Add WebSocket authentication
6. Add document authorization check
7. Add room concept using `documentId`
8. Add basic join/leave events
9. Add connection logging
10. Add ping/pong heartbeat
11. Add read-only viewer connection mode
12. Test WebSocket connection manually
13. Add WebSocket integration test

---

## `Phase 8: Yjs Real-Time Collaboration`

**Goal:** Sync document content between multiple users.

### Steps

1. Install Yjs dependencies
2. Understand CRDT document structure
3. Create Yjs document manager
4. Create document room map
5. Bind Yjs document to WebSocket room
6. Handle Yjs sync messages
7. Restrict Yjs updates for viewers
8. Open same document in two clients
9. Verify real-time text sync
10. Add cleanup when users disconnect
11. Add logging for sync lifecycle
12. Add basic Yjs sync test

---

## `Phase 9: Yjs Persistence in MongoDB`

**Goal:** Save collaborative edits permanently.

### Steps

1. Decide persistence strategy
2. Store Yjs updates as binary updates
3. Create `document_updates` collection
4. Add update append logic
5. Restore Yjs document from saved updates
6. Add update ordering
7. Add failure-handling logic
8. Test server restart restore
9. Add persistence integration tests

---

## `Phase 10: Snapshots & Version History`

**Goal:** Add document recovery and history.

### Steps

1. Create `document_snapshots` collection
2. Create `document_versions` collection
3. Add snapshot creation strategy
4. Compact old updates into snapshots
5. Add version metadata
6. Add list versions API
7. Add restore version API
8. Add background job for snapshotting
9. Add version restore tests

---

## `Phase 11: Cursor Presence & Awareness`

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

## `Phase 12: Offline Editing & Sync`

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

## `Phase 13: Comments & Notifications`

**Goal:** Add collaboration features beyond editing.

### Steps

1. Create comments collection
2. Add comment API
3. Add reply API
4. Add resolve comment API
5. Add reopen comment API
6. Add mention user logic
7. Create notifications collection
8. Add in-app notifications
9. Add background email job
10. Add comment permission checks
11. Add comment tests

---

## `Phase 14: API Documentation`

**Goal:** Make APIs usable and professional.

### Steps

1. Add Swagger/OpenAPI setup
2. Document auth APIs
3. Document document APIs
4. Document sharing APIs
5. Document comments APIs
6. Add request/response schemas
7. Add error response examples
8. Add authentication support in Swagger
9. Verify API docs locally

---

## `Phase 15: Testing Strategy`

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

## `Phase 16: Security Hardening`

**Goal:** Protect the backend properly.

### Steps

1. Add Helmet security headers
2. Configure CORS correctly
3. Add rate limiting
4. Add auth-specific rate limits
5. Add request payload limits
6. Add JWT expiration
7. Add refresh token reuse detection
8. Sanitize user input
9. Add document access checks everywhere
10. Prevent WebSocket abuse
11. Add production error handling
12. Add security tests

---

## `Phase 17: Observability & Logging`

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
9. Add Redis health check
10. Add metrics discussion
11. Add production monitoring plan

---

## `Phase 18: Redis & Background Jobs`

**Goal:** Prepare for scaling and async workflows.

### Steps

1. Add Redis connection
2. Add Redis health check
3. Add BullMQ setup
4. Add email job queue
5. Add snapshot job queue
6. Add cleanup job queue
7. Add failed job handling
8. Add retry strategy
9. Add job monitoring approach
10. Add queue tests

---

## `Phase 19: Multi-Instance Scaling`

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

## `Phase 20: Deployment`

**Goal:** Deploy backend professionally.

### Steps

1. Add Dockerfile
2. Add `.dockerignore`
3. Add production environment config
4. Add build script
5. Add start script
6. Add MongoDB Atlas option
7. Add Redis production option
8. Add deployment architecture
9. Add CI pipeline
10. Add test step in CI
11. Add build step in CI
12. Add deploy step
13. Add smoke test after deployment
14. Add backup strategy

---

```

```
