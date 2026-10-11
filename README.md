# NGDC Trials project

> A third-person action prototype built in **Unreal Engine** for the **NUST Game Dev Community trials**, featuring Motion Matching locomotion (GASP-based), hit stuns, camera shake, sound triggers, a live HUD and interactable objects.

<img width="1277" height="800" alt="image" src="https://github.com/user-attachments/assets/4e0f5c30-74c5-4e76-b411-acf92adde194" />



---

## Table of Contents

1. [Overview](#overview)
2. [Features](#features)
3. [Getting Started](#getting-started)
4. [Controls](#controls)
5. [Feature Breakdown](#feature-breakdown)
   - [Movable Characters](#1-movable-characters)
   - [Motion Matching Animation System](#2-motion-matching-animation-system)
   - [Hit Stun](#3-hit-stun)
   - [Camera Shake](#4-camera-shake)
   - [Sound Triggers](#5-sound-triggers)
   - [UI HUD](#6-ui-hud)
   - [Interactable Objects](#7-interactable-objects)
6. [Project Structure](#project-structure)
7. [Known Issues](#known-issues)
8. [Credits](#credits)
9. [Author](#author)

---

## Overview

This project is my submission for the NUST Game Dev Community trials. The goal was to build a responsive, polished character prototype where movement, animation, combat feedback and UI all work together.

The locomotion runs on **Motion Matching**, following the algorithms and structure of Epic's **Game Animation Sample Project (GASP)**, adapted onto my own character. Combat feedback is layered with hit stuns, camera shake and sound triggers, and a HUD reports live game information such as character speed.

- **Engine:** Unreal Engine [version]
- **Language / Scripting:** Blueprint [and C++ if used]
- **Platform:** Windows
- **Development time:** [e.g. 48 hours / 1 week]

---

## Features

- Playable, movable characters with smooth locomotion
- Motion Matching animation using GASP's Pose Search pipeline
- Hit stun system with reactions and temporary control lockout
- Camera shake on impacts and key actions
- Sound triggers tied to animation and gameplay events
- UI HUD with a live speed readout
- Interactable objects in the world

---

## Getting Started

### Requirements

- Unreal Engine [version]
- Windows 10/11
- [Visual Studio 2022, only if the project contains C++]
- Plugins enabled: **Pose Search**, **Animation Warping**, **Chooser** [edit to match your project]

### Installation

1. Clone or download this repository:
   ```
   git clone [repo-url]
   ```
2. Open `[ProjectName].uproject`.
3. If prompted, let Unreal rebuild the modules/shaders (first launch can take a few minutes).
4. Open the map `[MapName]` and press **Play**.

> **Note:** The Pose Search Database needs to be indexed on first open. If the character looks stiff or stuck in a T-pose, open the database asset and let it finish building.

---

## Controls

| Action | Keyboard / Mouse | Gamepad |
|---|---|---|
| Move | W A S D | Left Stick |
| Look | Mouse | Right Stick |
| Walk / Run | [key] | [button] |
| Jump | Space | [button] |
| Attack | [key] | [button] |
| Interact | [key] | [button] |

*(Edit this table to match your Input Mapping Context.)*

---

## Feature Breakdown

### 1. Movable Characters

- Controlled via Enhanced Input with 8-direction movement and analog support.
- Character movement speeds are tuned per gait (idle, walk, run), so each state produces a distinct velocity.
- The animation rig was **retargeted** from the default mannequin onto my own character, so the same GASP animation set drives a custom skeleton.

### 2. Motion Matching Animation System

Locomotion is driven by **Motion Matching** instead of hand-authored state machines and blend spaces. Each frame, the system searches an animation database for the pose that best matches the character's current pose and predicted future motion.

**Pipeline, following GASP:**

- **Pose Search Schema:** defines what is compared (trajectory samples for past/present/future position and velocity, plus pose channels such as bone positions and velocities).
- **Pose Search Database:** stores the animation clips (idle, start, cycle, stop, pivot, jump and others) that the search can pick from, split by locomotion state.
- **Trajectory Prediction:** the character's input and movement component produce a predicted path that is fed into the query.
- **Cost Function and Search:** candidate poses are scored against the query and the lowest-cost frame wins. Continuing the current clip is weighted so the animation doesn't jitter between near-identical frames.
- **Chooser Tables:** pick which database to search depending on gameplay state (for example, grounded versus in the air).
- **Blend Stack:** smoothly transitions between the selected animation and the previous one.
- **Warping and IK:** Orientation Warping and Stride Warping keep the feet aligned with the movement direction and speed, with foot IK to plant the feet on the ground.

**Locomotion states covered:** Idle, Start, Cycle (walk/run), Stop, Pivot, Jump / Fall / Land. *(Edit to match what you implemented.)*

### 3. Hit Stun

- When a character is hit, it enters a **stunned state** for a short, configurable duration.
- During the stun, input is locked and a hit-reaction animation plays.
- Recovery returns control to the player automatically and restores locomotion.
- Hit events also trigger camera shake and sound (see below).

### 4. Camera Shake

- Implemented using Unreal's camera shake system with [Legacy / Perlin / Wave Oscillator] shakes.
- Triggered on hits, heavy attacks and other impact events.
- Intensity and duration are adjustable per event so light hits feel different from heavy ones.

### 5. Sound Triggers

Sound is event-driven rather than hard-coded:

- **Animation notifies** fire footstep sounds on foot contact, so footsteps match the animation exactly.
- **Gameplay triggers** play sounds for attacks, hits, interactions and UI feedback.
- Sounds are attenuated in 3D space where appropriate.

### 6. UI HUD

- Built with a Widget Blueprint (`WBP_HUD`) and added to the viewport on BeginPlay through a custom HUD class.
- **Speed readout:** displays `Speed: ---` and updates every frame using the player pawn's velocity (horizontal speed only, truncated to a whole number). It reads 0 while standing and increases through walk and run.
- [Add any other HUD elements: score, health, interaction prompts, etc.]

### 7. Interactable Objects

- Objects use a shared **interaction interface**, so any actor can respond to the player's Interact input.
- Interaction can trigger [door opening / pickups / switches / lights, edit as needed].
- [Optional: on-screen prompt when the player is in range.]

---

## Known Issues

- [e.g. Cape material shows minor artifacts on certain animations]
- [e.g. Pivot transitions can look abrupt at very high turn rates]
- [Add anything else you know about; honesty here looks good.]

---

## Credits

- **Game Animation Sample Project (GASP):** Epic Games, for the Motion Matching setup and animation set
- **Character & animations:** [ScetchFab / Mixamo, as applicable]
- **Sound effects:** 
- **Unreal Engine:** Epic Games

---

## Author

**Abdul Rehman Riaz 542715**
Computer Science, SEECS, NUST

*Built for the NUST Game Dev Community trials.*
