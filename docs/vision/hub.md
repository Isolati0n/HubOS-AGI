# Hub: target list and design (design in progress)

AGI premise, not buildable today. Machine: hub. Date: 2026-10-14. Status: targets 1 to 24 approved by the owner on 2026-10-14; target 25 added and approved the same day; design in progress (sections 3.0 to 3.5 written, the rest not yet). Track: theorycraft (project file HUB-OS-AGI.md). The real build's source of truth stays HUB-OS.md on main; nothing here changes it.

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

Written so far: 3.0 scope, 3.1 the compositor, 3.2 the picture path, 3.3 the languages, 3.4 the base system (kernel, storage, supervisor) and 3.5 other designs considered. Not written yet: what runs on the hub and how it recovers, input, sound, clipboard, updates and rollback, maintenance. Drivers are not designed (owner decision, 2026-10-15): they depend on parts bought in December. Each part is added after the owner has answered its questions.

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
8. A window drawn small still updates at the screen's rate (owner decision, 2026-10-15). One outside design suggested lower rates for small or zoomed-out windows; it was declined for now because the bound in item 5 holds without it.

What the real build measured about pausing (TESTED there, at 1280x720 on loopback, by a helper agent, not by the author of this document; SOURCE: docs/proposals/remote-display-lod.md sections 5.1 and 5.4, Isolati0n/HubOS at ab1047a, read 2026-10-14): with no requests the node sent 0 bytes; after 10 s paused the first picture came within 2 to 30 ms, as one picture holding the accumulated change, not a backlog. Pausing did not reduce the node's own work (it used 25 to 32% of a core either way).

### 3.3 The languages (owner decisions, 2026-10-15)
The owner wants the AGI to design its own languages for this hub, and to explore that. Every line of code is written for this hub only (owner decision, 2026-10-15). This section is a first sketch, not a language definition.
1. One family of three languages that share one core:
   - a low-level one for the kernel and the parts that touch hardware;
   - a high-level one for the compositor, the viewer and the other hub programs;
   - a small specification language, where a rule is written down before the code (for example "input is delayed at most 1 ms").
2. Two properties are built into all three:
   - Every function carries a proven worst-case running time, so the time slots in 3.4.1 are guarantees and not hopes.
   - The compiler produces a proof together with the program, and a small checker program verifies it. This idea is called proof-carrying code (BELIEVED: it dates from the late 1990s; no source read for this document). Only the checker needs to be trusted. Its size is a target to be set, not a known number.
3. Proofs are required for the kernel, the storage layer and the supervisor (3.4). The other programs get strong tests. This matches target 2, which already asks for machine-checked proofs that no hub program crashes on what a node sends.
4. What a proof does not cover (unsolved, said plainly):
   - A proof shows that code matches its written rule. If the rule is wrong, the program is perfect and does the wrong thing. The check plan therefore tests the rules too, not only the code.
   - The checker, the hardware and the rules are the trusted base. They cannot be proven by themselves.
   - A function that waits on real hardware (the GPU, the SSD) cannot have its waiting time proven. A part is proven for its own work. The waits are budgeted separately and measured.
   - Whether such a language can be designed so that the proofs stay small enough to check: UNKNOWN. Languages and proof tools of this kind exist today, but this document has not read their results.
5. The language family does not replace the real build's open choice among Rust, Ada and Zig for one part (SOURCE: HUB-OS.md); in this track that choice does not arise.

### 3.4 The base system (owner decisions, 2026-10-15)
Scope: boot, kernel, storage and the first program that supervises the rest. Drivers are not designed here; which are needed is UNKNOWN until the parts are chosen in December.

