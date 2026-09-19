## ADDED Requirements

### Requirement: Voice channel pages do not show a join banner

The client SHALL NOT render the voice-call preview banner as the primary way to join or open a voice-channel chat.

#### Scenario: Open voice channel page

- **WHEN** the user opens a voice-channel page while not connected to voice
- **THEN** the page does not display the “Join Voice Channel” or “Start the Call” banner
- **AND** the normal channel content remains available

### Requirement: Clicking a voice channel joins voice automatically

The primary activation of a voice-channel entry SHALL connect the user to that voice channel through the existing voice connection flow.

#### Scenario: Click voice-channel entry

- **WHEN** the user activates a voice-channel entry
- **THEN** the client navigates to the voice-channel route
- **AND** the client automatically joins the voice channel when the user has Connect permission

#### Scenario: Voice connection is not permitted

- **WHEN** the user activates a voice-channel entry without Connect permission
- **THEN** the client preserves the existing permission-aware navigation behavior
- **AND** it does not attempt an unauthorized voice connection

### Requirement: Voice-channel entries expose an Open Chat action

The client SHALL provide an accessible `Open Chat` action for a voice-channel entry when the entry is hovered or focused.

#### Scenario: Open chat explicitly from hover action

- **WHEN** the user activates `Open Chat` on a voice-channel entry
- **THEN** the client navigates to that voice channel's chat route
- **AND** the activation does not trigger the primary voice-channel entry action a second time

#### Scenario: Open Chat is keyboard accessible

- **WHEN** the voice-channel entry or its `Open Chat` action has keyboard focus
- **THEN** the action can be reached and activated without requiring mouse hover
- **AND** its accessible name identifies it as `Open Chat`

### Requirement: Existing voice controls remain available

Removing the banner MUST NOT remove the channel-header voice control or alter active-call controls.

#### Scenario: Join from channel header

- **WHEN** the user activates the channel-header voice control
- **THEN** the client starts or switches the voice connection according to the existing behavior
