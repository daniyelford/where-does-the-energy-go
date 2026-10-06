# Where Does the Energy Go When Software Runs?

> From software instructions to CPU cycles, transistor switching, electrical energy, and heat.

When we run a piece of software, we usually think about the result:

```text
Input → Computation → Output
```

But underneath the software abstraction, something physical is happening.

A processor is made of billions of transistors. These transistors continuously change electrical states, charging and discharging tiny capacitances while data moves through registers, caches, execution units, interconnects, and memory.

So a simple question leads to a much deeper one:

> **Where does the energy go when software runs?**

We can start by following the physical chain:

```text
Software
   ↓
Runtime / Compiler / JIT
   ↓
Machine Instructions
   ↓
CPU Microarchitecture
   ↓
Transistor Switching
   ↓
Voltage + Current
   ↓
Power
   ↓
Energy
   ↓
Heat + Other Physical Effects
```

This article follows that chain from software all the way down to physics.

---

# 1. Voltage, Current, Power, and Energy

Before connecting software to a CPU, we need four basic electrical quantities.

## Voltage

Voltage is electrical potential difference.

We can represent it as:

$$
V = \frac{W}{Q}
$$

where:

* \(V\) is voltage in volts
* \(W\) is energy/work in joules
* \(Q\) is electric charge in coulombs

So voltage tells us how much energy is associated with moving a unit of charge between two points.

---

## Current

Electric current describes how quickly charge flows:

$$
I = \frac{dQ}{dt}
$$

where:

* \(I\) is current in amperes
* \(Q\) is charge
* \(t\) is time

In simple terms:

```text
Current = charge / time
```

---

## Power

Electrical power is the rate at which electrical energy is transferred:

$$
P = VI
$$

Therefore:

```text
Voltage × Current = Power
```

For example, if a circuit operates at:

$$
V = 1.0V
$$

and consumes:

$$
I = 10A
$$

then:

$$
P = 1.0 \times 10 = 10W
$$

---

## Energy

Energy is power accumulated over time:

$$
E = \int P(t)\,dt
$$

For constant power this becomes:

$$
E = Pt
$$

So:

```text
Power = how fast energy is being consumed
Energy = how much energy was consumed
```

This distinction will become extremely important when we introduce CPU frequency.

---

# 2. Frequency Enters the Picture

A modern CPU operates through coordinated switching controlled by a clock.

If the clock frequency is:

$$
f = 4GHz
$$

then the clock period is:

$$
T = \frac{1}{f}
$$

Therefore:

$$
T = \frac{1}{4\times10^9}
$$

which is:

$$
T = 250ps
$$

So one 4 GHz clock period is approximately:

```text
250 picoseconds
```

But this does **not** mean that the CPU executes exactly one software operation every 250 ps.

Modern CPUs can execute multiple instructions per cycle, while other instructions may require multiple cycles. Pipelines, caches, branch prediction, out-of-order execution, memory latency, and many other factors affect actual execution.

Therefore:

$$
\boxed{\text{CPU frequency} \neq \text{software operations per second}}
$$

Frequency is the rate at which clock cycles occur.

---

# 3. Why Frequency Affects Power

One of the most useful simplified models for dynamic CMOS power is:

$$
P_{dynamic} \approx \alpha C V^2 f
$$

where:

* \(\alpha\) = switching activity factor
* \(C\) = effective switched capacitance
* \(V\) = supply voltage
* \(f\) = switching/clock frequency

This equation is one of the key bridges between software execution and electrical energy.

Notice something interesting:

$$
P \propto f
$$

but:

$$
P \propto V^2
$$

Voltage appears **squared**.

This means voltage can have a particularly strong effect on dynamic power.

> This is a simplified CMOS model. Real processors also consume static/leakage power and have additional power components from clock distribution, memory, interconnects, I/O, and other circuits.

---

# 4. Energy of a Capacitive Switching Event

Digital CMOS circuits constantly charge and discharge capacitances.

A simplified expression for the energy associated with charging a capacitor is:

$$
E = \frac{1}{2}CV^2
$$

where:

* \(C\) is capacitance
* \(V\) is voltage

Suppose:

$$
C = 1pF
$$

and:

$$
V = 1V
$$

Then:

$$
E = \frac{1}{2}(1\times10^{-12})(1)^2
$$

