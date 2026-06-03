# Document Processing Module - Person 4

## Responsibility
Document ingestion, chunking strategies, text extraction, metadata handling

## Files in This Module
```
document-processing/
├── package.json
├── README.md
├── services/
│   ├── documentService.js     (Upload & management)
│   └── documentServiceSimple.js
├── utils/
│   ├── chunkingStrategies.js  (Multiple chunking approaches)
│   └── pdfParser.js           (Text extraction)
└── index.js                    (Module exports)
```

## Git Workflow - Person 4

### Initial Setup
```bash
# Clone repository
cd "c:/Users/priyanshu.pandey2/New folder"
git clone <repository-url> rag-assistant
cd rag-assistant

# Create your feature branch
git checkout -b feature/document-processing/initial-setup

# Navigate to your module
cd team/document-processing
npm install
```

### Daily Workflow

**Step 1: Sync with main**
```bash
git checkout main
git pull origin main
```

**Step 2: Create feature branch**
```bash
git checkout -b feature/document-processing/<feature-name>
# Example: feature/document-processing/improve-chunking
```

**Step 3: Work on document processing**
```bash
# Edit files in team/document-processing/services/
# Edit files in team/document-processing/utils/
```

**Step 4: Make focused commits**
```bash
# Commit chunking improvement
git add team/document-processing/utils/chunkingStrategies.js
git commit -m "feat(document-processing): add semantic chunking

Algorithm:
- Split on sentence boundaries
- Preserve semantic coherence
- Handle edge cases (abbreviations, etc.)
- Maintain position tracking

Results in higher quality chunks for retrieval
~20% improvement in Q&A accuracy"

# Commit document service improvement
git add team/document-processing/services/documentService.js
git commit -m "feat(document-processing): implement document upload

- Accept PDF, TXT, Markdown files
- Extract text from documents
- Apply chunking strategy
- Store chunks with metadata
- Generate chunk embeddings

Returns: {documentId, chunksCreated, chunkingStrategy}"
```

**Step 5: Push changes**
```bash
git push origin feature/document-processing/<feature-name>
```

**Step 6: Create Pull Request**
```bash
# On GitHub:
# 1. New Pull Request
# 2. Base: main, Compare: feature/document-processing/<feature-name>
# 3. Title: "feat(document-processing): <description>"
# 4. Explain algorithm and performance
# 5. Request review
# 6. Merge when approved
```

## Complete Example: Adding Paragraph-Aware Chunking

```bash
# 1. Sync with main
git checkout main
git pull origin main

# 2. Create feature branch
git checkout -b feature/document-processing/paragraph-chunking

# 3. Add chunking strategy
# Edit: team/document-processing/utils/chunkingStrategies.js
cat > team/document-processing/utils/chunkingStrategies.js << 'EOF'
export function paragraphAwareChunking(text, maxSize = 1000) {
  const paragraphs = text.split(/\n\n+/);
  const chunks = [];
  let currentChunk = '';
  let startPosition = 0;

  paragraphs.forEach((paragraph) => {
    if ((currentChunk + '\n\n' + paragraph).length > maxSize && currentChunk.length > 0) {
      chunks.push({
        content: currentChunk.trim(),
        startPosition,
        endPosition: startPosition + currentChunk.length,
        strategy: 'paragraph-aware'
      });
      startPosition += currentChunk.length;
      currentChunk = paragraph;
    } else {
      currentChunk += (currentChunk ? '\n\n' : '') + paragraph;
    }
  });

  if (currentChunk.trim()) {
    chunks.push({
      content: currentChunk.trim(),
      startPosition,
      endPosition: startPosition + currentChunk.length,
      strategy: 'paragraph-aware'
    });
  }

  return chunks;
}
EOF

# 4. Update document service to use it
# Edit: team/document-processing/services/documentService.js
# Add to chunkingStrategy selection:
# } else if (chunkingStrategy === 'paragraph') {
#   chunks = paragraphAwareChunking(text, CHUNK_SIZE);

# 5. Stage changes
git add team/document-processing/utils/chunkingStrategies.js
git add team/document-processing/services/documentService.js

# 6. Commit
git commit -m "feat(document-processing): add paragraph-aware chunking

Algorithm:
- Respect document paragraph structure
- Split on paragraph boundaries
- Group to reach target size
- Preserve contextual flow

Benefits:
- Better for structured documents
- Maintains section relationships
- Improves semantic coherence

Tested on: technical docs, user guides, FAQs"

# 7. Push
git push origin feature/document-processing/paragraph-chunking

# 8. Create PR
```

## Core Functions You'll Implement

### ingestDocument(file, metadata)
```javascript
// Upload and process a document
// Input: file object, metadata {department, category, version}
// Output: { documentId, chunksCreated, chunkingStrategy }

// Process:
// 1. Save file to disk/storage
// 2. Extract text from file
// 3. Apply chunking strategy
// 4. Store chunks with metadata
// 5. Generate embeddings for each chunk
```

### getDocuments(filters)
```javascript
// List all documents with optional filters
// Input: filters {department, category}
// Output: [{ id, name, department, category, version }]
```

### deleteDocument(id)
```javascript
// Remove document and all its chunks
// Input: document ID
// Output: success boolean
```

### getDocumentChunks(documentId)
```javascript
// Get all chunks from a document
// Input: document ID
// Output: [{ id, content, chunkIndex, metadata }]
```

## Chunking Strategies

