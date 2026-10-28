# Trip.com flights checkout

This is a bundled reference of the `trip` skill for completing the selected booking. Follow `trip` for search and booking choices, and the current payment skill selected by `trip` and the runtime for payment authorization. Read this reference only when entering the selected product’s booking page.

Default to the English interface unless the user requests another language; verify it after regional redirects. Language does not change market, currency or local time. Do not switch the site language mid-checkout.

For an Addon result, open its returned booking link intact and continue there; it reprices the fare and does not hold a seat.

## Continue the current step

Keep the selected Trip result's complete link, existing tab, language and currency. Start from the current page; new flight discovery still uses the Trip Addon. Read the current step below, not every step again after each action.

| Step | Page clues | Advance when |
| --- | --- | --- |
| Fare | Fare cards, often `/m/flights/detail/` | Select the chosen flight/fare and continue |
| Passengers | Passenger form, often `/m/flights/xbook/` | Required passengers and contact details are saved; resolve only remaining validation errors |
| Seats and extras | Often `/m/flights/xdiscount/` | Requested choices are set and unwanted extras declined |
| Unpaid order | `/online/orderdetail/`, “Awaiting payment” and `Pay` | Click the existing order's `Pay` to enter the cashier |
| Cashier | `secure.trip.com/webapp/cashier/home` or `homeh5` | Current order is authorized, paid once and its actual status read |

Routes are clues, not proof of state or permission to submit. Identify whether the next control navigates, creates an order or charges; respect the user's stopping point and the current payment workflow.

- **One observation, useful work, one check.** Inspect the active form or dialog, locate its controls and perform independent, supported actions in one browser call where possible. Check the resulting state together. After navigation or a dependent field change, reacquire the next controls and continue in the same call where supported. Return only for missing input or an actual blocker. Do not spend separate model turns only to reconfirm a usable target before acting.
- **Keep moving.** Keep the selected itinerary and reuse supplied passenger/contact details. Do not audit baggage, refunds or fare rules, compare fare variants, or open policy dialogs. Go straight to the cashier; the user reviews the current booking and amount in the single payment confirmation. Do not narrate routine clicks or announce another round of checks.
- **Resume narrowly.** On interruption or compaction, retain a short non-sensitive checkpoint: current tab, checkout stage, selected fare and currency, completed sections, pending authorization/submission status and next action. Inspect the live page and continue the unfinished step. Do not store identity, contact, document, card or credential values in notes. Reread only missing instructions for this step; follow any runtime-required rereads without restarting the booking.
- **Use the current browser contract.** Read its available schema/help once and use only supported methods. If resource discovery is unsupported, use the documented help entry instead of trying discovery again. Do not assume browser-JS globals such as `URL` or `setTimeout` exist. Return a bounded summary of the active area, not the full page or helper documentation on every call.

### Locating controls

Locate in this order; names and `aria-label` are localized and are not stable identifiers:

1. **Stable attributes** in the active form or panel: exact `testid`, `data-testid` or `id`. Keep the site's spelling `nationlity`; ignore dynamic IDs and CSS hashes.
2. **Relationships and state:** `ref`/`parentRef`/`scopeRef`, role, input type, `autocomplete`, editable/checked state, and which field opened a shared picker.
3. **Current meaning or a fresh screenshot** for unrecorded dialogs, countries, months or terms.

The locator notation below describes observed DOM attributes, not a required API syntax. Use the current browser's documented query schema. If attribute queries are unsupported or an attribute is absent, use current roles, relationships and visible controls; do not invent another schema.

Scope duplicate matches to the active fare card, passenger editor or dialog, then choose the matching interactive element. Do not repeatedly throw a “not unique” error for the same unscoped query. Normal page, passenger-form and non-date list scrolling is allowed; the no-scroll rule below applies only to selecting values in date wheels. Scroll the relevant container only when needed; if a visible control lacks a usable semantic target, use a fresh screenshot and the runtime's supported coordinate action. `nextCursor` is tool pagination, not a website page.

### 1. Fare

Select the chosen flight/fare and continue. Keep the user's dates, passengers, cabin and currency; do not change them to pass checkout. Skip upgrades, fare-rule comparisons and optional extras. Resolve only a missing required choice or a site rejection.

