---
name: dbt-workflows
description: Operational workflows for dbt monorepos — impact analysis, model creation, refactoring, debugging, DAG validation, schema testing, and performance optimization. Always operates in compliance with dbt-analytics-engineer agent rules.
origin: custom
---

# dbt Workflows

Lineage-aware workflows for safe dbt model operations. All commands require DAG integrity and upstream dependency validation. Pair with the `dbt-analytics-engineer` agent for full enforcement of global rules.

## When to Activate

- Evaluating downstream risk before changing a model or column
- Creating new staging, intermediate, or mart models
- Debugging data quality issues or broken transformations
- Refactoring models for readability or standardization
- Validating DAG structure before a large refactor
- Reviewing schema tests and coverage gaps
- Optimizing slow or expensive transformations

## Core Constraint (All Workflows)

Every workflow must:
- Respect DAG integrity
- Use lineage-aware reasoning
- Minimize destructive changes
- Validate impact before modification

---

## 1. Impact Analysis

**Use when:** Changing a model, column, or logic; evaluating downstream risk.

**Steps:**
1. Identify target model(s)
2. Trace upstream dependencies (`ref`/`source`/`manifest`)
3. Trace downstream dependencies (`manifest` preferred)
4. Identify affected transformations
5. Assess risk (low / medium / high)
6. Suggest safest modification layer

**Output:**
- Upstream lineage chain
- Downstream impact chain
- Risk assessment
- Required changes
- Optional improvements

---

## 2. Refactor Model

**Use when:** Improving structure, standardizing logic, or optimizing transformations.

**Steps:**
1. Analyze current model
2. Trace upstream/downstream dependencies
3. Identify inefficiencies or duplication
4. Evaluate grain correctness
5. Propose improved structure
6. Ensure DAG safety

**Rules:**
- Do not change business logic unless necessary
- Do not modify downstream models
- Prefer upstream consolidation of logic

**Output:** Refactored SQL (diff format preferred), explanation of improvements, dependency notes.

---

## 3. Debug Model

**Use when:** Data quality issues, unexpected outputs, or broken transformations.

**Steps:**
1. Start from affected model
2. Walk upstream via `ref`/`source`/`manifest`
3. Identify first incorrect transformation
4. Validate each upstream step
5. Isolate root cause
6. Recommend fix location (prefer upstream)

**Output:** Root cause explanation, lineage path, fix recommendation, suggested SQL fix.

---

## 4. Create Model

**Use when:** Building a new dbt model.

**Steps:**
1. Identify sources or upstream models
2. Define grain explicitly
3. Assign correct layer (`stg_`, `int_`, `mart_`)
4. Define transformations
5. Apply naming conventions
6. Add schema tests
7. Ensure proper `ref`/`source` usage

**Rules:**
- Do not duplicate existing logic
- Do not create downstream models unless required
- Ensure DAG consistency

**Output:** Full SQL model, suggested tests, design explanation.

---

## 5. Modify Model

**Use when:** Updating existing logic, adding/removing columns, or adjusting transformations.

**Steps:**
1. Read current model
2. Identify upstream dependencies
3. Identify downstream dependencies (`manifest` preferred)
4. Determine safest layer for change
5. Apply minimal necessary edits
6. Ensure backward compatibility unless explicitly breaking
7. Validate DAG integrity

**Rules:**
- Never break `ref()` chains
- Avoid unnecessary restructuring
- Prefer upstream edits over marts
- Flag breaking changes explicitly

**Output:** SQL diff, impacted downstream models, explanation of changes, risk assessment.

---

## 6. DAG Validation

**Use when:** Verifying lineage correctness, auditing models, or preparing large refactors.

**Steps:**
1. Load `manifest.json` if available
2. Extract upstream dependencies
3. Extract downstream dependencies
4. Compare with SQL `ref()` usage
5. Identify mismatches
6. Flag orphaned or duplicate logic

**Output:** DAG summary, dependency graph explanation, issues found, recommendations.

---

## 7. Schema & Testing Review

**Use when:** Preparing models for production or improving data quality.

**Steps:**
1. Analyze model grain and columns
2. Identify critical fields
3. Suggest tests:
   - `not_null`
   - `unique`
   - `relationships`
   - `accepted_values`
4. Identify missing coverage

**Output:** Suggested tests, justification, coverage gaps.

---

## 8. Performance Optimization

**Use when:** Models are slow or have expensive joins/transformations.

**Steps:**
1. Identify expensive operations
2. Detect redundant logic
3. Reduce unnecessary joins/CTEs
4. Suggest materialization improvements
5. Consider incremental models if appropriate

**Output:** Optimization plan, refactored SQL (if needed), expected gains.

---

## Examples

```
Analyze impact of modifying stg_orders.order_status column.

Refactor int_customer_lifetime_value following dbt best practices.

Trace upstream lineage and identify root cause of issue in mart_revenue.

Create a new mart model for monthly active users based on stg_events.

Validate DAG structure for mart_orders and flag any orphaned refs.
```
