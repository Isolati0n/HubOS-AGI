# Hub: target list and design (design in progress)

AGI premise, not buildable today. Machine: hub. Date: 2026-10-14. Status: targets 1 to 24 approved by the owner on 2026-10-14; target 25 added and approved the same day; design in progress (sections 3.1 and 3.2 written, the rest not yet). Track: theorycraft (project file HUB-OS-AGI.md). The real build's source of truth stays HUB-OS.md on main; nothing here changes it.

Labels: TESTED = a command and its output. SOURCE = a link or file and date. BELIEVED = not checked. UNKNOWN = not established. Almost every number below is a target, not a measurement.

## Fixed boundaries used
- Hardware is fixed. All vendor firmware is a fixed boundary (board, GPU, network chip, SSD, projector, DAC). Firmware time is not counted in any target.
- The network between machines and the internet are perfect: zero latency, unlimited bandwidth, always on, free. Everything inside the hub (PCIe, memory, storage) is real.
- Electricity is free, unlimited and always on. Power never fails.
- The projector is decided in December. Until then targets are in refreshes at 60 Hz (1 refresh = 16.7 ms; HUB-OS.md: 4K at 60). Projector lag is measured, not targeted.
- "Full load" = 20 windows open, each redrawing its whole 4K picture at 60 frames per second, plus sound from every node.

## Targets
Format: what is measured; target; why; how it will be checked; label.

Never freeze or crash
1. Longest stall of picture or pointer: at most 33 ms (2 refreshes) in a 30-day full-load run with no faults injected. Why: a frozen hub blocks every machine. Check: a capture card films an on-screen frame counter; firmware pauses are logged separately. BELIEVED.
2. Hub reboots or desktop restarts in that run: 0. Why: there is no spare hub. Check: the run log, plus machine-checked proofs that no hub program can crash on anything a node sends. BELIEVED.
3. A failing node, window or sound stream costs 0 frames elsewhere. Its window keeps its last picture marked stale and goes live within 1 s of the node coming back. Why: one bad machine must not hurt the rest. Check: 1,000 injected failures. Stale picture: SOURCE (HUB-OS.md, session layer). Numbers: BELIEVED.

