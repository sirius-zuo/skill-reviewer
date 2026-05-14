# Report Assembly Instructions

You are assembling the final HTML report from all sub-agent JSON results. You have:
- An array of skill result JSON objects (one per skill)
- The `support/report-template.html` file

---

## Step 1: Compute Rollup Values

```
overall_score = average of all skill overall_scores (2 decimal places)
overall_risk_level = worst risk level across all skills
  (Critical > High > Medium > Low)
critical_rec_count = sum of recommendations with priority "critical" across all skills
important_rec_count = sum of recommendations with priority "important" across all skills
```

---

## Step 2: Build the Dashboard Rows

For each skill, produce one `<tr>` with score cells for all 13 categories:

```html
<tr>
  <td><strong>{{skill_name}}</strong><br><small>{{skill_path}}</small></td>
  <td><span class="risk-badge risk-{{risk_class}}">{{risk_level}}</span></td>
  <td class="score-cell {{score_class(overall_score)}}">{{overall_score}}</td>
  <!-- one <td> per category using score_cell_html() below -->
</tr>
```

`score_cell_html(category_key, scores, na_categories)`:
- If category is in `na_categories`: `<td class="score-cell score-grey">N/A</td>`
- If score ≥ 8: `<td class="score-cell score-green">{{score}}</td>`
- If score 6–7: `<td class="score-cell score-amber">{{score}}</td>`
- If score ≤ 5: `<td class="score-cell score-red">{{score}}</td>`

`score_class(score)`: `score-green` if ≥8, `score-amber` if 6–7, `score-red` if ≤5.

Column order must match the dashboard table headers: Scope, Trigger, Prompt, Logic, Tools, Compose, Context, Tests, Reliability, Safety, Output, Perf, Autonomy.

---

## Step 3: Build Per-Skill Sections

For each skill, produce one `<details>` block:

```html
<details>
  <summary>
    <span class="risk-badge risk-{{risk_class}}">{{risk_level}}</span>
    {{skill_name}} — {{overall_score}}/10
  </summary>
  <div class="details-body">
    <p><strong>Risk rationale:</strong> {{risk_rationale}}</p>

    <h3 style="margin-top:1rem">Scorecard</h3>
    <table>
      <thead><tr><th>Category</th><th>Score</th><th>Key Issues</th></tr></thead>
      <tbody>
        <!-- applicable categories: one row per category -->
        <!-- N/A categories shown with grey score cell and "Not applicable" in issues column -->
      </tbody>
    </table>

    <h3 style="margin-top:1rem">Recommendations</h3>
    <!-- Sort: critical first, then important, then suggested -->
    <!-- Each recommendation as a div with class rec rec-{{priority}} -->
    <!-- Inside: div.rec-label with "🔴 Critical" / "🟡 Important" / "🟢 Suggested" -->
    <!-- Followed by the recommendation text and gate reference -->

    <!-- Dynamic test results section — only include if dynamic_test_results is non-empty -->
    <h3 style="margin-top:1rem">Dynamic Test Results</h3>
    <table>
      <thead><tr><th>Scenario</th><th>Category</th><th>Result</th><th>Observed</th></tr></thead>
      <tbody>
        <!-- one row per scenario result -->
      </tbody>
    </table>
  </div>
</details>
```

---

## Step 4: Build Cross-Skill Patterns

Find patterns that appear in 2+ skills:
- Categories that are amber or red in 2+ skills → `"N skills have weak [category display name]"`
- Recommendations with the same gate failure in 2+ skills → `"N skills need: [recommendation summary]"`

Format each as a `<li>` item. If no patterns exist, output `<li>No cross-skill patterns identified.</li>`.

---

## Step 5: Fill Template and Save

1. Read `support/report-template.html`.
2. Replace every `{{placeholder}}` with the computed value.
3. The `{{dashboard_rows}}` placeholder receives the full HTML of all `<tr>` elements.
4. The `{{skill_sections}}` placeholder receives the full HTML of all `<details>` blocks.
5. `{{overall_risk_class}}` is the lowercase risk level: `low`, `medium`, `high`, or `critical`.

Save the completed HTML to: `{{output_path}}/skill-review-{{YYYY-MM-DD}}.html`
- `output_path` comes from the configuration provided by the dispatcher (defaults to `docs/review/` inside the reviewed skill's root)
- Use today's date for `{{YYYY-MM-DD}}`

Create the output directory if it does not exist.

Print the absolute path of the saved file to the terminal.
