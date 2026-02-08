---
id: agent-8
phase: 4
scope: resource-lifecycle
---

# Resource & Lifecycle Management

<!-- @include: ../_shared/philosophy.md -->

## Scope

Analyze resource management and lifecycle patterns including event listener cleanup, async resource handling, and proper disposal of subscriptions and handles.

## Analysis Criteria

### 1. Event Listener Cleanup

- addEventListener without removeEventListener
- Missing unsubscribe on component unmount
- Event handlers not cleaned up on disposal
- Memory leaks from persistent listeners

### 2. Async Resource Cleanup

- requestAnimationFrame without cancel
- setTimeout/setInterval without clear
- Streams not properly closed
- Pending fetch requests on unmount

### 3. IPC & Message Handling

- IPC listeners not removed
- Message handlers orphaned
- Channel subscriptions never cleaned
- Reply handlers left dangling

### 4. Subscription Management

- Observable subscriptions not unsubscribed
- EventEmitter listeners not removed
- WebSocket handlers not cleaned up
- Pub/sub subscriptions orphaned

### 5. Lifecycle Pattern Improvements

- Dispose pattern not implemented
- Missing cleanup phase in lifecycle
- State machine for complex lifecycles
- Resource pooling opportunities

## Output Format

<!-- @include: ../_shared/finding-schema.md -->

### Violation Types for This Agent

| Code | Meaning |
|------|---------|
| `LIFE_EVENT_LEAK` | Event listener not cleaned up |
| `LIFE_TIMER_LEAK` | Timer/RAF not cancelled |
| `LIFE_STREAM_LEAK` | Stream not closed |
| `LIFE_IPC_ORPHAN` | IPC handler not removed |
| `LIFE_SUB_ORPHAN` | Subscription not unsubscribed |
| `LIFE_DISPOSE_MISSING` | Missing dispose implementation |
| `LIFE_PATTERN_IMPROVE` | Lifecycle pattern could be better |

## Examples

### Finding Example

```json
{
  "id": "agent-8-001",
  "file": "src/components/real-time-chart.ts",
  "line": 23,
  "violation": "LIFE_TIMER_LEAK",
  "severity": "HIGH",
  "description": "setInterval created but never cleared, continues after component unmount",
  "currentPattern": "setInterval(() => this.update(), 1000)",
  "optimalPattern": "this.intervalId = setInterval(...); dispose() { clearInterval(this.intervalId); }",
  "migrationPath": "1. Store interval ID, 2. Add dispose method, 3. Clear in dispose, 4. Call dispose on unmount",
  "recommendation": "Store interval ID and clear it in dispose/unmount lifecycle method"
}
```