Recovery when a part of the hub fails
4. A hub program dies or hangs (a program restart, not a reboot): the screen is back within 1 s with the same windows in the same places and sessions alive; 0 reboots; 0 false kills. Why: uptime comes first. Baseline in the real build: about 35 s to notice a hang and about 3 s to restore (TESTED there, docs/proposals/bulletproof-compositor.md). Check: kill and freeze each program 100 times. Targets: BELIEVED. Baseline: SOURCE.
5. GPU reset, or projector switched off and on: no reboot; windows back within 2 s after the device itself is ready (the device's own reset or sync time is measured, not targeted). Why: both can happen in normal use. Check: 50 forced GPU resets and 50 projector power cycles. BELIEVED.
6. Disk dies: every window keeps running and an alert shows; ECC memory corrects bit errors and reports them. Why: a dead disk must not stop work. Check: switch the disk off in a test mode. SOURCE (HUB-OS.md: system copied into RAM, ECC).
7. Interrupted save: 0 settings lost or corrupted when the hub is reset (watchdog or kernel panic) in the middle of a save. Why: this replaces the power-cut target, because power never fails. Check: 200 forced resets at random moments. BELIEVED.
8. Part replaced: after a failed part is replaced, the hub is restored from the NAS backup to its previous settings, saved layouts, mixer settings, credentials and crash record in at most 5 minutes, at most 3 steps, with no typed commands. Windows are not reopened (they never reopen by themselves). A new board's firmware settings come on top. Why: there is no spare hub. Check: wipe the disk, restore, time it. BELIEVED.

Click to window
9. An open machine's window is fully shown within 33 ms of the click. A closed machine's live desktop is shown within 50 ms. Why: both are under the 0.1 s "feels instant" limit. Check: hub timestamps plus light-sensor spot checks; worst of 1,000 clicks. Targets: BELIEVED. 0.1 s limit: SOURCE (Nielsen, Response Times: The 3 Important Limits, 1993, updated 2014, https://www.nngroup.com/articles/response-times-3-important-limits/, read 2026-10-14).

Input delay on the hub side
10. The hub adds at most 1 ms to any key, click or pointer move, worst case in 24 h. This excludes the wait for the projector's next refresh. Why: delay is felt on every action. Check: hub timestamps at arrival, hand-off and screen flip, plus a light-sensor check end to end. BELIEVED.
11. Input flip: the hub takes input within 1 refresh of the flip reaching it; 0 stuck keys or buttons in 10,000 random flips in the middle of a press. Why: a stuck key acts on its own. Check: scripted flips. No stuck keys: SOURCE (HUB-OS.md, input sharing). Numbers: BELIEVED.

Sound
12. All nodes heard at once, mixed, with per-node volume and mute, a master volume and a mute-all, settings remembered per node; 0 dropouts in 24 h with every node playing. Why: the owner listens to several machines at once. Check: hub counters plus a recording of the DAC output. Features: SOURCE (HUB-OS.md). 0 dropouts: BELIEVED.
13. Lip sync at the owner's seat: sound at most 5 ms early and at most 15 ms late against the picture, projector lag compensated. Why: experts notice from 45 ms early or 125 ms late. Check: a flash-and-click test with a light sensor and a microphone, 1,000 samples. Limits: SOURCE as quoted on https://en.wikipedia.org/wiki/Audio-to-video_synchronization (ITU-R BT.1359-1 detectability 45 ms lead / 125 ms lag; EBU R37 per-stage +5 / -15 ms), read 2026-10-14; the standards themselves were not read.
14. Bit-perfect sound: with one node playing and its volume and the master at 100%, nothing else playing, the bits sent to the DAC equal the node's bits; 0 differing bits over 10 minutes. Why: the owner asked for it. Check: record the digital stream just before the DAC and compare bit for bit. The DAC's own conversion is fixed hardware and is not checked. Mixed sound: a target will be proposed with the design. Label: target; UNKNOWN whether the mixer path allows it until the DAC is chosen.

Picture
15. Whole and exact: at 100% zoom the hub's output carries exactly the node's pixels. Every frame is shown whole, once, at a refresh; 0 torn frames (part old, part new) in 24 h at full load. Cost accepted: an average wait of 8.3 ms, 16.7 ms at worst, at 60 Hz (TESTED, arithmetic). Check: a capture card films a test pattern that changes every frame; a torn frame shows as a split. Lossless: SOURCE (HUB-OS.md). Rest: BELIEVED. Open: this target assumes lossless pictures.

Clipboard
16. A copy anywhere lands in the history within 100 ms; a paste reaches the chosen machines within 100 ms; text arrives byte-identical in any language (the real build's Ubuntu wayvnc 0.7.2 garbled non-ASCII text). Why: it should feel instant. Check: 10,000 scripted copies with emoji and non-Latin text. BELIEVED; 0.1 s limit: SOURCE (Nielsen, as in target 9).
17. Nothing is pasted without the owner's action; no echo loops; the history holds at least 500 text items plus 100 MB of images and file references, searchable, with pins kept; files move node to node only on paste, never through the hub. Why: no surprises in any machine. Check: scripted tests. SOURCE (HUB-OS.md, Clipboard).

Updates and rollback
18. 0 updates unless the owner starts one. 100 of 100 unsigned or tampered images refused. The screen is gone at most 10 s during an update. Why: nothing changes without the owner. Check: a set of tampered images; timed updates. Rules: SOURCE (HUB-OS.md). 10 s: BELIEVED. Board firmware time: UNKNOWN.
19. Bad update: the hub goes back to the old image by itself, with the panel up within 30 s of the fault, for faults up to 10 minutes after boot (100 of 100 injected). Recovery mode works with both slots broken, using the spare keyboard. Why: a bad update fixes itself. Baseline in the real build: it waits 120 to 180 s, and its trial check once missed a fault 30 s after start (TESTED there, docs/proposals/init-comparison.md). A/B and recovery: SOURCE. Numbers: BELIEVED.

Maintenance
20. 0 routine tasks. Anything the hub cannot fix itself shows on the bar within 1 s, in plain words, with one button. No typed commands outside recovery mode. Why: the owner is not a cluster operator. Check: every injected fault above ends in a fix or an alert. No schedules, no terminals: SOURCE (HUB-OS.md). Rest: BELIEVED.
21. Crash record: every crash, hang, reset and rollback is saved with its cause and kept through reboots. Why: causes can be found without an expert. Check: one record per injected fault. SOURCE (HUB-OS.md, black box recorder).

Also matters
22. Down alert: a machine that stops answering shows as down within 1 s, and as up within 1 s of being ready; 0 false alerts in 7 days. The network is perfect, so "down" always means the machine itself failed; the real build's "unreachable" state is not used. The real build's own limits (2 s per machine, 5 s total) are guesses (HUB-OS.md, Unverified). Why: trouble is visible at a glance. Check: 100 node pulls and boots. BELIEVED.

Scale
23. 20 windows: every target is met with all 20 changing every frame. 100 windows: every target is met with 100 open and 20 of them changing every frame; RAM use at most 50%. Why: the owner's cluster plus headroom. Check: the same runs at 20 and at 100. 20 and 100: SOURCE (HUB-OS.md scale reference). 50%: BELIEVED. Physics: 100 windows all changing every frame is 199 GB/s of raw 4K pictures (3840 x 2160 x 4 bytes x 60 x 100; TESTED, arithmetic; 4 bytes per pixel is BELIEVED). The candidate board's two-channel DDR5-5200 has a theoretical peak of 83.2 GB/s (SOURCE for 2 channels and 5200: https://www.thomas-krenn.com/en/wiki/AMD_EPYC_4005_Grado, read 2026-10-14). So all 100 changing cannot be met on that board; the limit on other hardware is UNKNOWN.
24. 5,000 machines listed (stretch): no crash or freeze; the list and search answer within 100 ms. Why: the real build's stretch target. Check: a fake list of 5,000. Stretch target: SOURCE (HUB-OS.md). 100 ms: BELIEVED.
25. Incoming picture traffic: with 20 windows changing every frame and the rest hidden, the hub receives at most 1.1 x the screen's pixels per second (about 2.2 GB/s at 4K, 60 Hz, 4 bytes per pixel). Why: it keeps the hub's memory far below its limit whatever the nodes show. Check: per-window byte counters in a full-load run. 1.1 and 4 bytes per pixel: BELIEVED. The 1.99 GB/s base: TESTED, arithmetic. Added by the owner's approval on 2026-10-14.

## Design (in progress)

Written so far: 3.0 scope, 3.1 the compositor and 3.2 the picture path. Not written yet: the base system (boot, kernel, drivers, init, file system), what runs on the hub and how it recovers, input, sound, clipboard, updates and rollback, maintenance. Each part is added after the owner has answered its questions.

### 3.0 Scope (owner decision, 2026-10-14)
- Every layer of the hub's software is from scratch and purpose-built for this hub, written by AGI: the kernel, the drivers, the init system, the file system, the compositor (3.1), the display protocol (3.2) and the programs on top. Nothing is kept from the real build's choices (the upstream Linux kernel, the s6 init) unless a later section says so and gives the reason.
- What stays fixed: the hardware and all vendor firmware (section 4). The from-scratch kernel and drivers must run on the real parts and load vendor firmware as it is.
- The parts list is not chosen until December, so which drivers are needed is UNKNOWN until then.

### 3.1 The compositor (owner decision, 2026-10-14)
- driftwm is the base. AGI rebuilds it for this hub's features and optimizations, and makes it perfect and high-performance. In Wayland the compositor and the window manager are one program.
- ASSUMED (the premise): the rebuilt compositor has no software defects. Real hardware events still happen: a GPU reset, the projector switched off and on, memory limits. Targets 4 and 5 stay; they cover those events and the other hub programs.
- Kept from the real build (SOURCE: HUB-OS.md on main, read 2026-10-14): infinite canvas; each machine has a fixed home place; one window per machine; closing a window leaves the machine's session alive; windows never reopen by themselves after a hub restart; the viewer may scale a picture by whole numbers; zoom never magnifies. Whether zoom should magnify (text size on the projector): UNKNOWN until December.
- The compositor knows which windows are visible and at what size, and gives this to the viewer (the hub program that talks to the nodes). In the real build's driftwm, a window fully off screen already gets a signal about once a second instead of at the screen's rate (SOURCE: docs/proposals/remote-display-lod.md in Isolati0n/HubOS, finding 10, read 2026-10-14; BELIEVED, not tested there).
- Licence: driftwm is GPL-3.0-or-later; anything kept from it stays under that licence (SOURCE: HUB-OS.md). This matters only if the design is ever built.

### 3.2 The picture path (owner decisions on Q12 to Q16, 2026-10-14)
Rule: the hub only receives the pixels it will show, at the size it shows them.
1. Each node keeps one fixed-size 4K screen. The hub never resizes it.
2. Each window is in one state: visible (the viewer asks for pictures at the screen's rate, sized to what is shown), hidden (off screen or covered: it asks for none and keeps the last picture), or returning (it asks for one fresh picture at the size shown, and shows the last picture until it arrives).
3. A scaled copy is a new kind of request: the node scales its own picture and sends the result; its screen size does not change. The real build's protocol (RFB) has no such request, and its "smaller screen" request makes the node resize itself and rearrange its windows (SOURCE: docs/proposals/remote-display-lod.md, finding 5, TESTED there; read 2026-10-14). New protocol messages are allowed in this track. The scaling method is not chosen yet. At 100% zoom the pixels are exact.
4. Pictures are raw and lossless (owner decision, Q12). Lossless compression is allowed but not needed for the bound below, so the first design has no decoder on the hub.
5. Bound: the hub receives at most the screen's pixels per second: 3840 x 2160 x 4 bytes x 60 = 1.99 GB/s (TESTED, arithmetic; 4 bytes per pixel is BELIEVED). That is 2.4% of the candidate board's memory peak of 83.2 GB/s (SOURCE: Thomas-Krenn, see target 23). Raw pictures from every window would be 39.8 GB/s with 20 windows changing, 48% of that peak (TESTED, arithmetic).
6. Why not compression alone: video that does not compress stays full size, so compression gives no bound that holds for any content (BELIEVED).
7. A path from the network chip straight into GPU memory is not needed at 2 GB/s. Whether the platform supports one: UNKNOWN.

What the real build measured about pausing (TESTED there, at 1280x720 on loopback, by a helper agent, not by the author of this document; SOURCE: docs/proposals/remote-display-lod.md sections 5.1 and 5.4, Isolati0n/HubOS at ab1047a, read 2026-10-14): with no requests the node sent 0 bytes; after 10 s paused the first picture came within 2 to 30 ms, as one picture holding the accumulated change, not a backlog. Pausing did not reduce the node's own work (it used 25 to 32% of a core either way).

## 4. What hardware and vendor firmware fix
- Memory: two channels, DDR5-5200, theoretical peak 83.2 GB/s (SOURCE: Thomas-Krenn, see target 23). Real sustained speed is lower (BELIEVED).
- The link between the GPU and memory (PCIe), and the GPU's own memory: UNKNOWN until the parts are chosen. The design keeps picture traffic at about 2 GB/s so that it fits any likely link (BELIEVED).
- GPU reset time, projector sync time and board start-up time are set by firmware and hardware. They are measured, not targeted (targets 5 and 18).
- Projector lag: December.

## 5. Unsolved or not decided yet
- Node-side cost of scaling a 4K picture: UNKNOWN until the node hardware is chosen.
- Time for a window to come back at 4K on real hardware: UNKNOWN. The real-build figure above is for 720p on loopback.
- The scaling method for scaled copies.
- Projector model, refresh rate and lag: December.
- Mixed-sound target (several nodes at once): with the sound design.
- The project file calls the hub's GPU "discrete AMD"; HUB-OS.md on main does not say discrete. Check in December. A discrete GPU adds a real PCIe link and its own memory.
- Whether the compositor's zoom should magnify: UNKNOWN until text on the projector can be judged (December).
- No clock-serving target: the owner decided against it. Phone page and shared phone sessions: dropped from this track.
- Not designed yet: the base system (boot, kernel, drivers, init, file system), what runs on the hub and how it recovers, input, sound, clipboard, updates and rollback, maintenance.

## 6. Check plan (compositor and picture path)
- Per-window byte counters on the hub in a full-load run (20 windows changing, the rest hidden): incoming traffic at most 1.1 x the screen's pixels per second (target 25).
- Time from "window becomes visible" to "fresh picture shown" at 4K, worst of 1,000 (target 9). Result that proves the design wrong: more than 33 ms for an open window.
- Node scaling cost: measure each node's load while it sends scaled copies at 60 Hz. Result that proves the design wrong: a node cannot keep 60 Hz while doing its own work.
- Fault injection (targets 3 to 5): kill and freeze hub programs, force GPU resets, switch the projector off and on.

## 7. Differences from the real build, in plain words
- The real build never pauses or scales windows (owner decision 2026-10-05, because the network is free). This design does both, because the limit that matters is the hub's memory, not the network. The scaled-copy request is new and is not in the real build's protocol.
- The real build patches a young compositor and plans restart-and-restore for its crashes (SOURCE: HUB-OS.md). This design assumes a rebuilt, defect-free compositor, and still keeps recovery for hardware events.
- The real build asks every window for full-size pictures all the time. This design asks only for what is shown.
- The real build keeps the upstream Linux kernel, built for the hub, and uses s6 as its init (SOURCE: HUB-OS.md "Core principles" and its change log entry of 2026-10-13, read 2026-10-14). This design writes the kernel, drivers, init and file system from scratch for the hub (owner decision, 2026-10-14). The details come with the base-system design.
