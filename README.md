# Easy Ring Styler

A Home Assistant Dashboard **custom card** for Ring cameras and doorbells — recordings, live view, two-way audio, door release, and snapshots in one viewer — with a **fully arrangeable control layout**, two recording sources side by side, real-time frame capture, ding **and** motion alerts, and a complete visual editor.

> Special Thanks to [thomasgregg/ring-view](https://github.com/thomasgregg/ring-view) for his project which inspired this one.
---

## Key features at a glance

- **Arrangeable controls** — put every control and readout on any of the four edges, in any order, with per-edge alignment. Hide any of them, or move a whole edge **outside the image** so the buttons sit in their own strip instead of over the video.
- **Two recording sources at once** — configure the official Ring camera *and* a Ring-MQTT Event Select, each with its own button and icon. One plays at a time; tap the source you want.
- **Real-time snapshots** — capture the frame your browser is actually decoding, not the cached still Ring hands back, and save it through Home Assistant's media source.
- **Snapshot popover** — the captured image appears over the card for a duration you set, so you can see what you saved.
- **Ding and motion alerts** — a temporary alert with its own icon and wording for each, sharing one placement so they never fight for space.
- **Centre tap** — a large invisible hit area in the middle of the image, clear of the edge controls, with a selectable action and an independent "also open the larger view".
- **Two render methods** — a light, periodically refreshing still image, or a real live stream embedded in the card.
- **Text sizing** — independent sizes for the camera name and the last-activity line, which can be a combined block or two separately placed items.
- **Full visual editor** — every option is point-and-click; no YAML required.

---

## New Stuff

- **Latching Talk button** — as well as hold-to-talk, the Talk button can latch: one tap starts talking, the next stops. Useful on a wallpanel where holding a button is awkward.

- **Recording controls stay available** — a paused or finished recording keeps its scrubber, volume, and fullscreen controls instead of switching them off after 2.5 seconds. Ported from upstream v0.12.4.

- **Button layout** — a per-edge editor: add a control to an edge, order it with arrows, hide it with the eye, or take it off entirely. Each edge has its own alignment (start / centre / end / spread out) and an **Outside the Parameter** toggle that reserves a strip beside the image instead of overlaying it. Overflow degrades gracefully: labels drop, controls shrink, then the edge wraps.
- **Second recording source** — point one field at the official Ring camera and the other at a Ring-MQTT Event Select, each with a user-selectable MDI icon. The mode switch grows a third button; only one source plays at a time.
- **Live frame capture** — a **Capture method** choice. *Ask Home Assistant* calls `camera.snapshot` as before; *Grab the frame on screen* reads the frame already decoded in the browser and uploads it via the media source, which is the only route to a genuinely current image from a Ring camera.
- **Snapshot source** — capture from the device snapshot camera, the live view camera, or automatically. Choosing the live camera skips the Ring-MQTT refresh step, which avoids a refresh timeout on interval-mode snapshot cameras.
- **Snapshot popover** — shows the captured bytes where possible, the saved file where it is servable, and the camera image otherwise, for a configurable 1–30 seconds.
- **Motion alerts** — an optional motion entity raises the same temporary alert as a ding, with a walking-person icon and its own wording, in whichever position the alert is placed. A ding always takes precedence.
- **Card no longer resizes itself** — with an automatic aspect ratio, Home Assistant's five-minute camera token rotation used to look like a new image and reset the measured shape, dropping the card to a 16:9 fallback minutes after load. A token-only change now preserves the ratio, while genuine changes still re-measure.
- **Better snapshot diagnostics** — the underlying Home Assistant error is logged rather than swallowed, and a folder outside `allowlist_external_dirs` now says so instead of failing generically.
- **Reorganised editor** — settings grouped by what they affect (Card behavior, Recording playback, Live stream, Snapshots, Events, Door access, Card appearance), an accordion so only one section is open at a time, and a **How snapshots work** explainer inside the Snapshots panel.
- **Date-stamped builds** — versions are `YYYY.MM.DD.BUILD`, shown in the console on load and at the top of the visual editor.

---

## Requirements

- **Home Assistant 2026.7 or newer.**
- **Ring media exposed to Home Assistant** — the official [Ring integration](https://www.home-assistant.io/integrations/ring/), the [Ring-MQTT](https://github.com/tsightler/ring-mqtt) add-on, or both. Recording access needs a suitable Ring subscription. The card itself never talks to Ring; it only uses entities already in Home Assistant.
- **Two-way audio** needs the official Ring **live view** camera, an HTTPS connection to Home Assistant, and microphone permission. Ring-MQTT's live stream has no microphone return path.
- **Snapshots to `/config/www/...`** need that folder in `allowlist_external_dirs`:

  ```yaml
  homeassistant:
    allowlist_external_dirs:
      - /config/www
  ```

  `/media` folders work without this, because Home Assistant already allows its media directories.
- **Live frame capture** needs Live playing in the card and a `/media` folder — it uploads through the media source, which cannot write to `/config/www`.

---

## Concepts

- **Image render method** — what the card *is*. **Still image with periodic refresh** is a plain image, refreshed every ten seconds, that starts no stream. **Live stream in the card** embeds the real viewer with playback. This is not a controls toggle; it selects two different cards.
- **Button layout** — each control belongs to one edge, at one position in that edge's order. Items on an edge form a single row (top/bottom) or column (left/right). Placement is shared by the card and the fullscreen viewer; **Outside the Parameter** applies to the card only.
- **Recording sources** — up to two, each with its own button. An official Ring **Last recording** camera follows Ring's history automatically; a Ring-MQTT **Event Select** divides recordings into Ding / Motion / Person / on-demand categories and can be automatic (newest) or manual (a chosen historical event).
- **Capture method** — where a snapshot's pixels come from. *Ask Home Assistant* asks the camera integration, which for Ring is often a cached still. *Grab the frame on screen* reads the decoded live frame in the browser.
- **Alerts** — a ding or motion event raises one temporary alert for twelve seconds, in the position given to the **Ding & Motion alert** item. Tapping during an alert jumps straight to Live.
- **Centre tap** — a large invisible button in the middle of the image, inset clear of the edge controls so it can never swallow a button press. Its action and whether it also opens the larger view are independent choices.

---

## Options at a glance

Every option below is point-and-click in the visual editor.

### Card behavior
- **Image render method** — still image with periodic refresh, or live stream in the card.
- **Card's initial image** and its fallback (still-image mode) — last recording, live, default, snapshot, or newest.
- **Start** (live-stream mode) — wait for a tap, last recording, or live.
- **Default mode** and **remember last mode**.
- **Tap centre of image** — do nothing, switch to Live, play last recording, or take a snapshot — plus an independent **also opens the larger view**.
- **Image shape** — automatic, 16:9, 4:3, or 1:1 — and **image fit**, contain or cover.

### Recording playback
- **Recording source** and, for a Ring-MQTT Event Select, its **event selection** (newest or selected).
- **Second recording source** with its own event selection — adds a second button to the mode switch.
- **Button icons** for each source, MDI assumed.
- **Play recording automatically**, and **start recordings muted** in fullscreen and on the card.

### Live stream
- **Live view camera**.
- **Start Live muted** in fullscreen and on the card.
- **Two-way audio** — adds the Talk button.
- **Talk button behaviour** — hold to talk, or tap on / tap off (latching).

### Snapshots
- **How snapshots work** — a collapsible explainer covering both capture methods and their constraints.
- **Capture method** — ask Home Assistant, or grab the frame on screen.
- **Take snapshot from** — automatic, device snapshot camera, or live view camera.
- **Save folder** — `/media/ring-view`, `/media/snapshots`, `/config/www/snapshots`, `/config/www/ring-view`, or any path you type.
- **Snapshot popover** and its **duration** in seconds.

### Events
- **Ding / button entity** — a Ring event entity or a Ring-MQTT Ding binary sensor.
- **Motion entity** — optional; raises the same alert with a walking-person icon.

### Door access
- **Lock entity**, optional **contact sensor**, **unlock** or **open door**, visibility (live only or all views), location (viewer only or dashboard and viewer), and **hold to activate**.

### Card appearance
- **Camera name** text, **last activity** entity, and whether activity sits **under the camera name** or is its own placeable item.
- **Text sizes** for the camera name and the activity line.
- **Show Talk and door button text**.

### Button layout
- One section per edge — **Top**, **Right**, **Bottom**, **Left** — each with its **alignment** and an **Outside the Parameter** toggle.
- Per control: **move to another edge**, **reorder**, **hide**, or **remove from every edge**.
- Placeable items: camera name, last activity (when separated), Recording / Live toggle, **Ding & Motion alert**, Snapshot, Expand / Close, Talk, and Door.

---

## How snapshots differ between the two capture methods

| | Ask Home Assistant | Grab the frame on screen |
|---|---|---|
| Where the pixels come from | the camera integration | the frame your browser already decoded |
| How current | often a cached still, sometimes hours old | the current moment |
| Needs Live playing | no | **yes** |
| Works without a dashboard open | yes | **no** — it runs in the browser |
| Can save to `/media` | yes | yes |
| Can save to `/config/www` | yes (with `allowlist_external_dirs`) | **no** — uploads go through the media source |
| Resolution | Ring's stored image | whatever the stream is delivering |

If a Ring-MQTT snapshot camera is in **interval** mode, its on-demand refresh may never land and *Ask Home Assistant* will time out after fifteen seconds. Either switch that camera's snapshot mode to one including on-demand or Ding, set **Take snapshot from** to the live view camera, or use the on-screen capture.

---

## How alerts are decided

- A **ding** fires on a Ring event entity whose `event_type` is `ring`, or on a binary sensor turning on. Motion events on a doorbell event entity are ignored.
- A **motion** event fires on a motion event entity or a binary sensor turning on.
- Either raises one alert for **twelve seconds**: a coloured pill on the still card, a badge in the viewer, and the tap-opens-Live override.
- **A ding always wins.** Motion never replaces an active ding alert, and a ding arriving during motion takes over immediately — a ding is nearly always preceded by motion.

---

## Notes & limitations

- **`camera.snapshot` cannot produce a live Ring frame.** It asks the integration, and both Ring paths return a stored still. On-screen capture is the only route to a current image — and it only works while you are watching Live.
- **On-screen capture saves to `/media` only.** The media source upload endpoint cannot write to `/config/www`.
- **A still image cannot play Live on the card.** Choosing Live or Recording for the centre tap in still-image mode opens the larger view, because there is no player on the card to run it in.
- **Placement is shared between the card and the fullscreen viewer.** Only **Outside the Parameter** is card-specific.
- **Recording and Live stay one toggle.** The mode switch is a single pill with grouped keyboard navigation; the two recordings are separate buttons within it.
- **Talk needs the official Ring live camera** over HTTPS. This is a limitation of Ring-MQTT's live stream, not a card setting.
- **Reserving an edge shifts the video.** With **contain** the image gains margins; with **cover** it is cropped. On a fixed-height card in a Sections dashboard the video shrinks, so allow an extra row or use automatic height.
- **Some editor polish leans on Home Assistant frontend internals** — the accordion, the panel spacing, and placing the help section inside the Snapshots panel. If a future release changes those elements, the settings all still work; only the arrangement degrades.
- **Fork, not affiliated.** The original Ring View card was created by [thomasgregg](https://github.com/thomasgregg/ring-view); Easy Ring Styler tracks its own changes from the 0.12.2 base. The card communicates through Home Assistant, never directly with Ring, and contains no telemetry.

---

## Installation - HACS

[![Open your Home Assistant instance and open this repository inside the Home Assistant Community Store.](https://my.home-assistant.io/badges/hacs_repository.svg)](https://my.home-assistant.io/redirect/hacs_repository/?owner=Ltek&repository=easy-ring-styler-card&category=dashboard)

1. Click the button above (or in HACS: **⋮ → Custom repositories**, add `https://github.com/Ltek/easy-ring-styler-card` as category **Dashboard**).
2. Open the repository in HACS and click **Download**.
3. Hard-refresh the browser (Ctrl/Cmd+Shift+R). HACS adds the Dashboard resource automatically.
4. Add the card to a dashboard: **Add Card → Custom: Easy Ring Styler** (or `type: custom:easy-ring-styler-card`).

Confirm the build you expect is running: the browser console logs `Easy Ring Styler 2026.09.25.37` on load, and the same version appears at the top of the visual editor.

### Minimum Starting YAML

```yaml
type: custom:easy-ring-styler-card
recording_entity: camera.front_door_last_recording
live_entity: camera.front_door_live_view
```

---

## Credits and licence

Easy Ring Styler is a distant fork of **[ring-view](https://github.com/thomasgregg/ring-view)**
by [thomasgregg](https://github.com/thomasgregg), used under the MIT License. The viewer,
media handling, Ring and Ring-MQTT integration paths, talkback, door access and
translations originate there. All other code is new and not shared with Ring View. Two upstream fixes have been ported and credited: camera-proxy
token fix (v0.12.3) and recording controls fix (v0.12.4).

Items added since the fork are in [CHANGELOG.md](CHANGELOG.md).

Released under the [MIT License](LICENSE); see [NOTICE](NOTICE) for attribution.

Not affiliated with, endorsed by, or supported by Ring LLC, Amazon, or the ring-view
project. The card communicates only through Home Assistant, never directly with Ring,
and contains no telemetry.

---

## Screenshots
<!-- SCREENSHOTS:START -->
<!-- SCREENSHOTS:END -->
