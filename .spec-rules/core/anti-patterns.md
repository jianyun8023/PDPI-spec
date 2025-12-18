# 🚫 Anti-Patterns - Prohibited Behavior List

> **Purpose**: List behavior patterns absolutely prohibited in SDD workflow. AI must recognize and reject these anti-patterns.

---

## 1. Process Violations

### ❌ AP-001: Phase Skipping

**Manifestation**:
```
User: "直接帮我实现用户登录" / "Implement user login directly for me"
Wrong AI: "好的,我现在开始写代码..." / "OK, I'll start writing code now..."
```

**Why Wrong**:
- No requirements doc → Don't know what to implement
- No design doc → Don't know how to implement
- No plan → Don't know steps, how to verify

**Correct Approach**:
```
✅ Correct AI: "Sorry, cannot skip phases. Need to complete first:
   1. REQUIREMENTS - Define login feature requirements and acceptance criteria
   2. DESIGN - Design auth architecture and API
   3. PLAN - Create executable steps
   
   Let's start with requirements analysis. Please describe specific requirements for login feature."
```

---

### ❌ AP-002: Cowboy Coding

**Manifestation**:
- Writing code before plan.md is complete or approved
- "Let me write some code to try"

**Why Wrong**:
- No verification criteria → Cannot know if correct
- No step decomposition → Easy to miss or rework
- Violates "documentation as code" principle

**Correct Approach**:
```
✅ Check: Does plan.md exist and status = APPROVED
   IF NO → Refuse to write code
   IF YES → Strictly execute according to plan.md
```

---

### ❌ AP-003: Rubber Stamp QA

**Manifestation**:
```
QA Check:
"Looks good! ✅ Approved for pass."
(Actually document missing critical parts)
```

**Why Wrong**:
- QA is the last line of defense, must be picky
- "Friendly" QA = Useless QA
- Lets errors flow into next phase

**Correct Approach**:
```
✅ QA must conduct adversarial review:
   - Actively seek problems (not seek strengths)
   - Use "rejection criteria" to check each item
   - Better to mistakenly reject than mistakenly approve
   
   Example:
   "❌ REQUIREMENTS rejected. Issues:
    1. Scenario 2 missing Given-When-Then format
    2. Acceptance criteria vague: 'fast response' should be quantified as '<200ms'
    3. Error handling scenario not specified"
```

---

## 2. Implementation Violations

### ❌ AP-004: Skipping Verification

**Manifestation**:
```
AI executes step 1.1: "Create User model"
AI: "Done! Now executing step 1.2..."
(Did not run `npm run build` to verify)
```

**Why Wrong**:
- May introduce compilation errors without knowing
- Violates "green-to-green" principle
- Subsequent steps based on wrong assumptions

**Correct Approach**:
```
✅ After each step must:
   1. Run verification command (defined in plan.md)
   2. Check output
   3. IF fail → Fix code → Re-verify
   4. IF pass → Mark step as [x], continue to next
```

---

### ❌ AP-005: Plan Deviation

**Manifestation**:
```
Plan.md: "Create user.ts in src/models/"
AI: "I think putting it in src/entities/ is better, let me change..."
```

**Why Wrong**:
- IMPLEMENTATION role is "junior developer", doesn't make architecture decisions
- Deviating from plan will cause subsequent steps to fail
- If plan has issues, should report, not self-modify

**Correct Approach**:
```
✅ IF discover plan issues:
   1. Stop execution
   2. Generate Deviation Report
   3. Suggest: "This step needs to backtrack to DESIGN/PLAN phase for modification"
   4. Continue after approval
```

---

### ❌ AP-006: Step Merging

**Manifestation**:
```
AI: "Steps 1.1 and 1.2 are simple, I did them together:
    - Created User model ✅
    - Created User API ✅
    - Created User UI ✅
    Done!"
```

**Why Wrong**:
- Large changes prone to errors and hard to locate
- Skipped intermediate verification points
- Violates atomicity principle

**Correct Approach**:
```
✅ Execute only one step at a time:
   Step 1.1: Create User model
   Verify: npm run build ✅
   
   Step 1.2: Create User API
   Verify: npm test user.test.ts ✅
   
   Step 1.3: Create User UI
   Verify: npm run dev, manual test ✅
```

---

## 3. Context Violations

### ❌ AP-007: Assuming Files Exist

**Manifestation**:
```
AI: "I will add fetchUser function in src/utils/api.ts..."
(Actually api.ts doesn't exist)
```

**Why Wrong**:
- Hallucination
- Causes plan to fail
- Wastes time

**Correct Approach**:
```
✅ PREWORK phase must:
   1. Use `ls src/utils/` to confirm file list
   2. Use `grep -r "api" src/` to search related code
   3. Only reference verified existing files
   
   IF file doesn't exist → Add "create file" step in PLAN
```

---

### ❌ AP-008: Reinventing the Wheel

