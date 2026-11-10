# Feature Specification: US-003
## Combined Keyword Search and Attribute Filter State in Shareable URL

> **Policy version**: `ace-spec-format/v1`
> **Run ID**: `3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2`
> **Project ID**: `61393f69-dfc8-40ff-8342-28015c6b565c`
> **Story ID**: `US-003`
> **Accepted Impact Hash**: `null` (no prior impact hash accepted)
> **Design status**: ⚠️ DEGRADED — all generated_context artifacts for SVC-003, SVC-002, and SVC-004 returned empty strings; design is grounded solely in the service identification manifest and functional specification.

---

## Description

Extends URL serialisation so that both the active keyword search term (from EP-002) and the active attribute filter parameters (from EP-001) are simultaneously reflected in the URL. Loading the combined URL fully restores both the search term and the filter state.

**In scope**: combined URL read/write for search term + EP-001 filter params, full state restoration on URL load.
**Out of scope**: the individual filter controls themselves (EP-001), analytics (EP-003).

---

## Behavior

The Next.js Frontend Shell extends URL serialisation so that both the active keyword search term and the active attribute filter parameters are simultaneously and continuously reflected in the browser's address bar via shallow routing. On initial page load the shell reads all query parameters from the URL and uses them to fully restore both the keyword search input and the attribute filter components to their corresponding values without requiring a subsequent navigation. As either the keyword or the filter state changes independently, the shell composes a single, merged query string that preserves whichever parameters remain active and removes whichever parameters have been cleared, all without triggering a full server-side reload or unmounting the React component tree.

---

## User Scenarios & Acceptance Criteria

- **US-003-ac-1**: The Next.js Frontend Shell must synchronise the URL query string whenever the user types or clears a keyword, using a shallow router push/replace so that the search query parameter appears in the address bar without triggering a full page reload.
- **US-003-ac-2**: The Next.js Frontend Shell must synchronise the URL query string whenever the user applies or removes one or more attribute filters, using a shallow router push/replace so that each active filter parameter appears in the address bar alongside any existing keyword parameter without triggering a full page reload.
- **US-003-ac-3**: When both a keyword and one or more attribute filters are active simultaneously, the Next.js Frontend Shell must compose a single URL query string that contains the search query parameter and all active filter parameters at the same time, and must keep both in sync as either value changes.
- **US-003-ac-4**: On initial page load, the Next.js Frontend Shell must read all query parameters present in the URL and use them to initialise both the keyword search input component and the attribute filter components to their corresponding values, so that the UI state fully reflects the URL without requiring a subsequent navigation.
- **US-003-ac-5**: When the user changes the keyword while filters are active, the Next.js Frontend Shell must update the search query parameter in the URL while leaving all active filter parameters unchanged.
- **US-003-ac-6**: When the user clears all filters while a keyword is active, the Next.js Frontend Shell must remove all filter parameters from the URL while preserving the search query parameter.
- **US-003-ac-7**: When the user clears the keyword while filters are active, the Next.js Frontend Shell must remove the search query parameter from the URL while preserving all active filter parameters.
- **US-003-ac-8**: All URL state transitions described above must be performed via the Next.js shallow routing mechanism so that the page does not perform a full server-side reload and the React component tree is updated in place.

---

## Functional Requirements

- **FR-001** [US-003-ac-1]: The Next.js Frontend Shell must synchronise the URL query string on every keyword type or clear event by invoking a shallow router push or replace, causing the search query parameter to appear in the address bar without triggering a full page reload.
- **FR-002** [US-003-ac-2]: The Next.js Frontend Shell must synchronise the URL query string on every attribute filter apply or remove event by invoking a shallow router push or replace, causing each active filter parameter to appear in the address bar alongside any existing keyword parameter without triggering a full page reload.
- **FR-003** [US-003-ac-3]: When both a keyword and one or more attribute filters are simultaneously active, the Next.js Frontend Shell must compose a single URL query string that contains the search query parameter and all active filter parameters concurrently, and must keep both sets of parameters in sync whenever either value changes.
- **FR-004** [US-003-ac-4]: On initial page load, the Next.js Frontend Shell must read all query parameters present in the URL and use them to initialise the keyword search input component and all attribute filter components to their corresponding values, such that the UI state fully reflects the URL without requiring any subsequent navigation event.
- **FR-005** [US-003-ac-5]: When the user changes the keyword while one or more attribute filters are active, the Next.js Frontend Shell must update only the search query parameter in the URL, leaving all active filter parameters unchanged.
- **FR-006** [US-003-ac-6]: When the user clears all attribute filters while a keyword is active, the Next.js Frontend Shell must remove all filter parameters from the URL while preserving the search query parameter.
- **FR-007** [US-003-ac-7]: When the user clears the keyword while one or more attribute filters are active, the Next.js Frontend Shell must remove the search query parameter from the URL while preserving all active filter parameters.
- **FR-008** [US-003-ac-8]: Every URL state transition covered by FR-001 through FR-007 must be executed via the Next.js shallow routing mechanism, ensuring the page does not perform a full server-side reload and the React component tree is updated in place.

---

## Success Criteria (Test Obligations)

All obligations are required before **release** and must pass in the **ci** environment.

| Obligation ID | Criterion | Test Type |
|---|---|---|
| `3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-1` | US-003-ac-1 | acceptance |
| `3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-2` | US-003-ac-2 | acceptance |
| `3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-3` | US-003-ac-3 | acceptance |
| `3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-4` | US-003-ac-4 | acceptance |
| `3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-5` | US-003-ac-5 | acceptance |
| `3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-6` | US-003-ac-6 | acceptance |
| `3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-7` | US-003-ac-7 | acceptance |
| `3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2:test:acceptance:US-003-ac-8` | US-003-ac-8 | acceptance |

