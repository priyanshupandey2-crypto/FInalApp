# RAG Services Module - Person 3

## Responsibility
RAG logic, answer generation, retrieval orchestration, LLM integration

## Files in This Module
```
rag-services/
├── package.json
├── README.md
├── services/
│   ├── ragService.js          (Main RAG orchestration)
│   ├── ragServiceDemo.js      (Demo mode without API)
│   └── retrievalService.js    (Retrieval strategies)
└── index.js                    (Module exports)
```

## Git Workflow - Person 3

### Initial Setup
```bash
# Clone repository
cd "c:/Users/priyanshu.pandey2/New folder"
git clone <repository-url> rag-assistant
cd rag-assistant

# Create your feature branch
git checkout -b feature/rag-services/initial-setup

# Navigate to your module
cd team/rag-services
npm install
```

### Daily Workflow

**Step 1: Stay in sync**
```bash
git checkout main
git pull origin main
```

**Step 2: Create feature branch**
```bash
git checkout -b feature/rag-services/<feature-name>
# Example: feature/rag-services/improve-retrieval
```

**Step 3: Implement RAG logic**
```bash
# Edit files in team/rag-services/services/
```

**Step 4: Make meaningful commits**
```bash
# Commit retrieval improvement
git add team/rag-services/services/retrievalService.js
git commit -m "feat(rag-services): implement hybrid search

Algorithm:
- Combine vector search (50% weight)
- Combine keyword search (50% weight)
- Deduplicate results
- Return top-K scored results

Performance: ~150ms average latency"

# Commit RAG orchestration
git add team/rag-services/services/ragService.js
git commit -m "feat(rag-services): add RAG orchestration

- Retrieve relevant chunks from knowledge base
- Generate answer using LLM
- Include source attribution
- Calculate confidence score
- Implement caching

Calls: retrievalService + LLM API"
```

**Step 5: Push your work**
```bash
git push origin feature/rag-services/<feature-name>
```

**Step 6: Create Pull Request**
```bash
# On GitHub:
# 1. New Pull Request
# 2. Base: main, Compare: feature/rag-services/<feature-name>
# 3. Title: "feat(rag-services): <description>"
# 4. Explain algorithm and performance
# 5. Request review
# 6. Merge when approved
```

## Complete Example: Adding Reranking

```bash
# 1. Sync with main
git checkout main
git pull origin main

# 2. Create feature branch
git checkout -b feature/rag-services/add-reranking

# 3. Implement reranking in retrievalService
# Edit: team/rag-services/services/retrievalService.js
cat > team/rag-services/services/retrievalService.js << 'EOF'
// Existing retrieval logic

export async function rerankedSearch(queryText, topK = 5, filters = {}) {
  // Get initial candidates
  const candidates = await hybridSearch(queryText, topK * 3, filters);
  
  // Score each candidate with LLM
  const rescored = await Promise.all(
    candidates.map(async (chunk) => {
      const relevanceScore = await scoreRelevance(queryText, chunk);
      return { ...chunk, relevanceScore };
    })
  );
  
  // Return top-K after reranking
  return rescored
    .sort((a, b) => b.relevanceScore - a.relevanceScore)
    .slice(0, topK);
}
EOF

# 4. Stage changes
git add team/rag-services/services/retrievalService.js

# 5. Commit
git commit -m "feat(rag-services): add result reranking

Implementation:
- Use initial hybrid search to get candidates
- Score each with LLM cross-encoder
- Sort by relevance score
- Return top results

Improves accuracy by ~15%
Adds ~200ms latency (can be cached)"

# 6. Push
git push origin feature/rag-services/add-reranking

# 7. Create PR and review
```

## Core Functions You'll Implement

### answerQuestion(query, options)
```javascript
// Input: query string, options
// Output: { answer, sources, confidence, timestamp }

// Process:
// 1. Check cache
// 2. Retrieve relevant chunks
// 3. Generate answer from LLM
// 4. Format with sources
// 5. Cache result
```

