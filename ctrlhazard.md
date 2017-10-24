| IF    | ID    | EX    | MEM   | WB    |
| ----- | ----- | ----- | ----- | ----- |
| LW1   |       |       |       |       |
| LW2   | LW1   |       |       |       |
| ADD   | LW2   | LW1   |       |       |
| SUB   | ADD   | LW2   | LW1   |       |
| SUB   | ADD   | STALL | LW2   | LW1   |
| ADDI1 | SUB   | ADD   | STALL | LW2   |
| ADDI2 | ADDI1 | SUB   | ADD   | STALL |
| -     | ADDI2 | ADDI1 | SUB   | ADD   |

No need to stall for LW2
For ADD, we have to stall cuz hazard <>
For SUB, no hazard so no stall
with AddI1, we don't need to stall at all
With ADDI2, you don't have to stall if we forward R4 from SUB

```
code 1

LW1 R1, 0(R2)
LW2 R3, 0(R4)
ADD R4, R1, R3
SUB R4, R4, R6
ADDI R2, R2, 4
ADDI R4, R4, 4
```
@ clkcycle SUB ADD STALL LW2 LW1

RegDst = 0
RegWrite = 1
ALUsrc
MEMWrite = 0
MemRead =1 (LW)
MemtoReg = 1 (LW)
ALU OP = 0 (STALL, do nothing)
PC src = 0 ()
ForwardA = 00 (no forwarding to stall)
ForwardB = 00

## control hazards
Prob with Branches
we don't know which instr to get next. 