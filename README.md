# FLOOR IS YOURS — Tidal Games

A side-on 3D build-and-race party game for 2–6 online players, with solo practice against three simple AI builders/racers. Create a course together, race it, score points, and make progressively worse architectural decisions over five rounds.

Original code, procedural art, and branding. Three.js for rendering; the same PeerJS/WebRTC room-code approach used by You Need 2 for networking. **This is a standalone addition to your arcade, not a replacement for its homepage.**

## Publish to GitHub Pages

### Add to your existing Tidal Games site

1. Extract the archive.
2. Upload the entire `floor-is-yours` folder into your existing repository.
3. Add a link from your existing homepage:

```html
<a href="./floor-is-yours/">Play Floor Is Yours ↗</a>
```

4. Let the existing GitHub Pages deployment finish, then open that link.

### Publish as a separate site

Upload the **contents** of the folder to a new repository root. In Settings → Pages, choose Deploy from a branch, your default branch (usually `main`), and `/(root)`. Open the HTTPS website after deployment. No build step, npm install, or backend deployment is required for the default configuration.

### Local preview

Serve this directory over HTTP. For example, with Python installed, run `python -m http.server 8000`, then open localhost:8000. Do not double-click index.html via `file://`; ES module imports need HTTP(S). The site imports Three.js from jsDelivr and imports PeerJS from esm.sh on demand. Internet is required to fetch these dependencies. Solo mode does not contact a signaling server, but is not a fully offline installation.

## Invite friends

1. Enter a name, choose a color, and Create a room.
2. Copy the invite link, or share the eight-character code privately.
3. Friends enter a name and join the same deployment before starting.
4. Host clicks Break ground once 2–6 players are in the lobby.
5. Online participants can join only in the lobby, not during an active match.

The host stays on the game tab for the whole match. A disconnected guest is removed from future construction turns and remaining races; their already-placed obstacles remain. There is no guest takeover bot online. Losing the host ends the match. There is no host migration or reconnection.

## Rules

- Five rounds; one placement turn per connected player per round.
- Each build turn lasts 18 seconds. Pick a piece and click a legal position, or Skip. Timeouts skip automatically.
- Pieces snap to a one-unit grid and remain through the entire match.
- Build inside the outlined zone. Placement checks protect the start/finish/checkpoint and keep pieces apart. There is no undo, player voting, map reset during a match, or solvability guarantee. A deliberately impossible course can result in no finishers.
- After everyone builds, a three-second countdown starts a 55-second race.
- Jump the base gaps, dash in the air, navigate the traps, and reach the checkered flag at the right.
- Touch the checkpoint on the center island to respawn there. Falling or touching a hazard respawns you after about 0.9 seconds. No life limit.
- Race ends when all connected players finish or the timer expires. An eight-second summary precedes the next build phase. Starting builder order rotates each round.
- Finishing order earns 10 / 7 / 5 / 3 / 3 / 3 points. Non-finishers get no race points.
- Player-owned spikes/hammers earn their owner +2 for each different rival they hit, at most once per rival per round across all that owner's traps. No self-trap points. Falling by itself earns no trap credit. Trap points can be earned while the owner has already finished.
- Highest total after five rounds wins; ties are shared. The host can return everyone to the lobby to play again. Solo practice can restart from the results screen.

## Pieces

| Piece | Behavior |
|---|---|
| Platform | Three-unit solid platform. Helpful, obstructive, or both. |
| Spikes | Two-unit hazard strip. Dangerous from all sides, not a solid support. |
| Swing hammer | Moving circular hazard suspended above a two-unit solid ledge. |
| Updraft | Lifts players in a narrow column above the fan; steer in flight. Fan housing is visual, not a solid platform. |
| Blink ledge | Three-unit platform, solid for 2.5 sec then gone for 1.5 sec. Pulses before disappearing. |

Movement/collision is 2D on the X/Y plane, rendered with 3D geometry and an angled orthographic camera. Players visually share the course but do not collide or push one another. The decorative third dimension does not allow walking around obstacles.

## Controls

Desktop racing: A/D or left/right arrows move; Space or up arrow jumps; Shift dashes; R respawns. Dash recharges in 1.05 seconds and can be used in midair. F toggles full course/follow camera.

Building: 1–5 selects a tool; click a course position to place. Arrow keys move the placement preview; Enter confirms. The preview turns red for rejected positions. Mouse hover updates the preview but does not place until clicked. There is no delete tool or undo.

