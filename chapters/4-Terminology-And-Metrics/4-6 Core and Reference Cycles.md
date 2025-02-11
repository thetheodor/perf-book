---
typora-root-url: ..\..\img
---

## Core vs. Reference Cycles {#sec:secRefCycles}

Most CPUs employ a clock signal to pace their sequential operations. The clock signal is produced by an external generator that provides a consistent number of pulses each second. The frequency of the clock pulses determines the rate at which a CPU executes instructions. Consequently, the faster the clock, the more instructions the CPU will execute each second.
$$
Frequency = \frac{Clockticks}{Time}
$$
The majority of modern CPUs, including Intel and AMD CPUs, don't have a fixed frequency at which they operate. Instead, they implement dynamic frequency scaling, which is called *Turbo Boost* in Intel's CPUs, and *Turbo Core* in AMD processors. It allows the CPU to increase and decrease its frequency dynamically -- scaling the frequency down reduces power-consumption at the expense of performance, and scaling the frequency up improves performance but sacrifices power savings.

The core clock cycles counter is counting clock cycles at the actual frequency that the CPU core is running at, rather than the external clock (reference cycles). Let's take a look at an experiment on Skylake i7-6000 processor running a single-threaded application, which has a base frequency of 3.4 GHz:

```bash
$ perf stat -e cycles,ref-cycles ./a.exe
  43340884632  cycles		# 3.97 GHz
  37028245322  ref-cycles	# 3.39 GHz
      10,899462364 seconds time elapsed
```

Metric `ref-cycles` counts cycles as if there were no frequency scaling. The external clock on the setup has a frequency of 100 MHz, and if we scale it by the *clock multiplier*, we will get the base frequency of the processor. The clock multiplier for Skylake i7-6000 processor equals 34: it means that for every external pulse, the CPU executes 34 internal cycles when it's running on the base frequency.

Metric `cycles` counts real CPU cycles, i.e., taking into account frequency scaling. Using the formula above we can confirm that the average operating frequency was `43340884632 cycles / 10.899 sec = 3.97 Ghz`. When you compare performance of two versions of a small piece of code, measuring the time in clock cycles is better than in nanoseconds, because you avoid the problem of the clock frequency going up and down. 
