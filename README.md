# FPGA Racing Game 🏎️

A two-player VGA racing game implemented entirely in Verilog on the 
DE1-SoC FPGA. Features a full physics engine, FSM-based game controller, 
PS/2 keyboard input, lap timing, and audio — all running in hardware logic.

> [!WARNING]
> Source code is kept private per course policy (UofT ECE).
> This repository contains documentation and visuals only.

## Demo
https://youtu.be/bbQpBk9fjP8

## System Architecture

### Top-Level Block Diagram
<img width="681" height="453" alt="image" src="https://github.com/user-attachments/assets/83d29524-cafa-4d4b-9116-58096db01a2c" />


### Physics & Inputs
<img width="653" height="392" alt="image" src="https://github.com/user-attachments/assets/fc5ca596-5d36-482b-acbf-24c4dff2813b" />

### Graphics Rendering and Game Logic
<img width="637" height="372" alt="image" src="https://github.com/user-attachments/assets/70bdcf1e-c1b6-4854-a241-dae6b6a73e65" />

## How It Works

The game is built as a collection of hardware modules wired together 
through a top-level FSM game controller. There is no CPU, all logic 
runs in parallel hardware.

**Game State Controller (FSM)**
Manages four game states: start screen, gameplay, blue screen (player 2 
wins), and red screen (player 1 wins). Transitions on the gameover signal 
from the lap detection module.

**Player Controllers (×2)**
Each player has an independent controller that reads PS/2 keyboard input, 
applies physics (acceleration, friction, velocity clamping), detects wall 
and grass collisions, and updates car position every clock cycle.

**Physics Engine**
- Acceleration/deceleration via WASD keys
- Friction: velocity decays toward zero when no key is held
- Wall collision: velocity reverses on impact
- Grass detection: max velocity clamped when off-track

**VGA Controller**
A VGA timing generator counts through each pixel at 640×480. A draw 
pipeline layers the game background, sprite overlay 1 (player 1, red), 
and sprite overlay 2 (player 2, blue) to produce the final RGB output.

**Checkpoint & Lap Logic**
Detects when a car crosses the lap line by checking which side of the 
line the car was on in the previous cycle. Increments lap counter and 
triggers gameover after 9 laps.

**Lap Timer**
Seconds and deciseconds counter that resets each lap, displayed on 
HEX[5:4] and HEX[3:0].

**Audio Manager**
Plays sounds tied to game events via the DE1-SoC audio codec.

## I/O
| Input | Description |
|---|---|
| CLOCK_50 | 50 MHz system clock |
| KEY[3:0] | Reset |
| PS2_CLK / PS2_DAT | Keyboard input for both players |

| Output | Description |
|---|---|
| VGA_R/G/B + sync | 640×480 VGA display |
| HEX[5:0] | Lap count + timer |
| LEDR | Status indicators |
| Audio | Game sound effects |

## Tech Stack
- **Language:** Verilog (SystemVerilog)
- **Board:** DE1-SoC FPGA
- **Tools:** Quartus Prime, ModelSim
- **Display:** VGA 640×480
- **Input:** PS/2 keyboard decoder