### 2. Passengers

Scope to the current passenger editor (`[testid="passenger_view"]` when present), not other passengers or `[testid="contact_view"]`. Reuse supplied or already saved values. Collect details according to the fields the page currently requires:

1. **Initial collection.** Inspect the initial passenger and contact form and request its missing required details together through the ordinary form workflow described in `trip`. Include nationality/region and document type when needed; do not guess them or request hypothetical fields that have not been required.
2. **Dependent-field collection.** Apply the user's nationality/region and document type, then let the form refresh and inspect its requirements again. If that selection reveals additional required details, such as document expiry, promptly show a second form requesting only those newly missing details. This follow-up is expected; do not wait until all other fields are filled, and do not ask again for unchanged details already supplied. If a coordinator collects the information, send it the newly required field list.
3. **Complete and save.** Fill the remaining passenger and contact fields using the collected values, batch independent actions, then save. Recheck dependent requirements after any later nationality or document-type correction.

Do not infer sex or nationality from a name, language or dialing code. A passport number and expiry must come from the same passport; request them only when the selected document and current page require them.

**Older saved references:** a supplied plain date takes precedence over an older opaque `{{secret:...}}` reference for that field. Use an existing reference unchanged only where the runtime supports it. If a custom date picker needs year/month/day but only an opaque reference is available, ask the coordinator for that missing date through an ordinary `date` form once; do not try to reveal or parse the saved secret. Once supplied, use the date-list steps below and verify the saved field. Passport fields alone do not require browser takeover; request takeover only for an actual remaining interaction limitation after the bounded recovery below.

| Field | Locator | Action |
| --- | --- | --- |
| Surname | `[testid="passenger_edit_en_lastname_mask_replay_TextInput"]` | Fill |
| Given name | `[testid="passenger_edit_en_firstname_mask_replay_TextInput"]` | Fill |
| Sex | `[testid="passenger_edit_male_gender_mask_replay_touchable"]` / `[testid="passenger_edit_female_gender_mask_replay_touchable"]` | Click the outer option, verify selected |
| Nationality | `[testid="nationlity"]` | Open country picker |
| Birth date | `[testid="birthday"]` | Open date wheels |
| Document type | `[testid="expose_form_mode_card_type_select"]` | Open document panel |
| Passport number | `[testid="passenger_edit_1_ID_1_mask_replay_TextInput"]` | Fill |
| Passport expiry | Button around `[testid="passenger_edit_1_ID_2_mask_replay_TextInput"]` | Open date wheels |
| Save | `[testid="expose_form_passenger_confirm_btn"]` | Save, then check errors |

- **Sex:** if the fixed step bar covers the option, scroll the row toward the centre, reacquire and click once.
- **Offscreen Save:** saving passenger details is not placing an order. If `[testid="expose_form_passenger_confirm_btn"]` is offscreen or covered, scroll the active passenger editor, reacquire the button and click it, then check validation and the saved passenger. If page scrolling leaves its bounds unchanged, target the editor's scroll container using a fresh screenshot and the runtime's documented capture-bound scroll action. When page evaluation is permitted, the uniquely located Save element's `scrollIntoView({block: "center"})` is another way to reveal it; verify visibility before clicking. Do not guess coordinates or viewport dimensions, repeat ineffective page scrolling, or request takeover merely to scroll and save. Passenger fields on screen do not by themselves block the necessary inspection; follow actual runtime restrictions. After one container-targeted recovery still fails, report the observed interaction limitation.
- **Saved passengers:** leave matching details alone. Edit through the passenger card, not `[testid="passenger_contact_edit_button"]`; a single click opens the editor even if a hint says double-click.

**List pickers: inspect the list, then click the target directly.** Open the active picker and read its option elements in one bounded inspection. Match the requested value using the actual row text, accessible name and attributes; return only matching rows and selected state, not the entire list. Locate and click in the same browser call where supported. Use observed options and geometry; the date-wheel adapter may move through intermediate values. A DOM match identifies a target; verify the selected value after clicking rather than assuming success. If a native element click cannot reach a transformed/offscreen option, use the DOM-click recipe below when the runtime permits it. For non-date lists, scrolling their active container to reveal the matched row is allowed. For date wheels, use the Browser skill's bounded mouse drag adapter if clicking does not change selection. If the bounded recovery does not change selection, report the specific limitation instead of cycling through gestures or keyboard experiments.

