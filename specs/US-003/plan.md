# Implementation Plan: US-003
## Combined Keyword Search and Attribute Filter State in Shareable URL

> **Policy version**: `ace-spec-format/v1`
> **Run ID**: `3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2`
> **Project ID**: `61393f69-dfc8-40ff-8342-28015c6b565c`
> **Story ID**: `US-003`
> **Accepted Impact Hash**: `null`
> **Design status**: ⚠️ DEGRADED — see Open Items below.

---

## Technical Context

### Services in Scope

| Service | Change | Notes |
|---|---|---|
| SVC-003 — Next.js Front-End Application | **Modified** | All URL state management work is contained within this service. |
| SVC-002 — BFF Integration Layer | **No change** | URL state management is entirely client-side within SVC-003; no new BFF API endpoints or data model changes required. |
| SVC-004 — Async Queue Worker | **No change** | URL state management is entirely client-side within SVC-003; no queue message types, delivery logic, or worker responsibilities affected. |

### Unresolved References

The following references were present in the approved specification but could not be resolved to confirmed provider-scanned artifacts. Implementation decisions must be grounded in these references once resolved:

- `service:SVC-003`, `service:SVC-002`, `service:SVC-004`
- `artifact-SVC-003-c3`, `artifact-SVC-003-c4`, `artifact-SVC-003-api_spec`
- `artifact-SVC-002-c3`, `artifact-SVC-002-c4`, `artifact-SVC-002-api_spec`
- `artifact-SVC-004-c3`, `artifact-SVC-004-c4`, `artifact-SVC-004-api_spec`
- `story:US-003`
- `ac:US-003-ac-1`, `ac:US-003-ac-2`, `ac:US-003-ac-3`, `ac:US-003-ac-4`, `ac:US-003-ac-5`, `ac:US-003-ac-6`, `ac:US-003-ac-7`, `ac:US-003-ac-8`
- `fr:FR-001`, `fr:FR-002`, `fr:FR-003`, `fr:FR-004`, `fr:FR-005`, `fr:FR-006`, `fr:FR-007`, `fr:FR-008`
- `run:3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2`
- `project:61393f69-dfc8-40ff-8342-28015c6b565c`

---

## Design Steps

**STEP 1** [SVC-003 / US-003-ac-4 / FR-004]: Implement `URLStateInitialiser` component in SVC-003. On initial page load (server render), read all URL query parameters from the Next.js request context (`useSearchParams` / page props). Extract the `q` keyword parameter and all attribute filter parameters. Pass them as initialisation props to `KeywordSearchInput` and `AttributeFilterPanel` so that UI state fully reflects the URL without a subsequent navigation event.
_Ground_: `service:SVC-003`; `artifact-SVC-003-c3`; `artifact-SVC-003-c4`.

**STEP 2** [SVC-003 / US-003-ac-3 / FR-003]: Implement `URLSearchState` TypeScript model and `URLQueryStringComposer` utility module in SVC-003. `URLSearchState` holds `{ keyword: string | null; filters: Record<string, string | string[]> }`. `URLQueryStringComposer` is a pure function that accepts a `URLSearchState` and returns a `URLSearchParams` instance with all active parameters merged, correctly adding, updating, or removing individual parameters without disturbing unrelated ones.
_Ground_: `service:SVC-003`; `artifact-SVC-003-c3`.

**STEP 3** [SVC-003 / US-003-ac-1, US-003-ac-2, US-003-ac-3, US-003-ac-8 / FR-001, FR-002, FR-003, FR-008]: Implement `useURLSearchState` custom React hook in SVC-003. The hook reads current keyword and filter state from Next.js `useSearchParams`, exposes typed setters for keyword and filters, and on every setter invocation calls `URLQueryStringComposer` to produce the merged query string, then invokes `router.push` (or `router.replace`) with `{ shallow: true }` so the URL updates without a full server-side reload and the React component tree is updated in place.
_Ground_: `service:SVC-003`; `artifact-SVC-003-c3`; `artifact-SVC-003-c4`.

