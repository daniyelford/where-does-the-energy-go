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

## 1. Voltage, Current, Power, and Energy

Before connecting software to a CPU, we need four basic electrical quantities.

### Voltage

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

### Current

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

### Power

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

### Energy

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

Modern CPUs can execute multiple instructions per cycle, while other instructions may require multiple cycles. Pipelines, caches, branch prediction, out-of-order execution, and memory latency all affect the actual behavior.

Therefore:

$$
\boxed{\text{CPU frequency} \neq \text{software operations per second}}
$$

Frequency is the rate at which the clock cycles occur.

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
Micro-operations
    ↓
CPU Execution Units
    ↓
Transistor Switching
    ↓
Electrical Energy
```

The software determines **what computation needs to happen**.

The processor determines **how that computation is physically executed**.

This distinction is essential.

---

# 7. The Real Question

Now we can ask a much more interesting question:

If two programs produce the same result, can they consume different amounts of energy?

Absolutely.

For example:

```text
Program A
    ↓
Fewer instructions
    ↓
Fewer memory accesses
    ↓
Less switching
    ↓
Less execution time
    ↓
Potentially less energy
```

while:

```text
Program B
    ↓
More instructions
    ↓
More cache misses
    ↓
More memory traffic
    ↓
More switching
    ↓
More execution time
    ↓
Potentially more energy
```

So software can influence physical energy consumption even though the source code itself is an abstract mathematical representation.

That gives us the central idea of this article:

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

And this is where our journey begins.