Touch racing: left/right buttons plus Jump and Dash. Touch building: tap the course directly, or use the placement directional pad and Place piece button for precision on small screens.

Menus: How to play and Menu buttons. Escape closes dialogs using native browser dialog behavior. Movement is neutralized while dialogs are open. Online does not pause; solo practice pauses when help/menu dialogs are open. Solo practice still counts build/race time while its tab is hidden and browser scheduling permits it; keep the game visible.

## Networking and configuration

`network.js` uses PeerJS/WebRTC reliable data channels, **not NetplayJS**. Host simulates at 60 ticks/sec and sends authoritative snapshots at approximately 20 Hz or on phase transitions. Guest players send controls at 20 Hz; guest rendering smooths the latest authoritative positions. No rollback, prediction, dedicated authoritative server, or anticheat. Guest input delay therefore includes network latency. A 350 ms stale-input timeout neutralizes missing guest controls; a roughly 16-second connection watchdog removes disconnected peers.

Room codes use eight cryptographically random symbols from a 32-character alphabet without I/O/0/1, namespaced under `tidal-floorisyours-v1-`. PeerServer registers the host ID; ID conflicts are retried. This is a private invitation mechanism, not authentication. Share codes with trusted friends. No passwords, accounts, chat, matchmaking, or room directory.

Default networking depends on the public PeerJS signaling service and the default ICE configuration. Restrictive NATs/firewalls/VPNs/school/work networks may require a TURN relay; a relay is **not configured or bundled**. Edit `config.js` to set a custom PeerServer and ICE services. Static GitHub Pages cannot run those services. Use short-lived TURN credentials from a backend/service rather than exposing permanent credentials in a public repository.

Keep the host tab visible: browser throttling or device sleep can stall the game and trigger disconnects. A browser-hosted match isn't a persistent server. Dependency CDN or public signaling outages can prevent loading/connection. No two-device or real internet WebRTC test has been run in this build environment.

## Privacy and security

No analytics, browser storage, account collection, text chat, uploaded images, or game telemetry. Dependency CDNs, signaling/ICE infrastructure and peers may receive normal IP/network metadata. Host is authoritative and can modify game state; this is not secure competitive infrastructure. Incoming commands bind to the sending connection's assigned slot; placement, phase, and ownership are host-validated. Rate limits and simple payload bounds exist but are not a comprehensive security audit. Avoid real personal names and invite only trusted players.

## Files and tests

- `index.html`, `style.css`: home, lobby, HUD, help, results, responsive controls.
- `engine.js`: rendering-independent simulation, build turns, physics, traps and scoring.
- `scene.js`: Three.js course, models, preview, camera, visual interpolation.
- `network.js`: PeerJS room registration, handshake, roster, peer messages and watchdog.
- `app.js`: game lifecycle, host simulation, guest state, controls, UI and practice bots.
- `config.js`: optional custom signaling/ICE configuration.
- `tests/`: pure simulation tests, mock-wire networking tests, mock-DOM application-flow tests.

Run `npm test` with Node 18+ for the tests. No packages need installing for those tests. Tests verify placement/turn authority, overlap bounds, countdowns, race order, score totals, trap credit caps, self-trap exclusion, checkpoints, basic course traversability, piece persistence, five-round completion, disconnect turn skips, room protocol and input identity, guest building/start lifecycle, room return, host turns, and solo bot setup.

**Not tested here:** real browser rendering, pixel output, touch hardware, actual WebRTC/NAT traversal, bandwidth and frame rate under six players, CDN responses, full game balance or generated player-course solvability. See TESTING.md before wider release.

## Scope

Implemented: one course layout, five pieces, five rounds, four-player solo practice, 2–6 human private matches, build/race scoring, checkpoint, construction preview, keyboard and touch controls, follow/full-course view, rematch/lobby flow.

Not implemented: multiple courses, moving goals, player voting, replay clips, sound/music, cosmetics unlocks, persistent progression, controller input, reconnect, public lobbies, map saving, local same-keyboard multiplayer, rollback/prediction, mobile landscape camera tuning or accessibility-compliant nonvisual gameplay. Practice bots use simple heuristics, can fail user-made courses, and are not competitive AI.

Original procedural models and textures; no copied assets. Project source is MIT-licensed. Third-party libraries retain their own licenses.