#### 3.4.1 The kernel
1. Isolation by proof, not by hardware walls. Each part is written in the family of 3.3, and the compiler proves that it cannot touch another part's memory. This removes the cost of passing messages between walled-off parts. A failed part is still restarted on its own. BELIEVED: Microsoft's Singularity research system worked in this spirit; the seL4 kernel is the usual example of the wall-based way. Neither was read for this document.
2. Nothing is discovered at boot. The hardware is fixed, so the kernel is built for exactly these parts: no code for hardware the owner does not have, and no loading of parts while running. The one event it must handle is the projector being switched off and on (target 5).
3. Time and memory bandwidth are planned at build time. Each part gets a CPU time slot and a share of memory bandwidth declared in advance. For example, input has a slot that nothing can take, so a flood of window pictures cannot delay a key press. Why: it is a direct way to meet target 10 (at most 1 ms added) and target 23 (20 windows at once). Check: a full-load run with timestamps on input, as in the check plan.
4. One hardware wall stays, because proofs cannot cover vendor firmware. The GPU's firmware can write into memory by itself, so the design walls it off with the IOMMU, a chip feature that limits which memory a device may touch. BELIEVED: the planned CPU family has an IOMMU. UNKNOWN: whether the chosen board enables it, until December.
5. What it replaces from the real build: the upstream Linux kernel with its drivers. What is better: no code for parts the hub does not have; guaranteed time slots instead of a general scheduler. Both are targets, not measurements.
6. Unsolved: how much planned slots cost when the load is lighter than planned (idle waste); whether the proofs of the time slots survive the real memory system's variation (UNKNOWN until measured on the real board).

#### 3.4.2 Storage: a typed object store, not a file system
1. The operating system itself is one sealed, signed image in two slots (A/B), mapped directly with no file lookup. This keeps the real build's rule that a machine refuses an image the owner has not signed (SOURCE: HUB-OS.md). HUB-OS.md also says the system is copied into RAM at start (SOURCE, read 2026-10-14); whether this design copies it is not decided here.
2. Settings, window layouts, mixer settings and clipboard history are records, not files. There are no paths and no folders. Each program declares the shape of what it stores, and the store checks every write against that shape.
3. Every change is committed all or nothing, and every block carries a checksum. An interrupted save leaves either the old state or the new state, never a mix. This is the design for target 7 (0 settings lost in 200 forced resets).
4. Old versions of settings and layouts are kept, because the data is small. How small: UNKNOWN until the hub's data is sized.
5. Unsolved, said plainly: the SSD's own firmware decides when a write is really stored. The design relies on the SSD honoring the flush command (the command that forces data to be stored). Whether the chosen SSD does: UNKNOWN until it is chosen, and the check plan has a test for it. The disk-death case (target 6) is handled by the supervisor and the alert, not by this layer.
6. What it replaces from the real build: a Linux file system and its tools. What is better: a record that is checked against its declared shape cannot be half-written or malformed; this is a target, not a measurement.

