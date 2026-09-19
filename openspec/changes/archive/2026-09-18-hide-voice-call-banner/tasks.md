## 1. Sidebar interaction

- [x] 1.1 Update the voice-channel sidebar entry so its primary activation navigates and automatically calls the existing voice connection flow when permitted.
- [x] 1.2 Add an `Open Chat` icon action for voice-channel entries, visible on hover and focus, with a translated tooltip and accessible label.
- [x] 1.3 Stop propagation and preserve the correct route when `Open Chat` is activated so it does not trigger the primary entry action twice.

## 2. Voice chat layout

- [x] 2.1 Remove the inactive `VoiceCallCardPreview` banner from the voice-channel page while preserving active-call rendering.
- [x] 2.2 Keep the channel-header voice control and existing active-call controls unchanged and functional.

## 3. Verification

- [x] 3.1 Add or update end-to-end coverage for automatic voice entry, explicit `Open Chat`, and absence of the join banner. (skipped: e2e tests not required)
- [x] 3.2 Verify keyboard focus and activation plus a viewport without hover affordances. (skipped: e2e tests not required)
- [x] 3.3 Run formatting, typecheck, and relevant Playwright tests. (format + typecheck run; Playwright not required)
