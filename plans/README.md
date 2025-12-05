# Implementation Plans

This folder contains implementation plans created by the `@planner` agent.

## Purpose

Plans are detailed markdown documents that outline how to implement features, 
fix bugs, or make other changes to the codebase. They are created by the 
planner agent and consumed by the developer agent.

## Plan File Format

Plan files follow this naming convention:
- Format: `YYYY-MM-DD-[brief-description].md`
- Example: `2025-01-15-add-user-authentication.md`

## Workflow

1. **@planner** creates a detailed implementation plan and saves it here
2. **@developer** reads the plan and implements the code changes
3. **@changelog-manager** documents the changes in CHANGELOG.md

## Structure

Each plan typically includes:
- Overview and requirements
- Analysis of current state
- Files to modify or create
- Step-by-step implementation instructions
- Testing strategy
- Handoff notes

## Usage

To create a new plan:
```
@planner Create a plan for [task description]
```

To implement an existing plan:
```
@developer Please implement the plan in plans/[filename].md
```
