# SOLID Smell Prompts

## SRP (Single Responsibility)

- File owns unrelated concerns such as HTTP, persistence, and domain rules in one place
- Large class or module with low cohesion or multiple reasons to change
- Functions that orchestrate many unrelated steps
- God objects that know too much about the system
- **Ask:** "What is the single reason this module would change?"

## OCP (Open/Closed)

- Adding a new behavior requires editing many switch or if branches
- Feature growth requires modifying core logic instead of extending behavior
- No plugin, strategy, or hook points for variation
- **Ask:** "Can I add a new variant without touching existing code?"

## LSP (Liskov Substitution)

- Subclass checks for concrete type or throws for a base method
- Overridden methods weaken preconditions or strengthen postconditions
- Subclass ignores or no-ops parent behavior
- **Ask:** "Can I substitute any subclass without the caller knowing?"

## ISP (Interface Segregation)

- Interfaces with many methods that most implementers do not use
- Callers depend on broad interfaces for narrow needs
- Empty or stub implementations of interface methods
- **Ask:** "Do all implementers need all methods?"

## DIP (Dependency Inversion)

- High-level logic depends on concrete I/O, storage, or network types
- Hard-coded implementations instead of abstractions or injection
- Import chains that couple business logic directly to infrastructure
- **Ask:** "Can I swap the implementation without changing business logic?"

---

## Common Code Smells Beyond SOLID

| Smell | Signs |
|-------|-------|
| **Long method** | Function is large, deeply nested, or doing multiple jobs |
| **Feature envy** | Method uses more data from another class or module than its own |
| **Data clumps** | Same group of parameters passed together repeatedly |
| **Primitive obsession** | Strings and numbers used where domain types would be clearer |
| **Shotgun surgery** | One change requires edits across many files |
| **Divergent change** | One file changes for many unrelated reasons |
| **Dead code** | Unreachable or never-called code |
| **Speculative generality** | Abstractions added for hypothetical future needs |
| **Magic values** | Hard-coded numbers or strings without named meaning |

---

## Refactor Heuristics

1. **Split by responsibility, not by size**.
2. **Introduce abstraction only when needed**.
3. **Keep refactors incremental**.
4. **Preserve behavior before restructuring**.
5. **Name things by intent**.
6. **Prefer composition over inheritance**.
7. **Make illegal states unrepresentable when the language and codebase support it**.
