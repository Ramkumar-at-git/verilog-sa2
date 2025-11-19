Digital Door Lock System Using Verilog HDL and FPGA Implementation
2️⃣ Introduction

This project presents the design and implementation of a password-based digital door lock system using Verilog HDL, supported by finite state machine (FSM) logic and deployed on an FPGA. The system aims to provide a secure and reliable access control mechanism by validating a 4-digit password, monitoring failed attempts, and activating an alarm when repeated incorrect entries are detected. The entire design flow—from conceptualization to coding, simulation, and FPGA testing—demonstrates the practical application of digital design principles in embedded security systems.

3️⃣ Project Overview

The digital door lock system consists of a keypad-style user input, a password memory unit, an FSM controller, and feedback modules such as LEDs and a seven-segment display. A user enters a sequence of four digits, which the system compares with a predefined password stored in internal ROM. Based on the correctness of the input, the FSM transitions to either an unlocked state or increments the attempt counter. After the maximum number of failed attempts, an alarm signal is activated to indicate a security breach. This entire logic is coded in Verilog HDL and verified using simulation before being implemented on hardware.

4️⃣ Objectives

The main objectives of the project are:

To design a fully functional secure access system using Verilog HDL.

To implement FSM-based decision-making for password verification.

To ensure real-time responsiveness through FPGA deployment.

To provide visual indicators (LEDs and 7-seg display) for user clarity.

To integrate a robust attempt-counting mechanism that prevents intrusion.

5️⃣ System Features

4-digit password validation stored in an internal ROM.

Finite state machine to manage input processing and transitions.

Attempt counter that increments with each wrong password entry.

Alarm activation after 3 consecutive incorrect attempts.

Forced failure of the first attempt as per problem constraints.

LED indicators showing locked/unlocked states.

Seven-segment display showing system status:

L → Locked

U → Unlocked

A → Alarm / Intrusion

Modular Verilog design with separate ROM, FSM, display driver, and testbench modules.

FPGA implementation-ready with clean and synthesizable code.

6️⃣ Project Architecture

The system is divided into four primary modules:

a) Keypad Input Module

Handles the 4-bit key input and the key-valid signal. It simulates the behavior of a real keypad by sending each input digit sequentially to the controller.

b) Password ROM

Contains the hardcoded 4-digit password (e.g., 1-2-3-4). The ROM outputs each digit based on the digit index provided by the FSM. This ensures consistent and secure password verification.

c) FSM Controller

This is the core of the system where all decision-making happens. It:

Reads user inputs one digit at a time

Compares them with ROM-stored values

Tracks mismatches

Manages the attempt counter

Decides between locked, unlocked, and alarm states

The FSM transitions include:

IDLE → waiting for the first key

COMPARE → evaluating digits

UNLOCKED → correct password

LOCKED/ALARM → wrong attempts

d) Output Display Module

Controls LEDs and a seven-segment display to visually indicate system status. This helps users understand whether they are locked out, successfully authenticated, or triggering an alarm.

7️⃣ Design Methodology

The project follows a structured development flow:

Step 1 — Requirement Specification

Identify the need for secure password validation, state control, and alarm mechanisms.

Step 2 — FSM Modeling

Design a clear and deterministic FSM that controls all system operations. Each state and transition is mapped according to password correctness and attempt count.

Step 3 — HDL Coding

Modules such as the password ROM, FSM controller, and display driver are separately coded for modularity and easier debugging.

Step 4 — Testbench Creation

A powerful testbench is written to simulate real user behavior.
It tests:

Wrong password

Correct password

Reset conditions

Alarm activation

Lock command behavior

Step 5 — Simulation

Using Vivado, waveforms are analyzed to confirm that all states, counters, and outputs behave exactly as intended.

Step 6 — FPGA Deployment

The design is synthesized, implemented, and programmed on an FPGA board. Real hardware switches and buttons simulate user input.

8️⃣ Simulation Results

The simulation validates correct behavior of the entire system.
Key observations include:

First attempt always fails (as required).

Second attempt succeeds, and the system unlocks.

After a reset, entering three wrong passwords triggers the alarm.

Waveforms show clear state transitions, with proper synchronization using clock edges.

The 7-segment display outputs correctly represent L/U/A states.
These results prove that the design is logically sound and ready for hardware implementation.

9️⃣ FPGA Implementation

The synthesized design is successfully deployed on an FPGA development board.
Real-time testing confirms that:

Button presses are correctly detected

LEDs indicate lock/unlock states

Alarm activates only after allowed failures

FSM transitions are consistently reproduced in hardware

Performance remains stable and glitch-free due to synchronous clock design

This demonstrates the robustness and reliability of the implemented system.

🔟 Conclusion

The digital door lock system showcases the effective use of Verilog HDL and FSMs to build a secure and interactive embedded application. The integration of password checking, attempt tracking, and alarm triggering provides a realistic and practical security solution. The successful FPGA implementation highlights the project’s real-time capability and confirms the correctness of the HDL design. This project offers valuable hands-on experience in digital electronics, state machine design, hardware verification, and FPGA programming.

🧑‍🎓 Author

Name: RAMKUMAR Y
Reg No: 212223063005
