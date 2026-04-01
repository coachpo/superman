# Security and Reliability Checklist

## Input and Output Safety

- **XSS**: unsafe HTML injection, `dangerouslySetInnerHTML`, unescaped templates, `innerHTML` assignments
- **Injection**: SQL, NoSQL, command, or GraphQL injection via string concatenation or template literals
- **SSRF**: user-controlled URLs reaching internal services without allowlist validation
- **Path traversal**: user input in file paths without sanitization
- **Prototype pollution**: unsafe object merging in JavaScript or TypeScript

## AuthN and AuthZ

- Missing tenant or ownership checks for read or write operations
- New endpoints without auth guards or RBAC enforcement
- Trusting client-provided roles, flags, or IDs
- Broken access control such as IDOR patterns
- Session fixation or weak session handling

## JWT and Token Security

- Algorithm confusion attacks
- Weak or hard-coded secrets
- Missing expiration or failure to validate it
- Sensitive data placed in token payloads
- Missing issuer or audience validation

## Secrets and PII

- API keys, tokens, or credentials in code, config, or logs
- Secrets exposed to clients or leaked through environment handling
- Excessive logging of PII or sensitive payloads
- Missing masking in logs or error messages

## Supply Chain and Dependencies

- Unpinned dependencies allowing risky updates
- Dependency confusion risks
- Imports from untrusted sources without integrity controls
- Outdated dependencies with known vulnerabilities

## CORS and Headers

- Overly permissive CORS configuration
- Missing security headers such as CSP or X-Frame-Options
- Exposed internal headers or stack traces

## Runtime Risks

- Unbounded loops, recursion, or large in-memory buffers
- Missing timeouts, retries, or rate limits on external calls
- Blocking operations on the request path
- Resource exhaustion such as file handles, connections, or memory
- ReDoS-prone regular expressions

## Cryptography

- Weak algorithms for security-sensitive purposes
- Hard-coded IVs or salts
- Encryption without authentication
- Insufficient key length

## Race Conditions

Race conditions often cause intermittent bugs and security issues. Review with concurrency in mind.

### Shared State Access

- Multiple threads, goroutines, workers, or async tasks touching shared state without synchronization
- Global state or singletons modified concurrently
- Lazy initialization without proper locking
- Non-thread-safe collections used in concurrent paths

### Check-Then-Act (TOCTOU)

- `if (exists) then use` patterns without atomicity
- Authorization or balance checks followed by a separate write step
- File existence checks followed by file operations
- Inventory or quota checks followed by allocation

### Database Concurrency

- Missing optimistic locking or version checks
- Missing pessimistic locking where required
- Read-modify-write sequences without transaction safety
- Counter increments without atomic operations
- Concurrent inserts that rely on uniqueness without proper guarantees

### Distributed Systems

- Missing distributed locks for shared resources
- Leader election race conditions
- Cache invalidation races
- Event ordering assumptions without sequencing guarantees
- Split-brain style failure modes

### Questions to Ask

- "What happens if two requests hit this code at the same time?"
- "Is this operation atomic or interruptible?"
- "What shared state does this code touch?"
- "How does this behave under load or retry?"

## Data Integrity

- Missing transactions, partial writes, or inconsistent state updates
- Weak validation before persistence
- Missing idempotency for retryable operations
- Lost updates under concurrent modification
