# 🕵️ Phase 0: PREWORK (Context Gathering)

> **Role**: Context Detective  
> **Objective**: Before generating requirements/design/plan/code, gather project state to prevent hallucinations  
> **QA Role**: Fact Checker

---

## Core Principle

**Iceberg Theory**: User request is just the tip of the iceberg (10%), existing codebase is the 90% underwater. Ignoring the underwater part leads to:
- ❌ Redundancy: Re-implementing existing util functions
- ❌ Inconsistency: Project uses `fetch`, you use `axios`
- ❌ Regression: Breaking hidden dependencies

---

## Three-Step Protocol: DNA → Trace → Align

### Step 1: Project DNA Analysis (Skeleton)

**Objective**: Identify project's "genes"

**Actions**:
1. **Read manifest files**: `package.json`, `Cargo.toml`, `requirements.txt`, `go.mod`
   - Identify: Which libraries installed? (e.g. `trpc`, `prisma`, `redux` vs `zustand`)

2. **Read config files**: `tsconfig.json`, `next.config.js`, `.env.example`
   - Identify: What are constraints? (strict mode, path aliases like `@/`)

3. **Scan directory structure**: `ls -R` (depth 2-3)
   - Identify: Where is logic? (`src/features` vs `src/app` vs `internal/domain`)

---

### Step 2: Semantic Tracing (Nervous System)

**Objective**: Not just grep strings, but trace relationships

**Actions**:
1. **Keyword Expansion**
   - User says: "edit script"
   - You search: "Script", "Screenplay", "Document", "Page", "Editor"

2. **Dependency Tracing**
   - Find `TaskService` → Search its **callers** (Controllers/Routers) and **callees** (DB Models/Utils)
   - Goal: Map "blast radius" of changes

3. **Pattern Matching**
   - Look for "twin features": If building "comment on script", check "comment on post"
   - Copy **patterns**, not just code

---

### Step 3: Spec Alignment (Gap)

**Objective**: Overlay request onto reality

**Actions**:
1. **Read integration points**
   - Open `schema.prisma` (database)
   - Open `server/api/root.ts` (API registry)
   - Open `routes.tsx` (navigation)

2. **Verify reusability**
   - "Do we have `Modal` component?" → `ls src/components/ui`
   - "Do we have date formatting function?" → `ls src/utils`

---

## Output: Verified Context Artifact

Generate structured document:

```markdown
# 🧩 PREWORK Context Artifact
> **Module**: [module-name]
> **Date**: YYYY-MM-DD
> **Status**: Verified

## 1. Project DNA
- **Framework**: Next.js 14 (App Router)
- **State Management**: Zustand + React Query
- **Styling**: Tailwind + Shadcn UI
- **Database**: Prisma (PostgreSQL)
- **Testing**: Vitest + React Testing Library

## 2. Relevant Reality (Map)
- **Core Entity**: `model Script` in `schema.prisma` (Line 45)
- **API Pattern**: tRPC routers in `server/api/routers/`
- **UI Components**: Found `src/components/ui/dialog.tsx` (reusable)
- **Similar Feature**: `Note` feature uses same "optimistic update" pattern

## 3. Gaps (Missing)
- [ ] Database doesn't have `Scene` entity yet
- [ ] `Editor` component exists but lacks "multi-cursor" support
- [ ] No `deleteScene` tRPC procedure

## 4. Risks & Constraints
- **Auth**: Must use `ctx.session.user.id`
- **I18n**: All user-facing text must use `useTranslations`
- **Performance**: Editor must handle 10k+ blocks

## 5. Key Constraints for Downstream Phases
> **Important**: These constraints must be referenced in REQUIREMENTS and DESIGN

1. Must use existing `Modal` component from `src/components/ui/dialog.tsx`
2. Auth pattern: `ctx.session.user.id` (no custom auth)
3. State management: Zustand (not Redux)
4. API pattern: tRPC procedures in `server/api/routers/`

## 6. Verification Commands
- `ls src/components/ui` → Verify UI components exist
- `grep -r "model Script" prisma/` → Verify Schema location
```

---

## Trigger Timing

**Must run PREWORK**:
- ✅ At task start
- ✅ Before creating new files (check duplication)
- ✅ When user mentions terms you don't fully understand **in this codebase**

---

## QA Checklist (Embedded)

### 🧐 PREWORK QA: Fact Check

**Check Items**:

#### 1. Project DNA Check
- [ ] **Framework Awareness**: Identified project framework (Next.js/React/Prisma)? No hallucinating Express/Mongo?
- [ ] **Library Awareness**: Checked `package.json` before suggesting new libraries?
- [ ] **Style Alignment**: If project uses CSS Modules, not suggesting Tailwind classes?

#### 2. Semantic Trace Check
- [ ] **Relationship Mapping**: Found **related** files, not just exact keyword matches?
- [ ] **Twin Features**: Identified similar existing features to copy patterns?
- [ ] **Dependency Check**: Identified what will **break** by changing this file?

#### 3. Spec Alignment Check
- [ ] **File Verification**: Used `ls` to verify file paths?
- [ ] **Code Verification**: Read contents of integration points (`schema.prisma`, `api/root.ts`)?
- [ ] **Reuse Verification**: Have existing component but creating new one (e.g. `MyButton` when `ui/button` exists)?

#### 4. Rejection Criteria (Strict)

**🔴 Immediate Rejection**:
- Suggest using `axios` but `package.json` only has `ky` or `fetch`
- Assume `src/utils/api.ts` exists without checking
- Create new Table component, but `components/ui/table.tsx` already exists
- Didn't read `schema.prisma` before suggesting schema changes

---

## Output Deliverable

**Pass Criteria**:
- ✅ Created `specs/[module]/prework.md`
- ✅ Identified project tech stack
- ✅ Listed reusable components
- ✅ Verified all file paths
- ✅ No unverified assumptions

**Rejection Criteria**:
- ❌ Any "I think file is at..." (must verify with `ls`)
- ❌ Suggesting libraries not in project
- ❌ Missed obvious similar features
