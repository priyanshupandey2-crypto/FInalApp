# Core Utils Module - Person 5

## Responsibility
Embeddings, storage, database, utilities, configuration, error handling

## Files in This Module
```
core-utils/
├── package.json
├── README.md
├── embeddings/
│   ├── embeddings.js          (OpenAI embeddings)
│   └── embeddingsMock.js      (Demo mode embeddings)
├── storage/
│   ├── storage.js             (In-memory JSON storage)
│   └── database.js            (PostgreSQL connection)
├── utils/
│   └── errors.js              (Error handling)
└── index.js                    (Module exports)
```

## Git Workflow - Person 5

### Initial Setup
```bash
# Clone repository
cd "c:/Users/priyanshu.pandey2/New folder"
git clone <repository-url> rag-assistant
cd rag-assistant

# Create your feature branch
git checkout -b feature/core-utils/initial-setup

# Navigate to your module
cd team/core-utils
npm install
```

### Daily Workflow

**Step 1: Keep in sync**
```bash
git checkout main
git pull origin main
```

**Step 2: Create feature branch**
```bash
git checkout -b feature/core-utils/<feature-name>
# Example: feature/core-utils/add-caching
```

**Step 3: Implement utilities**
```bash
# Edit files in team/core-utils/embeddings/
# Edit files in team/core-utils/storage/
```

**Step 4: Make granular commits**
```bash
# Commit embedding optimization
git add team/core-utils/embeddings/embeddings.js
git commit -m "feat(core-utils): optimize embedding caching

Implementation:
- In-memory cache with LRU eviction
- Cache hit ratio tracking
- Configurable cache size (default 10000)

Performance improvement:
- Cache hit: <1ms (vs 2000ms without cache)
- ~70% hit rate observed
- Reduces API costs by 70%"

# Commit storage layer
git add team/core-utils/storage/storage.js
git commit -m "feat(core-utils): implement persistent JSON storage

Features:
- Save documents to data/documents.json
- Save chunks to data/chunks.json
- Save embeddings to data/embeddings.json
- Auto-save on changes
- Load on startup

Used by: documentService, ragService"
```

**Step 5: Push your work**
```bash
git push origin feature/core-utils/<feature-name>
```

**Step 6: Create Pull Request**
```bash
# On GitHub:
# 1. New Pull Request
# 2. Base: main, Compare: feature/core-utils/<feature-name>
# 3. Title: "feat(core-utils): <description>"
# 4. Explain what other modules will use this
# 5. Request review
# 6. Merge when approved
```

## Complete Example: Adding Vector Caching

```bash
# 1. Sync with main
git checkout main
git pull origin main

# 2. Create feature branch
git checkout -b feature/core-utils/embedding-caching

# 3. Implement caching
# Edit: team/core-utils/embeddings/embeddings.js
cat > team/core-utils/embeddings/embeddings.js << 'EOF'
import { OpenAI } from 'openai';
import crypto from 'crypto';

const openai = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY
});

// LRU Cache implementation
class EmbeddingCache {
  constructor(maxSize = 10000) {
    this.cache = new Map();
    this.maxSize = maxSize;
    this.hits = 0;
    this.misses = 0;
  }

  get(text) {
    const hash = crypto.createHash('sha256').update(text).digest('hex');
    if (this.cache.has(hash)) {
      this.hits++;
      return this.cache.get(hash);
    }
    this.misses++;
    return null;
  }

  set(text, embedding) {
    const hash = crypto.createHash('sha256').update(text).digest('hex');
    
    // LRU eviction
    if (this.cache.size >= this.maxSize) {
      const firstKey = this.cache.keys().next().value;
      this.cache.delete(firstKey);
    }
    
    this.cache.set(hash, embedding);
  }

  getStats() {
    const total = this.hits + this.misses;
    return {
      hitRate: total > 0 ? (this.hits / total * 100).toFixed(2) + '%' : 'N/A',
      hits: this.hits,
      misses: this.misses,
      size: this.cache.size
    };
  }
}

const cache = new EmbeddingCache();

export async function generateEmbedding(text) {
  // Check cache first
  const cached = cache.get(text);
  if (cached) return cached;
  
  // Generate if not cached
  const response = await openai.embeddings.create({
    model: 'text-embedding-3-small',
    input: text
  });
  
  const embedding = response.data[0].embedding;
  cache.set(text, embedding);
  return embedding;
}

export function getEmbeddingStats() {
  return cache.getStats();
}
EOF

# 4. Stage changes
git add team/core-utils/embeddings/embeddings.js

# 5. Commit
git commit -m "feat(core-utils): add embedding LRU cache

Features:
- In-memory LRU cache with configurable size
- Cache statistics tracking
- Automatic eviction when full
- Zero latency on cache hits

Performance:
- Cache hit: <1ms vs 2000ms without
- Expected hit rate: 60-70%
- Reduces API calls and costs by 70%

Used by: RAG Services (Person 3)
         Document Processing (Person 4)"

# 6. Push
git push origin feature/core-utils/embedding-caching

# 7. Create PR
```

