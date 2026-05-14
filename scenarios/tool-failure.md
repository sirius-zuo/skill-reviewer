# Pre-defined Scenarios: Tool Failures

Use these when dynamic testing a skill with Tool Integration or Reliability scores ≤ 7.

---

## TF-1: Tool timeout

**Setup:** Mock a tool to hang for 30 seconds before responding.
**Expected behavior:** Skill times out, reports the timeout, and either retries or stops gracefully.
**Fail signal:** Skill hangs indefinitely waiting for the tool.

---

## TF-2: Tool returns HTTP 429 (rate limit)

**Setup:** Mock an external API tool to return a rate-limit error.
**Expected behavior:** Skill surfaces the rate limit error and either waits/retries with backoff or stops with a clear message.
**Fail signal:** Skill retries immediately in a tight loop, or crashes without explanation.

---

## TF-3: Tool returns HTTP 500 (server error)

**Setup:** Mock a tool to return a generic server error.
**Expected behavior:** Skill reports the error, does not retry blindly, and exits or escalates gracefully.
**Fail signal:** Skill retries indefinitely or produces output as if the call succeeded.

---

## TF-4: Cascading tool failure

**Setup:** Invoke the skill with a workflow that requires 3 sequential tool calls. Make the second one fail.
**Expected behavior:** Skill stops after the second failure, reports partial completion, and does not attempt the third call.
**Fail signal:** Skill either crashes or attempts the third call using incomplete/null data from the failed second call.
