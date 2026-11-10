---
name: witness-repro
description: Record a user session with @three-ws/witness and compile the trace into a Playwright spec that fails until the reported bug is fixed. Use when asked to reproduce a user-reported web bug, or to add always-on privacy-safe bug capture to a site.
---

# Witness: session to failing test

`@three-ws/witness` (ESM, no dependencies, about 6 KB, Node >= 18 for compiling) records intent instead of pixels: navigation, clicks, field fills (length only), failed requests and uncaught errors. `compileToPlaywright` turns that trace into a spec that asserts the failure is gone, so it is red until the bug is fixed.

## Install and record (in the browser app)

```bash
npm install @three-ws/witness
```

```js
import { witness } from '@three-ws/witness';

witness.start({ maxEvents: 60, coalesceMs: 900, captureNetwork: true, captureConsole: true, ignore: (path) => path.startsWith('/api/report') });
witness.onFailure(() => showFeedbackPrompt());   // ask at the moment of failure
const trace = witness.trace();                   // send this with the user's report
```

`start()` is idempotent, `stop()` restores every patched global, `hasFailure({ withinMs })` says whether the browser saw a failure.

## Compile a trace (Node or browser)

```js
import { compileToPlaywright, narrate, replayConfidence } from '@three-ws/witness/compile';

const { filename, source, confidence, steps } = compileToPlaywright(trace, { title: 'Export does nothing', baseUrl, reportId });
console.log(narrate(trace));           // the same steps in plain English, for an issue body
console.log(replayConfidence(trace));  // { score, weakest, note }
```

Write `source` to a `*.spec.js` file and run it with Playwright against the app.

## Reading the result

- The spec is red while the bug exists. Fix the app, rerun, expect green.
- If the session recorded no exception and no failed request, the compiler emits the steps plus `test.fail(true, 'Add the assertion this report is really about.')`. Replace that with the assertion the report is about. Do not invent one.
- `replayConfidence` is the weakest locator in the trace (testid 100, id 95, label 85, role 80, text 70, name 65, placeholder 60, class 40, path 10). A low score means add `data-testid` to the primary actions.

## Privacy rules to preserve

- Typed values are never stored (a fill records `text:<length>`). Password, hidden, email, tel and date inputs record nothing.
- URLs lose userinfo and hash. Secret-shaped text is redacted.
- Mark a subtree `data-witness="off"` to exclude it entirely.
- Send `x-witness: handled` on a request whose 4xx/5xx the UI already handles, so it does not trigger the failure prompt.

Other exports: `describeElement(el)`, `toPlaywrightLocator(desc)` (stable locators on their own), `redactText`, `redactUrl`. Subpath imports: `/compile`, `/selector`, `/redact`.
