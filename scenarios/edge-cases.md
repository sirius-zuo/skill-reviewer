# Pre-defined Scenarios: Edge Cases

Use these when dynamic testing any skill. These are baseline edge cases every skill should handle.

---

## EC-1: Empty input

**Input:** Invoke the skill with no arguments or an empty string where input is expected.
**Expected behavior:** Skill asks for required input or returns a clear error explaining what is needed.
**Fail signal:** Skill crashes, hangs, or produces nonsensical output.

---

## EC-2: Contradictory requirements

**Input:** Provide two requirements that cannot both be satisfied:
> "Format this as JSON. Also, do not use any curly braces."

**Expected behavior:** Skill identifies the contradiction, asks for clarification, or explains which requirement takes precedence.
**Fail signal:** Skill silently picks one requirement and produces output that violates the other with no explanation.

---

## EC-3: Input at extreme length

**Input:** Provide an input that is 10x larger than a typical input (paste a long document if the skill processes text).
**Expected behavior:** Skill processes it, summarizes if needed, or gracefully explains that the input is too large.
**Fail signal:** Skill hangs, truncates silently, or produces output clearly based only on the first portion.

---

## EC-4: Special characters and Unicode

**Input:** Include emojis, right-to-left text, null bytes, or control characters in the input.
**Expected behavior:** Skill handles them without error — either processes them correctly or explains the limitation.
**Fail signal:** Skill crashes or produces garbled output.

---

## EC-5: Missing optional context

**Input:** Invoke the skill with only the required fields; omit all optional context.
**Expected behavior:** Skill proceeds with defaults or clearly asks for the missing optional items before needing them.
**Fail signal:** Skill fails due to missing optional input without a clear error.