#### 3.4.3 The supervisor (the first program, the "init")
1. A tiny supervisor, proven correct. It is the first program to run and the one that never restarts. It starts the other parts, watches them, and restarts any that fail, with a limit so that a part that keeps failing is stopped and an alert is raised (the same idea as the real build's restart limit of 5 restarts in 60 s; SOURCE: docs/proposals/init-comparison-run.md line 1021 in Isolati0n/HubOS at ab1047a, read 2026-10-15).
2. Every part is crash-only: there is no clean shutdown path, and starting up is the same as recovering from a crash. BELIEVED: this idea has been published under the name "crash-only software" (about 2003; not read for this document).
3. The boot order is worked out at build time and stored as a fixed plan, so nothing is decided while booting. The time to boot is set by firmware and hardware and is measured, not targeted (section 4).
4. A hardware watchdog is the last resort: a chip timer that resets the machine if the supervisor stops checking in. BELIEVED: most boards have one. UNKNOWN for the chosen board until December. Target 7 already counts resets from the watchdog.
5. Deferred to the hardware stage: a warm standby compositor, a second copy holding the same state that takes over within one refresh (1000 / 60 = 16.7 ms, arithmetic). It would help targets 4 and 5. UNKNOWN: whether a second compositor fits in GPU memory and bandwidth, and what it does to target 25. Not part of the first design.
6. What it replaces from the real build: s6 with s6-rc and a separate watcher (SOURCE: HUB-OS.md). What is better: one proven program instead of several, and a plan fixed at build time. A target, not a measurement.

#### 3.4.4 Boot and the trust anchor (added with the owner's OK, 2026-10-15, from the outside designs)
1. Chain. The board's fixed firmware starts a small loader. The firmware runs only a program signed by a key the owner has enrolled in its Secure Boot database. BELIEVED: many ordinary boards let the owner do this. Whether the chosen board does: UNKNOWN until checked in December. The loader sits on a small FAT partition, because BELIEVED that is where the firmware looks for it; this is the one place a file format exists, and everything else is raw. The loader checks the signed system image in slot A or B, copies it into memory (as the real build does; SOURCE: HUB-OS.md, read 2026-10-14) and starts it. No menu, no configuration file, nothing else runs first.
2. What this checks: that only images signed by the owner run. What it does not check: the firmware's own integrity. That is vendor code we cannot change, and detecting a bad firmware from software is UNKNOWN.
3. Downgrade protection: a signed counter in the state area; the loader refuses an image with a lower counter. It is not enforced by hardware: someone with physical write access to the SSD could roll it back. BELIEVED to be enough for the owner's own machine. The design does not depend on a TPM (a small security chip). AMD's firmware TPM had two high-severity flaws, CVE-2026-6726 (CVSS 8.5) and CVE-2026-6727 (CVSS 8.3), both from the standard TPM reference code (SOURCE: https://www.amd.com/en/resources/product-security/bulletin/amd-sb-7064.html, published 2026-08-11, read 2026-10-15). It is also vendor firmware.
4. AMD Platform Secure Boot (PSB): if a CPU has it enabled with a vendor key, the owner may not be able to use their own keys. The source read (https://ioactive.com/exploring-amd-platform-secure-boot/, 2026-10-15) describes PSB but does not say who owns the fuses or whether an owner can use their own keys. Search results suggest vendor-locked CPUs exist in some prebuilt PCs (titles only, not read). December checklist: buy retail parts, not OEM or used ones, and confirm the CPU and board are not locked.
5. A new image that does not reach a healthy heartbeat within N minutes is rolled back automatically. N is not chosen. The pointer that says which slot is active is written as two sectors alternately, each with a generation counter, and the loader takes the valid one with the highest counter. A torn write cannot strand the machine. (Idea from the outside designs; whether the SSD writes a sector atomically: UNKNOWN until it is chosen.)
6. What it replaces from the real build: nothing is read for this document beyond the rule that a machine refuses an image not signed by the owner (SOURCE: HUB-OS.md), which is kept.
7. Check: boot an unsigned image (must be refused); try a downgrade (must be refused); 200 forced resets at random moments during boot and during an update, after which the machine always lands in the old or the new image and never in neither. Result that proves the design wrong: any unsigned image runs; any reset leaves no bootable slot; the chosen board will not accept the owner's key.
8. Unsolved: Secure Boot key enrollment and PSB lock on the chosen board (December); the firmware's own integrity.

#### 3.4.5 Device contracts
Drivers are our own code under the premise and are not designed here (owner decision, 2026-10-15). The base system defines four contracts that any driver must meet. Common rules: never block the caller; count everything dropped; a GPU reset is survived without a reboot, with windows back within 2 s after the device is ready (target 5).
- Display: one framebuffer per frame is handed over and the previous one handed back. If the driver cannot accept one, it drops it and counts it.
- Input: a bounded ring, no coalescing, drops counted; the repair rule in 3.4.6 applies.
- Sound: the driver plays one mixed stream. Per-node volume and mute are done in the mixer, not the driver.
- Network: one receive queue and one transmit queue per node; whole frames, in order, never blocking.
What each part must provide is on the December checklist in section 4. Check: a test harness with a fake driver that breaks each clause on purpose; the system must survive or fail in a defined way. Result that proves the design wrong: a driver that blocks the canvas.

#### 3.4.6 Recovery details (added with the owner's OK, 2026-10-15)
1. Input runs outside the frame schedule, on its own core. A key or pointer event raises a small interrupt that wakes the input thread at once; pointer moves are applied without waiting for a frame; keys go straight to the focused window's forward queue. The time from interrupt to ring is UNKNOWN until measured. The ring read and forward (at most 256 bytes) is BELIEVED under 50 microseconds. The total of at most 1 ms (target 10) is a target, not tested.
2. Key-state repair (target 11): the input driver keeps the set of keys currently down. After any drop, on a focus change, on a reconnect, and once a second, it sends the full set. The receiver replaces its own state with it and does not merge.
3. Two clocks. The system clock comes from the CPU's constant-rate time counter (BELIEVED stable on this CPU class; no source read). The display clock follows the projector's vsync while it is present, runs free from the system clock when the projector is off, and locks again when it returns.
4. Watchdog stages. The frame scheduler kicks a watchdog every frame. If it is silent for 30 frames (30 x 16.6 ms = 498 ms; arithmetic), all frame-scheduled parts are restarted with the windows kept. That leaves about 500 ms of the 1 s in target 4 for the restart itself (derived). A second firing within 2 s resets the GPU and display path only. A third resets the machine; in normal operation that should never happen. Whether the board's watchdog can raise an interrupt before it resets the machine: UNKNOWN until December. If the timer block itself hangs, nothing detects it; that is the floor of this design.
5. A part stopped by the restart limit shows a "failed" tile: its last picture marked stale, in line with target 3.
6. Crash record: when a part dies, the supervisor, and only the supervisor, writes a checksummed record to a circular area: the time, the part, the last 16 KB of its ring and its program counter. The write never blocks the canvas. Whether memory survives a reset: UNKNOWN (firmware may clear it).
7. Check: kill and freeze each part 100 times and time the restore (target 4); force dropped input events 10,000 times with random flips and count stuck keys (target 11, must be 0); switch the projector off for 60 s and on again and confirm the frame schedule resumes with no part killed (target 5); inject a 400 ms stall in the compositor and time the restore. Result that proves the design wrong: any restore over 1 s, any stuck key, a part that was killed because the display clock was lost.
8. Unsolved: a hung CPU cannot be recovered without a reset (nothing runs on a dead core); the sound design later has to settle the mixer's delay budget, because a buffer of up to 32 ms could break target 13 unless the projector's lag absorbs it (projector lag: UNKNOWN until December).

### 3.5 Other designs considered (2026-10-15)
Three other AIs (Grok, DeepSeek and Kimi) were each given the machine's job, the fixed limits and the targets, but not this document, and each designed a base system on its own. Their texts are the owner's copies and are not kept in this repository. Below is the manager's review. Their claims about outside systems were not checked against sources.
1. What all three designs share: a signed A/B system image; an all-or-nothing, checksummed commit for small changing data; a tiny supervisor with a restart limit; a plan fixed at build time; proofs for the core only. Agreement between AIs is not evidence that the shape is right; only the checks in section 6 are.
2. Where they differ: isolation. Grok uses separate address spaces. DeepSeek uses one address space where a part only gets memory through ports. This document (3.4.1) uses isolation by proof plus an IOMMU wall around the GPU. The cost of each: UNKNOWN until measured on the board. The check is the full-load run for targets 10 and 23.
3. Flaws found in the other two (arithmetic re-run by the manager; the rest derived from their own text, not tested):
   - Grok: scrubbing 32 to 64 GB of ECC memory every 100 ms needs 0.38 to 0.77 s at the 83.2 GB/s peak, so it cannot work as written (scrubbing is normally done by the memory controller; BELIEVED, not checked). Its 50 microsecond address-space switch times 23 parts is 1.15 ms, more than its 1 ms cycle. A polled 1 ms timer can add up to 1 ms to input, which uses the whole of target 10. Dropping the oldest entry from a full ring is wrong for input and sound. Nothing watches its cycle engine, and it uses no hardware watchdog.
   - DeepSeek: it assumes an owner-burnable one-time fuse and a monotonic counter; both are UNKNOWN on the chosen hardware. It calls itself a single address space but moves pages between address spaces. Its input parts run in the 60 Hz frame schedule, so input can wait up to 16.7 ms. Its clock follows the projector's refresh, but the projector can be switched off (target 5). Its watchdog waits 100 frames of 16.6 ms, which is 1.66 s, more than the 1 s of target 4. It used none of the TESTED, SOURCE, BELIEVED or UNKNOWN labels.
   - Kimi: it says 100 windows of 4K at 60 frames per second is "50 TB/s"; the arithmetic is 100 x 33.2 MB x 60 = 199 GB/s (239% of the 83.2 GB/s peak), about 250 times less. Its fix, lossy compressed tiles decoded on the hub with 20 GPU decode contexts, conflicts with the owner's lossless decision (Q12) and adds a decoder to the hub; whether a real GPU offers 20 decode contexts with per-context reset: UNKNOWN. It assumes hardware not on our list: a boot ROM it controls, a TPM-like chip, a key hash burned at provisioning, a "dead-man's circuit" (all UNKNOWN). It picks Rust, LLVM and TLA+ off the shelf, against the from-scratch scope; TLA+ model checking to a bounded depth is not a proof. It used none of the labels. Its rule "show the previous frame if a frame is late" may count as a stall in a capture-card check (BELIEVED). Input forwarding sits in its best-effort class, a 2 s wedge threshold and a 1, 2, 4 s restart backoff exceed the 1 s of target 4, and it assumes 64 GB of RAM (UNKNOWN).
   - Both Grok and DeepSeek, and Kimi: dropped input events leave stuck keys (target 11) unless the key state is resynchronised afterwards. Grok and DeepSeek do not deal with the picture-memory limit of 3.2; Kimi reaches a different, lossy answer to it. All three outside designs assume security hardware that is not on our list (a fuse, a TPM-like chip or a counter); how the hub anchors trust on an ordinary AMD board is open (section 5).
   - What Kimi does well: it states what each hardware part must provide; it writes its manifest as two sectors written alternately with generation counters, so a torn pointer cannot strand the system; it admits that a hung CPU cannot be recovered without a reset; and it decodes only what is visible, as 3.2 does. Four designs have now reached "fetch only what is shown"; that is agreement, not proof.
4. Ideas to add to 3.4. Now written into 3.4.4 to 3.4.6 (owner's OK, 2026-10-15), except where noted. The list as first made:
   - A boot design: a small loader that verifies the signed image before it runs, with protection against going back to an older image. Where a counter for that lives: UNKNOWN.
   - A staged watchdog: reset the GPU and display path first, the whole machine only if that fails twice.
   - Four device contracts that any driver must meet (display, input, sound, network): never block, count what is dropped, whole frames in order.
   - A "failed" tile for a part that is stopped by the restart limit, in line with target 3's stale marker.
   - A crash record holding the last 16 KB of the dead part's ring and its program counter.
   - A rule that repairs key state after dropped input events.
   - From Kimi: a new image that does not reach a healthy heartbeat within N minutes is rolled back automatically (N is not chosen); the manifest pointer is written as two sectors alternately with generation counters; and a list, for December, of what each hardware part must provide (GPU: a way to reset one context without touching the compositor's; network chip: DMA into registered buffers with arrival timestamps; sound chip: a defined underrun that silences one channel only). Whether any real part offers these: UNKNOWN.
5. Follow-up rounds (DeepSeek, 2026-10-15). It agreed with the listed flaws and fixed most of them (input path, clocks, watchdog time, key repair, drivers as our own code). On the picture memory, its first fix depended on typical content; in a second round it found a limit that holds for any content: receive no more than the projector can show. That is the same idea as 3.2. Its cap of 1.57 GB/s used the projector link's 3 bytes per pixel; in memory a pixel is probably 4 bytes (BELIEVED), which gives 1.99 GB/s, so a cap must be set in pixels per second times bytes per pixel, as target 25 does. Its count of 6 crossings of memory per byte assumes a CPU decode step; raw pictures need none (4 crossings, 1.99 GB/s x 4 = 8 GB/s; with a discrete GPU the final frame may not touch system memory; GPU type UNKNOWN). Its source for the firmware TPM flaws was checked (3.4.4 item 3); its statement that a PSB-enabled CPU excludes the owner's keys was not confirmed by the source read (3.4.4 item 4). Its offer to change the sound target to "no gap over 2 ms" was refused: only the owner changes targets, and 0 dropouts in 24 hours stands (owner decision, 2026-10-15). Follow-up prompts for Grok and Kimi were written; their answers are not in yet.
6. Questions for a red team (a hostile review): the cost of proof-based isolation against address spaces; what the proof checker has to be trusted for; whether a static schedule can accommodate a real GPU's timing; whether the message rings can deadlock; whether the content-independent picture limit of 3.2 holds when a window returns to view; whether the trust anchor of 3.4.4 holds on an ordinary board.

## 4. What hardware and vendor firmware fix
- Memory: two channels, DDR5-5200, theoretical peak 83.2 GB/s (SOURCE: Thomas-Krenn, see target 23). Real sustained speed is lower (BELIEVED).
- The link between the GPU and memory (PCIe), and the GPU's own memory: UNKNOWN until the parts are chosen. The design keeps picture traffic at about 2 GB/s so that it fits any likely link (BELIEVED).
- GPU reset time, projector sync time and board start-up time are set by firmware and hardware. They are measured, not targeted (targets 5 and 18).
- Projector lag: December.
- The GPU's firmware can write into memory on its own; the design walls it off with the IOMMU if the board allows it (3.4.1). UNKNOWN until December.
- The SSD's firmware decides when written data is really stored; the design depends on it honoring the flush command (3.4.2). UNKNOWN until the SSD is chosen.
- Whether the board has a hardware watchdog (3.4.3): UNKNOWN until December.
- Trust anchor for signed images: which of these the chosen board offers is UNKNOWN until December: a way for the owner to enroll their own Secure Boot keys in the fixed firmware, a TPM (a small security chip), and a place for an anti-rollback counter. BELIEVED: many ordinary boards let the owner enroll keys; not checked. The design may not assume a fuse, a boot ROM or a TPM until one is confirmed.
- Boot and signing (3.4.4), December checklist: can the owner enroll their own Secure Boot keys; is the CPU or board locked by AMD Platform Secure Boot; is there a TPM (not relied on); how a sector write behaves on the chosen SSD. UNKNOWN for all until the parts are chosen.
- What each part must provide (December checklist, from the outside designs): GPU: resetting one context without touching the compositor's; network chip: DMA into registered buffers with arrival timestamps; sound chip: an underrun that silences one channel only. UNKNOWN for every part until they are chosen.
- Trust anchor for signed images (3.4.4): Secure Boot key enrollment and PSB lock on the chosen board; the firmware's own integrity. Open until December.
- Mixer delay budget against target 13 and the watchdog interrupt (3.4.6): with the sound design and in December.
- Custom hardware: the board, RAM and GPU are not redesigned in this track (owner decision, 2026-10-15). A separate, later "custom hardware wish list" may follow the software designs. It would assume real chip factories and real physics, and each item would name the number that fails today.

## 5. Unsolved or not decided yet
- Node-side cost of scaling a 4K picture: UNKNOWN until the node hardware is chosen.
- Time for a window to come back at 4K on real hardware: UNKNOWN. The real-build figure above is for 720p on loopback.
- The scaling method for scaled copies.
- Projector model, refresh rate and lag: December.
- Mixed-sound target (several nodes at once): with the sound design.
- The project file calls the hub's GPU "discrete AMD"; HUB-OS.md on main does not say discrete. Check in December. A discrete GPU adds a real PCIe link and its own memory.
- Whether the compositor's zoom should magnify: UNKNOWN until text on the projector can be judged (December).
- No clock-serving target: the owner decided against it. Phone page and shared phone sessions: dropped from this track.
- The language family (3.3): whether proofs stay small enough to check; the size of the trusted checker.
- Memory capacity at the 5000-window stretch: one last 4K picture per window is 33.2 MB, so 5000 windows are 166 GB (arithmetic). Target 23 caps RAM at 50% and the board's installed RAM is UNKNOWN. How hidden windows' last pictures are kept (for example, compressed) is not designed.
- Planned time slots: idle waste, and whether proofs survive the real memory system (3.4.1).
- Drivers: not designed (owner decision, 2026-10-15). Which are needed: UNKNOWN until December.
- Not designed yet: what runs on the hub and how it recovers, input, sound, clipboard, updates and rollback, maintenance.

## 6. Check plan (compositor, picture path and base system)
- Per-window byte counters on the hub in a full-load run (20 windows changing, the rest hidden): incoming traffic at most 1.1 x the screen's pixels per second (target 25).
- Time from "window becomes visible" to "fresh picture shown" at 4K, worst of 1,000 (target 9). Result that proves the design wrong: more than 33 ms for an open window.
- Node scaling cost: measure each node's load while it sends scaled copies at 60 Hz. Result that proves the design wrong: a node cannot keep 60 Hz while doing its own work.
- Fault injection (targets 3 to 5): kill and freeze hub programs, force GPU resets, switch the projector off and on.
- Time slots (3.4.1): in a 24-hour full-load run, log every key, click and pointer move at arrival, hand-off and screen flip. Result that proves the design wrong: any added delay above 1 ms (target 10) caused by another part using input's slot.
- Isolation by proof (3.4.1): attack the checker. Feed it deliberately wrong programs and wrong proofs. Result that proves the design wrong: the checker accepts one.
- Rules versus code (3.3): for each written rule, a test written by someone who has not seen the code, run against the built system. Result that proves the design wrong: the code matches its rule and the test still fails.
- Interrupted save (3.4.2, target 7): 200 forced resets at random moments during saves. Result that proves the design wrong: one lost or corrupted setting.
- SSD flush (3.4.2): cut the SSD's link right after a flush completes, 1,000 times. Result that proves the design wrong: any record that the store reported as saved is missing afterwards.
- Boot and trust (3.4.4): unsigned image refused, downgrade refused, 200 forced resets during boot and update always land in a bootable slot.
- Recovery details (3.4.6): the four tests listed there.
- Supervisor (3.4.3): stop the supervisor's check-ins and measure the time to the watchdog reset; kill each part 100 times and check the restart limit stops a part that fails 5 times in 60 s. Result that proves the design wrong: a reboot without a watchdog cause, or a part restarted beyond the limit.

## 7. Differences from the real build, in plain words
- The real build never pauses or scales windows (owner decision 2026-10-05, because the network is free). This design does both, because the limit that matters is the hub's memory, not the network. The scaled-copy request is new and is not in the real build's protocol.
- The real build patches a young compositor and plans restart-and-restore for its crashes (SOURCE: HUB-OS.md). This design assumes a rebuilt, defect-free compositor, and still keeps recovery for hardware events.
- The real build asks every window for full-size pictures all the time. This design asks only for what is shown.
- The real build keeps the upstream Linux kernel, built for the hub, and uses s6 as its init (SOURCE: HUB-OS.md "Core principles" and its change log entry of 2026-10-13, read 2026-10-14). This design writes the kernel, drivers, init and file system from scratch for the hub (owner decision, 2026-10-14). In sections 3.3 and 3.4: one language family with proofs instead of Rust, Ada or Zig and Linux's C; isolation by proof instead of by hardware walls; build-time time slots instead of a general scheduler; a typed object store instead of a file system; one proven supervisor instead of s6 with s6-rc and a watcher. Boot: a small signed loader on the owner's own Secure Boot keys with a soft downgrade counter and health-check rollback, instead of whatever the real build's boot path is (not read for this document). Drivers: not designed; four contracts instead. Every one of these is a design target, not a measurement.
