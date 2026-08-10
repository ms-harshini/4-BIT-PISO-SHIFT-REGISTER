# 4-Bit PISO Shift Register using Verilog

## 📌 Project Overview

This project implements a **4-bit Parallel-In Serial-Out (PISO) Shift Register** using Verilog HDL.

A PISO shift register accepts multiple bits simultaneously through parallel inputs and then shifts the stored data out one bit at a time through a serial output.

---

## 🎯 Objective

The objective of this project is to design and verify a 4-bit PISO shift register.

The project demonstrates:

* Sequential logic
* Shift registers
* Parallel data loading
* Serial data transmission
* Clocked operation
* Reset operation
* Testbench-based verification

---

## 🛠️ Technologies Used

* Verilog HDL
* VS Code
* Icarus Verilog
* ModelSim / QuestaSim
* GTKWave (optional)
* GitHub

---

## 📂 Project Structure

```text
4-Bit-PISO-Shift-Register/
│
├── README.md
├── src/
│   └── piso_shift_register_4bit.v
│
├── testbench/
│   └── tb_piso_shift_register_4bit.v
│
└── simulation/
    └── simulation_results.txt
```

---

## 🔢 Inputs and Outputs

| Signal      | Width | Description         |
| ----------- | ----: | ------------------- |
| CLK         | 1-bit | Clock signal        |
| RESET       | 1-bit | Asynchronous reset  |
| LOAD        | 1-bit | Loads parallel data |
| PARALLEL_IN | 4-bit | Parallel input      |
| SERIAL_OUT  | 1-bit | Serial output       |

---

## 🧠 What Does PISO Mean?

PISO stands for:

**Parallel-In Serial-Out**

Four bits can be loaded simultaneously:

```text
PARALLEL_IN = 1011
```

After loading, the bits are shifted out sequentially.

---

## ⚙️ Working Principle

The internal register contains:

```text
Q3 Q2 Q1 Q0
```

When:

```text
LOAD = 1
```

the parallel input is loaded:

```text
Q3 Q2 Q1 Q0 = PARALLEL_IN
```

When:

```text
LOAD = 0
```

the register shifts toward the MSB output:

```text
Q3 ← Q2
Q2 ← Q1
Q1 ← Q0
Q0 ← 0
```

The serial output is:

```text
SERIAL_OUT = Q3
```

---

## 🔢 Example

Suppose:

```text
PARALLEL_IN = 1011
```

After loading:

```text
1011
```

The data shifts as:

```text
1011
0110
1100
1000
0000
```

The serial output sequence is:

```text
1 → 0 → 1 → 1
```

Thus, the original 4-bit parallel data is transmitted serially.

---

## 🔄 PISO Operation

```text
        PARALLEL INPUT
             │
             ▼
        ┌───────────┐
        │  4-BIT    │
        │  SHIFT    │
        │  REGISTER │
        └─────┬─────┘
              │
              ▼
        SERIAL OUTPUT
```

---

## 🔌 LOAD Signal

The `LOAD` signal determines the operation.

### LOAD = 1

Parallel data is loaded:

```text
shift_reg <= parallel_in;
```

### LOAD = 0

The register shifts:

```text
shift_reg <= {shift_reg[2:0], 1'b0};
```

---

## 🧪 Testbench

The testbench verifies:

* Reset
* Parallel loading
* Serial shifting
* Serial output
* Multiple clock cycles

---

## ▶️ Simulation Using Icarus Verilog

Open the terminal inside the project directory.

### Compile

```bash
iverilog -o piso_sim src/piso_shift_register_4bit.v testbench/tb_piso_shift_register_4bit.v
```

### Run

```bash
vvp piso_sim
```

---

## 📋 Expected Output

For:

```text
PARALLEL_IN = 1011
```

the serial output sequence should be:

```text
1
0
1
1
```

---

## 📚 Concepts Demonstrated

* PISO shift register
* Sequential logic
* Flip-flops
* Parallel data loading
* Serial data transmission
* Clocked circuits
* Asynchronous reset
* Non-blocking assignments
* Testbench development
* Functional verification

