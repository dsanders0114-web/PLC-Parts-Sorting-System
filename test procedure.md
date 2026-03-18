# Test Procedure — PLC Parts Sorting System

## 1. Objective

The purpose of this test procedure is to validate the functionality of the PLC-based parts sorting system. This includes verifying correct part detection, classification, shift register tracking, reject logic, and counter accuracy.

---

## 2. System Overview

The system simulates a conveyor-based sorting process where:

* A photoeye detects incoming parts
* A vision input classifies parts as good or bad
* Two shift registers track part position and classification
* A reject gate activates when a bad part reaches the reject position
* Counters track system performance

---

## 3. Test Environment

* Platform: Studio 5000 Logix Designer
* Mode: Emulator or Offline Simulation
* All inputs are manually toggled via controller tags

---

## 4. Tags to Monitor (Watch Window)

### Core Signals

* System_Run
* Conveyor_Run
* PE_Entry
* Vision_Bad
* Entry_Event
* Shift_Pulse

### Load Bits

* Load_PresentBit
* Load_BadBit

### Shift Registers

* PartPresentTrack[0]
* BadPartTrack[0]
* PartPresentTrack[0].9
* BadPartTrack[0].9

### Reject Logic

* Part_At_Reject
* Bad_At_Reject
* Reject_Request
* Reject_Gate

### Counters

* C_TotalParts.ACC
* C_GoodParts.ACC
* C_BadParts.ACC
* C_RejectedParts.ACC

---

## 5. Test Procedures

---

### Test 1 — System Start/Stop

**Steps:**

1. Set PB_Start = 1, then return to 0
2. Observe System_Run and Conveyor_Run
3. Set PB_Stop = 1

**Expected Results:**

* System_Run = 1 after start
* Conveyor_Run = 1 while running
* System_Run = 0 after stop
* Conveyor_Run = 0 after stop

---

### Test 2 — Conveyor Pulse Generation

**Steps:**

1. Start the system
2. Observe T_Pulse.ACC, T_Pulse.DN, and Shift_Pulse

**Expected Results:**

* T_Pulse counts up to preset value
* T_Pulse.DN briefly turns true
* Shift_Pulse activates for one scan only
* Timer resets and repeats continuously

---

### Test 3 — Entry Detection (One-Shot)

**Steps:**

1. Set PE_Entry = 1 and hold
2. Observe Entry_Event

**Expected Results:**

* Entry_Event pulses once only
* Entry_Event does not remain true while PE_Entry is held

---

### Test 4 — Good Part Processing

**Steps:**

1. Set Vision_Bad = 0
2. Pulse PE_Entry
3. Allow several shift pulses

**Expected Results:**

* C_TotalParts increments by 1
* C_GoodParts increments by 1
* C_BadParts does not change
* Load_PresentBit = 1 during entry
* Load_BadBit = 0
* Part shifts through register
* No reject occurs at position 9

---

### Test 5 — Bad Part Processing

**Steps:**

1. Set Vision_Bad = 1
2. Pulse PE_Entry
3. Allow shift pulses until part reaches position 9

**Expected Results:**

* C_TotalParts increments
* C_BadParts increments
* Load_PresentBit = 1
* Load_BadBit = 1
* At position 9:

  * Part_At_Reject = 1
  * Bad_At_Reject = 1
  * Reject_Request = 1
  * Reject_Gate activates for ~300 ms
  * C_RejectedParts increments by 1

---

### Test 6 — Shift Register Tracking

**Steps:**

1. Enter multiple parts (mix of good and bad)
2. Observe shift registers during movement

**Expected Results:**

* PartPresentTrack shifts a "1" for each part
* BadPartTrack shifts a "1" only for bad parts
* Bit patterns move exactly one position per shift pulse
* Classification stays aligned with the correct part

---

### Test 7 — Mixed Part Sequence

**Steps:**

1. Enter sequence:

   * Good → Bad → Good → Bad
2. Allow full shift to reject position

**Expected Results:**

* Only bad parts trigger reject gate
* Good parts pass without triggering reject
* Counters reflect correct totals

---

### Test 8 — Empty Conveyor

**Steps:**

1. Run system without triggering PE_Entry

**Expected Results:**

* No bits appear in shift registers
* No reject events occur
* Counters remain unchanged

---

### Test 9 — Reset Function

**Steps:**

1. Accumulate parts and counts
2. Set PB_Reset = 1

**Expected Results:**

* All counters reset to 0
* Shift registers clear to 0
* Timers reset
* System_Run resets to 0
* Reject_Gate turns off

---

## 6. Pass Criteria

The system is considered fully functional if:

* Each part is detected once
* Shift registers move exactly one position per pulse
* Good and bad parts are tracked correctly
* Reject gate activates only for bad parts at the correct position
* Counters accurately reflect system activity
* Reset restores the system to a clean state

---

## 7. Notes

* Use binary view for shift registers for easier visualization
* Slowing the pulse timer improves debugging clarity
* Manual step mode can be used for precise verification of bit movement

---

## 8. Conclusion

This test procedure verifies that the PLC logic correctly implements conveyor tracking, classification, and reject control using shift registers. Successful completion confirms accurate synchronization between data tracking and simulated motion.
