# Tasks: US-003
## Combined Keyword Search and Attribute Filter State in Shareable URL

> **Policy version**: `ace-spec-format/v1`
> **Run ID**: `3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2`
> **Story ID**: `US-003`
> **Design status**: ⚠️ DEGRADED — see plan.md Open Items.

---

### Implementation Tasks

- [ ] **T001** — [SVC-003 / FR-001 / US-003-ac-1] Implement keyword URL sync: on every keyword type or clear event in `KeywordSearchInput`, invoke a shallow router push or replace so that the `q` search query parameter appears in the address bar without triggering a full page reload.
  _Grounding_: `service:SVC-003`, `fr:FR-001`, `ac:US-003-ac-1`

- [ ] **T002** — [SVC-003 / FR-002 / US-003-ac-2] Implement attribute filter URL sync: on every filter apply or remove event in `AttributeFilterPanel`, invoke a shallow router push or replace so that each active filter parameter appears in the address bar alongside any existing `q` parameter without triggering a full page reload.
  _Grounding_: `service:SVC-003`, `fr:FR-002`, `ac:US-003-ac-2`

- [ ] **T003** — [SVC-003 / FR-003 / US-003-ac-3] Implement `URLSearchState` TypeScript model (`{ keyword: string | null; filters: Record<string, string | string[]> }`) and `URLQueryStringComposer` pure utility function that merges keyword and filter key-value pairs into a single `URLSearchParams` object, keeping both sets of parameters in sync whenever either value changes.
  _Grounding_: `service:SVC-003`, `fr:FR-003`, `ac:US-003-ac-3`

- [ ] **T004** — [SVC-003 / FR-004 / US-003-ac-4] Implement `URLStateInitialiser` server/client boundary component: on initial page load, read all query parameters from the Next.js request context (`useSearchParams` / page props) and pass them as initialisation props to `KeywordSearchInput` and `AttributeFilterPanel` so that UI state fully reflects the URL without a subsequent navigation event.
  _Grounding_: `service:SVC-003`, `fr:FR-004`, `ac:US-003-ac-4`

- [ ] **T005** — [SVC-003 / FR-005 / US-003-ac-5] Implement keyword-only update path in `useURLSearchState`: when the user changes the keyword while one or more attribute filters are active, update only the `q` parameter in the URL, leaving all active filter parameters unchanged.
  _Grounding_: `service:SVC-003`, `fr:FR-005`, `ac:US-003-ac-5`

- [ ] **T006** — [SVC-003 / FR-006 / US-003-ac-6] Implement filter-clear path in `useURLSearchState`: when the user clears all attribute filters while a keyword is active, remove all filter parameters from the URL while preserving the `q` parameter.
  _Grounding_: `service:SVC-003`, `fr:FR-006`, `ac:US-003-ac-6`

- [ ] **T007** — [SVC-003 / FR-007 / US-003-ac-7] Implement keyword-clear path in `useURLSearchState`: when the user clears the keyword while one or more attribute filters are active, remove the `q` parameter from the URL while preserving all active filter parameters.
  _Grounding_: `service:SVC-003`, `fr:FR-007`, `ac:US-003-ac-7`

- [ ] **T008** — [SVC-003 / FR-008 / US-003-ac-8] Enforce shallow routing across all URL state transitions: audit every `router.push`/`router.replace` call in `useURLSearchState` and its consumers to assert the `shallow: true` flag is set; add a lint rule or test assertion that no URL state mutation triggers a full server-side reload.
  _Grounding_: `service:SVC-003`, `fr:FR-008`, `ac:US-003-ac-8`

- [ ] **T009** — [SVC-003] Implement `useURLSearchState` custom React hook: encapsulate all read/write logic for the combined URL query string via Next.js `useRouter`/`useSearchParams`; expose typed setters for keyword and filters; on every setter invocation call `URLQueryStringComposer` and invoke shallow router push/replace.
  _Grounding_: `service:SVC-003`, `ac:US-003-ac-1`, `ac:US-003-ac-2`, `ac:US-003-ac-3`, `ac:US-003-ac-8`

- [ ] **T010** — [SVC-002 — no change] Confirm SVC-002 (BFF Integration Layer) requires no modifications for US-003; document confirmation.
  _Grounding_: `service:SVC-002`

- [ ] **T011** — [SVC-004 — no change] Confirm SVC-004 (Async Queue Worker) requires no modifications for US-003; document confirmation.
  _Grounding_: `service:SVC-004`

---

### Verification Tasks

- [ ] **T012** — Verify US-003-ac-1 (`3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-1`): The Next.js Frontend Shell must synchronise the URL query string whenever the user types or clears a keyword, using a shallow router push/replace so that the search query parameter appears in the address bar without triggering a full page reload.

- [ ] **T013** — Verify US-003-ac-2 (`3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-2`): The Next.js Frontend Shell must synchronise the URL query string whenever the user applies or removes one or more attribute filters, using a shallow router push/replace so that each active filter parameter appears in the address bar alongside any existing keyword parameter without triggering a full page reload.

- [ ] **T014** — Verify US-003-ac-3 (`3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-3`): When both a keyword and one or more attribute filters are active simultaneously, the Next.js Frontend Shell must compose a single URL query string that contains the search query parameter and all active filter parameters at the same time, and must keep both in sync as either value changes.

- [ ] **T015** — Verify US-003-ac-4 (`3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-4`): On initial page load, the Next.js Frontend Shell must read all query parameters present in the URL and use them to initialise both the keyword search input component and the attribute filter components to their corresponding values, so that the UI state fully reflects the URL without requiring a subsequent navigation.

- [ ] **T016** — Verify US-003-ac-5 (`3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-5`): When the user changes the keyword while filters are active, the Next.js Frontend Shell must update the search query parameter in the URL while leaving all active filter parameters unchanged.

- [ ] **T017** — Verify US-003-ac-6 (`3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-6`): When the user clears all filters while a keyword is active, the Next.js Frontend Shell must remove all filter parameters from the URL while preserving the search query parameter.

- [ ] **T018** — Verify US-003-ac-7 (`3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-7`): When the user clears the keyword while filters are active, the Next.js Frontend Shell must remove the search query parameter from the URL while preserving all active filter parameters.

- [ ] **T019** — Verify US-003-ac-8 (`3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-8`): All URL state transitions described above must be performed via the Next.js shallow routing mechanism so that the page does not perform a full server-side reload and the React component tree is updated in place.

---

### Open Item Resolution Tasks

- [ ] **T020** — Resolve or explicitly accept degraded grounding: confirm or obtain usable C3/C4/API-spec artifacts for SVC-003 (`artifact-SVC-003-c3`, `artifact-SVC-003-c4`, `artifact-SVC-003-api_spec`), SVC-002 (`artifact-SVC-002-c3`, `artifact-SVC-002-c4`, `artifact-SVC-002-api_spec`), and SVC-004 (`artifact-SVC-004-c3`, `artifact-SVC-004-c4`, `artifact-SVC-004-api_spec`).

- [ ] **T021** — Resolve or explicitly accept filter parameter name gap: define and document the exact set of URL query parameter names used for attribute filters; verify no collision with reserved Next.js or application query parameters.

- [ ] **T022** — Resolve or explicitly accept EP-001 boundary gap: formally confirm the prop interface exposed by EP-001 filter components and validate that `URLStateInitialiser` hydration props are compatible.

- [ ] **T023** — Resolve or explicitly accept SVC-002/SVC-004 no-change caveat: confirm via provider-scanned C3/C4 artifacts that neither service has undisclosed client-side integration points with SVC-003 URL state.
