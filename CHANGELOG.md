# Changelog

## [2026.09.25.37]

### Added

- **Talk button behaviour** — choose **Hold to talk** (microphone open only while the
  button is held, as before) or **Tap on / tap off**, which latches: one tap starts
  talking, the next stops. The button's wording follows the mode.

### Removed

- Drop the remembered-state migration that copied `ring-view-*` browser storage keys.
  A browser that never loaded a build between 2026.09.25.33 and .36 starts with no
  remembered mode.

## [2026.09.25.36]

### Changed

- Replace the four per-edge alignment settings and the per-row edge dropdown with
  a **Position on edge** selector on each item — Left / Middle / Right on
  horizontal edges, Top / Middle / Bottom on vertical ones. Each zone anchors
  independently, so a centred item is centred on the container rather than drifting
  with its neighbours' widths. `space-between` equalised the gaps, which left the
  middle item off-centre by half the difference between the outer two.
- Existing cards migrate automatically: a zone is derived from the previous
  `layout_<edge>_align` value, and `space-between` maps by order — first item Left,
  last Right, anything between Middle.

## [2026.09.25.35]

### Removed

- Drop the `custom:ring-view` and `custom:easy-ring-styler` element aliases. All
  cards use `custom:easy-ring-styler-card`. Keeping `ring-view` registered would
  have collided with the upstream card if both were installed together.

## [2026.09.25.34]

### Changed

- Card type is `custom:easy-ring-styler-card`, matching the repository name.

## [2026.09.25.33]

### Changed

- Rename the card, its elements, CSS custom properties, storage keys and default
  snapshot folder from `ring-view` to `easy-ring-styler`.

## [2026.09.25.32]

### Fixed

- Keep the native recording controls available while a direct recording is paused
  and after playback finishes. Ported from upstream v0.12.4.

## [2026.09.18.x] – [2026.09.17.x]

### Added

- **Button layout** — place every control and readout on any of the four edges,
  ordered within the edge, with per-item visibility. Overflow degrades by dropping
  labels, then shrinking controls, then wrapping.
- **Outside the Parameter** — reserve a strip beside the image for an edge instead
  of overlaying the video (dashboard card only).
- **Second recording source** — configure an official Ring camera and a Ring-MQTT
  Event Select together, each with its own button and user-selectable MDI icon.
  One source plays at a time.
- **Live frame capture** — a capture method that reads the frame the browser has
  already decoded and uploads it through the media source, instead of asking the
  camera integration for a still that Ring often serves from cache.
- **Snapshot source** — capture from the device snapshot camera, the live view
  camera, or automatically.
- **Snapshot popover** — briefly show the captured image over the card, for a
  configurable duration.
- **Motion alerts** — an optional motion entity raises the same temporary alert as
  a ding, with a walking-person icon and its own wording, in the same position. A
  ding always takes precedence.
- **Centre tap** — a large invisible hit area in the middle of the image, inset
  clear of the edge controls, with a selectable action and an independent
  "also opens the larger view".
- **Text sizing** — independent sizes for the camera name and the last-activity
  line, which can be one block or two separately placed items.
- Editor preview now renders every control, inert, so placement can be judged.

### Changed

- Settings regrouped into Card behavior, Recording playback, Live stream,
  Snapshots, Events, Door access and Card appearance, with an accordion so only
  one section is open at a time, and a **How snapshots work** explainer.
- **Image render method** replaces the previous dashboard-behaviour naming, and
  describes what it selects: a periodically refreshing still image, or a live
  stream in the card.
- Tapping anywhere on the still image no longer opens the viewer; the centre tap
  area carries that action.

### Fixed

- The card no longer resizes itself minutes after load. Home Assistant rotates
  camera access tokens on a five-minute timer, and a token-only change to the
  poster URL looked like a new image, resetting the measured shape to a 16:9
  fallback. Ported from upstream v0.12.3.
- Surface the real Home Assistant error when a snapshot fails, and name the cause
  when a folder sits outside `allowlist_external_dirs`.
- Treat a changed `entity_picture` token as a valid snapshot-refresh signal, and
  proceed rather than failing when no refresh signal is observable at all.
