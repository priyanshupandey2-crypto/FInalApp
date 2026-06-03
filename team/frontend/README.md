# Frontend Module - Person 1

## Responsibility
React UI development, components, state management, styling

## Files in This Module
```
frontend/
├── package.json
├── vite.config.js
├── tailwind.config.js
├── postcss.config.js
├── index.html
├── src/
│   ├── main.jsx
│   ├── App.jsx
│   ├── index.css
│   ├── api.js
│   ├── store.js
│   └── components/
│       ├── Layout.jsx
│       ├── ChatInterface.jsx
│       ├── MessageBubble.jsx
│       └── DocumentManager.jsx
```

## Git Workflow - Person 1

### Initial Setup
```bash
# Clone repository
cd "c:/Users/priyanshu.pandey2/New folder"
git clone <repository-url> rag-assistant
cd rag-assistant

# Create your feature branch
git checkout -b feature/frontend/initial-setup

# Navigate to your module
cd team/frontend
npm install
```

### Daily Workflow

**Step 1: Update from main**
```bash
git checkout main
git pull origin main
```

**Step 2: Create feature branch**
```bash
git checkout -b feature/frontend/<feature-name>
# Example: feature/frontend/add-chat-interface
```

**Step 3: Make changes**
```bash
# Edit files in team/frontend/src/
# Keep all changes within team/frontend/
```

**Step 4: Commit frequently (Best Practice)**
```bash
# Commit individual features
git add team/frontend/src/components/ChatInterface.jsx
git commit -m "feat(frontend): implement chat message interface

- Add message input component
- Add message display with markdown support
- Add send button with loading state

Relates to: RAG chat functionality"

# Another commit
git add team/frontend/src/store.js
git commit -m "feat(frontend): add Zustand state management

- Create store for chat messages
- Add store for documents
- Implement cache management"
```

**Step 5: Push your branch**
```bash
git push origin feature/frontend/<feature-name>
```

**Step 6: Create Pull Request**
```bash
# On GitHub/GitLab:
# 1. Go to repository
# 2. Click "New Pull Request"
# 3. Select:
#    - Base branch: main
#    - Compare branch: feature/frontend/<feature-name>
# 4. Add title: "feat(frontend): <description>"
# 5. Add description explaining changes
# 6. Request review from team
# 7. Wait for approval
# 8. Click "Merge Pull Request"
```

## Complete Example: Adding Chat Feature

```bash
# 1. Update main branch
git checkout main
git pull origin main

# 2. Create feature branch
git checkout -b feature/frontend/add-message-bubble

# 3. Make changes
# Edit: team/frontend/src/components/MessageBubble.jsx
# Edit: team/frontend/src/index.css

# 4. Stage changes
git add team/frontend/src/components/MessageBubble.jsx
git add team/frontend/src/index.css

# 5. Commit with descriptive message
git commit -m "feat(frontend): add MessageBubble component

Features:
- Display AI and user messages separately
- Show message with sources
- Include confidence score
- Add helpful/not helpful buttons

Related tests pass locally"

# 6. Push to remote
git push origin feature/frontend/add-message-bubble

# 7. Create PR on GitHub and get review
# After approval, merge is done on GitHub

# 8. Back to main for next feature
git checkout main
git pull origin main
git checkout -b feature/frontend/next-feature
```

## Testing Your Code

```bash
# In team/frontend directory
npm run dev
# Visit http://localhost:5173 in browser
```

## Before Committing

- ✅ Code is tested locally
- ✅ No console errors
- ✅ Styling looks good
- ✅ Commit message is descriptive
- ✅ Changes are only in team/frontend/

## Useful Git Commands

```bash
# See your changes before committing
git diff team/frontend/

# See commit history for your module
git log --oneline team/frontend/

# Undo changes in a file
git checkout team/frontend/src/App.jsx

# See current status
git status

# See what you're about to commit
git diff --cached

# Amend last commit (if not pushed)
git commit --amend -m "new message"

# Reset to main (careful!)
git reset --hard origin/main
```

## Troubleshooting

**Merge Conflict?**
```bash
# If there's a conflict during merge
git diff team/frontend/
# Fix the conflicts in your editor
git add team/frontend/
git commit -m "fix: resolve merge conflict"
git push origin feature/frontend/<feature-name>
```

**Accidentally committed to main?**
```bash
# Revert the commit
git revert HEAD

# Or reset (if not pushed)
git reset --soft HEAD~1
# Then create proper branch and commit again
```

## Integration with Other Modules

Your frontend code will:
- Call APIs in `/api/*` (defined by **Person 2 - Backend API**)
- Use state from store (your responsibility)
- Import utilities from core-utils (defined by **Person 5**)

Example API call:
```javascript
// team/frontend/src/api.js
export async function askQuestion(query) {
  const response = await fetch('/api/chat/ask', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ query })
  });
  return response.json();
}
```

## Code Style

- Use functional components with hooks
- Use Tailwind CSS for styling
- Follow React best practices
- Keep components small and focused
- Comment complex logic

## Deployment

Your build output goes to:
```bash
npm run build
# Creates: team/frontend/dist/
# This will be served by backend
```

---

**Need Help?**
- Check TEAM_STRUCTURE.md for overall process
- Ask in team chat for blockers
- Review PR comments for feedback
