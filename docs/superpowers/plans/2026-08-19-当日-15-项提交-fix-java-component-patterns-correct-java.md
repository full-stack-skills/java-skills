# java-skills 历史任务：当日 15 项提交（fix(java-component-patterns): correct java.version rule, add Jackson BOM management 等）（2026-08-19）

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.
> **本计划为历史任务回填**：依据 git 提交记录还原，全部任务已完成，复选框均为 `- [x]`。

**Goal:** 本日完成 15 项提交：

1. fix(java-component-patterns): correct java.version rule, add Jackson BOM management
2. fix: java.version write style 1.8 ≠ 8 unified (compiler 3.15.0 internally uses 8)
3. feat: add Maven 4 template (pom-template-maven4.xml) to both skills' assets
4. feat: add Maven 4 migration examples to both skills
5. fix: correct POM model claim — Maven 4 adds model 4.1.0 alongside 4.0.0 compat
6. feat: add Maven 4 adaptation guide (references/maven4.md) to both skills
7. docs: add 8-step checklist navigation to both SKILL.md (TRACE E2)
8. refactor: restructure both skills to standard layout (seata-patterns style)
9. fix: XML comments cannot contain double hyphen; now well-formed
10. fix: correct placeholder syntax in comment; XML well-formed
11. feat: add java-component-patterns skill (standalone SDK/library POM template)
12. fix: replace illegal placeholder tag with comment; XML now well-formed
13. fix: add licenses/scm/developers metadata sections to template
14. refactor: make spring-boot-starter-patterns org-agnostic
15. feat: add spring-boot-starter-patterns skill

**Architecture:** 技能内容更新（SKILL.md）、技能参考/示例资料更新。

**Tech Stack:** Java/Maven、Markdown、Shell。

**Spec:** 无独立规格文档；依据提交 `bb05931`, `cadb181`, `5128a94`, `fa1cbe5`, `8c8dd43`, `f9e4749`, `a443f96`, `8d4384f`, `63672cd`, `dd4f7a9`, `540c4d6`, `6e8a251`, `3c5f2f6`, `f845b3f`, `26e439f` 还原。

## Global Constraints

- 本计划依据 git 历史回填，仅记录已完成工作（15 个提交均已落地）
- 不含未完成或计划中的工作；步骤复选框全部为已完成状态

---

### Task 1: fix(java-component-patterns): correct java.version rule, add Jackson BOM management

**Files:**
- `skills/java-component-patterns/SKILL.md`
- `skills/java-component-patterns/assets/pom-template.xml`
- `skills/java-component-patterns/references/branch-release.md`
- `skills/java-component-patterns/references/pom-structure.md`

- [x] **Step 1: 完成「fix(java-component-patterns): correct java.version rule, add Jackson BOM management」（loong10k）**
- [x] **Step 2: 提交** — `bb05931` fix(java-component-patterns): correct java.version rule, add Jackson BOM management

---

### Task 2: fix: java.version write style 1.8 ≠ 8 unified (compiler 3.15.0 internally uses 8)

**Files:**
- `skills/java-component-patterns/references/pom-structure.md`
- `skills/spring-boot-starter-patterns/references/pom-structure.md`

- [x] **Step 1: 完成「fix: java.version write style 1.8 ≠ 8 unified (compiler 3.15.0 internally uses 8)」（loong10k）**
- [x] **Step 2: 提交** — `cadb181` fix: java.version write style 1.8 ≠ 8 unified (compiler 3.15.0 internally uses 8)

---

### Task 3: feat: add Maven 4 template (pom-template-maven4.xml) to both skills' assets

**Files:**
- `skills/java-component-patterns/SKILL.md`
- `skills/java-component-patterns/assets/pom-template-maven4.xml`
- `skills/spring-boot-starter-patterns/SKILL.md`
- `skills/spring-boot-starter-patterns/assets/pom-template-maven4.xml`

- [x] **Step 1: 完成「feat: add Maven 4 template (pom-template-maven4.xml) to both skills' assets」（loong10k）**
- [x] **Step 2: 提交** — `5128a94` feat: add Maven 4 template (pom-template-maven4.xml) to both skills' assets

---

### Task 4: feat: add Maven 4 migration examples to both skills

**Files:**
- `skills/java-component-patterns/SKILL.md`
- `skills/java-component-patterns/examples/maven4-migration.md`
- `skills/spring-boot-starter-patterns/SKILL.md`
- `skills/spring-boot-starter-patterns/examples/maven4-migration.md`

- [x] **Step 1: 完成「feat: add Maven 4 migration examples to both skills」（loong10k）**
- [x] **Step 2: 提交** — `fa1cbe5` feat: add Maven 4 migration examples to both skills

---

### Task 5: fix: correct POM model claim — Maven 4 adds model 4.1.0 alongside 4.0.0 compat

**Files:**
- `skills/java-component-patterns/SKILL.md`
- `skills/java-component-patterns/references/maven4.md`
- `skills/spring-boot-starter-patterns/SKILL.md`
- `skills/spring-boot-starter-patterns/references/maven4.md`

- [x] **Step 1: 完成「fix: correct POM model claim — Maven 4 adds model 4.1.0 alongside 4.0.0 compat」（loong10k）**
- [x] **Step 2: 提交** — `8c8dd43` fix: correct POM model claim — Maven 4 adds model 4.1.0 alongside 4.0.0 compat

---

### Task 6: feat: add Maven 4 adaptation guide (references/maven4.md) to both skills

