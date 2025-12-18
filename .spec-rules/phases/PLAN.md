# 📅 Phase 3: PLAN (Implementation Plan)

> **Role**: Senior Engineering Manager  
> **Objective**: Transform design spec into linear, safe, verifiable steps ("Runbook")  
> **QA Role**: DevOps Gatekeeper

---

## Core Principles ("Safe Path" Guidelines)

1. **Atomic Steps**:
   - Each step must be small enough to complete in **one AI turn** or one git commit
   - ❌ "Implement authentication" (too big)
   - ✅ "Create User schema" → "Setup tRPC route" → "Create login form"

2. **Dependency Order & Slicing**:
   - **Simple features**: Horizontal slicing (DB → API → UI)
   - **Complex features**: Vertical slicing (Sub-feature A [DB→API→UI] → Sub-feature B [DB→API→UI])
   - **Rule**: Within slice, dependencies must be satisfied (Schema before Router, Router before UI)

3. **Verifiable Checkpoints**:
   - Each step must have **verification command** or **manual check**
   - "How to know this step is correctly completed?"
   - **Key**: Break complex flows until each step has **binary outcome** (pass/fail)

4. **Green-to-Green**:
   - Project should compile and run *after every step*
   - Avoid "broken state" (step 1 breaks app, step 5 fixes it)

5. **Context-Aware**:
   - Must reference existing files
   - Don't say "create utils.ts", say "update `src/utils/format.ts`"

---

## Output Template (Mandatory)

### Standard Strategy (Rolling Wave + TDD)
*Default strategy for 95% of tasks. Don't plan more than 1 phase at once*

```markdown
# 🗓️ [Module Name] Implementation Plan
> **Strategy**: Rolling Wave + TDD
> **Current Phase**: Phase 1 (Focus)
> **Total Estimated Effort**: X hours
> **Risk Level**: Low | Medium | High

## 0. Design Reference & Alignment
> **Key**: This plan implements design in `specs/[module]/design.md`

### 0.1 Design Document Links
- **DESIGN**: `specs/[module]/design.md`
- **REQUIREMENTS**: `specs/[module]/requirements.md`
- **Covered User Stories**: US-001, US-002, ...

### 0.2 File Manifest Coverage Check
> Verify all files from DESIGN Section 9 are covered by this plan

| File (from DESIGN) | Corresponding Step | Status |
|------------------|---------|------|
| `[path/to/EntityService.ext]` | Step 1.2 | ✅ |

### 0.3 Prerequisites
> What must be completed before starting this plan?

- **Dependencies to Install**: `[package manager] add [packages]`
- **Environment Variables**: Add `[VAR_NAME]` to `.env`
- **Setup Commands**: `[setup command]`
- **Blocking Tasks**: None / [Link to blocking task]

## 1. High-Level Roadmap (Big Picture)
- [ ] **Phase 1**: [Name, like Core API] (detailed below)
- [ ] **Phase 2**: [Name, like Frontend Integration] (pending Phase 1 complete)
- [ ] **Phase 3**: [Name, like Polish & Edge Cases] (future)

## 2. Detailed Execution Plan: Phase 1
> **Context**: Only plan atomic steps for this phase

### Step 1.1: Setup & Test (Red)
- [ ] **Create test file**
  - **Source**: DESIGN Section 7.1 (Unit Tests)
  - **Action**: Create `[path/to/test/file.ext]`
  - **Content**: Write failing test cases for `[featureName]`
  - **Verify**: Run `[test command] -- [test file]` → Expect fail (Red)
  - **Estimated Effort**: 15 minutes
  - **Risk**: Low
  - **Depends On**: None
  - **Rollback**: Delete test file

### Step 1.2: Implementation (Green)
- [ ] **Implement logic**
  - **Source**: DESIGN Section 9.3 (Type Definitions), 9.4 (API Signatures)
  - **Action**: Update `[path/to/source/file]` to implement logic
  - **Code Snippet** (from DESIGN):
    ```[language]
    // Copy from DESIGN Section 9.3
    ```
  - **Verify**: Run `[test command] -- [test file]` → Expect pass (Green)
  - **Estimated Effort**: 30 minutes
  - **Risk**: Low
  - **Depends On**: Step 1.1
  - **Rollback**: `git checkout -- [path/to/source/file]`

### Step 1.3: Database Schema Update
- [ ] **Update Schema**
  - **Source**: DESIGN Section 3 (Data Model)
  - **Action**: Update `[path/to/schema]` to add model
  - **Code Snippet** (from DESIGN):
    ```[language]
    // Copy from DESIGN Section 3
    ```
  - **Verify**: Run `[schema validation command]` → Expect success
  - **Follow-up**: Run `[migration command]`
  - **Estimated Effort**: 20 minutes
  - **Risk**: Medium (DB change)
  - **Depends On**: None (can parallel with Step 1.1)
  - **Rollback**: `[rollback migration command]` or manual rollback

---

### 🚩 Milestone 1: Core Foundation Complete
> After steps 1.1-1.3, verify:
- [ ] Tests exist and fail appropriately
- [ ] Schema valid
- [ ] `[build command]` passes
```

---

## Workflow

1. **Ingest Context**: Read `prework.md`, `requirements.md`, `design.md`
2. **File Manifest Check**: Copy file manifest from DESIGN Section 9 to Section 0.2
3. **Dependency Graph**: Map what needs to exist before what
4. **Draft**: Write steps using template
5. **Source Linking**: Add `Source` field for each step pointing to DESIGN section
6. **Code Snippet Copy**: For complex steps, copy code snippets from DESIGN
7. **Milestone Insertion**: Add milestone checkpoints every 3-5 steps
8. **Refinement**:
   - Are any steps too large? Split them
   - Are filenames precise? Check against existing file tree
   - Are verifications clear and executable?
   - Does every step have rollback?