Implement and compare:

### 1. Semantic Chunking (Quality)
```javascript
export function semanticChunking(text, targetChunkSize = 1000) {
  const sentences = text.match(/[^.!?]+[.!?]+/g) || [text];
  const chunks = [];
  let currentChunk = '';
  let startPosition = 0;

  sentences.forEach((sentence) => {
    if ((currentChunk + sentence).length > targetChunkSize && currentChunk.length > 0) {
      chunks.push({
        content: currentChunk.trim(),
        startPosition,
        endPosition: startPosition + currentChunk.length,
        strategy: 'semantic'
      });
      startPosition += currentChunk.length;
      currentChunk = sentence;
    } else {
      currentChunk += sentence;
    }
  });

  return chunks;
}
```

### 2. Fixed-Size Chunking (Speed)
```javascript
export function fixedSizeChunking(text, chunkSize = 1000, overlap = 200) {
  const chunks = [];
  let position = 0;

  while (position < text.length) {
    const end = Math.min(position + chunkSize, text.length);
    const chunk = text.substring(position, end);
    
    chunks.push({
      content: chunk.trim(),
      startPosition: position,
      endPosition: end,
      strategy: 'fixed-size'
    });

    position += chunkSize - overlap;
  }

  return chunks;
}
```

### 3. Paragraph-Aware (Structure)
```javascript
// Implemented above
```

## File Format Support

You'll handle:

### PDF Files
```javascript
import pdf from 'pdf-parse';

export async function extractTextFromPDF(filePath) {
  const data = await pdf(fs.readFileSync(filePath));
  return { text: data.text, pages: data.numpages };
}
```

### Text Files
```javascript
export async function extractTextFromTXT(filePath) {
  const text = await fs.readFile(filePath, 'utf-8');
  return { text, pages: 1 };
}
```

### Markdown Files
```javascript
export async function extractTextFromMarkdown(filePath) {
  const text = await fs.readFile(filePath, 'utf-8');
  return { text, pages: 1 };
}
```

## Integration with Other Modules

You will:
- **Call** Core Utils (Person 5) to store chunks and embeddings
- **Be called by** Backend API (Person 2) for upload endpoint
- **Be called by** RAG Services (Person 3) to retrieve chunks

```javascript
// team/document-processing/services/documentService.js
import { generateEmbedding } from '../../core-utils/index.js';
import { storeChunk, storeEmbedding } from '../../core-utils/index.js';

export async function ingestDocument(file, metadata) {
  // Extract text
  const { text } = await extractDocumentText(filePath, fileType);
  
  // Chunk text
  const chunks = semanticChunking(text);
  
  // Store each chunk
  for (const chunk of chunks) {
    // Generate embedding (from Person 5)
    const embedding = await generateEmbedding(chunk.content);
    
    // Store chunk (from Person 5)
    await storeChunk(chunk);
    await storeEmbedding(chunkId, embedding);
  }
}
```

## Metadata Structure

Each document should have:

```javascript
{
  id: "uuid",
  name: "document.pdf",
  fileType: "pdf",
  fileSize: 45200,
  department: "Engineering",
  category: "Troubleshooting",
  version: "1.0",
  createdAt: "2026-06-03T10:00:00Z"
}
```

Each chunk should have:

```javascript
{
  id: "uuid",
  documentId: "uuid",
  content: "Text content...",
  chunkIndex: 0,
  startPosition: 0,
  endPosition: 500,
  metadata: {
    strategy: "semantic",
    sentenceCount: 3
  }
}
```

## Testing Your Code

```bash
# Test PDF extraction
const text = await extractTextFromPDF('sample.pdf');
console.log(text.pages);

# Test chunking
const chunks = semanticChunking(largeText);
console.log(chunks.length, "chunks created");

# Test document upload
const result = await ingestDocument(file, {
  department: 'Engineering',
  category: 'Troubleshooting'
});
console.log(result.chunksCreated);
```

## Before Committing

- ✅ Document upload works without errors
- ✅ Chunks are correctly created
- ✅ Metadata is properly stored
- ✅ Tested with actual files
- ✅ Changes only in team/document-processing/

## Useful Git Commands

```bash
# View your changes
git diff team/document-processing/

# View commits in your module
git log --oneline team/document-processing/

# Undo changes
git checkout team/document-processing/services/documentService.js

# View status
git status

# View staged changes
git diff --cached
```

## Performance Metrics to Track

```javascript
// Measure extraction time
console.time('extraction');
const text = await extractTextFromPDF(filePath);
console.timeEnd('extraction');
// Target: < 500ms for 10MB file

// Measure chunking time
console.time('chunking');
const chunks = semanticChunking(text);
console.timeEnd('chunking');
// Target: < 100ms for 10K words

// Measure total ingestion
console.time('ingestion');
const result = await ingestDocument(file);
console.timeEnd('ingestion');
// Target: < 5000ms including embeddings
```

## Chunking Comparison

Compare strategies in your commits:

```
Strategy          | Quality | Speed  | Size Consistency
Semantic          | ★★★★★  | ★★★   | Variable
Fixed-Size        | ★★★    | ★★★★★ | Consistent
Paragraph-Aware   | ★★★★   | ★★★★  | Semi-consistent
```

---

**Need Help?**
- Check TEAM_STRUCTURE.md for overall process
- Coordinate with Person 5 (Core Utils) for storage
- Ask in team chat for blockers
