## ADDED Requirements

### Requirement: Stream-only mode toggle

The voice call controls SHALL provide a toggle beside the fullscreen control that enables and disables stream-only mode.

#### Scenario: Enable stream-only mode

- **WHEN** a user in an active voice call activates the stream-only control
- **THEN** the client SHALL enable stream-only mode and update the control to indicate that chat can be restored

#### Scenario: Disable stream-only mode

- **WHEN** a user activates the stream-only control while stream-only mode is enabled
- **THEN** the client SHALL disable stream-only mode and restore the divided transmission-and-chat layout

### Requirement: Stream-only content layout

While stream-only mode is enabled, the active call card SHALL occupy the available channel content area and the active voice channel's text messages and composer SHALL be hidden.

#### Scenario: Transmission fills channel content

- **WHEN** stream-only mode is enabled in a voice channel
- **THEN** the call card SHALL use the channel content rectangle without the normal inset padding or top offset

#### Scenario: Chat is hidden only for the active call channel

- **WHEN** stream-only mode is enabled and the current channel is the channel hosting the active call
- **THEN** the client SHALL not render that channel's message list or message composer

#### Scenario: Other channels retain chat

- **WHEN** stream-only mode is enabled and the user navigates to a different channel
- **THEN** the different channel's messages and composer SHALL remain visible

### Requirement: Independent fullscreen behavior

Stream-only mode SHALL be independent from browser fullscreen and the existing expanded, collapsed, and default call-window layout states.

#### Scenario: Fullscreen remains independent

- **WHEN** the user enables or disables stream-only mode
- **THEN** the browser fullscreen state SHALL not be entered or exited as a side effect

#### Scenario: Existing layout is preserved

- **WHEN** the user enables stream-only mode from an existing call-window layout and later disables it
- **THEN** the client SHALL preserve the pre-existing call-window layout state

### Requirement: Call lifecycle reset

Stream-only mode SHALL be cleared when the voice call disconnects.

#### Scenario: Disconnect resets mode

- **WHEN** the active voice call ends while stream-only mode is enabled
- **THEN** the client SHALL clear stream-only mode before the next call
