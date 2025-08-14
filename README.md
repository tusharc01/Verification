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

Dr. Robit begins by discussing a "**very important topic**" today: **"task and function"** (tasks and functions). His goal is to provide **motivation** for why we need them, and **where to use `task` and where to use `function`**.

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

In summary, he reiterates that "**wherever you are using some many timing information you're using you have to use Tasks**". He hopes this concept is clear and will be reinforced during lab exercises.
