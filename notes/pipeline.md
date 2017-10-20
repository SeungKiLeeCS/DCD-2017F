# Pipeline

## Pipelining with Hazard
Hazards are complications in pipelining that occur because instructions are executing in parellel, but the programmer assumes that the execute one at a time.

### 1) Structure Hazard
2 instructions in the pipeline want simulataneous access to the same piece of hardware
#### example
In our MIPS machine, we have instruction mem -> what's the problem of having single memory? : When two try to read at the same time. all of instructions needs to read from instruciton mem. But loadword has to read from data mem as well.

```
code 1

ADD R1, R1, R3
LW R5, 0(R6)
OR R4 ...
AND ...
ADDi ...
SUB ...
```

this table prob on the test

| IF   | ID   | EX   | MEM  | WB   |
| ---- | ---- | ---- | ---- | ---- |
| ADDI | AND  | OR   | LW   | ADD  |

This is structure hazard is you only have one mem.

Reg File could have structure hazard. We deal with this one by writing in the first half of the clk cycle and reading in the second half.

We do this in this order because the instruction in WB is an earlier instruction in ID, may need the result of the earlier instruction.

#### How you deal with structure hazard
1) make 2 copies of the hardware
2) time share a single copy within the clock cycle
3) insert a stall/nop so that one instruction uses the hardware while the other instruction waits.

-> With the Stall / Assume one memory only. / each row is a cycle

| IF      | ID      | EX      | MEM  | WB   |
| ------- | ------- | ------- | ---- | ---- |
| ADD     |         |         |      |      |
| LW      | ADD     |         |      |      |
| OR      | LW      | ADD     |      |      |
| AND     | OR      | LW      | ADD  |      |
| **NOP** | AND     | OR      | LW   | ADD  |
| ADDI    | **NOP** | AND     | OR   | LW   |
| SUB     | ADDI    | **NOP** | AND  | OR   |
-> this NOP, called bubbles/stalls in the book. This will result in increase in cycle, because we're skiping an instruction to avoid structure hazard

structure hazards are easily avoided in design of computer

### 2) Data Hazard
occur due to the fact that instructions may be reading/writing from the same register

Why avoid large variety of register when writing code? to avoid data hazard

#### Example

```
code 2

LW R1, 0(R2)
ADD R3, R1, R4
ADDI R3, R3, -4
SW R3, 0, R2
```

there's data dependency in this code, because LW R1 - ADD R1 and ADD R3 - ADDI R3, and ADDI R3 - SW R3. Basically check the diagonal registers and if they overlap then data hazard cuz we have read-write problem.

Store Word here, arrow go staright down (cuz it reads)

RAW (Read after Write) Hazard : True Dependency.
-> 2 other hazards type
1) WAW (Write after Write)
2) WAR (Write after Read)


#### Example

```
code 3

ADD R1, R2, R3
ADDI R4, R1, 6
SUB R1, R9, R10
AND R3, R6, R7
```

WAW hazard is ADD R1, SUB R1
WAR hazard is ADD R3, AND R3

We don't have problem with these cuz everything is in order in this class. -> for Test, list where the dependencies are.

#### Fix of Data Hazard using 
1) STall the instruction that needs the result in the ID stage until it can be readfrom the register file.

The control logic will determine whether or not the instruction should stall

| IF   | ID   | EX   | MEM  | WB   |
| ---- | ---- | ---- | ---- | ---- |
| LW   |      |      |      |      |
| ADD  | LW   |      |      |      |
| ADDI | ADD  | LW   |      |      |
| ADDI | ADD  | NOP  | LW   |      |
| ADDI | ADD  | NOP  | NOP  | LW   |
| SW   | ADDI | ADD  | NOP  | NOP  |
| SW   | ADDI | NOP  | ADD  | NOP  |
| SW   | ADDI | NOP  | NOP  | ADD  |
| NOP  | SW   | ADDI | NOP  | NOP  |
| NOP  | SW   | NOP  | ADDI | NOP  |
| NOP  | NOP  | SW   | NOP  | ADDI |

1. LW decoded, no prob
2. Add decoded, and now we see the R1 hazard / so stall
3. now ADD stays in ID, and NOP fills the place to avoid data hazard
4. LW write to reg in the first half, and read on 2nd half. no stall needed!
5. Stall cuz ADDI need result from ADD
6. stall again
7. now ADDI moves
8. But SW can't move cuz it needs the result of ADDI, so stall
9. Now it can move 

as you can see, with all the NOP, its not the optimal solution.

Question : At what point in the pipeline is the data available? -> at writing to regfile. So using this, comes data forwarding.

2) Data Forwarding

IF the data an instruction needs is currently present in the pipeline, I can "FORWARD" it to the appropriate stage in the pipeline without having to go through the register file first. As a result, I can avoid a lot of my stall cycles. If not, we still need to stall

| IF   | ID   | EX   | MEM  | WB   |
| ---- | ---- | ---- | ---- | ---- |
| LW   |      |      |      |      |
| ADD  | LW   |      |      |      |
|ADDI|ADD|LW||| > now ADD need result of LW of R1. ADD needs the data at EX. But data need to be taken out from mem, so stall.
|ADDI|ADD|NOP|LW|| > now I can get the data from MEM, so ADD moves.
|SW|ADDI|ADD|NOP|LW| > LW's R1 is now forwarded to ADD at EX
|NOP|SW|ADDI|ADD|NOP| > R3 goes from ADD to ADDI. But with SW, it needs the R3's result at MEM because it stores in memory.
|NOP|NOP|SW|ADDI|ADD| > SW does not need the data yet, so another way of doing this would be :

| IF   | ID   | EX   | MEM  | WB   |
| ---- | ---- | ---- | ---- | ---- |
| ...  | ...  | ...  | ...  | ...  |
| ...  | ...  | ...  | ...  | ...  |
|NOP|NOP|NOP|SW|ADDI| > where R3 still goes from ADDI to SW

Reference : Book [figure 4.53]()

### 3) Control Hazard
Due to branched, and we don't know which instruction should come next until the branches are completed.

| IF    | ID               | EX      | MEM   | WB    |
| ----- | ---------------- | ------- | ----- | ----- |
| empty | Hazard Detection | Forward | empty | empty |

Hazard Detection determines stall cycles.
Forward determines which data (if any) to forward

The stalls always happen in ID stage

```
code 4

add R1, R2, R3
sub R4, R1, R5
```

R1 is rd in add, rs in sub. R5 is rt in sub


```pseudocode bitch
if:
	ex/mem.regwrite && 
	ex/mem.regrd != 0 &&
	ex/mem.regrd = ID/EX.regrs 
then:
	forward A = 10
```