## 1. Voice state

- [x] 1.1 Add an ephemeral `streamOnly` signal and `toggleStreamOnly()` action to the voice controller.
- [x] 1.2 Reset `streamOnly` when the voice room disconnects.

## 2. Call card layout and control

- [x] 2.1 Add a stream-only toggle control beside the fullscreen control with active/inactive icons and localized tooltips.
- [x] 2.2 Position the call card over the active channel content rectangle while stream-only mode is enabled.
- [x] 2.3 Add stream-only styling that removes the normal inset padding and top offset while preserving independent fullscreen and window-layout state.

## 3. Channel content behavior

- [x] 3.1 Hide the active voice channel's message list and composer while stream-only mode is enabled.
- [x] 3.2 Scope the hidden content to the active call channel so other channels retain normal chat behavior.
- [x] 3.3 Restore the message list and composer when stream-only mode is disabled.

## 4. Validation

- [ ] 4.1 Verify toggling stream-only mode, restoring chat, fullscreen independence, navigation scope, and disconnect reset manually.
- [x] 4.2 Run client typecheck, formatting checks, and the relevant build or end-to-end checks.
