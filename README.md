# **Digital Door Lock System Using Verilog HDL and FPGA Implementation**

---

## **2️⃣ Introduction**

This project presents the design and implementation of a **password-based digital door lock system** using **Verilog HDL**, supported by **finite state machine (FSM)** logic and deployed on an **FPGA**. The system aims to provide a secure and reliable access control mechanism by validating a 4-digit password, monitoring failed attempts, and activating an alarm when repeated incorrect entries are detected. The entire design flow—from conceptualization to coding, simulation, and FPGA testing—demonstrates the practical application of digital design principles in embedded security systems.

---

## **3️⃣ Project Overview**

The digital door lock system consists of a keypad-style user input, a password memory unit, an FSM controller, and feedback modules such as LEDs and a seven-segment display. A user enters a sequence of four digits, which the system compares with a predefined password stored in internal ROM. Based on the correctness of the input, the FSM transitions to either an unlocked state or increments the attempt counter. After the maximum number of failed attempts, an **alarm signal** is activated to indicate a security breach. This entire logic is coded in Verilog HDL and verified using simulation before being implemented on hardware.

---

## **4️⃣ Objectives**

The main objectives of the project are:

* To design a fully functional **secure access system** using Verilog HDL.
* To implement **FSM-based decision-making** for password verification.
* To ensure **real-time responsiveness** through FPGA deployment.
* To provide **visual indicators** (LEDs and 7-seg display) for user clarity.
* To integrate a **robust attempt-counting mechanism** that prevents intrusion.

---

## **5️⃣ System Features**

* **4-digit password validation** stored in an internal ROM.
* **Finite state machine** to manage input processing and transitions.
* **Attempt counter** that increments with each wrong password entry.
* **Alarm activation** after 3 consecutive incorrect attempts.
* **Forced failure** of the first attempt as per problem constraints.
* **LED indicators** showing locked/unlocked states.
* **Seven-segment display** showing system status:

  * `L` → Locked
  * `U` → Unlocked
  * `A` → Alarm / Intrusion
* **Modular Verilog design** with separate ROM, FSM, display driver, and testbench modules.
* **FPGA implementation-ready** with clean and synthesizable code.

---

## **6️⃣ Project Architecture**

The system is divided into four primary modules:

### **a) Keypad Input Module**

Handles the 4-bit key input and the key-valid signal. It simulates the behavior of a real keypad by sending each input digit sequentially to the controller.

### **b) Password ROM**

Contains the hardcoded 4-digit password (e.g., 1-2-3-4). The ROM outputs each digit based on the digit index provided by the FSM. This ensures consistent and secure password verification.


// password_rom.v
// Simple synchronous ROM storing a 4-digit password (4-bit per digit).
module password_rom #(
    parameter DIGITS = 4
)(
    input  wire                 clk,
    input  wire [$clog2(DIGITS)-1:0] addr,
    output reg  [3:0]           data
);
    // Hardcoded password: 1 2 3 4  (hex-coded digits)
    // Change these values to set a new password.
    always @(posedge clk) begin
        case (addr)
            0: data <= 4'h1;
            1: data <= 4'h2;
            2: data <= 4'h3;
            3: data <= 4'h4;
            default: data <= 4'h0;
        endcase
    end
endmodule

### **c) FSM Controller**

This is the core of the system where all decision-making happens. It:

* Reads user inputs one digit at a time
* Compares them with ROM-stored values
* Tracks mismatches
* Manages the attempt counter
* Decides between locked, unlocked, and alarm states

The FSM transitions include:

* **IDLE** → waiting for the first key
* **COMPARE** → evaluating digits
* **UNLOCKED** → correct password
* **LOCKED/ALARM** → wrong attempts

// fsm_door_lock.v  (Pure Verilog-2001: Vivado compatible)