**Manifestation**:
```
AI: "I created a new Button component..."
(Project already has src/components/ui/button.tsx)
```

**Why Wrong**:
- Code redundancy
- Style inconsistency
- Increased maintenance cost

**Correct Approach**:
```
✅ PREWORK phase must:
   1. Use `ls src/components/ui/` to list existing components
   2. Search similar functionality: "Do we have similar Modal/Button/Table?"
   3. Explicitly in DESIGN: Reuse vs Create new
```

---

### ❌ AP-009: Tech Stack Mismatch

**Manifestation**:
```
AI: "I use axios to make requests..."
(Project uses fetch or ky)
```

**Why Wrong**:
- Introduces unnecessary dependencies
- Style inconsistency
- May have configuration conflicts

**Correct Approach**:
```
✅ PREWORK phase must:
   1. Read package.json to identify installed libraries
   2. Check tsconfig.json / next.config.js etc configurations
   3. Follow project existing patterns
   
   Example:
   "📋 Project Tech Stack:
    - HTTP Client: ky
    - State Management: Zustand
    - Styling: Tailwind + Shadcn UI
    
    All designs must follow this stack."
```

---

## 4. Communication Violations

### ❌ AP-010: Silent Failures

**Manifestation**:
```
AI runs: "npm run build"
Output: Error: Cannot find module 'zod'
AI: "Done! Continue to next step..."
(Ignored the error)
```

**Why Wrong**:
- Errors accumulate
- Subsequent steps based on wrong assumptions
- Wastes time tracking root cause

**Correct Approach**:
```
✅ Any command failure must:
   1. Stop execution
   2. Analyze error
   3. Try to fix (max 3 attempts)
   4. If cannot fix → Report
   
   Output:
   "❌ Step 1.2 verification failed
    Error: Cannot find module 'zod'
    
    Analysis: Missing dependency
    Fix: Run `npm install zod`
    Re-verifying..."
```

---

### ❌ AP-011: Chat-Only Decisions

**Manifestation**:
```
User: "用户表需要加一个 avatar 字段" / "User table needs an avatar field"
AI: "好的,记住了!" / "OK, noted!"
(Did not update design.md or create Change Request)
```

**Why Wrong**:
- Chat history gets lost
- Subsequent sessions cannot get this information
- Documentation inconsistent with actual

**Correct Approach**:
```
✅ Important decisions must write to files:
   1. IF change requirement → Create Change Request in STATUS.json
   2. IF change design → Update design.md
   3. IF change plan → Update plan.md
   
   Output:
   "✅ Change recorded:
    - Created CR-001 in STATUS.json
    - Updated design.md Section 3.2 (User Schema)
    - Marked PLAN as INVALIDATED (needs regeneration)"
```

---

### ❌ AP-012: Infinite Retry

**Manifestation**:
```
AI: "Attempt 1 failed, trying again..."
AI: "Attempt 2 failed, trying again..."
AI: "Attempt 3 failed, trying again..."
AI: "Attempt 4 failed, trying again..."
(Infinite loop)
```

**Why Wrong**:
- Wastes resources
- May be fundamental issue (like plan error)
- Cannot self-resolve problems

**Correct Approach**:
```
✅ 3-Attempt Rule:
   1. Attempt 1: Direct fix
   2. Attempt 2: Deep analysis
   3. Attempt 3: Last try
   4. If still fails → Stop and report
   
   Output:
   "❌ Step 2.1 still failed after 3 attempts
    
    Problem: TypeScript type mismatch
    Attempted methods:
    1. Adjusted type definition
    2. Added type assertion
    3. Checked tsconfig.json
    
    Suggestion: This problem may be type definition error in DESIGN phase.
    Need to backtrack to DESIGN phase to re-examine type system.
    
    Generating deviation report..."
```

---

## 5. Quality Violations

### ❌ AP-013: Big Bang Planning

**Manifestation**:
```
Plan.md:
Phase 1: Complete all database models (20 tables)
Phase 2: Complete all APIs (50 endpoints)
Phase 3: Complete all UIs (30 pages)
```

**Why Wrong**:
- Horizontal slicing, cannot verify integration early
- Risks delayed until the end to discover
- Cannot demo intermediate states

**Correct Approach**:
```
✅ Vertical slicing:
Phase 1: User registration complete flow
  - Step 1.1: User table
  - Step 1.2: POST /api/signup
  - Step 1.3: Signup page
  → Milestone: Can register users ✅

Phase 2: User login complete flow
  - Step 2.1: Session table
  - Step 2.2: POST /api/login
  - Step 2.3: Login page
  → Milestone: Can login ✅
```

---

### ❌ AP-014: Over-Engineering

**Manifestation**:
```
Requirement: "用户可以上传头像" / "User can upload avatar"
Design: "我们构建一个微服务架构的图片处理系统,
      支持实时滤镜、AI美颜、CDN分发..." /
      "We build a microservice architecture image processing system,
       supporting real-time filters, AI beauty, CDN distribution..."
```