so:

$$
E = 0.5pJ
$$

This is an extremely small amount of energy.

But a processor performs an enormous number of switching events.

That is where the scale changes.

---

# 5. From One Switching Event to CPU Power

If approximately \(N\) switching events happen per second, then:

$$
P \approx N \times E_{switch}
$$

Using:

$$
E_{switch} \approx \frac{1}{2}CV^2
$$

we get:

$$
P \approx \frac{1}{2}NCV^2
$$

If the switching activity is related to clock frequency:

$$
N \propto \alpha f
$$

then the simplified model becomes:

$$
P_{dynamic} \propto \alpha CV^2f
$$

which gives us the familiar approximation:

$$
\boxed{P_{dynamic} \approx \alpha CV^2f}
$$

This is the first major connection between:

```text
Frequency
    ↓
Switching activity
    ↓
Electrical power
    ↓
Energy consumption
```

---

# 6. But Software Does Not Directly Control Frequency

Consider:

```js
let sum = 0;

for (let i = 0; i < 1_000_000_000; i++) {
    sum += i;
}
```

The JavaScript code does not directly say:

```text
"CPU, switch your transistors one billion times."
```

Instead, the execution path is more complicated:

```text
JavaScript
    ↓
JavaScript Engine
    ↓
Interpreter / JIT Compiler
    ↓
Machine Code
    ↓
CPU Instructions
    ↓
CPU Microarchitecture
    ↓
Transistor Switching
    ↓
Electrical Energy
```

The software determines **what computation needs to happen**.

The processor determines **how that computation is physically executed**.

This distinction is essential.

---

# 7. From `x++` to Transistor Switching

Now let's take an extremely small piece of software:

```js
x++;
```

At first glance, it looks almost trivial.

But physically, it represents a surprisingly long chain of transformations.

```text
x++
 │
 ▼
JavaScript semantics
 │
 ▼
JavaScript Engine
 │
 ▼
Interpreter / JIT
 │
 ▼
Optimized machine code
 │
 ▼
CPU instructions
 │
 ▼
Registers / Cache
 │
 ▼
CPU execution units
 │
 ▼
Transistor switching
 │
 ▼
Electrical activity
 │
 ▼
Power
 │
 ▼
Energy
```

The important point is that `x++` does **not** necessarily become one specific CPU instruction.

For example, a JavaScript engine such as V8 may optimize the operation based on:

* the type of `x`
* whether the value is stored in a register
* whether the value is in memory
* surrounding operations
* type feedback
* optimization state
* deoptimization possibilities
* the target CPU architecture

Therefore, this would be an oversimplification:

```text
x++
 ↓
INC instruction
```

A more accurate model is:

```text
x++
 ↓
JavaScript semantics
 ↓
JIT compiler
 ↓
Target-specific machine code
 ↓
CPU execution
```

---

## 7.1 JavaScript Engine

JavaScript is a high-level language.

The CPU does not directly understand:

```js
x++;
```

A JavaScript engine such as V8 transforms JavaScript into lower-level representations that can eventually execute as machine code.

A simplified conceptual pipeline is:

```text
JavaScript source
       ↓
Parsing
       ↓
Internal representation
       ↓
Bytecode / intermediate execution
       ↓
Profiling / type feedback
       ↓
JIT optimization
       ↓
Machine code
```

The exact pipeline is engine- and version-dependent, so this diagram should be treated as a conceptual model rather than a fixed implementation.

---

# 8. Machine Instructions Are Still Not Transistors

Suppose the JIT eventually produces machine instructions.

We are still not at the physical transistor level.

There is another abstraction layer:

```text
Machine Instruction
       ↓
CPU Front End
       ↓
Decode
       ↓
Micro-operations
       ↓
Scheduler
       ↓
Execution Units
       ↓
Registers / Caches
       ↓
Transistor Networks
```

Modern CPUs are extremely complex.

A single machine instruction may involve many internal circuits, and several instructions may execute simultaneously.

This is why:

$$
\boxed{
1\ JavaScript\ operation
\neq
1\ CPU\ instruction
\neq
1\ clock\ cycle
\neq
1\ transistor\ switch
}
$$

This distinction is one of the most important ideas in understanding software energy consumption.

---

# 9. Registers and Cache Matter