- **Nationality:** open the control around `[testid="nationlity"]`, not its disabled/read-only input. The shared `#regionpanel` also serves phone dialing codes; verify which field opened it. Read `#regionlist #regionitem li[role="button"]` and match the requested country's current `aria-label`/text (for example `中國內地`). Selected, popular and alphabetic sections repeat countries: scope to one group, such as the current `role="group"` named `國家及地區列表`, and select exactly one matching row. Click the row directly, then verify the nationality field and any changed document requirements. Use the picker's searchbox only if the target is not present in the inspected list. Close a wrongly opened dialing-code panel with `.m-close`.
- **Document type:** open `[testid="expose_form_mode_card_type_select"]` and read the option rows in the active bottom sheet. Rows may expose `role="button"` and a `testid` beginning with `passenger_edit_ID_type_`; use these only to discover rows, never to infer document type from a number or position. Available documents depend on nationality/region and itinerary. Match the actual displayed document name to the user's supplied document, then click that row directly. After nationality changes, discard old option references and reread the list. Verify the selected type and inspect its current required fields. If the user's document is absent or ambiguous, report the available choices and ask only for the missing decision; do not change nationality or substitute a different document to make it fit. Use the sheet's close control to cancel.

**Date lists** (birth date and expiry of any supported identity document). Leave a correct date unchanged. Open the outer date control, not its read-only input, and let the panel settle. Inspect `.wheel-wrapper-inner` and read its three lists; an empty field's initial selection is a site default, not passenger data.

| Column | List DOM hint | Target option example |
| --- | --- | --- |
| Year | `ul[role="listbox"][aria-label="yyyy"]` | `li[role="option"][aria-label="1996年"]` |
| Month | `ul[role="listbox"][aria-label="MM"]` | `li[role="option"][aria-label="7月"]` |
| Day | `ul[role="listbox"][aria-label="dd"]` | `li[role="option"][aria-label="27日"]` |

These are DOM hints, not browser API syntax or passenger defaults. Use the supplied birth date or the selected document's expiry date and the live options' names, including their localized suffixes. Do not reuse a birth-date value or target reference for document expiry; inspect the newly opened panel. Scope to the active panel and the correct list; the year list can contain the target even when its row is outside the visible wheel area.

1. Read each list's current `li[role="option"][aria-selected="true"]` and locate the exact requested option in that list. Set year, then month, then day; skip values already correct.
2. Click the target option and verify selection. If clicking does not change it, read the Browser skill's **Mouse-driven wheel selection**. For the In-app Browser, use its `SKILL.md` locator from the session's skill catalog and read `references/stable-wheel.md` relative to that file's directory, not the Trip skill or project directory. Prefer this installed reference over runtime help. Run its function through `tab.evaluate(selectWheelValue, [column, exactOptionText], {timeoutMs:5000})` when the runtime supports page evaluation. Use observed labels and localized text. The adapter performs at most two short mouse drags per call, holds before release and verifies selection; continue bounded calls only on `progress`, and inspect other non-`done` results. It supports the non-touch desktop carrier, not native touch mode. Do not assume Playground-only handles exist.
3. After each change, let the animation settle and verify that the target is now `aria-selected="true"`. Year/month changes can clamp the day or replace its options, so reacquire the day list after those changes. Use bounded inspect → action → verify batches where supported.
4. When all three selected values match, click the panel's Confirm/Done (`.panel-header-done` when present), then verify the saved field. `.panel-close` discards the change.

If the Browser skill or its wheel reference is unavailable, use `iab.help('wheel')` only if the runtime's reference index advertises `wheel`; do not retry an unsupported topic. Missing documentation does not prove the picker is inoperable. As a bounded fallback, use a fresh screenshot and a supported coordinate click on one visible adjacent option, then verify selection. Continue in bounded batches only while each click makes verified progress toward the target; stop on no progress or a runtime denial and report the actual limitation.

