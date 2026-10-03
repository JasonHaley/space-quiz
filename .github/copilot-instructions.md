# Orbit space quiz

## Running and checking the app

Open `index.html` directly in a browser using a `file://` URL. On Windows, from the project directory:

```powershell
Start-Process .\index.html
```

The app is a single offline HTML file with embedded CSS and JavaScript. Keep it usable without a server, package installation, external fonts, assets, or network requests.

There are no configured build, lint, or automated test commands. For a focused browser check, reload the file, Tab to the second answer on question 1, and press Enter: Yuri Gagarin should produce green feedback, a score of `1 / 10`, progress of one answered question, disabled answer buttons, and focus on Next.

For a full smoke check, use Tab/Shift+Tab and Enter/Space through all ten questions, including an incorrect answer. Check red shake feedback, the revealed correct answer, unchanged score on a wrong answer, results totals and emoji, and restart resetting score, progress, feedback, and answer locking. Check a 320px viewport, both color schemes, and reduced motion. Browser automation must exercise actual keyboard events rather than only calling application functions.

## Architecture and state

`index.html` contains the complete app: theme/layout styles, static quiz and results sections, an inline SVG illustration, question data, and imperative DOM updates. There is no framework or persistence; reloading starts a new quiz.

The state machine uses `current` (zero-based question index), `score`, `answered`, and `locked`. `renderQuestion()` creates answer buttons and disables Next. `selectAnswer()` locks immediately, increments answered and optionally score, disables every answer, reveals feedback, updates progress, and enables Next. The Next handler advances or calls `showResults()`. Restart resets state and renders question 1.

Progress measures questions **answered**, not the question currently displayed. Preserve both the `locked` guard and disabled buttons to prevent repeat scoring.

## Codebase conventions

- Question records contain `category`, `question`, four `answers`, a zero-based `correct` index, and `explanation`. Keep the ten-question format and factual explanatory feedback. If changing the count, also update static HTML totals, progress maximum, results text, and reaction thresholds; these are not all derived from `questions.length`.
- DOM IDs are shared contracts between HTML, `el(id)`, CSS, and accessibility attributes. Build dynamic content with `createElement`, `textContent`, `append`, and `replaceChildren`, following the existing rendering code.
- Preserve deliberate focus transitions: no forced focus on initial load; question heading after Next/restart; Next after answering; results heading on completion. Headings use `tabindex="-1"` and visible focus outlines. Score and feedback use polite live regions, and progress exposes ARIA values.
- Answer states use `.correct`, `.wrong`, and `.dimmed`; feedback uses `.incorrect`. Keep explanatory text and check/cross indicators alongside green/red colors. Results emoji have descriptive accessible labels.
- Use existing CSS variables for colors. Light values live in `:root`; dark values override them in `prefers-color-scheme: dark`. All motion is disabled by the existing `prefers-reduced-motion` rule.
- Keep the centered `.shell` narrow (548px maximum), the sans-serif body, and shared mission-console styling for `#title` and `#question`. Scope question-specific sizing to `#question` so the results heading retains its own style. Decorative SVGs and the mission label are hidden from assistive technology.
