## Context

The banner is rendered by `VoiceCallCardPreview` in `packages/client/components/ui/components/features/voice/callCard/VoiceCallCardPreview.tsx`. It currently appears on the voice-channel page and provides a large connect action. The channel entry in `ServerSidebar` uses a `MenuButton` for navigation, while `ChannelHeader` has a separate control for joining voice. The desired behavior matches Discord: the primary channel interaction joins voice, and a secondary `Open Chat` action shown on hover or focus opens the channel chat.

## Goals / Non-Goals

**Goals:**

- Remove the inactive voice-call banner from the voice-channel chat page.
- Make the primary voice-channel entry activation connect to the channel.
- Add an `Open Chat` secondary action that navigates to the channel chat.
- Make both actions accessible by keyboard and usable without hover.

**Non-Goals:**

- Do not change voice permissions or the underlying RTC state machine.
- Do not remove the existing channel-header voice control while it remains useful.
- Do not add a persisted preference for hiding the banner; the banner is no longer needed in this flow.
- Do not update generated translation catalogs; extraction and compilation remain part of the existing maintenance flow.

## Decisions

1. **Remove the inactive preview from the voice card.** `VoiceCallCard` will no longer mount `VoiceCallCardPreview` when the user is not in the call. The active call room remains unchanged.

2. **Separate navigation and connection in the sidebar entry.** The primary voice-channel entry will retain semantic navigation and invoke the existing `voice.connect` flow when activated, subject to the existing permission and connection checks.

3. **Add `Open Chat` as a secondary action.** The action will appear for voice channels on hover and focus, stop propagation so it does not trigger the primary entry action, and navigate to the channel route. Keyboard focus must expose the same action without requiring a mouse.

4. **Reuse existing design-system patterns.** Use existing `Symbol`, tooltip/floating, and sidebar-action patterns with a translated `Open Chat` accessible label. No new dependency or global state is required.

## Risks / Trade-offs

- **[Open Chat also connects to voice]** → stop propagation and keep its handler limited to route navigation.
- **[Duplicate connection attempts]** → reuse the existing RTC connection flow and cover repeated clicks and already-connected channels in tests.
- **[Action hidden on touch devices]** → make `Open Chat` reachable by focus and keyboard, with a visible fallback on devices without hover.
- **[Responsive layout changes]** → reuse sidebar spacing and controls and verify desktop and mobile viewports.

## Migration Plan

1. Update the sidebar interaction and remove the inactive voice preview.
2. Validate navigation, automatic connection, and `Open Chat` on desktop, keyboard, and touch-sized viewports.
3. Run formatting, typecheck, and relevant end-to-end tests.

Rollback consists of restoring the preview mount and removing automatic connection from the primary sidebar activation.

## Open Questions

None. The intended behavior is: primary click joins voice; `Open Chat` is the deliberate action for opening the voice-channel chat.
