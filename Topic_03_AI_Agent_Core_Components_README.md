# Topic 03 - AI Agent Core Components | README

## Overview
This n8n workflow demonstrates a conversational AI agent with an LLM, short-term memory, a Calculator tool, and structured JSON output. The workflow was tested in a multi-turn chat session with John and an apple-count scenario.

## Workflow architecture
When chat message received -> AI Agent
- Chat Model: OpenAI Chat Model node using `qwen/qwen-2.5-7b-instruct` through configured gateway credentials (as shown during setup).
- Memory: Simple Memory (the node available in this n8n installation).
- Tool: Calculator.
- Output Parser: Structured Output Parser with required fields `resolved_math_value`, `calculation_steps`, `user_feedback`.

## Key configuration
- Prompt input: `{{ $json.chatInput }}`.
- Temperature: 0.0; maximum model tokens: 1000 (configured in screenshots during setup).
- Output schema: `resolved_math_value` (number or null), `calculation_steps` (string), `user_feedback` (string).
- System instructions: retain conversation context, use Calculator for math, address the user by name, and return only schema-compatible JSON.
- The supplied Topic 3 guide specifies a context window of 5 messages and max iterations of 5; these specific values are not independently visible in the final screenshots and should be checked before submission.

## Validation results
| Test | Input | Observed result | Status |
|---|---|---|---|
| T1: Initial memory | Hi, my name is John. I have 15 apples. | Agent addressed John and acknowledged 15 apples; `resolved_math_value` null. | PASS |
| T2: Math and memory | I give 3 apples to Sarah and buy 5 more. How many apples do I have left? | `resolved_math_value`: 17; `calculation_steps`: `15 - 3 + 5 = 17`; addressed John. Calculator node shown executed. | PASS |
| T3: Memory recall | Hi, do you remember my name and how many apples I have now? | Agent recalled John and 17 apples; no new calculation. | PASS |

## Observed JSON outputs
T1:
```json
{"output":{"resolved_math_value":null,"calculation_steps":"No calculation requested","user_feedback":"Hello John! I remember you have 15 apples."}}
```
T2:
```json
{"output":{"resolved_math_value":17,"calculation_steps":"15 - 3 + 5 = 17","user_feedback":"John, after giving 3 apples to Sarah and buying 5 more, you have 17 apples left."}}
```
T3:
```json
{"output":{"resolved_math_value":null,"calculation_steps":"No calculation requested","user_feedback":"Hello John! I remember you have 17 apples now."}}
```

## Troubleshooting performed
1. Initial model request failed with `gpt-5-mini` via Responses API; switched to working Qwen model and disabled Responses API.
2. Structured parser rejected free-form responses; configured schema and reinforced JSON-only system instructions.
3. Auto-Fix initially requested an additional model connection. Subsequent screenshots show successful parsing; the optional auto-fix model connection should be reviewed if Auto-Fix remains enabled.

## Evidence and submission notes
The accompanying evidence PDF contains user-provided screenshots showing the workflow and test results. This README documents observed behavior; it does not assert a final assessment grade. Confirm the exact memory window and iteration settings against the Topic 3 implementation guide before submission.