Suppose `x` is already available in a CPU register.

The processor may be able to perform the necessary arithmetic without accessing main memory.

Conceptually:

```text
CPU Core
   │
   ├── Registers
   │
   ├── L1 Cache
   │
   ├── L2 Cache
   │
   └── L3 Cache
           │
           ▼
          RAM
```

The physical cost of an operation depends heavily on where its data comes from.

A computation involving registers can be very different from one that repeatedly waits for data from memory.

This introduces another important concept:

> **Software energy consumption depends not only on how much computation is performed, but also on how data moves through the memory hierarchy.**

---

# 10. Frequency vs. Execution Time

Suppose a workload requires approximately:

$$
3\times10^9
$$

clock cycles.

At:

$$
f = 3GHz
$$

the idealized execution time would be:

$$
t = \frac{3\times10^9}{3\times10^9}
$$

so:

$$
t = 1s
$$

At:

$$
f = 6GHz
$$

the same idealized number of cycles would take:

$$
t = \frac{3\times10^9}{6\times10^9}
$$

so:

$$
t = 0.5s
$$

But real CPUs are more complicated because changing frequency often changes voltage, and workload performance may be limited by memory latency, dependencies, branches, or other bottlenecks.

So we should not conclude:

```text
2× frequency = 2× performance
```

in general.

---

# 11. Higher Frequency Does Not Automatically Mean Higher Total Energy

This is where power and energy must be separated.

We have:

$$
E = Pt
$$

and approximately:

$$
P_{dynamic} \approx \alpha CV^2f
$$

Increasing frequency can increase power.

But increasing frequency can also reduce execution time.

Therefore the total energy for a fixed task depends on both:

```text
Power
   ×
Execution time
   =
Energy
```

A simplified conceptual comparison:

```text
Lower frequency
    ↓
Lower instantaneous power
    ↓
Longer execution time
    ↓
Total energy = ?

Higher frequency
    ↓
Higher instantaneous power
    ↓
Shorter execution time
    ↓
Total energy = ?
```

There is no universal rule that the higher-frequency execution always consumes more or less total energy.

The actual result depends on:

* voltage-frequency behavior
* workload characteristics
* CPU architecture
* cache behavior
* memory traffic
* idle states
* leakage
* thermal conditions
* operating-system scheduling
* other active hardware

---

# 12. Where Is the Electric Field?

At this point we have reached a physical question.

How does voltage actually affect a transistor?

Voltage represents an electric potential difference.

Electric fields are related to spatial changes in electric potential:

$$
\vec{E} = -\nabla V
$$

In a simplified one-dimensional case:

$$
E \approx \frac{\Delta V}{d}
$$

where:

* \(E\) is electric field magnitude
* \(\Delta V\) is potential difference
* \(d\) is distance

At transistor scales, the electric field is central to controlling charge behavior inside semiconductor structures.

So our chain becomes:

```text
Software
   ↓
Machine activity
   ↓
Transistor states
   ↓
Voltage differences
   ↓
Electric fields
   ↓
Charge movement
   ↓
Current
   ↓
Power
   ↓
Energy
```

---

# 13. Is JavaScript Energy Traveling as a Radio Wave?

Not in the way the metaphor might suggest.

It would be misleading to imagine:

```text
CPU
  ~~~~~~~~~~~~~~~~>
                  RAM
```

as if the energy of JavaScript execution were being transmitted through the computer as a radio signal.

Real systems contain electromagnetic fields associated with changing voltages and currents, and electromagnetic effects are important in high-speed interconnects.

However, most computing energy is not usefully described as software energy radiating away like a radio transmitter.

A better conceptual model is:

```text
Power Delivery Network
        │
        ▼
      CPU
        │
        ├── Registers
        ├── Cache
        ├── Execution Units
        └── Interconnects
              │
              ▼
             RAM
```

Energy is supplied electrically through the power-delivery system, while information is transferred through physical electrical interconnects.

Electromagnetic fields are part of the underlying physics of those signals, but that is different from saying that the program itself is an electromagnetic wave.

---

# 14. CPU → Cache → RAM → I/O

A software workload may activate many parts of the system:

```text
                 ┌──────────────┐
                 │   CPU Core   │
                 └──────┬───────┘
                        │
                  ┌─────▼─────┐
                  │ Registers │
                  └─────┬─────┘
                        │
                  ┌─────▼─────┐
                  │    L1     │
                  └─────┬─────┘
                        │
                  ┌─────▼─────┐
                  │    L2     │
                  └─────┬─────┘
                        │
                  ┌─────▼─────┐
                  │    L3     │
                  └─────┬─────┘
                        │
                Memory Controller
                        │
                  ┌─────▼─────┐
                  │    DRAM   │
                  └───────────┘
```

And beyond memory:

```text
CPU
 │
 ├── GPU
 ├── Network
 ├── Storage
 ├── USB
 └── Other I/O
```

Therefore the energy footprint of software is not necessarily confined to the CPU core.

A program can cause additional activity in:

* caches
* memory controllers
* DRAM
* storage
* network interfaces
* GPUs
* buses and interconnects
* cooling systems

---

# 15. The Same Result Can Have Different Energy Costs

Consider two programs that produce the same output.

```text
Program A
    ↓
Fewer instructions
    ↓
Better cache locality
    ↓
Less memory traffic
    ↓
Shorter execution
```

and:

```text
Program B
    ↓
More instructions
    ↓
Poor cache locality
    ↓
More memory traffic
    ↓
Longer execution
```

They may produce exactly the same logical result.

But their physical behavior can be very different.

This gives us an important principle:

$$
\boxed{
Same\ result
\neq
Same\ physical\ cost
}
$$

Software abstractions hide the physical implementation.

---

# 16. A Small JavaScript Experiment

We can start measuring software rather than only discussing it theoretically.

For example:

```js
function work(n) {
    let sum = 0;

    for (let i = 0; i < n; i++) {
        sum += i;
    }

    return sum;
}

console.time("work");

work(1_000_000_000);

console.timeEnd("work");
```

The JavaScript program gives us a measurable execution time.

But execution time alone is not energy.

To study energy, we need to observe or estimate additional quantities such as:

```text
Execution time
CPU utilization
CPU frequency
CPU package power
Memory activity
Temperature
```

Depending on the operating system and hardware, some of these values can be measured through hardware counters, processor telemetry, OS interfaces, or external power measurement.

---

# 17. From Code to Energy

We can now summarize the complete conceptual path:

```text
┌──────────────────────┐
│     Source Code      │
│      x++             │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ JavaScript Engine    │
│ Interpreter / JIT    │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│   Machine Code       │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ CPU Instructions     │
│ Microarchitecture    │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Registers / Cache    │
│ Execution Units      │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Transistor Switching │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Voltage + Current    │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Electric Fields      │
│ Electromagnetic      │
│ Effects              │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Power                │
│ P ≈ αCV²f            │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Energy               │
│ E = ∫P(t)dt          │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Heat + Other Effects │
└──────────────────────┘
```

---

# 18. The Core Idea

We started with a simple line:

```js
x++;
```

But that line exists simultaneously at several abstraction levels:

```text
Programming language
        ↓
Compiler / runtime
        ↓
Machine instructions
        ↓
Microarchitecture
        ↓
Digital logic
        ↓
Transistors
        ↓
Semiconductor physics
        ↓
Electromagnetism
        ↓
Electrical power
        ↓
Energy
        ↓
Heat
```

The source code is abstract.

The execution is physical.

That is the central idea of this article.

$$
\boxed{
\text{Software behavior}
\rightarrow
\text{Hardware activity}
\rightarrow
\text{Electrical activity}
\rightarrow
\text{Energy}
}
$$

And the deeper we go, the more interesting the question becomes:

> **Can we actually measure the energy cost of a specific piece of software and connect that measurement back to what the CPU is physically doing?**

That is where theory becomes an experiment.

---

# 19. What We Will Explore Next

The next step is to move from the conceptual model to real measurements.

We can investigate:

* How a JavaScript engine actually compiles a small program
* What machine code a JIT can generate
* How many CPU instructions are executed
* How CPU frequency changes during the workload
* How cache behavior changes execution
* How CPU power changes with workload
* How voltage and frequency interact
* How to estimate energy in joules
* How much of the energy is spent in CPU vs. memory
* Where the consumed electrical energy ultimately goes

The goal is not to say that software is "electricity."

The goal is more precise:

> **To follow the physical consequences of software execution across the abstraction layers of a modern computer.**