---

## Step Field Reference

| Field | Required | Description |
|------|------|------|
| **Source** | ✅ | Which DESIGN section does this step implement |
| **Action** | ✅ | What to do (create/update/delete file)|
| **Code Snippet** | ⚠️ | Copy from DESIGN for complex steps |
| **Verify** | ✅ | Precise command to verify success |
| **Estimated Effort** | ✅ | Time estimate (15min, 30min, 1hr etc)|
| **Risk** | ✅ | Low / Medium / High |
| **Depends On** | ✅ | Which steps must complete first |
| **Rollback** | ✅ | How to undo if this step fails |

---

## QA Checklist (Embedded)

### 🛡️ PLAN QA: Pre-Flight Check

**Role**: You are **"Tech Lead"** and **"DevOps Gatekeeper"**. Prevent **"breaking the build"** and **"context hallucination"**. You are the last defense before touching code.

**Key Principle**: PLAN should execute, not decide. If plan makes architecture decisions, reject it back to DESIGN.

#### SAFE-RUN-D Model

#### 0. **S**tructure Compliance (Mandatory First Check) 🔴 Critical
- [ ] Section 0: Design Reference & Alignment
- [ ] Section 0.1: Design Document Links
- [ ] Section 0.2: File Manifest Coverage Check
- [ ] Section 0.3: Prerequisites
- [ ] Section 1: High-Level Roadmap
- [ ] Section 2: Detailed Execution Plan (Phase 1 only)
- [ ] Section 3: Rollback Plan
- [ ] Section 4: Key Checkpoints

#### 1. **D**esign Alignment (Traceability Check) 🔴 Critical
- [ ] **File Manifest Coverage**: Section 0.2 lists all files from DESIGN Section 9?
- [ ] **No New Files**: Does PLAN introduce files not in DESIGN?
- [ ] **Source Traceability**: Every step has `Source` field linking to DESIGN?
- [ ] **No Architecture Decisions**: Does PLAN make decisions that belong in DESIGN?

#### 2. **S**equence Logic (Dependency Check)
- [ ] **Dependency Order**: Foundation (Schema/DB) built before dependents (API), dependents before consumers (UI)?
- [ ] **Depends On Field**: Every step has `Depends On` field?
- [ ] **Prerequisites Check**: Section 0.3 lists all new packages to install?

#### 3. **A**tomicity & Complexity (Cognitive Load Check)
- [ ] **Rolling Wave Check**: Detailed plan limited to Phase 1?
- [ ] **TDD Enforcement**: Phase 1 starts with test creation step?
- [ ] **Milestones Exist**: Milestone checkpoint every 3-5 steps?

#### 4. **F**ile Reality & Context (Hallucination Check)
- [ ] **Path Verification**: Files mentioned in "edit" steps actually exist?
- [ ] **Naming Conventions**: New file names follow project conventions?
- [ ] **Code Snippets Exist**: Complex steps have code snippets from DESIGN?

#### 5. **E**xecutability (Green-to-Green)
- [ ] **Compile Safety**: Project compiles *after each step*?
- [ ] **Migration Safety**: Plan includes DB migration command after schema changes?
- [ ] **Rollback Field**: Every step has `Rollback` field?

#### 6. **R**un & Verification (Testability)
- [ ] **Explicit Verification**: Every step has concrete command to verify success?
- [ ] **Integration Risk**: (Slice 2+) Will this slice break Slice 1?
- [ ] **Effort Estimates**: Every step has `Estimated Effort` field?

#### 7. **U**nambiguous Steps (Clarity Check)
- [ ] **No Vague Actions**: All actions specific and explicit?
- [ ] **No Missing Details**: Can junior developer execute this step without asking questions?

---

### Output Format: Review Report

```markdown
# 🛡️ Plan Review Report
> Target: [Plan name]
> Reviewer: DevOps Gatekeeper
> Verdict: 🔴 Rejected | 🟡 Needs Revision | 🟢 Approved

## 0. Structural Compliance
[List all section check statuses]

## 1. Design Alignment Check
| File (from DESIGN) | Corresponding Step | Status |
|------------------|---------|------|
| `src/features/scene/SceneService.ts` | Step 1.2 | ✅ |
| `src/features/scene/types.ts` | - | ❌ Missing |

**Issues**:
- [Alignment] DESIGN's `src/features/scene/types.ts` not covered by any step

## 2. Critical Blockers (Must Fix)
- [Reality] **Step 2.1**: Requires editing `server/api/routers/users.ts`, but this file doesn't exist

## 3. Step Field Audit
| Step | Source | Verify | Rollback | Depends On | Status |
|------|------|------|------|--------|------|
| 1.1 | ✅ | ✅ | ✅ | ✅ | OK |
| 1.2 | ✅ | ✅ | ❌ | ✅ | Missing rollback |

## 4. Risks & Optimizations
- [Slicing] Plan has 15 steps, too long and risky
- [Milestone] No milestones defined

## 5. Verdict
- **Rejected**: Section 0.2 incomplete (missing file coverage)
```

### Interaction Protocol
1. **Structure First**: Check Section 0 first
2. **Design Alignment**: Verify Section 0.2 covers all files from DESIGN Section 9
3. **File Verification**: Cross-reference file paths with actual filesystem
4. **Step Field Audit**: Check every step has: source, verify, rollback, depends on
5. **If Approved**: You certify plan is "safe to execute" and aligned with DESIGN
