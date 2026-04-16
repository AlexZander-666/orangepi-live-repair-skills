# Orange Pi Live Repair Skills

Reusable Codex skills for recovering and validating CareSense Orange Pi live runtime transport.

## Included skills

- `skills/orangepi-live-direct-lan-repair`
  - Recover a `COM10`-attached Orange Pi live runtime with strict TDD, forced `Xiaomi 14` retargeting, classified transport failures, and dual real-validation runs.
- `skills/orangepi-live-evidence-reporting`
  - Assemble real run artifacts into an operator-grade final report with exact commands, run directories, extracted report fields, and a final success or classified-failure conclusion.

## Install

Copy or symlink the desired skill folder into your local Codex skills directory, or clone this repository and reference the skill from there.

## Current focus

This repository currently focuses on:

1. serial-shell recovery before transport work
2. Xiaomi 14 direct-LAN admission as the known-good baseline
3. provider startup truth and classified failures
4. mandatory real validation with preserved artifacts
5. evidence extraction and final operator reporting
