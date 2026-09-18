## Context

The voice call card is rendered in the global floating layer and uses `VoiceLayout` for fullscreen, expanded, and collapsed window behavior. In a voice channel, the card is mounted inside the text channel's main content area while messages and the composer remain siblings in that area. The feature needs to hide those text-channel elements without changing browser fullscreen or the existing call-window layout state.

## Goals / Non-Goals

**Goals:**

- Add an ephemeral stream-only state owned by the voice controller.
- Make the active call card cover the current channel content rectangle in stream-only mode.
- Hide only the messages and composer belonging to the active voice channel.
- Provide an accessible toggle beside the existing fullscreen control.
- Restore the previous call-window layout when stream-only mode is disabled.

**Non-Goals:**

- No backend or LiveKit protocol changes.
- No persistence across calls, sessions, or devices.
- No changes to browser or native fullscreen behavior.
- No redesign of participant tiling or focus behavior.

## Decisions

### Separate state from `VoiceLayout`

Add a boolean `streamOnly` accessor and `toggleStreamOnly()` method to the voice controller instead of adding another `VoiceLayout` union member. This preserves the existing fullscreen/expanded/collapsed state and makes the new mode independently toggleable. The state resets on disconnect.

Alternatives considered:

- Adding `"stream-only"` to `VoiceLayout`: rejected because it conflates an orthogonal visibility mode with window sizing and would lose the prior layout on restore.
- Local state in the call-card component: rejected because `TextChannel` must reactively hide its own messages and composer.

### Cover the channel content rectangle

When stream-only is active, reuse the existing expanded positioning path based on the mounted marker's `parentRect`. Pass a separate `streamOnly` styling variant to remove the normal card padding and top offset. This fills the available channel content area while leaving browser fullscreen untouched.

### Scope chat hiding to the active call channel

`TextChannel` will hide messages and composition only when `voice.streamOnly()` is true and the current channel ID matches `voice.channel()?.id`. This prevents stream-only state from hiding chat after navigating to another channel.

### Reset behavior

Disconnecting the voice room clears `streamOnly`. Toggling the control off restores the existing divided layout and leaves all other layout state unchanged.

## Risks / Trade-offs

- [Unmounting chat while active] → Messages and composition are conditionally removed and recreated; keep the mode ephemeral and verify drafts/messages restore correctly when toggled off.
- [Mobile drawer geometry] → The existing expanded positioning path depends on the mounted channel rectangle and drawer visibility; validate stream-only behavior on desktop and supported mobile layouts.
- [Localization catalog freshness] → New tooltip strings require the normal Lingui extraction workflow; do not manually commit generated catalog changes in this feature.