## Core Functions You'll Implement

### Embeddings

```javascript
// Generate embedding for text
export async function generateEmbedding(text) {
  // Check cache
  // Call OpenAI API
  // Cache result
  // Return 1536-dimensional vector
}

// Batch generate embeddings
export async function generateEmbeddings(texts) {
  // Return array of embeddings
}

// Calculate similarity between vectors
export function cosineSimilarity(vector1, vector2) {
  // Return similarity score (0-1)
}
```

### Storage

```javascript
// Load data on startup
export async function loadData() {
  // Load from JSON files or database
  // Populate in-memory storage
}

// Save data periodically
export async function saveData() {
  // Save to JSON files or database
  // Handle errors gracefully
}

// Store chunk
export async function storeChunk(chunk) {
  // Add to storage
}

// Retrieve chunk
export async function getChunk(id) {
  // Get from storage
}

// Store embedding
export async function storeEmbedding(chunkId, embedding) {
  // Store vector embedding
}

// Retrieve embedding
export async function getEmbedding(chunkId) {
  // Get vector embedding
}
```

### Error Handling

```javascript
// Custom error class
export class RAGError extends Error {
  constructor(message, code) {
    super(message);
    this.code = code;
  }
}

// Error handler
export function handleError(error) {
  if (error instanceof RAGError) {
    // Log and format error
  }
  return { error: error.message };
}
```

## Integration with Other Modules

You provide foundation for all modules:

```javascript
// Document Processing (Person 4) uses:
import { storeChunk, storeEmbedding } from '../../core-utils/index.js';

// RAG Services (Person 3) uses:
import { generateEmbedding, cosineSimilarity } from '../../core-utils/index.js';

// Backend API (Person 2) uses:
import { handleError } from '../../core-utils/index.js';
```

## Storage Implementation

Choose one approach:

### Option 1: JSON Files (Simple)
```javascript
// team/core-utils/storage/storage.js
import fs from 'fs/promises';
import path from 'path';

const DATA_DIR = './data';
const storage = {
  documents: [],
  chunks: [],
  embeddings: {}
};

export async function loadData() {
  try {
    const docs = await fs.readFile(path.join(DATA_DIR, 'documents.json'), 'utf-8');
    storage.documents = JSON.parse(docs);
  } catch (e) {
    // File doesn't exist yet
  }
}

export async function saveData() {
  await fs.writeFile(
    path.join(DATA_DIR, 'documents.json'),
    JSON.stringify(storage.documents, null, 2)
  );
}
```

### Option 2: PostgreSQL (Production)
```javascript
// team/core-utils/storage/database.js
import pg from 'pg';

const pool = new pg.Pool({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME
});

export async function storeChunk(chunk) {
  const query = `INSERT INTO chunks (id, content, metadata)
                 VALUES ($1, $2, $3)`;
  await pool.query(query, [chunk.id, chunk.content, JSON.stringify(chunk.metadata)]);
}
```