**Why Wrong**:
- Complexity explosion
- Extends development time
- Increases maintenance cost

**Correct Approach**:
```
✅ Simplicity first (KISS principle):
   MVP design: "Use existing cloud storage service (like S3),
           resize to 256x256 when uploading,
           save URL to database"
   
   Complex features leave to Phase 2 (if really needed)
```

---

### ❌ AP-015: Vague Verification

**Manifestation**:
```
Plan.md Step 1.1:
  Action: Create User API
  Verify: "测试一下" / "test it"
```

**Why Wrong**:
- "Test it" too vague
- Different people understand differently
- Cannot automate

**Correct Approach**:
```
✅ Precise verification commands:
   Step 1.1: Create User API
   Verify: `npm test src/api/user.test.ts`
   Expected: All tests pass (0 failed)
   
   Step 1.2: Integrate to router
   Verify: `curl -X POST http://localhost:3000/api/user -d '{"name":"test"}'`
   Expected: HTTP 200, returns user ID
```

---

## 6. Violation Detection Checklist

At end of each phase, AI should self-check:

### PREWORK Phase
- [ ] Did I verify all mentioned file paths? (AP-007)
- [ ] Did I search for existing similar features? (AP-008)
- [ ] Did I check package.json? (AP-009)

### REQUIREMENTS Phase
- [ ] Did I avoid mentioning specific technical implementation? (Not REQUIREMENTS scope)
- [ ] Did I write specific acceptance criteria? (AP-015)

### DESIGN Phase
- [ ] Did I avoid over-engineering? (AP-014)
- [ ] Did I consider reusing existing components? (AP-008)
- [ ] Did I use project tech stack? (AP-009)

### PLAN Phase
- [ ] Did I use vertical slicing? (AP-013)
- [ ] Does every step have precise verification command? (AP-015)
- [ ] Are steps atomic (≤30 min)? (AP-006)

### IMPLEMENTATION Phase
- [ ] Did I strictly follow plan? (AP-005)
- [ ] Did I run verification after each step? (AP-004)
- [ ] Do I do only one step at a time? (AP-006)
- [ ] Did I stop and report when failing? (AP-010, AP-012)

### QA Phase
- [ ] Did I adopt adversarial review? (AP-003)
- [ ] Did I actively seek problems?
- [ ] Did I use explicit rejection criteria?

---

## 7. Violation Penalties (Requirements for AI)

When detecting anti-patterns, AI **MUST**:

1. **Stop current operation**
2. **Identify anti-pattern type** (e.g. "AP-005: Plan Deviation")
3. **Explain why this is wrong**
4. **Provide correct approach**
5. **Ask user if continuing correct way**

**Example Output**:
```
⚠️  Detected Anti-Pattern: AP-005 (Plan Deviation)

You said: "把文件放在 entities/ 而不是 models/" / "Put file in entities/ instead of models/"

Why this is wrong:
- IMPLEMENTATION phase should not make architecture decisions
- Plan.md explicitly specified path
- Deviation may cause subsequent steps to fail

Correct approach:
1. If think plan is wrong, generate deviation report
2. Suggest backtracking to DESIGN/PLAN phase
3. Execute modification after approval

Generate deviation report? (yes/no)
```

---

## 8. Anti-Pattern Priority

| Priority | Anti-Pattern | Severity | Auto-Reject |
|--------|--------|--------|------------|
| P0 | AP-001 Phase Skipping | 🔴 Critical | ✅ Yes |
| P0 | AP-002 Cowboy Coding | 🔴 Critical | ✅ Yes |
| P0 | AP-004 Skipping Verification | 🔴 Critical | ✅ Yes |
| P1 | AP-003 Rubber Stamp QA | 🟠 High | ✅ Yes |
| P1 | AP-005 Plan Deviation | 🟠 High | ✅ Yes (generate report) |
| P1 | AP-007 Assuming Files | 🟠 High | ✅ Yes |
| P1 | AP-010 Silent Failures | 🟠 High | ✅ Yes |
| P2 | AP-006 Step Merging | 🟡 Medium | ⚠️ Warning |
| P2 | AP-008 Reinventing Wheel | 🟡 Medium | ⚠️ Warning |
| P2 | AP-011 Chat-Only Decisions | 🟡 Medium | ⚠️ Warning |
| P2 | AP-012 Infinite Retry | 🟡 Medium | ⚠️ Warning (force stop after 3) |
| P3 | AP-013 Big Bang Planning | 🔵 Low | ⚠️ Suggest improvement |
| P3 | AP-014 Over-Engineering | 🔵 Low | ⚠️ Suggest simplification |

---

## Summary

Remember:
- **Anti-Pattern is not "suggestion", it's "prohibition"**
- **When detecting P0/P1 anti-patterns, must refuse to continue**
- **Better be blamed "too strict" than let errors pass**

> **Motto: "Quality is designed in, not tested in; Process is enforced, not suggested."**
