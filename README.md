
---

### **1️⃣ Title & Short Description**

**Digital Door Lock System Using Verilog HDL**
This project implements a secure digital door lock using Verilog HDL, featuring password verification, attempt tracking, and alarm activation. The system is verified through simulation and implemented on an FPGA.

---

### **2️⃣ Project Overview**

The digital door lock system uses a 4-digit password and a finite state machine (FSM) to regulate access. The FSM evaluates each digit, counts incorrect attempts, and activates an alarm if the maximum number of failures is exceeded. The design is fully modular and suitable for FPGA deployment.

---

### **3️⃣ Key Features**

* 4-digit password authentication
* FSM-based control logic
* Attempt counter with alarm activation
* Forced first-attempt failure (as per requirement)
* Seven-segment display indicators (L/U/A)
* Complete Verilog testbench
* FPGA-ready modular structure

---

### **4️⃣ Project File Structure**

```
📦 Digital-Door-Lock-System
│
├── password_rom.v
├── door_lock_fsm.v
├── display_driver.v
├── tb_top_door_lock.v
├── README.md
└── project_document.md
```

---

### **5️⃣ Block Diagram Explanation**

The system consists of four functional blocks:

1. **Keypad Input Module** – Accepts digit inputs and key-valid signals.
2. **Password ROM** – Stores the 4-digit authentication code.
3. **FSM Controller** – Manages state transitions, password checking, and attempt counting.
4. **Output Module** – Controls LEDs, seven-segment display, and_alarm_ signal.

Data flows in the sequence: **Key Input → FSM → (ROM + Attempt Counter) → Outputs**.

---

### **6️⃣ Methodology**

The design process begins with defining system requirements and building a finite state machine to control password validation and lock states. The modules are coded in Verilog, simulated using a structured testbench, and then synthesized using Vivado for FPGA deployment. Real-time testing verifies the accuracy of state transitions and alarm activation.

---

### **7️⃣ Simulation Summary**

Simulation results confirm:

* The first attempt intentionally fails.
* The second attempt succeeds and unlocks the system.
* After a reset, three wrong attempts trigger the alarm.
* Seven-segment and LED outputs match the FSM logic.

Waveforms validate correct timing and functional behavior.

---

### **8️⃣ FPGA Implementation**

The design is synthesized and deployed on an FPGA using Vivado. Pin mapping is completed for the keypad input, LEDs, and seven-segment display. Real-time testing on hardware confirms that password entry, locking/unlocking, and alarm operations match simulation results, proving reliable and stable performance.

---

### **9️⃣ Conclusion**

This project demonstrates the complete development cycle of a digital security system using Verilog HDL and FPGA technology. With features like password validation, attempt monitoring, alarm triggering, and an FSM-driven control logic, it showcases practical embedded system design and real-time hardware implementation.

---

### **🔟 Author**

**Name:** RAMKUMAR Y
**Reg No:** 212223063005

---
