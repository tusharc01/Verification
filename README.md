# Verification
ASIC


---
https://www.youtube.com/watch?v=ZfLRzeGHUr8&list=PL0-xus8sJBCQpMw8MeV8bBZ0bvAdAZx8n&index=9&pp=iAQB

This video by Dr. Robit aims to "**balance out the theoretical underpinnings in design verification and practical thing that you need to know**," focusing more on "some practical aspects". Specifically, it delves into **SystemVerilog's support for verification**, starting with **data types**.

He first reviews what was available in **Verilog 2001** for design:
*   `module`
*   `assign`
*   `always`
*   `always @(...)` and `initial` blocks
*   `generate`
*   `multi-dimensional arrays`

Then, he introduces **SystemVerilog design extensions**, which are there to "**increase productivity and efficiency in AAL design**":
*   **`logic`**: A new data type that eliminates the need to "worry about why and W we have to use y to R" (referring to `wire` and `reg`).
*   **Interfaces**: Available for connectivity.
*   **Connectivity enhancements**: You "don't have to actually specify all the signals in the port," you can just use "wild CTIC" (referring to `.*`).
*   **Procedural statements**: Like `always_comb`, `always_ff`, and `always_latch`, for efficient hardware synthesis.

For **verification**, SystemVerilog offers "a lot of support" including:
*   **Classes**
*   **Randomization**
*   **Assertions**
*   **Coverage**
*   **Dynamic data Structures (Arrays & queues)**

The main focus of this video is on **data types** for verification. He categorizes them into three types: **hardware, variables, and simulation**.

1.  **Hardware Data Types**:
    *   **`reg` and `wire`**: Traditionally, `reg` could be a register or a net depending on usage (e.g., with a clock in a procedural block for a register, or in an `always` sensitivity list for a net), while `wire` is always a net used with continuous assignments.
    *   **`logic`**: This is a key improvement. Instead of `reg` and `wire`, you can just use `logic`.
    *   **Four-State vs. Two-State**: `reg`, `wire`, and `logic` are **four-state data types**. This means they can take values of **0, 1, X (unknown), and Z (high impedance)**.
    *   **Importance for Hardware**: He stresses, "**all the signals that are going into your Hardware they have to be four state**". This is crucial to "check whether you are getting any of the signal is not connected inside your module or there are some something that is driven by two of two modules do you get an X". In summary, "all signals going into Hardware or for state".

2.  **Variable Data Types**:
    *   These are **two-state data types**, meaning they can only have **0 and 1**.
    *   **Fixed Types**:
        *   **Signed**: `byte` (8-bit), `short` (16-bit), `int` (32-bit), `longint` (64-bit).
        *   **Unsigned**: `bit` (single bit, but can be a vector like `bit [7:0]`).
    *   **Float Type**: `real`.
    *   **Efficiency**: He states, "we prefer two State signal because **it's really efficient in the simulation**".
    *   **Usage**: All "Loop variables or your expected data result that you want to you know see they can be variables". Also, "some knob that you want to control" can be two-state variables.

3.  **Simulation Data Types**:
    *   **`time` and `realtime`**: These are used for measuring time. If you have a `timescale` of `1ns/1ps` and use `$time`, a value like `12.2ns` would only give you `12` with the `time` data type. If you want to "see the P second as well then you just go for the real time and then you will get the complete 12.2," including picoseconds.

The **"take home" message** regarding data types is:
*   "**for all your Hardware the signals that are going into your Hardware they will be four state and you typically use logic for State**".
*   "all the signals have to be logic in your test bench for your other signals".
*   "variables that you want to keep for like some knob that you want to control or let's say the some Loop variables they can be two State," as "this is more efficient".
*   The X and Z states are specifically for scenarios like "unconnected signals or multiple driving signals".

He concludes by stating he "try to compact the whole discussion around the data types" and the next video will discuss tasks and functions.

---
https://youtu.be/3qMkbL2yQho?list=PL0-xus8sJBCQpMw8MeV8bBZ0bvAdAZx8n

Dr. Abid begins by discussing a "**very important topic**" today: **"task and function"** (tasks and functions). His goal is to provide **motivation** for why we need them, and **where to use `task` and where to use `function`**.

He refers back to a previous discussion about the `test` module, where the driver and checker were combined to make the verification environment more **portable**. The problem arises when we want to **"replicate the code for read multiple times"** for memory operations, such as "write memory, then read after write, and then write write read". To keep the verification environment **"neat and clean"** and **"reuse the code,"** he suggests a solution similar to using functions in C language.

**Crucially, he states that this `function` is "not allowed in SystemVerilog" because "it contains the timing information at `negedge of clock`"**.

The key rule is that **"if we have to include any sort of a timing information then we cannot use function, so we have to just replace the function with `task`"**. This also applies if you "want to give the delay specifically," like `#10`. You "can't do it in function but we can do it in task". This is why `M_read` and `M_write` will be made into `task`.

He then provides a clear comparison between `task` and `function`:

*   **`Task`**:
    *   **Can contain timing elements** (e.g., `negedge clk`, specific delays like `#10`).
    *   Can take **multiple inputs and outputs** through arguments.
    *   **Cannot return any value**. He illustrates this: "let's say you have a task over here instance the TOs from name t input a and you want to return something a logic a so this is not this is not allowed you cannot return anything from the task."

*   **`Function`**:
    *   **Cannot contain timing** because "it runs in zero time."
    *   Can take **multiple inputs and outputs** through arguments (like tasks).
    *   **Can return single value**. He explains: "let's say you have logic function f it can take input a output B whatever but you can return a value as well."
    *   Typically used if you "really want to return some status signal" or for "printing" (in which case you'd typically use `void` functions).

In summary, he reiterates that "**wherever you are using some many timing information you're using you have to use Tasks**".

---
https://youtu.be/7AZZJuaNUdY?list=PL0-xus8sJBCQpMw8MeV8bBZ0bvAdAZx8n

Dr. Abid opens this video by focusing on the "**third pillar which is polymorphism**" of object-oriented programming. He emphasizes the goal is not to discuss it in an abstract way, but to see "**how this can be used effectively for verification**".

He uses the familiar example of the **memory module** that has been tested in previous videos, which includes a "VIP" (Verification IP) and a "test generating the stimulus and doing the automatic checking as well". He depicts this as a **layered form**:
*   **Layer zero**: The "design test".
*   **Driver**: This layer "**is actually applying different signals**," such as `read = 1`, `write = 0`, address, and data for a memory read.
*   **Higher Abstraction Layer**: He explains they want to move to "**much higher obstruction layer**," where a **generator** is "generating multiple transactions," like "read write and error". These transactions are "send one by one to the driver," which then "checks what is the type of the transaction appli these signals lower level signals accordingly".
*   **Scoreboard**: "In the reverse we are getting the feedback from the dut" (DUT), and both the stimulus and feedback are "transend to the scoreboard for automated checking".

The **generator** is designed to "generating multiple type of transactions":
*   **Read transaction**
*   **Write transaction**
*   **Error transaction**: Defined as "read=1, Write=1" which "cannot be one" for a "single port memory".
*   **Random transaction**: This is "basically any of the three above possible types".

He then introduces the **inheritance hierarchy**:
*   "**transaction is a base class**" (base class).
*   All the specific transactions (random, read, write, error) are "**derived class**" (derived classes).

The core concept of **polymorphism** is that it "**allows us to store instance of any sub class in handle of superclass**". In this case, the "super Class" or "base Class" is the `transaction` class. This means "we can store any of these instances in the handle of the super class".

He explains **how this is used on the generator side**:
*   The generator is "**generating actually multiple of these transactions**".
*   These can be pure random transactions, read transactions, write transactions, or error transactions.
*   **Crucially, "we are generating them but we assigning them into the uh base law handle"**.
*   The generator "**only sending it a Basel handle T and assigning the instance to the Bas loss handle**". It sends "the base Closs uh handle not the drive CLA handles".

On the **receiver side, at the driver side**:
*   "we checking whether this Basel handle contains any of the re transaction handle if it does contain then we apply the read signals onto the interface and likewise for write and error and our random transaction".
*   Therefore, "on the receiver side we check what does this base loss contains and then apply the signals accordingly".

He describes the conceptual code structure:
*   A `class transaction` (the base class) would contain `right_data`, `address`, a constructor, and a `function generate_transaction` (which "doesn't contain anything" initially).
*   A `random class` is "**drive from The Base Class transaction**" and its `generate_transaction` would "actually generating a pure random transaction add Des data right read".

He illustrates **how the generator side code works**:
*   "we actually declare um handles of handles array of Base L" (base class handles).
*   Derived class handles are also declared.
*   In a loop, a "random case" decides which type of transaction to create.
*   For example:
    *   If `random_case` is `0`, a `r_transaction` (read transaction) object is generated, and "**assigning it to trans Z is equal to RR**" (where `trans_0` is a base class handle).
    *   If `random_case` is `1`, a `right_transaction` object is created, and "**trans two is equal to right trans**".
*   He reiterates, "**this is where we generate um the different transaction but assign it to the base class handle so this is really important we are assigning them to the base loss**".
*   Then, `base_loss.generate_transaction` is called, even though the "base loss gener transaction is empty".

He highlights the **advantages** of this approach:
*   "**the type of transaction is decided at the start of the stimulus code only**".
*   "**all subsequent references are made to the base law**".
*   The "**good beauty of this thing is that more Drive classes can be added into the design**" by simply adding "a new case for a new Drive class".
*   "in future we just be referring to the base class handle not any of the drive classes so the **code is very neat and green**".

The video concludes with an open question left for the next part: "now the question is we are generating the base loss uh using the base loss uh do generate transaction so whether this base loss will be called or drive loss". He states, "in the next video I will let let you know that uh which function will be called the base Closs one or the drive cross one".