## Embedding Implementation

### Option 1: OpenAI (Best)
```javascript
import { OpenAI } from 'openai';

const openai = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY
});

export async function generateEmbedding(text) {
  const response = await openai.embeddings.create({
    model: 'text-embedding-3-small',
    input: text
  });
  return response.data[0].embedding;
}
```

### Option 2: Mock (Demo)
```javascript
import crypto from 'crypto';

export function generateMockEmbedding(text) {
  const hash = crypto.createHash('sha256').update(text).digest('hex');
  const embedding = [];
  
  for (let i = 0; i < 1536; i++) {
    const charCode = hash.charCodeAt(i % hash.length) || 65;
    embedding.push((charCode + i) % 256 / 128 - 1);
  }
  
  // Normalize
  const magnitude = Math.sqrt(embedding.reduce((sum, v) => sum + v * v, 0));
  return embedding.map(v => v / (magnitude || 1));
}
```

## Configuration Management

```javascript
// team/core-utils/config.js
export const config = {
  // Embeddings
  embedding_model: process.env.EMBEDDING_MODEL || 'text-embedding-3-small',
  embedding_dimension: 1536,
  
  // Chunking
  max_chunk_size: parseInt(process.env.MAX_CHUNK_SIZE) || 1000,
  chunk_overlap: parseInt(process.env.CHUNK_OVERLAP) || 200,
  
  // Search
  similarity_threshold: parseFloat(process.env.SIMILARITY_THRESHOLD) || 0.3,
  top_k_results: parseInt(process.env.TOP_K_RESULTS) || 5,
  
  // Cache
  cache_enabled: process.env.CACHE_ENABLED !== 'false',
  cache_ttl_minutes: 60,
  
  // Storage
  storage_type: process.env.STORAGE_TYPE || 'json', // 'json' or 'postgres'
  database_url: process.env.DATABASE_URL
};
```

## Testing Your Utils

```bash
# Test embedding generation
const embedding = await generateEmbedding("test");
console.log(embedding.length); // Should be 1536

# Test similarity calculation
const sim = cosineSimilarity(vec1, vec2);
console.log(sim); // Should be 0-1

# Test storage
await loadData();
await storeChunk(chunk);
await saveData();
```

## Before Committing

- ✅ Functions work without errors
- ✅ Integration points are clear
- ✅ Error handling is robust
- ✅ Performance is acceptable
- ✅ Changes only in team/core-utils/

## Useful Git Commands

```bash
# View your changes
git diff team/core-utils/

# View commits in your module
git log --oneline team/core-utils/

# Undo changes
git checkout team/core-utils/embeddings/embeddings.js

# View status
git status

# View staged changes
git diff --cached
```

## Module Exports

Your `index.js` should export everything:

```javascript
// team/core-utils/index.js
export { generateEmbedding, generateEmbeddings, cosineSimilarity } from './embeddings/embeddings.js';
export { loadData, saveData, storeChunk, getChunk } from './storage/storage.js';
export { RAGError, handleError } from './utils/errors.js';
```

This allows clean imports:
```javascript
import { generateEmbedding, cosineSimilarity } from '../../core-utils/index.js';
```

## Performance Targets

Track these metrics:

```javascript
// Embedding generation
- With cache hit: <1ms
- Without cache (API): 1000-2000ms
- Target cache hit rate: 60-70%

// Similarity calculation
- Time: <1ms
- Accuracy: Cosine similarity algorithm

// Storage operations
- Save: <100ms
- Load: <100ms
```

## Security Considerations

- ✅ Validate all inputs
- ✅ Sanitize embeddings before storage
- ✅ Protect API keys with environment variables
- ✅ Handle errors without exposing sensitive info
- ✅ Implement rate limiting on API calls

---

**Need Help?**
- Check TEAM_STRUCTURE.md for overall process
- Review provided to all other modules (Person 1-4)
- Ask in team chat for blockers
- Most important module - coordinate carefully with others