**Files:**
- `skills/java-component-patterns/SKILL.md`
- `skills/java-component-patterns/references/maven4.md`
- `skills/spring-boot-starter-patterns/SKILL.md`
- `skills/spring-boot-starter-patterns/references/maven4.md`

- [x] **Step 1: 完成「feat: add Maven 4 adaptation guide (references/maven4.md) to both skills」（loong10k）**
- [x] **Step 2: 提交** — `f9e4749` feat: add Maven 4 adaptation guide (references/maven4.md) to both skills

---

### Task 7: docs: add 8-step checklist navigation to both SKILL.md (TRACE E2)

**Files:**
- `skills/easy4j-deploy/SKILL.md`
- `skills/easy4j-deploy/references/dependency-matrix.md`
- `skills/easy4j-deploy/references/troubleshooting.md`
- `skills/easy4j-deploy/scripts/cve-scan.sh`
- `skills/easy4j-deploy/scripts/release-sdk.sh`
- `skills/easy4j-deploy/scripts/release-starter.sh`
- `skills/java-component-patterns/SKILL.md`
- `skills/spring-boot-starter-patterns/SKILL.md`

- [x] **Step 1: 完成「docs: add 8-step checklist navigation to both SKILL.md (TRACE E2)」（loong10k）**
- [x] **Step 2: 提交** — `a443f96` docs: add 8-step checklist navigation to both SKILL.md (TRACE E2)

---

### Task 8: refactor: restructure both skills to standard layout (seata-patterns style)

**Files:**
- `skills/java-component-patterns/SKILL.md`
- `skills/java-component-patterns/assets/pom-template.xml`
- `skills/java-component-patterns/examples/create-component.md`
- `skills/java-component-patterns/references/branch-release.md`
- `skills/java-component-patterns/references/pom-structure.md`
- `skills/spring-boot-starter-patterns/SKILL.md`
- `skills/spring-boot-starter-patterns/assets/pom-template.xml`
- `skills/spring-boot-starter-patterns/examples/create-new-starter.md`
- `skills/spring-boot-starter-patterns/references/pom-structure.md`
- `skills/spring-boot-starter-patterns/references/version-matrix.md`

- [x] **Step 1: 完成「refactor: restructure both skills to standard layout (seata-patterns style)」（loong10k）**
- [x] **Step 2: 提交** — `8d4384f` refactor: restructure both skills to standard layout (seata-patterns style)

---

### Task 9: fix: XML comments cannot contain double hyphen; now well-formed

**Files:**
- `skills/java-component-patterns/assets/pom-template.xml`

- [x] **Step 1: 完成「fix: XML comments cannot contain double hyphen; now well-formed」（loong10k）**
- [x] **Step 2: 提交** — `63672cd` fix: XML comments cannot contain double hyphen; now well-formed

---

### Task 10: fix: correct placeholder syntax in comment; XML well-formed

**Files:**
- `skills/java-component-patterns/assets/pom-template.xml`

- [x] **Step 1: 完成「fix: correct placeholder syntax in comment; XML well-formed」（loong10k）**
- [x] **Step 2: 提交** — `dd4f7a9` fix: correct placeholder syntax in comment; XML well-formed

---

### Task 11: feat: add java-component-patterns skill (standalone SDK/library POM template)

**Files:**
- `skills/java-component-patterns/SKILL.md`
- `skills/java-component-patterns/assets/pom-template.xml`

- [x] **Step 1: 完成「feat: add java-component-patterns skill (standalone SDK/library POM template)」（loong10k）**
- [x] **Step 2: 提交** — `540c4d6` feat: add java-component-patterns skill (standalone SDK/library POM template)

---

### Task 12: fix: replace illegal placeholder tag with comment; XML now well-formed

**Files:**
- `skills/spring-boot-starter-patterns/assets/pom-template.xml`

- [x] **Step 1: 完成「fix: replace illegal placeholder tag with comment; XML now well-formed」（loong10k）**
- [x] **Step 2: 提交** — `6e8a251` fix: replace illegal placeholder tag with comment; XML now well-formed

---

### Task 13: fix: add licenses/scm/developers metadata sections to template

**Files:**
- `skills/spring-boot-starter-patterns/SKILL.md`
- `skills/spring-boot-starter-patterns/assets/pom-template.xml`

- [x] **Step 1: 完成「fix: add licenses/scm/developers metadata sections to template」（loong10k）**
- [x] **Step 2: 提交** — `3c5f2f6` fix: add licenses/scm/developers metadata sections to template

---

### Task 14: refactor: make spring-boot-starter-patterns org-agnostic

**Files:**
- `skills/spring-boot-starter-patterns/SKILL.md`
- `skills/spring-boot-starter-patterns/assets/pom-template.xml`

- [x] **Step 1: 完成「refactor: make spring-boot-starter-patterns org-agnostic」（loong10k）**
- [x] **Step 2: 提交** — `f845b3f` refactor: make spring-boot-starter-patterns org-agnostic

---

### Task 15: feat: add spring-boot-starter-patterns skill

**Files:**
- `skills/spring-boot-starter-patterns/SKILL.md`
- `skills/spring-boot-starter-patterns/assets/pom-template.xml`

- [x] **Step 1: 完成「feat: add spring-boot-starter-patterns skill」（loong10k）**
- [x] **Step 2: 提交** — `26e439f` feat: add spring-boot-starter-patterns skill
