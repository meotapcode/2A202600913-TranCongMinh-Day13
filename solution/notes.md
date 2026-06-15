# Diagnosis scratchpad

Run the practice simulator, read YOUR telemetry, and note what you find.
Fault classes to hunt: error_spike · latency_spike · cost_blowup · quality_drift · infinite_loop · tool_failure · pii_leak · prompt_injection.

| symptom (from telemetry) | which requests | suspected cause | config fix? | wrapper fix? |
|---|---|---|---|---|
| **latency_spike** (P95 > 10s) | Multiple repeat queries | Cache was disabled and high temperature caused long generation trails. | `cache: enabled`, `temperature: 0.2` | Implemented global cache lookup. |
| **error_spike** (tool errors) | Intermittent failures | Opaque agent tools fail randomly due to tool error rate parameter. | `retry: max_attempts: 5` | Status-checking wrapper retries. |
| **cost_blowup** (48k+ tokens) | Large session histories | Premium price tier, verbose system prompts, and large context size. | `model_price_tier: "economy"`, `verbose_system: false`, `context_size: 2` | - |
| **quality_drift** (drifted coupon logic) | Long multi-turn sessions | Accumulating history in context window corrupted LLM states. | `context_reset_every: 4` | - |
| **infinite_loop** (`status="loop"`) | Stalled tool calls | loop_guard disabled, or capitalized product names like "MacBook" triggered tool failure loops. | `loop_guard: true` | Normalize product capitalization to lowercase; set wrapper retry. |
| **tool_failure** (diacritic errors) | VN diacritic cities | Special characters mismatched database entries. | `normalize_unicode: true` | - |
| **pii_leak** (customer contacts) | Email/Phone echoed | No constraints in prompt and redact_pii disabled. | `redact_pii: true` | Regex PII redaction (fixing false IP redaction bugs). |
| **prompt_injection** (fake prices) | GHI CHU KHACH orders | Model treated order notes as active override instructions. | - | Neuter note prefixes in input; add strict prompt instructions. |