Native scrolling (including CDP `Input.synthesizeScrollGesture`) is not a mouse-button drag. Use the verified adapter instead of arbitrary gesture or arrow-key experiments. These are custom lists, not native `<select>` inputs: do not use `selectOption`, fill read-only fields, or modify transforms/`aria-selected` to fake selection. DOM presence and a successful click call alone do not prove the component accepted the date.

**DOM-click recipe.** When the native SDK cannot express the target click and the runtime allows page evaluation, use the observed list's option element and its normal `.click()` handler. For the date DOM above, the following page function locates one exact option without requiring that option to be in the visible wheel strip. Pass `column` (`yyyy`, `MM` or `dd`) and `label` from the current list inspection; do not paste passenger values into reusable scripts.

```js
(column, label) => {
  const panels = [...document.querySelectorAll('.wheel-wrapper-inner')]
    .filter(p => p.getClientRects().length && !p.closest('[aria-hidden="true"]'));
  if (panels.length !== 1) return { clicked: false, reason: 'ambiguous-panel' };
  const lists = [...panels[0].querySelectorAll('ul[role="listbox"]')]
    .filter(el => el.getAttribute('aria-label') === column);
  if (lists.length !== 1) return { clicked: false, reason: 'missing-or-ambiguous-list' };
  const matches = [...lists[0].querySelectorAll('li[role="option"]')]
    .filter(el => (el.getAttribute('aria-label') || el.textContent.trim()) === label);
  if (matches.length !== 1) return { clicked: false, reason: 'missing-or-ambiguous-option' };
  if (matches[0].getAttribute('aria-selected') === 'true')
    return { clicked: false, alreadySelected: true };
  matches[0].click();
  return { clicked: true }; // Dispatch only; verify selection after the animation.
}
```

Use the runtime's documented evaluation wrapper and argument format, not an invented `page` global. With the observed In-app Browser API this is `await tab.evaluate(fn, [column, label])`; its returned value is at `result.page.data.value`. After each click, allow the animation to settle, reacquire that list and check its selected option before the next column. Keep this bounded sequence in one browser call when supported, returning only completion/mismatch status; do not add a screenshot or model turn for each click. Stop on a mismatch and preserve completed columns. Confirm only after all three match, then verify the saved field. A runtime refusal is not permission to switch APIs or remove protections.

For nationality and document type, use the same inspect → exact row → click → verify pattern with the active list selectors above. Use the full observed row name and one unique match; never derive document type from a `testid` suffix. These choices refresh the form, so inspect the changed requirements before filling dependent fields.

**Contact and frequent flyer:** fill only when required or requested. The dialing code `[testid="contact_phone_area_code"]` opens the shared region picker (rows show `.region-code`); do not increment it, and verify `[testid="contact_phone_area_code_text"]`. Contact fields are `[testid="contact_name_mask_replay_TextInput"]`, `[testid="contact_phone_mask_replay_TextInput"]` and `[testid="contact_email_mask_replay_TextInput"]`. For a requested frequent-flyer number, expand `[testid="flyer_card_title_container"]`, open `[testid="flyerCard"]`, search the airline code, pick its row, then verify `[testid="passenger_edit_1_mask_replay_travel_card_TextInput"]`.

**Then:**

1. Save each passenger and check that all required passengers are saved. Saving is not ordering.
2. Insurance: select `[testid="0_insurance_unselect_panel"]` and verify it; leaving both options blank may not count as declining.
3. Terms: click the checkbox for an unchecked `[testid="regulation_unselect_icon"]` once and verify; leave it if checked. If the page says continuing implies agreement, there is no box to find.
4. Click the main action. `[testid="pay_button"]` may only navigate despite its name.
5. On the passenger review page, continue with the supplied details without a new user confirmation; correct only a known input error.

### 3. Seats and extras

Skip or "select later" for seats; once past the seat map, do not reopen it. Decline popups through their own control (baggage protection: `[testid="retain_brb_ignore_button"]`). In AirHelp, select the not-needed option and verify no paid service was added; a selected radio that names the product is not necessarily a purchase. Skip lounges and other offers. A countdown can change the Next button's name, so reacquire it before clicking. Tell promotions apart from price changes, validation errors and required confirmations.

