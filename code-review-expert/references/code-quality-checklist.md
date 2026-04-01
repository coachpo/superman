# Code Quality Checklist

## Error Handling

### Anti-Patterns to Flag

- **Swallowed exceptions**: empty catches or catch blocks that only log and continue
- **Overly broad catch**: catching base exception types when narrower handling is available
- **Error information leakage**: internal details exposed to users
- **Missing error handling**: no boundary handling around fallible I/O, parsing, or network work
- **Async error handling gaps**: unhandled promise rejections, missing `.catch()`, missing async boundary handling

### Best Practices to Check

- [ ] Errors are handled at the right boundary
- [ ] User-facing messages avoid internal implementation details
- [ ] Logs include enough context for debugging
- [ ] Async failures are propagated or handled intentionally
- [ ] Recoverable failures have fallback behavior
- [ ] Critical failures surface to monitoring or alerts

### Questions to Ask

- "What happens when this operation fails?"
- "Will the caller know something went wrong?"
- "Is there enough context to debug the failure?"

---

## Performance and Caching

### CPU-Intensive Operations

- Expensive work in hot paths
- Blocking main-thread or request-path work
- Unnecessary recomputation
- Missing memoization or reuse for pure repeated work

### Database and I/O

- **N+1 queries** or one-by-one remote calls
- Missing indexes on frequent query paths
- Over-fetching more data than needed
- No pagination or streaming for large datasets

### Caching Issues

- Missing cache for expensive repeated work
- Cache without TTL or invalidation strategy
- Cache key collisions
- User-specific data cached too broadly

### Memory

- Unbounded collections
- Large object retention preventing cleanup
- Inefficient string building in loops
- Loading large files entirely when streaming would be safer

### Questions to Ask

- "What is the time complexity here?"
- "How does this behave with 10x or 100x more data?"
- "Should this be cached or batched?"
- "What is the memory growth pattern?"

---

## Boundary Conditions

### Null and Undefined Handling

- Missing null checks on optional values
- Truthy or falsy checks that reject valid values such as `0` or empty string
- Optional chaining hiding structural problems
- Inconsistent null vs undefined conventions

### Empty Collections

- Code assumes an array or collection has items
- First or last element access without length checks
- Empty object cases ignored

### Numeric Boundaries

- Division by zero risks
- Overflow or unsafe integer handling
- Floating point equality mistakes
- Negative values where they should be impossible
- Off-by-one errors in loops, slices, or pagination

### String Boundaries

- Empty or whitespace-only strings treated as meaningful input
- Very long strings without limits
- Unicode edge cases ignored

### Questions to Ask

- "What if this is null or undefined?"
- "What if the collection is empty?"
- "What is the valid numeric range here?"
- "What happens exactly at the boundary values?"