**STEP 4** [SVC-003 / US-003-ac-1, US-003-ac-5, US-003-ac-7 / FR-001, FR-005, FR-007]: Integrate `KeywordSearchInput` island component in SVC-003 with `useURLSearchState`. Wire the input's `onChange` and `onClear` handlers to the hook's `setKeyword` setter. Verify that: (a) typing updates only the `q` parameter while leaving filter parameters unchanged (FR-005); (b) clearing the keyword removes the `q` parameter while preserving all active filter parameters (FR-007).
_Ground_: `service:SVC-003`; `artifact-SVC-003-c3`.

**STEP 5** [SVC-003 / US-003-ac-2, US-003-ac-6 / FR-002, FR-006]: Integrate `AttributeFilterPanel` island component in SVC-003 with `useURLSearchState`. Wire the panel's `onFilterApply` and `onFilterRemove` handlers to the hook's `setFilters` setter. Verify that: (a) applying a filter adds its parameter to the URL alongside any existing `q` parameter (FR-002); (b) clearing all filters removes all filter parameters while preserving the `q` parameter (FR-006).
_Ground_: `service:SVC-003`; `artifact-SVC-003-c3`.

**STEP 6** [SVC-003 / US-003-ac-3 / FR-003]: Validate combined state synchronisation in SVC-003. Write integration tests covering the scenario where both keyword and one or more attribute filters are simultaneously active: assert that the composed URL query string contains both the `q` parameter and all active filter parameters, and that mutating either value produces a correctly merged shallow-routed URL update without dropping the other set of parameters.
_Ground_: `service:SVC-003`; `artifact-SVC-003-c3`; `artifact-SVC-003-c4`.

**STEP 7** [SVC-003 / US-003-ac-8 / FR-008]: Confirm shallow routing enforcement across all URL state transitions in SVC-003. Audit every `router.push`/`router.replace` call introduced in Steps 3–5 to assert the `shallow` flag is set. Add a lint rule or test assertion that no URL state mutation in the `useURLSearchState` hook or its consumers triggers a full server-side reload.
_Ground_: `service:SVC-003`; `artifact-SVC-003-c3`.

**STEP 8** [SVC-002 — no change]: Confirm SVC-002 (BFF Integration Layer) requires no modifications for US-003. URL state management is entirely client-side within SVC-003 and does not introduce new BFF API endpoints or data model changes. Carry forward unchanged.
_Ground_: `service:SVC-002`; `artifact-SVC-002-c3`; `artifact-SVC-002-c4`.

**STEP 9** [SVC-004 — no change]: Confirm SVC-004 (Async Queue Worker) requires no modifications for US-003. URL state management is entirely client-side within SVC-003 and does not affect queue message types, delivery logic, or worker responsibilities. Carry forward unchanged.
_Ground_: `service:SVC-004`; `artifact-SVC-004-c3`; `artifact-SVC-004-c4`.

**STEP 10** [SVC-003 / degraded disclosure]: Mark design degraded. The generated_context artifacts for SVC-003 (`artifact-SVC-003-c3`, `artifact-SVC-003-c4`, `artifact-SVC-003-api_spec`), SVC-002 (`artifact-SVC-002-c3`, `artifact-SVC-002-c4`, `artifact-SVC-002-api_spec`), and SVC-004 (`artifact-SVC-004-c3`, `artifact-SVC-004-c4`, `artifact-SVC-004-api_spec`) were all auto-generated but returned empty strings, providing no usable C3/C4/API-spec grounding content. The design is therefore grounded solely in the service identification manifest and the functional specification. Component-level detail for Steps 1–7 is derived from the functional requirements and service responsibilities, not from confirmed provider-scanned architecture artifacts.

---

## Contract Changes

No material API, event, or data contract change is required for US-003. All new and modified components are internal to SVC-003 and operate entirely client-side. (`contract_change_required: false`)

