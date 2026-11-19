# Floor Is Yours — pre-release playtest

## Checks performed

All five JavaScript modules passed Node syntax validation. HTML IDs are unique and direct `$()` ID selectors match the document. `npm test` covers:

- Host-validated placement bounds, overlap and turn ownership.
- Countdown, race finishing order/points, build-order rotation and obstacle persistence.
- Trap reward once per owner/victim/round and exclusion of self-trap points.
- A simulated racer successfully traverses the unmodified base course and activates its checkpoint.
- Five-round completion and the expected total finishing points.
- Disconnect turn-skipping.
- Mock-wire room handshake, guest identity binding, roster, input, command and snapshot delivery.
- Mock-DOM app flow: guest enters the match, receives their build turn, submits a placement, returns to lobby; host takes turns and enters racing; solo mode creates practice bots.

These are not browser/WebGL tests and do not establish end-to-end connectivity.

## Browser/UI checklist

- Serve over HTTP(S). Confirm both dependency CDN imports succeed, including the on-demand PeerJS bundle.
- Test Chrome, Firefox, Edge and Safari; confirm WebGL is enabled.
- Look at the home scene, active build preview, all five piece models, placement grid, scoreboard and race camera.
- Keyboard-only building: 1–5, arrows, Enter, Skip. Help/menu dialogs and focus/escape.
- Test mouse hover/click placement and rejected overlap/protected-area positions.
- Test portrait/landscape phones: tap placement, directional preview controls, simultaneous move+jump/dash, pointer cancel and orientation changes.
- Check follow/full-course camera, early/late terrain, readability with six players, and checkpoint/goal positioning.
- Compare rendered hammer/blink timing and host collisions. Guest interpolation can visibly lag.

## Gameplay checklist

- 2 and 6 players; practice bots; all five rounds and rotating initial builder.
- Host and guest placement, skips, placement timeout, disconnection during active/inactive build turn.
- Gaps, underside collisions, ledge edges, jump/dash recharges, updrafts, falls, checkpoint, manual respawn.
- Spikes/hammers score only one +2 per opponent per round per owner, even across multiple traps. Self-traps/falls never score.
- Race ranks, simultaneous finishes, no finisher round, all-player finish early, race timeout, trap points after finishing.
- Final tie messaging, inspect completed course, online host return-to-lobby, online guest lobby refresh, solo replay.
- Course can become impossible by design; verify timeout allows progression, without promising every user course is traversable.

## Online checklist

- Run on two independent devices and two different networks, not just two tabs.
- Test host creation, invite link and manual code, bad code, unknown room, public signaling failure, full room, version mismatch, join while match active.
- Ensure guest actually controls their own avatar and receives a placement turn.
- Latency, packet delay/loss, shared Wi-Fi vs cellular, VPN and restrictive NAT. Configure/test TURN if needed.
- Host leave, guest leave, host device sleep, background tabs, connection watchdog. There is no reconnect/host migration.
- Guest controls must neutralize after about 350 ms without input updates; no stuck motion after blur/menu.
- No pending peer should enter after host starts the match. All connected players should see consistent persistent pieces, scores, rounds and phase timers.

## Known limits

- No actual browser, screenshot, 3D rendering or two-device networking test was available during generation.
- Practice AI is basic; it can get stuck or repeatedly fall when players construct difficult obstacles.
- Physics is a small custom 2D AABB simulation, not a robust general-purpose 3D physics engine.
- Reliable snapshots can queue on poor connections; guest prediction/adaptive networking are absent.
- Menus pause solo practice only. Background browser throttling can change match pacing.
- No voting, undo, deletion, balance certification, audio, local same-keyboard mode, replay capture, map exports, matchmaking or server persistence.
