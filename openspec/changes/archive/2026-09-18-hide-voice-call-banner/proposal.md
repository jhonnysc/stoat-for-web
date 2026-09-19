## Why

The “Join Voice Channel, Start the Call” banner does not match Discord's voice-channel interaction model and occupies the chat area. The desired experience is to operate voice channels directly from the channel list: a primary click joins voice, while an `Open Chat` button revealed on hover opens the channel chat deliberately.

## What Changes

- Remove the voice-call banner from the voice-channel chat page.
- Make the primary click on a voice-channel entry automatically join that voice channel.
- Add an `Open Chat` button visible on hover and focus for opening the voice-channel chat.
- Keep joining voice and opening chat as distinct, accessible actions.
- Preserve existing header and active-call controls.

## Capabilities

### New Capabilities

- `voice-channel-discord-navigation`: Defines bannerless voice-channel navigation with automatic voice entry and an explicit `Open Chat` action.

### Modified Capabilities

None.

## Impact

- `ServerSidebar` and `MenuButton`, to separate primary channel activation from `Open Chat`.
- `VoiceCallCard` and `VoiceCallCardPreview`, to remove the inactive banner from the chat page.
- Existing routing and RTC connection flow, to connect when the primary channel entry is activated.
- Translated labels and accessibility behavior for `Open Chat`.
- End-to-end coverage for voice-channel navigation and automatic connection.
