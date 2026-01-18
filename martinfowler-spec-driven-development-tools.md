# Spec-Driven Development Tools

**Source:** https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html
**Author:** Birgitta Böckeler (Martin Fowler's blog)

## Overview

Exploration of emerging spec-driven development (SDD) tools and practices in AI-assisted coding. Evaluates three tools—Kiro, spec-kit, and Tessl—while examining what "spec-driven development" actually means in practice.

## What is Spec-Driven Development?

Writing a spec before writing code with AI, where specifications become the source of truth. Three implementation levels:

| Level | Description |
|-------|-------------|
| **Spec-first** | Create detailed specs before development, discard afterward |
| **Spec-anchored** | Maintain specs throughout feature evolution and maintenance |
| **Spec-as-source** | Specs are primary artifacts; humans never edit generated code |

## Tools Compared

| Tool | Approach | Workflow |
|------|----------|----------|
| **Kiro** | Lightweight, spec-first | Requirements → Design → Tasks |
| **spec-kit** | GitHub's elaborate approach | Constitution + Specify → Plan → Tasks |
| **Tessl** | Beta, spec-anchored focus | Bidirectional spec-code sync |

## Key Concerns

- **Workflow mismatch:** Neither Kiro nor spec-kit scales appropriately across problem sizes; small bugs get disproportionately complex workflows
- **Review burden:** Generated markdown artifacts become verbose and tedious compared to reviewing code directly
- **Control illusions:** Despite detailed specs and checklists, AI agents frequently ignore instructions or misinterpret requirements
- **Separation challenge:** Distinguishing functional specs from technical implementation remains difficult
- **Historical parallels:** Model-driven development (MDD) offers lessons about spec-as-source pitfalls

## Recommendations

1. Develop flexible workflows for various problem sizes
2. Improve spec review experiences to reduce markdown overhead
3. Embrace iterative approaches over extensive upfront specification
4. Learn from past code-generation attempts (MDD) to avoid similar failures
5. Define clearer boundaries between functional and technical specs

## Conclusion

While spec-first principles offer value, "spec-driven development" lacks consistent definition. Current tools may amplify existing challenges like review fatigue and hallucinations rather than solving them.