---

## Security and Quality Obligations

No security obligations or quality attribute obligations were specified in the approved implementation specification for US-003. This section is intentionally empty; no content has been fabricated.

---

## Verification Plan

All test obligations are of type **acceptance**, required before **release**, in the **ci** environment.

- **US-003-ac-1** (`3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-1`): The Next.js Frontend Shell must synchronise the URL query string whenever the user types or clears a keyword, using a shallow router push/replace so that the search query parameter appears in the address bar without triggering a full page reload.
- **US-003-ac-2** (`3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-2`): The Next.js Frontend Shell must synchronise the URL query string whenever the user applies or removes one or more attribute filters, using a shallow router push/replace so that each active filter parameter appears in the address bar alongside any existing keyword parameter without triggering a full page reload.
- **US-003-ac-3** (`3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-3`): When both a keyword and one or more attribute filters are active simultaneously, the Next.js Frontend Shell must compose a single URL query string that contains the search query parameter and all active filter parameters at the same time, and must keep both in sync as either value changes.
- **US-003-ac-4** (`3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-4`): On initial page load, the Next.js Frontend Shell must read all query parameters present in the URL and use them to initialise both the keyword search input component and the attribute filter components to their corresponding values, so that the UI state fully reflects the URL without requiring a subsequent navigation.
- **US-003-ac-5** (`3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-5`): When the user changes the keyword while filters are active, the Next.js Frontend Shell must update the search query parameter in the URL while leaving all active filter parameters unchanged.
- **US-003-ac-6** (`3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-6`): When the user clears all filters while a keyword is active, the Next.js Frontend Shell must remove all filter parameters from the URL while preserving the search query parameter.
- **US-003-ac-7** (`3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-7`): When the user clears the keyword while filters are active, the Next.js Frontend Shell must remove the search query parameter from the URL while preserving all active filter parameters.
- **US-003-ac-8** (`3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-8`): All URL state transitions described above must be performed via the Next.js shallow routing mechanism so that the page does not perform a full server-side reload and the React component tree is updated in place.

---

## Open Items & Gap Disclosures

> ⚠️ Resolve or explicitly accept the following implementation-readiness items before proceeding to implementation.

1. **Degraded grounding**: All generated_context artifacts for SVC-003 (`artifact-SVC-003-c3`, `artifact-SVC-003-c4`, `artifact-SVC-003-api_spec`), SVC-002 (`artifact-SVC-002-c3`, `artifact-SVC-002-c4`, `artifact-SVC-002-api_spec`), and SVC-004 (`artifact-SVC-004-c3`, `artifact-SVC-004-c4`, `artifact-SVC-004-api_spec`) were auto-generated but returned empty strings, providing no usable C3/C4/API-spec grounding content. The design is grounded solely in the service identification manifest and functional specification; component-level detail is derived from functional requirements, not from confirmed provider-scanned architecture artifacts. The design is marked degraded accordingly.
2. **Filter parameter names unspecified**: The exact set of URL query parameter names used for attribute filters (beyond the `q` keyword parameter) is not specified in the story or acceptance criteria. If filter parameter names collide with reserved Next.js or application query parameters, the `URLQueryStringComposer` logic may require adjustment.
3. **EP-001 boundary unconfirmed**: The story is explicitly out of scope for the individual filter controls themselves (EP-001) and analytics (EP-003), but the boundary between `URLSearchState` initialisation and EP-001 filter component contracts has not been formally confirmed. If EP-001 filter components expose a different prop interface than assumed, the `URLStateInitialiser` hydration props may need renegotiation.
4. **SVC-002 / SVC-004 no-change caveat**: SVC-002 and SVC-004 are confirmed as no-change for US-003, but this assessment is based solely on the service manifest and functional spec, not on confirmed provider-scanned C3/C4 artifacts. If those services have undisclosed client-side integration points with SVC-003 URL state, the no-change determination may need revisiting.
