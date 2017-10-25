# DCD HW3

## 4.3.1

- critical path :

​	pc -> instruction mem -> register -> MUX -> ALU -> D-mem -> MUX

​	400 + 200 + 30 + 120 + 350 + 30 = **1,130 ps**

- additional ALU Latency: 300ps, so

​	1130 + 300 = **1,430 ps**

- control latency = 1430 + 100 - 400 = **1,130 ps**

## 4.3.2

Speed Up by adding : No Speed Up in critical path. 

- at 1130 ps, we have 1000 instructions : running time = **1,130,000 ps**
- at 1430 ps, we have 950 instruction : running time = **1,358,500 ps**
- No Speed up is actually achieved.

## 4.3.3

$ Cost \over (1 / ExTime) $, where Extime = performance

1 Million * 1,130 ps = 1,130,000,000 ps = **0.00113 s**

cost performance = 3890/(1/0.00113) = **4.4**

for with and without improvement,

​	3890 + 600 = 4490

​	1000000 * 0.95 * 1430 = **0.0013585 s**,

​	4490/(1/0.0013585) = **6.1**



​	3890 - 400 = 3490

​	10000000 * 1130 = **0.00113 s**

​	3490/(1/0.00113) = **3.94**

## 4.5.1

Data memory is only used by LW and SW, so 25+10 = 35%

## 4.5.2

Sign-extend circuit is used for all of the instructions except for ADD and NOT, so 100 - 0 - 20 = 80%

## 4.6.1

## 4.6.2

## 4.6.3



## 4.8.1

pipelined: Cycle time is at slowest stage = **350ps**

no pipeline: cycle time is at summing of all stages = 250+350+150+300+200 = **1250ps**

## 4.8.2

LW instruction uses all 5 stages. pipelined processor can take 5 cycles at $350ps \over cycle$ for total latency of **1750ps**. no pipeline processor can take **1250ps.**

## 4.8.3

Splitting longest stage is the way.  Since the longest stage is ID as 350ps, we can split that. New longest stage is mem, as 300ps.

## 4.8.4

Data memory is used for LW and SW only. Without stall, we will use 20+15 = 35% of clock cycle for data mem

## 4.8.5

Write reg is used by ALU and LW instruction. 20+45 = 65% of clock cycle for write reg

## 4.8.6

Multi cycle organization has same clk cycle time as pipelined organization. 

Multi Cycle ExTime = (5 * 20%) + (4 * (45%+20%+15%)) = (1)+(3.2) = **4.2**

Single Cycle ExTime = $$no \ pipeline \ cycle \over pipeline \ cycle \ time$$ = $$1250 \over 350$$ = **3.5**