---

## Clause Traceability

| Clause ID | Criterion ID | Trust Status | Tier | Load Bearing |
|---|---|---|---|---|
| US-003-ac-1 | US-003-ac-1 | HYPOTHESIS | B | true |
| US-003-ac-2 | US-003-ac-2 | HYPOTHESIS | B | true |
| US-003-ac-3 | US-003-ac-3 | HYPOTHESIS | B | true |
| US-003-ac-4 | US-003-ac-4 | HYPOTHESIS | B | true |
| US-003-ac-5 | US-003-ac-5 | HYPOTHESIS | B | true |
| US-003-ac-6 | US-003-ac-6 | HYPOTHESIS | B | true |
| US-003-ac-7 | US-003-ac-7 | HYPOTHESIS | B | true |
| US-003-ac-8 | US-003-ac-8 | HYPOTHESIS | B | true |

> All clauses carry trust status **HYPOTHESIS** (greenfield origin; no prior evidence collected). Review date: **2026-12-10**.

---

## API & Contract Impact

**No material API, event, or data contract change is required.** (`contract_change_required: false`)

Impacted service: **SVC-003** (Next.js Front-End Application). SVC-002 and SVC-004 are confirmed no-change.

### Touchpoints (SVC-003 — all internal, no contract change)

| Kind | Name | Description |
|---|---|---|
| data_model | `URLSearchState` | New client-side TypeScript model `{ keyword: string \| null; filters: Record<string, string \| string[]> }` — single source of truth for composing and parsing the URL query string via shallow routing. Purely client-side; no server API contract surface. |
| data_model | `ProductListingPage` | Existing model extended to accept `URLSearchState` as initialisation props for keyword and filter hydration on page load. Client-side prop extension; no new server API endpoint or event contract. |
| internal_component | `useURLSearchState` | New custom React hook encapsulating all read/write logic for the combined URL query string via Next.js `useRouter`/`useSearchParams` and shallow router push/replace. No external API or event contract surface. |
| internal_component | `URLQueryStringComposer` | New pure utility function that merges keyword and filter key-value pairs into a `URLSearchParams` object. Entirely client-side; no API, event, or data contract surface. |
| internal_component | `URLStateInitialiser` | New server/client boundary component that reads URL query parameters on initial page load and passes them as props to `KeywordSearchInput` and `AttributeFilterPanel`. Operates within the existing SSR/SSG page rendering pipeline of SVC-003; no new API endpoint or event contract. |
| internal_component | `KeywordSearchInput` | Existing React island component wired to `useURLSearchState` for shallow URL updates on keyword change/clear. No new API or event contract surface. |
| internal_component | `AttributeFilterPanel` | Existing React island component wired to `useURLSearchState` for shallow URL updates on filter apply/remove. No new API or event contract surface. |

---

## Open Items & Gap Disclosures

> ⚠️ The following unresolved items are carried forward from the approved specification and must be resolved or explicitly accepted before implementation is considered complete.

1. **Degraded grounding**: All generated_context artifacts for SVC-003 (`artifact-SVC-003-c3`, `artifact-SVC-003-c4`, `artifact-SVC-003-api_spec`), SVC-002 (`artifact-SVC-002-c3`, `artifact-SVC-002-c4`, `artifact-SVC-002-api_spec`), and SVC-004 (`artifact-SVC-004-c3`, `artifact-SVC-004-c4`, `artifact-SVC-004-api_spec`) were auto-generated but returned empty strings, providing no usable C3/C4/API-spec grounding content. The design is grounded solely in the service identification manifest and functional specification; component-level detail is derived from functional requirements, not from confirmed provider-scanned architecture artifacts.
2. **Filter parameter names unspecified**: The exact set of URL query parameter names used for attribute filters (beyond the `q` keyword parameter) is not specified in the story or acceptance criteria. If filter parameter names collide with reserved Next.js or application query parameters, the `URLQueryStringComposer` logic may require adjustment.
3. **EP-001 boundary unconfirmed**: The story is explicitly out of scope for the individual filter controls themselves (EP-001) and analytics (EP-003), but the boundary between `URLSearchState` initialisation and EP-001 filter component contracts has not been formally confirmed. If EP-001 filter components expose a different prop interface than assumed, the `URLStateInitialiser` hydration props may need renegotiation.
4. **SVC-002 / SVC-004 no-change caveat**: SVC-002 and SVC-004 are confirmed as no-change for US-003, but this assessment is based solely on the service manifest and functional spec, not on confirmed provider-scanned C3/C4 artifacts. If those services have undisclosed client-side integration points with SVC-003 URL state, the no-change determination may need revisiting.

---

## Evidence References

`service:SVC-003`, `service:SVC-002`, `service:SVC-004`, `artifact-SVC-003-c3`, `artifact-SVC-003-c4`, `artifact-SVC-003-api_spec`, `artifact-SVC-002-c3`, `artifact-SVC-002-c4`, `artifact-SVC-002-api_spec`, `artifact-SVC-004-c3`, `artifact-SVC-004-c4`, `artifact-SVC-004-api_spec`, `story:US-003`, `ac:US-003-ac-1`, `ac:US-003-ac-2`, `ac:US-003-ac-3`, `ac:US-003-ac-4`, `ac:US-003-ac-5`, `ac:US-003-ac-6`, `ac:US-003-ac-7`, `ac:US-003-ac-8`, `fr:FR-001`, `fr:FR-002`, `fr:FR-003`, `fr:FR-004`, `fr:FR-005`, `fr:FR-006`, `fr:FR-007`, `fr:FR-008`, `run:3d791acc-1bc2-4db5-8f68-ad46c5dcc6a2`, `project:61393f69-dfc8-40ff-8342-28015c6b565c`
