## Why

When viewing a live screen share in a voice channel, the text chat competes for space with the transmission and makes shared content difficult to read. Users need a dedicated viewing mode that maximizes the transmission area without invoking browser or window fullscreen.

## What Changes

- Add a control beside the fullscreen control for toggling a stream-only viewing mode.
- Hide the active voice channel's text messages and composer while stream-only mode is enabled.
- Expand the live-share surface to fill the available channel content area.
- Restore the divided transmission-and-chat layout when stream-only mode is disabled.
- Keep stream-only state independent from fullscreen, expanded, collapsed, and other call-window layout states.
- Reset stream-only mode when leaving the voice call.

## Capabilities

### New Capabilities

- `stream-only-live-share`: Provides a dedicated live-share viewing mode that hides chat and uses the available channel area for the transmission.

### Modified Capabilities

## Impact

- Voice state gains an ephemeral stream-only layout flag and toggle action.
- The voice call card layout and controls gain the new mode and control.
- The text channel conditionally hides messages and message composition for the active voice channel.
- No backend, API, persistence, or dependency changes are required.