---

## 🚀 Applications

PISO shift registers are used in:

* Parallel-to-serial conversion
* Serial communication
* Data transmission
* Microcontrollers
* Communication interfaces
* Digital systems
* GPIO reduction
* Data serialization

---

## 🔄 SIPO vs PISO

| Feature      | SIPO               | PISO                         |
| ------------ | ------------------ | ---------------------------- |
| Input        | Serial             | Parallel                     |
| Output       | Parallel           | Serial                       |
| Main purpose | Serial-to-parallel | Parallel-to-serial           |
| Data loading | One bit at a time  | Multiple bits simultaneously |

---

## 🚀 Future Improvements

This project can be extended to:

* 8-bit PISO register
* 16-bit PISO register
* Universal shift register
* Bidirectional shift register
* SPI-style serializer
* FPGA implementation

---

## 👩‍💻 Author

**Harshu**

B.Tech - Electronics and Communication Engineering

---

## 📄 License

This project is created for educational and academic purposes.
```verilog
`timescale 1ns/1ps

module piso_shift_register_4bit (
    input  wire       clk,
    input  wire       reset,
    input  wire       load,
    input  wire [3:0] parallel_in,
    output reg        serial_out
);

    reg [3:0] shift_reg;

    always @(posedge clk or posedge reset) begin

        if (reset) begin
            shift_reg <= 4'b0000;
        end

        else if (load) begin
            shift_reg <= parallel_in;
        end

        else begin
            shift_reg <= {shift_reg[2:0], 1'b0};
        end

    end

    assign serial_out = shift_reg[3];

endmodule
```
```verilog
`timescale 1ns/1ps

module tb_piso_shift_register_4bit;

    reg clk;
    reg reset;
    reg load;
    reg [3:0] parallel_in;

    wire serial_out;

    piso_shift_register_4bit DUT (
        .clk(clk),
        .reset(reset),
        .load(load),
        .parallel_in(parallel_in),
        .serial_out(serial_out)
    );

    // Clock generation
    always #5 clk = ~clk;

    initial begin

        clk        = 1'b0;
        reset      = 1'b1;
        load       = 1'b0;
        parallel_in = 4'b0000;

        $display("================================================");
        $display("           4-BIT PISO SHIFT REGISTER");
        $display("================================================");

        // Reset
        #10;

        reset = 1'b0;

        $display(
            "TIME=%0t | RESET RELEASED | SERIAL_OUT=%b",
            $time,
            serial_out
        );

        // Load parallel data
        parallel_in = 4'b1011;
        load = 1'b1;

        @(posedge clk);
        #1;

        $display(
            "TIME=%0t | LOAD=%b | PARALLEL_IN=%b | SERIAL_OUT=%b",
            $time,
            load,
            parallel_in,
            serial_out
        );

        // Disable load and begin shifting
        load = 1'b0;

        @(posedge clk);
        #1;

        $display(
            "TIME=%0t | SHIFT | SERIAL_OUT=%b",
            $time,
            serial_out
        );

        @(posedge clk);
        #1;

        $display(
            "TIME=%0t | SHIFT | SERIAL_OUT=%b",
            $time,
            serial_out
        );

        @(posedge clk);
        #1;

        $display(
            "TIME=%0t | SHIFT | SERIAL_OUT=%b",
            $time,
            serial_out
        );

        @(posedge clk);
        #1;

        $display(
            "TIME=%0t | SHIFT | SERIAL_OUT=%b",
            $time,
            serial_out
        );

        $display("================================================");

        $finish;

    end

endmodule
```
# 4-BIT PISO SHIFT REGISTER SIMULATION RESULTS

RESET = 1
SERIAL_OUT = 0

RESET RELEASED
SERIAL_OUT = 0

PARALLEL_IN = 1011
LOAD = 1
SERIAL_OUT = 1

SHIFT 1
SERIAL_OUT = 0

SHIFT 2
SERIAL_OUT = 1

SHIFT 3
SERIAL_OUT = 1

SHIFT 4
SERIAL_OUT = 0

================================================
Simulation completed successfully.
==================================
