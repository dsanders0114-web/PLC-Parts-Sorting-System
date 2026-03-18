# PLC Parts Sorting System (Studio 5000)

## Overview
This project simulates an industrial parts sorting system using Studio 5000 ladder logic.

A photoeye detects incoming parts, and a simulated vision sensor classifies each part as good or bad. The PLC uses bit shift registers (BSL) to track each part as it moves down a conveyor and triggers a reject gate at the correct position.

## Key Features
- Entry photoeye with one-shot detection
- Vision-based classification (good/bad)
- Dual shift registers:
  - Part presence tracking
  - Bad part tracking
- Conveyor movement simulation using timer pulses
- Reject gate control with timed output
- Production counters:
  - Total parts
  - Good parts
  - Bad parts
  - Rejected parts

## How It Works
1. A part is detected at the entry photoeye
2. The vision input determines if the part is bad
3. The PLC loads:
   - 1 into presence register
   - 1 or 0 into bad register
4. Each conveyor pulse shifts the registers forward
5. When the tracked bit reaches the reject position:
   - If bad → reject gate fires
   - If good → part passes

## Technologies Used
- Studio 5000 Logix Designer
- Ladder Logic (RSLogix)
- BSL (Bit Shift Left) instruction
- Timers and Counters

## Testing
The system was validated using a watch window to confirm:
- Correct bit shifting behavior
- Accurate part tracking
- Proper reject timing
- Correct counter increments

## Key Learning Outcomes
- Conveyor tracking using shift registers
- One-shot logic for event detection
- Synchronizing data with motion
- Industrial reject system logic design

                PARTS SORTING SYSTEM (PLC SIMULATION)

   ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
   │  Photoeye    │     │  Vision      │     │ Shift Logic  │
   │ (PE_Entry)   │────▶│ (Good/Bad)   │────▶│  (BSL)       │
   └──────────────┘     └──────────────┘     └──────────────┘
                                                       │
                                                       ▼
                                            ┌──────────────────┐
                                            │ Tracking Registers│
                                            │                  │
                                            │ PartPresentTrack │
                                            │ BadPartTrack     │
                                            └──────────────────┘
                                                       │
                                                       ▼
                                         ┌────────────────────────┐
                                         │ Reject Decision Logic  │
                                         │                        │
                                         │ If Present AND Bad     │
                                         │ → Trigger Reject Gate  │
                                         └────────────────────────┘
                                                       │
                                                       ▼
                                             ┌──────────────┐
                                             │ Reject Gate  │
                                             │ (Diverter)   │
                                             └──────────────┘


CONVEYOR TRACKING (BIT SHIFT REPRESENTATION)

Position:   0   1   2   3   4   5   6   7   8   9
Location:  Entry --------------------------- Reject

Example: Bad Part Moving

Step 0:
Present:   1 0 0 0 0 0 0 0 0 0
Bad:       1 0 0 0 0 0 0 0 0 0

Step 1:
Present:   0 1 0 0 0 0 0 0 0 0
Bad:       0 1 0 0 0 0 0 0 0 0

Step 2:
Present:   0 0 1 0 0 0 0 0 0 0
Bad:       0 0 1 0 0 0 0 0 0 0

...

Step 9 (Reject Position):
Present:   0 0 0 0 0 0 0 0 0 1
Bad:       0 0 0 0 0 0 0 0 0 1

→ Reject Gate Fires
## Files Included
- `.ACD` file (full project)
- `.L5X` export (readable logic)
  


LOGIC FLOW

[Part Detected]
        │
        ▼
[Classify Part]
 (Good or Bad)
        │
        ▼
[Load Bits into Registers]
        │
        ▼
[Shift on Conveyor Pulse]
        │
        ▼
[Reached Reject Position?]
        │
   ┌────┴────┐
   ▼         ▼
[Yes]       [No]
   │         │
   ▼         │
[Bad Part?]  │
   │         │
 ┌─┴─┐       │
 ▼   ▼       │
Yes  No      │
 │    │      │
 ▼    ▼      │
Reject Pass  │
 │           │
 ▼           ▼
[Update Counters]
## Author
Daryl Sanders
