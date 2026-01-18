# How to Write a Good Spec for AI Agents

**Source:** https://addyosmani.com/blog/good-spec/
**Author:** Addy Osmani

## Overview

Guide for creating effective specifications for AI coding agents. Addresses why simply providing massive specs fails due to context limitations and attention constraints.

## Key Principles

### High-Level Vision First
Start with concise objectives, let AI expand into detailed plans, then refine before coding begins.

### Structured Documentation
Organize specs like PRDs covering:
- Commands
- Testing
- Project structure
- Code style
- Git workflow
- Boundaries

### Modular Task Breakdown
- Divide work into focused prompts
- Avoid monolithic instructions
- Prevent the "curse of instructions" where too many requirements reduce adherence

### Quality Guardrails
- Three-tier boundaries: Always / Ask First / Never
- Self-verification steps
- Conformance testing to catch errors early

### Iterative Refinement
- Treat spec-writing as ongoing
- Test outputs, gather feedback
- Update documentation continuously

## Core Insight

> "Aim for a clear spec covering just enough nuance to guide the AI without overwhelming it. Break large tasks into smaller ones. Plan first in read-only mode, then execute and iterate."