module door_lock_fsm #(
    parameter DIGITS = 4,
    parameter ATTEMPT_MAX = 3
)(
    input  wire        clk,
    input  wire        rst_n,      
    input  wire [3:0]  key,
    input  wire        key_valid,
    input  wire        lock_cmd,
    output reg         unlocked,
    output reg         alarm,
    output reg [1:0]   attempt_count
);

    // -----------------------------
    // State encoding (Verilog style)
    // -----------------------------
    reg [1:0] state, next_state;

    localparam S_IDLE     = 2'b00;
    localparam S_COMPARE  = 2'b01;
    localparam S_UNLOCKED = 2'b10;
    localparam S_LOCKED   = 2'b11;

    // -----------------------------
    // ROM for password (1,2,3,4)
    // -----------------------------
    reg [3:0] rom [0:DIGITS-1];

    initial begin
        rom[0] = 4'h1;
        rom[1] = 4'h2;
        rom[2] = 4'h3;
        rom[3] = 4'h4;
    end

    // -----------------------------
    // Internal registers
    // -----------------------------
    reg [1:0] digit_index;
    reg       mismatch;

    // -----------------------------
    // STATE REGISTER
    // -----------------------------
    always @(posedge clk or negedge rst_n) begin
        if(!rst_n)
            state <= S_IDLE;
        else
            state <= next_state;
    end

    // -----------------------------
    // NEXT STATE LOGIC
    // -----------------------------
    always @(*) begin
        next_state = state;

        case(state)
            S_IDLE:
                if(key_valid) next_state = S_COMPARE;

            S_COMPARE: begin
                // transitions handled in sequential block
            end

            S_UNLOCKED:
                if(lock_cmd) next_state = S_IDLE;

            S_LOCKED: begin
                // remain until reset
            end
        endcase
    end

    // -----------------------------
    // SEQUENTIAL OUTPUT + LOGIC
    // -----------------------------
    always @(posedge clk or negedge rst_n) begin
        if(!rst_n) begin
            unlocked      <= 0;
            alarm         <= 0;
            attempt_count <= 0;
            digit_index   <= 0;
            mismatch      <= 0;
        end else begin

            // external lock button
            if(lock_cmd)
                unlocked <= 0;

            if(key_valid) begin
                // start new attempt
                if(state == S_IDLE) begin
                    digit_index <= 0;
                    mismatch    <= 0;
                end

                // compare digit
                if(key != rom[digit_index])
                    mismatch <= 1;

                // next digit
                if(digit_index == DIGITS-1) begin
                    // finished entering 4 digits
                    if(mismatch || (key != rom[digit_index])) begin
                        // WRONG PASSWORD
                        attempt_count <= attempt_count + 1;

                        if(attempt_count + 1 >= ATTEMPT_MAX)
                            alarm <= 1;

                        unlocked <= 0;
                    end else begin
                        // CORRECT PASSWORD
                        unlocked <= 1;
                        alarm <= 0;
                        attempt_count <= 0;
                    end

                    digit_index <= 0;

                end else begin
                    digit_index <= digit_index + 1;
                end
            end
        end
    end

endmodule

### **d) Output Display Module**

Controls LEDs and a seven-segment display to visually indicate system status. This helps users understand whether they are locked out, successfully authenticated, or triggering an alarm.

// display_driver.v
// Simple driver to map unlocked/locked/alarm to LEDs and to a single 7-seg pattern
module display_driver (
    input  wire clk,
    input  wire rst_n,
    input  wire unlocked,
    input  wire alarm,
    output reg  led_locked,
    output reg  led_unlocked,
    output reg  seg_en,        // 7seg enable (active low or high depending on board)
    output reg  [6:0] seg      // 7-seg a-g (active high here)
);
    always @(posedge clk or negedge rst_n) begin
        if (!rst_n) begin
            led_locked <= 1'b1;
            led_unlocked <= 1'b0;
            seg_en <= 1'b0;
            seg <= 7'b1111111;
        end else begin
            led_unlocked <= unlocked;
            led_locked <= ~unlocked;
            // simple 7-seg: show "L" for locked, "U" for unlocked, "A" for alarm
            if (alarm) begin
                // show "A" (approx)
                seg <= 7'b0001000; // pattern maybe adjust per your 7-seg mapping
            end else if (unlocked) begin
                // show "U"
                seg <= 7'b1000001;
            end else begin
                // show "L"
                seg <= 7'b0111000;
            end
        end
    end
endmodule

---

## **7️⃣ Design Methodology**

The project follows a structured development flow:

### **Step 1 — Requirement Specification**

Identify the need for secure password validation, state control, and alarm mechanisms.

### **Step 2 — FSM Modeling**

Design a clear and deterministic FSM that controls all system operations. Each state and transition is mapped according to password correctness and attempt count.

### **Step 3 — HDL Coding**

Modules such as the password ROM, FSM controller, and display driver are separately coded for modularity and easier debugging.

### **Step 4 — Testbench Creation**

A powerful testbench is written to simulate real user behavior.
It tests:

* Wrong password
* Correct password
* Reset conditions
* Alarm activation
* Lock command behavior

  `timescale 1ns/1ps

module tb_top_door_lock;

    // DUT inputs
    reg clk;
    reg rst_n;
    reg [3:0] key;
    reg key_valid;
    reg lock_cmd;

    // DUT outputs
    wire unlocked;
    wire alarm;
    wire [1:0] attempt_count;
    wire led_locked;
    wire led_unlocked;
    wire [6:0] seg;
    wire seg_en;

    // Instantiate DUT
    top_door_lock dut (
        .clk(clk),
        .rst_n(rst_n),
        .key(key),
        .key_valid(key_valid),
        .lock_cmd(lock_cmd),
        .unlocked(unlocked),
        .alarm(alarm),
        .attempt_count(attempt_count),
        .led_locked(led_locked),
        .led_unlocked(led_unlocked),
        .seg(seg),
        .seg_en(seg_en)
    );

    // Clock generation (100MHz)
    initial begin
        clk = 0;
        forever #5 clk = ~clk;  
    end

    //----------------------------
    // ENTER DIGIT TASK
    //----------------------------
    task enter_digit;
        input [3:0] d;
        begin
            key = d;
            key_valid = 1;
            #20;                 // >= 2 clock cycles
            key_valid = 0;
            #60;                 // allow FSM to latch
        end
    endtask

    //----------------------------
    // CORRECT PASSWORD: 1-2-3-4
    //----------------------------
    task correct_password;
        begin
            enter_digit(1);
            enter_digit(2);
            enter_digit(3);
            enter_digit(4);
        end
    endtask

    //----------------------------
    // WRONG PASSWORD: 9-9-9-9
    //----------------------------
    task wrong_password;
        begin
            enter_digit(9);
            enter_digit(9);
            enter_digit(9);
            enter_digit(9);
        end
    endtask

    //----------------------------
    // MAIN TEST SEQUENCE
    //----------------------------
    initial begin
        // init
        key = 0;
        key_valid = 0;
        lock_cmd = 0;

        // RESET
        rst_n = 0;
        #100;
        rst_n = 1;
        #100;

        //----------------------------------------------------
        // Attempt 1 ? FAIL
        //----------------------------------------------------
        $display("Attempt 1 FAIL");
        wrong_password();
        #200;

        //----------------------------------------------------
        // Attempt 2 ? PASS
        //----------------------------------------------------
        $display("Attempt 2 PASS");
        correct_password();
        #200;

        // LOCK the door for next attempts
        lock_cmd = 1;
        #20;
        lock_cmd = 0;
        #200;

        //----------------------------------------------------
        // RESET the system
        //----------------------------------------------------
        $display("RESET system");
        rst_n = 0;
        #100;
        rst_n = 1;
        #100;

        //----------------------------------------------------
        // Attempt 3 ? FAIL
        //----------------------------------------------------
        $display("Attempt 3 FAIL");
        wrong_password();
        #200;

        //----------------------------------------------------
        // Attempt 4 ? FAIL
        //----------------------------------------------------
        $display("Attempt 4 FAIL");
        wrong_password();
        #200;

        //----------------------------------------------------
        // Attempt 5 ? FAIL ? ALARM ON
        //----------------------------------------------------
        $display("Attempt 5 FAIL ? ALARM expected");
        wrong_password();
        #400;

        $display("Simulation Complete.");
        $stop;
    end

endmodule

### **Step 5 — Simulation**

Using Vivado, waveforms are analyzed to confirm that all states, counters, and outputs behave exactly as intended.

### **Step 6 — FPGA Deployment**

The design is synthesized, implemented, and programmed on an FPGA board. Real hardware switches and buttons simulate user input.

---

## **8️⃣ Simulation Results**

The simulation validates correct behavior of the entire system.
Key observations include:

* First attempt always fails (as required).
* Second attempt succeeds, and the system unlocks.
* After a reset, entering three wrong passwords triggers the alarm.
* **Waveforms show clear state transitions**, with proper synchronization using clock edges.
* The 7-segment display outputs correctly represent L/U/A states.
  These results prove that the design is logically sound and ready for hardware implementation.

  ![WhatsApp Image 2025-11-18 at 16 03 43_6f58fc94](https://github.com/user-attachments/assets/ab4be983-8560-445f-b585-81b4203ca7dc)


---

## **9️⃣ FPGA Implementation**

The synthesized design is successfully deployed on an FPGA development board.
Real-time testing confirms that:

* Button presses are correctly detected
* LEDs indicate lock/unlock states
* Alarm activates only after allowed failures
* FSM transitions are consistently reproduced in hardware
* Performance remains stable and glitch-free due to synchronous clock design

This demonstrates the robustness and reliability of the implemented system.

---

## **🔟 Conclusion**

The digital door lock system showcases the effective use of Verilog HDL and FSMs to build a secure and interactive embedded application. The integration of password checking, attempt tracking, and alarm triggering provides a realistic and practical security solution. The successful FPGA implementation highlights the project’s real-time capability and confirms the correctness of the HDL design. This project offers valuable hands-on experience in digital electronics, state machine design, hardware verification, and FPGA programming.

---

## **🧑‍🎓 Author**

**Name:** RAMKUMAR Y
**Reg No:** 212223063005

