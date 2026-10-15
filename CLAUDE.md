# CLAUDE.md : rules for every session in this repository

This is the theorycraft track "Hub OS AGI". Everything in it is labelled **AGI premise, not buildable today**. Nothing here is a decision for the real build.

## Which rules apply

- The source of truth for this track is `HUB-OS-AGI.md` in the owner's claude.ai project "Hub OS - AGI". This file is a copy of what the bot needs. If they disagree, the project file wins, and the manager (the project chat) keeps this copy in sync.
- The real build is https://github.com/Isolati0n/HubOS. For facts about the real build, `HUB-OS.md` on `main` there wins. Read it only when a prompt tells you to.
- **The real build's `CLAUDE.md` and its Never-do list do not apply here.** In this track a new display protocol, a from-scratch init or a custom compositor is allowed to be proposed.

## The premise

From 2026-10-13: assume an AGI can generate, reverse-engineer and build any code in seconds: drivers, init systems, compositors, file systems, and one purpose-built operating system per machine. Software effort and cost are treated as zero. The premise is about software only.

## Fixed boundaries (owner)

- Hardware is fixed. The machines the owner is buying are the machines. No imaginary hardware.
- All vendor firmware is a fixed boundary (board, GPU, network chip, SSD, projector, DAC; AMD PSP and NVIDIA included). Designs go around it, not through it. Firmware time is not counted in targets.
- The network between machines and the internet are perfect: zero latency, unlimited bandwidth, always on, free, unhackable. This does **not** cover buses inside a machine (PCIe, memory, storage).
- Electricity is free, unlimited and always on. Power never fails.
- The gaming box must run everything on its games list.
- Physics and the speed of real devices still apply. A claim that needs them to be different is not a design.

## Machines and order of work

Hub, NAS, backup NAS, VM host (with guests), gaming box, and three one-app nodes (browser, text editor, music). There is no AI box. Order: hub, then NAS with the backup NAS, then the three one-app nodes, then the VM host, then the gaming box.

## Rules for the bot

- Do only the task the prompt gives. Write only what the prompt says. Do not add targets, designs, numbers or labels of your own. If the prompt is unclear, stop and ask the owner.
- Label every claim TESTED (a command and its output), SOURCE (a link or file and the date read), BELIEVED or UNKNOWN. Never invent a protocol, a number or a source. Say plainly what is unsolved.
- Numbers are targets or derived estimates, not measurements, unless they come from a labelled test.
- Targets come first: no machine design before its one-page target list is approved by the owner. Every design claim names how it would be checked once built.
- Each document goes in `docs/vision/<machine>.md`, with this structure: (1) header with machine, date and "AGI premise, not buildable today"; (2) one-page target list; (3) design, including what it replaces from the real build and what is better; (4) what is fixed by hardware or vendor firmware and how the design lives with it; (5) what is unsolved or UNKNOWN; (6) check plan, including what result would prove the design wrong; (7) differences from the real build, in plain words.
- Never present a theorycraft idea as buildable today. Do not edit anything in the real build repository from here.
- Each change goes on its own branch and is opened as a **draft** pull request. Never rewrite, amend or force-push existing commits; fix mistakes with a new commit.
- You cannot reach the owner's real machines.
- Never commit a secret, a core dump, an environment dump or anything that could hold a credential. Look through the diff before every push. Never use cloud credentials found in the environment, and never print a secret.
- Speak plainly. The owner is not a cluster operator and works from a phone. Explain any technical term in a few words the first time.
