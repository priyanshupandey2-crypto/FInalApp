# Backend API Module - Person 2

## Responsibility
Express routes, API controllers, request handling, response formatting

## Files in This Module
```
backend-api/
├── package.json
├── README.md
├── routes/
│   ├── chat.js
│   ├── documents.js
│   └── health.js
└── controllers/
    ├── chatController.js
    ├── documentController.js
    └── healthController.js
```

## Git Workflow - Person 2

### Initial Setup
```bash
# Clone repository
cd "c:/Users/priyanshu.pandey2/New folder"
git clone <repository-url> rag-assistant
cd rag-assistant

# Create your feature branch
git checkout -b feature/backend-api/initial-setup

# Navigate to your module
cd team/backend-api
npm install
```

### Daily Workflow

**Step 1: Sync with main**
```bash
git checkout main
git pull origin main
```

**Step 2: Create your feature branch**
```bash
git checkout -b feature/backend-api/<feature-name>
# Example: feature/backend-api/add-chat-endpoint
```

**Step 3: Implement your endpoint**
```bash
# Edit files in team/backend-api/routes/
# Edit files in team/backend-api/controllers/
```

**Step 4: Commit with meaningful messages**
```bash
# Commit route definition
git add team/backend-api/routes/chat.js
git commit -m "feat(backend-api): add chat endpoint route

- Create POST /api/chat/ask route
- Add request validation
- Route to chatController

This endpoint will receive questions and return AI answers"

# Commit controller logic
git add team/backend-api/controllers/chatController.js
git commit -m "feat(backend-api): implement chat controller

- Extract question from request
- Validate input parameters
- Call RAG service (from Person 3)
- Format response with sources

Returns: {answer, sources, confidence}"
```

**Step 5: Push your changes**
```bash
git push origin feature/backend-api/<feature-name>
```

**Step 6: Create Pull Request**
```bash
# On GitHub:
# 1. Click "New Pull Request"
# 2. Base: main, Compare: feature/backend-api/<feature-name>
# 3. Title: "feat(backend-api): <description>"
# 4. Describe endpoint behavior
# 5. Request review
# 6. Merge when approved
```

## Complete Example: Adding Document Upload Endpoint

```bash
# 1. Get latest code
git checkout main
git pull origin main

# 2. Create feature branch
git checkout -b feature/backend-api/document-upload

# 3. Create route file
# Edit: team/backend-api/routes/documents.js
cat > team/backend-api/routes/documents.js << 'EOF'
import express from 'express';
import { uploadDocument } from '../controllers/documentController.js';

const router = express.Router();

// POST /api/documents/upload
router.post('/upload', async (req, res) => {
  try {
    const result = await uploadDocument(req);
    res.json(result);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

export default router;
EOF

# 4. Create controller file
# Edit: team/backend-api/controllers/documentController.js
cat > team/backend-api/controllers/documentController.js << 'EOF'
// Controller logic here
export async function uploadDocument(req) {
  // Validate file
  // Call document service (from Person 4)
  // Return result
}
EOF

# 5. Stage both files
git add team/backend-api/routes/documents.js
git add team/backend-api/controllers/documentController.js

# 6. Commit
git commit -m "feat(backend-api): add document upload endpoint

Routes:
- POST /api/documents/upload
  - Accept multipart form data
  - Validate file type (PDF, TXT, MD)
  - Return {documentId, chunksCreated}

Controllers:
- Validate request
- Call documentService from Person 4
- Handle errors gracefully"

# 7. Push
git push origin feature/backend-api/document-upload

# 8. Create PR and get approval from team
```

## API Endpoints You'll Create

```
POST /api/chat/ask
- Request: { query, searchType, topK, filters }
- Response: { answer, sources, confidence }

POST /api/documents/upload
- Request: multipart/form-data with file
- Response: { documentId, chunksCreated }

GET /api/documents
- Response: [ { id, name, department, ... } ]

DELETE /api/documents/:id
- Response: { success }

POST /api/chat/feedback
- Request: { query, feedback }
- Response: { success }
```

## Integration with Other Modules

You will:
- **Call RAG Services** (Person 3) for `answerQuestion()`
- **Call Document Processing** (Person 4) for `uploadDocument()`
- **Use Core Utils** (Person 5) for error handling

Example:
```javascript
// team/backend-api/controllers/chatController.js
import { answerQuestion } from '../../rag-services/index.js';

export async function askQuestion(req, res) {
  const { query } = req.body;
  
  // Call RAG service from Person 3
  const answer = await answerQuestion(query);
  
  res.json(answer);
}
```

## Testing Your Endpoint

```bash
# Test with curl
curl -X POST http://localhost:5001/api/chat/ask \
  -H "Content-Type: application/json" \
  -d '{"query":"How do I fix CrashLoopBackOff?"}'

# Response:
# {
#   "answer": "...",
#   "sources": [...],
#   "confidence": 0.92
# }
```

## Before Committing

- ✅ Endpoint works when tested
- ✅ Proper error handling
- ✅ Request validation
- ✅ Response format is correct
- ✅ Changes only in team/backend-api/

## Useful Git Commands

```bash
# See your endpoint changes
git diff team/backend-api/

# View commit history for your module
git log --oneline team/backend-api/

# Undo changes to a file
git checkout team/backend-api/routes/chat.js

# View status
git status

# Show staged changes
git diff --cached

# Undo last commit (if not pushed)
git reset --soft HEAD~1
git reset team/backend-api/  # unstage all
```

## Handling Dependencies

When Person 3, 4, or 5 finishes their work:

```bash
# Update main
git checkout main
git pull origin main

# Now your code can import from other modules
import { answerQuestion } from '../../rag-services/services/ragService.js';
```

## Error Handling

All endpoints should return proper HTTP status codes:

```javascript
// Success
res.status(200).json({ answer: "..." });

// Bad request
res.status(400).json({ error: "Query is required" });

// Server error
res.status(500).json({ error: "Internal server error" });

// Not found
res.status(404).json({ error: "Document not found" });
```

## Response Format

Standardize all responses:

```javascript
// Success response
{
  "data": { ... },
  "timestamp": "2026-06-03T10:00:00Z"
}

// Error response
{
  "error": "Error message",
  "code": "ERROR_CODE",
  "timestamp": "2026-06-03T10:00:00Z"
}
```

---

**Need Help?**
- Check TEAM_STRUCTURE.md for overall process
- Coordinate with Person 3 (RAG Services) and Person 4 (Document Processing)
- Ask in team chat for blockers
