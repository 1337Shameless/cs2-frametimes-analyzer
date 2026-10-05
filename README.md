# CS2 Frametimes & Tick-Pacing Analyzer

A lightweight, zero-dependency web analyzer designed to diagnose frame pacing inconsistencies, sub-tick synchronization stalls, and GPU starvation in **Counter-Strike 2** using captures from **Intel PresentMon**.

![Platform: GitHub Pages](https://img.shields.io/badge/Hosted%20on-GitHub%20Pages-blue)
![Stack: HTML5 / JS](https://img.shields.io/badge/Stack-Vanilla%20HTML%20%2F%20JS-orange)
![Privacy: 100% Local](https://img.shields.io/badge/Data%20Privacy-100%25%20Client--Side-green)
![License: MIT](https://img.shields.io/badge/License-MIT-brightgreen.svg)

---

## Live Tool

You can use the analyzer directly in your browser without installing anything:  
**[https:/1337Shameless.github.io/cs2-frametimes-analyzer/](https://1337Shameless.github.io/cs2-frametimes-analyzer/)**

---

## Overview

Despite running uncapped on high-end hardware, Counter-Strike 2 frequently suffers from erratic frame pacing, heavy mouse feel, and micro-stutters during engagements. This tool provides reproducible, empirical proof of engine-level frame delivery bottlenecks by parsing low-level telemetry from Intel PresentMon captures.

### The Core Issue: GPU Starvation
While rendering a frame may take a fraction of a millisecond on modern GPUs (`MsGPUBusy` ~1.0–1.5 ms), the graphics pipeline is frequently halted at the **15.625 ms (64 Hz) server tick boundaries**. The engine's synchronous Main Thread stalls execution to reconcile network packets and sub-tick timestamps, starving the GPU of render commands (`MsGPUWait` spikes).

---

## Features

- **100% Client-Side Privacy**: Your CSV benchmarks never leave your machine. All calculations, rendering, and parsing happen locally in browser memory.
- **Tick-Alignment Grid**: Automatically projects a 64 Hz (15.625 ms) periodic metronome across your timeline to expose tick-synchronized stalls.
- **Statistical Correlation Engine**: Computes the Pearson correlation coefficient ($r$) between `Frametime` and `MsGPUWait` to mathematically prove whether a stutter is GPU-bound or CPU tick-starved.
- **Key Metrics Tracked**:
  - `MsBetweenPresents` (Frametime delta)
  - `MsGPUBusy` (Pure GPU raster/compute execution time)
  - `MsGPUWait` (GPU idle time waiting for CPU dispatch)
- **1080p Share Card Export**: Generates a high-resolution report card ready to share on Reddit, X, Discord, or bug reports to Valve.

---

## How to Capture and Test

1. Download and install [Intel PresentMon](https://game.intel.com/us/intel-presentmon/) (Free & Open Source).
2. Set the target application to `cs2.exe` and configure a capture hotkey.
3. Jump into a Counter-Strike 2 match (Online or Local with bots) and record a **1 to 10-second capture** during active movement.
4. Locate the generated `.csv` in your PresentMon folder (`Documents\PresentMon\Captures`).
5. Open the [Live Analyzer](https://1337shameless.github.io/cs2-frametimes-analyzer/) and drop your CSV into the page.

---

## Telemetry Metrics Breakdown

| Metric | Description | Expected Normal Behavior | CS2 Observed Behavior |
| :--- | :--- | :--- | :--- |
| **`MsGPUBusy`** | Time the GPU actively spends computing the frame. | Smooth, scaling only with graphical load (smoke, effects). | Consistently flat (~1.0–2.0 ms on modern GPUs). |
| **`MsGPUWait`** | Time the GPU sits idle waiting for draw calls from the CPU. | Minimal and flat when running uncapped. | Regular periodic spikes locking to 15.625 ms intervals. |
| **Pearson Correlation ($r$)** | Correlation between Frametime and GPU Wait. | Close to 0 (performance drops are GPU-bound). | High correlation ($r > 0.80$), confirming GPU Starvation. |