### 4. Cashier

On the selected order's desktop details page (`/online/orderdetail/`), “Awaiting payment” plus `Pay` is an entry to the cashier, not the final charge. Click `Pay` and continue the same order without asking permission to enter payment or checking terms. Confirm the destination is the cashier (`/webapp/cashier/home` on desktop or `homeh5` on mobile); do not restart booking. The button may include a live countdown: locate its current Pay control without matching the timer, reacquire after `STALE_REF`, and use a fresh screenshot-grounded click if semantic targeting still fails. Verify the transition before retrying. Final payment follows the authorization step below.

At the cashier, immediately present the displayed booking summary and payable amount/currency for the user to review through the payment flow in `trip`. Do not leave the cashier to fetch baggage, refund or change rules, or delay the confirmation because those details are unavailable. Reuse an existing authorization for the same unchanged booking. Use the user's payment method, otherwise a credit card. If saved cards exist, a masked-card choice belongs in that confirmation; if only CVV/CVC is missing, ask only for that.

Select the new-card method from its editable form and radio state; do not switch an already selected method. Card fields are in the top document; `iframe#preload-iframe` holds order details, not card inputs.

| Detail | Locator | Note |
| --- | --- | --- |
| Card number | `#cardNumber` (`autocomplete="cc-number"`) | `type="tel"` |
| Cardholder | `#card_holder` | The card's holder, not the passenger |
| Expiry | `#validity` (`autocomplete="cc-exp"`) | Observed `MM/YY` |
| Security code | `#verify_no` | CVV/CVC |

Fill through the runtime's secure-field workflow. When its schema supports secure batch fill, submit the independent observed card fields together using the authorized temporary references unchanged, then check completion and validation without exposing values. Otherwise follow its required per-field flow; never fall back to plaintext script injection. Ask for a billing address only if the form requires it. Keep the agreed currency even if the cashier offers another. `[data-testid="payment-btn-active"]` submits payment: check it is enabled, click once and read the result before any retry. Hand the user only a step that needs them, such as bank verification.

Report the order reference, itinerary, amount/currency and observed status: awaiting payment, paid, ticketing in progress or ticketed. An order number, a filled card or a countdown proves neither payment nor ticketing; keep separate statuses for separate orders.

## Recovery

| Problem | Action |
| --- | --- |
| Attribute missing, duplicate or localized target | Refresh the active scope once and change locator strategy; do not repeat the same failed query |
| Click rejected or option covered | Check overlays, move the row clear of the fixed bar, reacquire and click once |
| `STALE_PAGE`, `STALE_CAPTURE` or expired reference | Read fresh state and reacquire only remaining targets; inspect completed actions before retrying, never replay the whole batch |
| JS context or tab binding lost; device restarted | Reinitialize through the documented browser entry, rediscover the existing tab and inspect live state. Old variables and handles are invalid; saved page edits may remain |
| Output limit | Keep preserved runtime state and emit a smaller summary; do not replay completed actions |
| Dialog still animating | Wait before reading; a transitional snapshot is not a failure |
| Terms or details opened by mistake | Go back once in the same tab, or return to the original tab, and check details are intact |
| “Search results expired” / “搜尋結果已過期” | Click the dialog's Reload / 重新加載 immediately without asking the user, wait for fresh results, then reselect the same flight/fare and resume using saved passenger/contact details. Keep the refreshed quote for the final payment confirmation; refill only lost fields and decline unwanted extras again. A quote change alone does not require permission to restart or continue filling. If reload still leaves an expired/unusable result, repeat the same Trip Addon search once, show its fresh results card and continue the same selection if available; ask only if a replacement choice is needed. Do not loop. At payment, apply the latest total and existing authorization under `trip`. If an order/payment submission might already have occurred, inspect its status first instead of restarting |
| Navigation timed out | Find and bind the existing Trip tab and read its state; do not reopen the link |
| Blank page or `ERR_CONNECTION_CLOSED` / `NAVIGATION_FAILED` | If clearly unsubmitted, reload once; if it still fails, report the blocker and keep the session |
| Submission result unknown | Check order and payment status first; never resubmit or pay again blindly |