### vectorSearch(query, topK, filters)
```javascript
// Semantic similarity search
// Input: query string
// Output: [{ chunk, similarity, source }]
```

### keywordSearch(query, topK, filters)
```javascript
// Full-text search
// Input: query string
// Output: [{ chunk, rank, source }]
```

### hybridSearch(query, topK, filters)
```javascript
// Combined vector + keyword
// Input: query string
// Output: [{ chunk, hybridScore, source }]
```

## Integration with Other Modules

You will:
- **Receive** queries from Backend API (Person 2)
- **Call** Document Processing (Person 4) to retrieve chunks
- **Use** Core Utils (Person 5) for embeddings and storage
- **Call** LLM API for answer generation

```javascript
// team/rag-services/services/ragService.js
import { retrieveChunks } from '../../document-processing/index.js';
import { generateEmbedding } from '../../core-utils/index.js';
import { OpenAI } from 'openai';

export async function answerQuestion(query) {
  // 1. Generate query embedding (from Person 5)
  const embedding = await generateEmbedding(query);
  
  // 2. Retrieve chunks (from Person 4)
  const chunks = await retrieveChunks(embedding, topK);
  
  // 3. Call LLM
  const llm = new OpenAI();
  const answer = await llm.chat.completions.create({...});
  
  // 4. Format and return
  return { answer, sources: chunks, confidence };
}
```

## LLM Integration

Use OpenAI API or demo mode:

```javascript
import { OpenAI } from 'openai';

const openai = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY
});

export async function generateAnswer(context, query) {
  const response = await openai.chat.completions.create({
    model: 'gpt-3.5-turbo',
    messages: [
      { role: 'system', content: 'You are a helpful assistant...' },
      { role: 'user', content: `Context:\n${context}\n\nQuestion: ${query}` }
    ],
    temperature: 0.3,
    max_tokens: 1000
  });
  
  return response.choices[0].message.content;
}
```

## Testing Your Service

```bash
# Test retrieval
const results = await hybridSearch("CrashLoopBackOff");
console.log(results);

# Test answer generation
const answer = await answerQuestion("How do I fix CrashLoopBackOff?");
console.log(answer);
```

## Before Committing

- ✅ Retrieval works and returns relevant chunks
- ✅ Answer generation works without errors
- ✅ Sources are properly attributed
- ✅ Caching is implemented
- ✅ Changes only in team/rag-services/

## Useful Git Commands

```bash
# View your changes
git diff team/rag-services/

# View commits in your module
git log --oneline team/rag-services/

# Undo changes to a file
git checkout team/rag-services/services/ragService.js

# View status
git status

# Show what will be committed
git diff --cached
```

## Performance Optimization

Track these metrics:

```javascript
// Measure retrieval time
console.time('retrieval');
const chunks = await hybridSearch(query, topK);
console.timeEnd('retrieval');

// Measure LLM latency
console.time('llm');
const answer = await generateAnswer(context, query);
console.timeEnd('llm');

// Total time
console.time('total');
const result = await answerQuestion(query);
console.timeEnd('total');
```

**Target times:**
- Retrieval: < 200ms
- LLM generation: < 5000ms
- Total: < 10000ms

## Caching Strategy

```javascript
// Implement query result caching
const cache = new Map();

export function getCachedAnswer(query) {
  return cache.get(query);
}

export function cacheAnswer(query, answer) {
  cache.set(query, answer);
  // Expire after 1 hour
  setTimeout(() => cache.delete(query), 3600000);
}
```

## Hallucination Prevention

Your service should:

```javascript
// 1. Check similarity threshold
if (topChunk.similarity < 0.3) {
  return { answer: "Cannot find relevant information" };
}

// 2. Include confidence score
// 3. Always cite sources
// 4. Use conservative temperature (0.3)
```

---

**Need Help?**
- Check TEAM_STRUCTURE.md for overall process
- Coordinate with Person 4 (Document Processing)
- Coordinate with Person 5 (Core Utils)
- Ask in team chat for blockers
