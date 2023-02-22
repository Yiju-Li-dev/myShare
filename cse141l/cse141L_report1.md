# CSE 141L Milestone 1
- Yiju Li, A16637309
- Haochen Wang, A17000549
- Sandy Wu, A16460868


## TOC
- [CSE 141L Milestone 1](#cse-141l-milestone-1)
  - [TOC](#toc)
  - [Academic Integrity](#academic-integrity)
  - [Team](#team)
  - [Introduction](#introduction)
  - [Architectural Overview](#architectural-overview)
  - [Machine Specification](#machine-specification)
  - [Programmer’s Model](#programmers-model)
  - [Program Implementations](#program-implementations)
    - [Program 1](#program-1)
    - [Program 2](#program-2)
    - [Program 3](#program-3)

## Academic Integrity

> Your work will not be graded unless the signatures of all members of the group are present beneath the honor code.

> To uphold academic integrity, students shall:
Complete and submit academic work that is their own and that is an honest and fair representation of their knowledge and abilities at the time of submission.
Know and follow the standards of CSE 141L and UCSD.

> Please sign (type) your name(s) below the following statement:
I pledge to be fair to my classmates and instructors by completing all of my academic work with integrity. This means that I will respect the standards set by the instructor and institution, be responsible for the consequences of my choices, honestly represent my knowledge and abilities, and be a community member that others can trust to do the right thing even when no one is watching. I will always put learning before grades, and integrity before performance. I pledge to excel with integrity.

- Yiju Li
- Haochen Wang
- Sandy Wu


## Team

| Team Name| Members | 
| --- | --- |
| The Three Body | Yiju Li, Haochen Wang, Sandy Wu|

## Introduction
- Machine Name
  - Wallfacer
  - The name comes from *The Three Body Problem*, a famous Sci-Fi.
- Machine type
  - Classical Load-Store
- Philosophy
  - "Wallfacer" is one of the most sensational character[s] in the Sci-Fi mentioned above. Similarly, our machine would be the most exhilarating machine designed for this course: Calm and Collected.
- Goals
  - The goals of our machine is solely to help our group to achieve A+ for this course.

## Architectural Overview
![test](archi.png)

[//]: <> (To edit the picture above, simply download draw.io integration in VS code)

## Machine Specification
- Instruction formats and Operations
  - P(prepare)
    - Perparing for other instructions
    - Format: opcode(2 bits)+func(2 bit)+Operand1(1 bit)+Operand2(4 bits)
  - M(Memory)
    - All instructions related to memory
    - Format: opcode(2 bits)+func(2 bit)+operand1(4bits)+extra bit
  - O(Operations)
    - Including Arithmetic/Logical/Shift instructions
    - Format: opcode(2 bits)+func(3 bit)+input(4 bits)
  - C(Counter)
    - Including all instructions related to modifing PC
    - Format: opcode(2 bits)+func(2 bit)+input(5 bits)
- Note our machine does not necessarily need to be seperated into these types. But for better scalability in the future, let's leave it as this.
  
  | Mnemonic | Meaning | Type | opCode | func |Format| Result |
  | --- | --- | --- | --- | --- | --- | --- |
  | hsd | Set the value of holder directly | P | 00 | 00 |hsd 0 x | R[h0] = x
  | hsr | Set the value of holder from the register| P | 00 | 01 |hsr 1 R[x] | R[h1] = R[x]
  | hclr | Cleaning the holders | P | 00 | 10 | hclr | R[h0]=0, R[h1] = 0
  |ldh | Load holder to reigister | P | 00 | 11 | ldh 0 R[x] | R[x] = R[0]
  | load | Load with full address| M | 01 | 000| load R[x] | R[x] = mem[ R[h0] ~ R[h1] ]
  | save | Save with full address | M | 01|001 | save R[x] | mem[ R[h0] ~ R[h1] ] = R[x]
  | loadi| Load with in-complete address | M | 01 | 010 | loadi R[x] | R[x] = mem[ R[h0] ]
  | savei| Save with in-complete address | M | 01 | 011 | savei R[x]  | mem[R[h0]] = R[x]
  | lss | Left shift and save | O |10|000 | lss R[x]| R[x] = R[x] << R[0] |
  |rss | Right shift and save | O | 10 | 001 | rss R[x]| R[x] = R[x] >> R[0]
  |xors | Bitwise XOR and save| O | 10 | 010 | xors R[x] | R[x] = R[x] ^ R[0]
  |adds | Add and save | O | 10 | 011 | adds R[x] | R[x] = R[x] + R[0]
  |ands| Bitwise AND and save | O | 10 | 100 | ands R[x] | R[x] = R[x] AND R[0]
  |jumpf | Alter PC to some given value with fake absolute| C | 11 |00 | jumpf X  | PC = X
  |bne | 	Branch if Not Equal | C | 11 |01 | bne X  | if R[0] != R[1], PC=X
  |bl | Branch if less | C | 11| 10 | bl X  | if R[0] < R[1], PC = X
  |bg | Branch if greater| C | 11 | 11 | bg X | if R[0] > R[1], PC = X
- Operands
  
  | Name | Number | Comment | 
  | --- | --- |---
  | Preparing Registers | 0,1 | --- | 
  |Zero Register | 2 | --- |
  | General Purpose Registers | 3-15| --- |
- Branch Operations
  
  There are three types of branching operation that is supported by our machine:
  - `bne`
  - `bl`
  - `bg`
  
  The address of these braches are calculated relatively, and the maximum distance supoorted is $$2^5$$ lines, currently.
- Addressing
  - Jump(Direct)
    - Enabled: Yes
    - Explanationn: Jump to abosolute address
    - Max. Distance: $$2^{12}$$ lines. 
  - branch(Immediate)
    - Enabled: Yes
    - Explanationn: Set PC to relative address
    - Max. Distance: $$2^{5}$$ lines. 
  - save/load(Direct)
    - Enabled: Yes
    - Explanationn: Jump to abosolute address
    - Max. Distance: $$2^{8}$$ lines. 
  - savei/loadi(Immediate)
    - Enabled: Yes
    - Explanationn: Jump to abosolute address
    - Max. Distance: $$2^{5}$$ lines. 

## Programmer’s Model
- The way of operation
    - Due to the limitations on the registers, our machine uses the "load-operations-save-load" model, meaning that we would recommend the programmer seperate the loading behaviours into different steps, instead of loading all the data at once. In other words, our machine requires the knowledge of how the data is structures before the operations.
  - One could compile the code from ARM/MIPS to Wallfacer(our machine) and vice versa. However, it won't work at all if one just simply copy the code. This is because that we implement with similar logic to ARM/MIPS but with different design, such that there's at most one operand for each instruction.

## Program Implementations
### Program 1
```python
program1():
  for i in range(15):
    word = mem[i*2]+mem[i*2+1][:3]
    p8 = ^(word[4:11])
    P4 = ^(word[7:11])^(word[1:5])
    P4 = ^(word[9:11])^(word[5:7])^word[3]^word[2]^word[1]
    p2 = word[0]^word[2]^word[3]^word[5]^word[6]^[9]^[10]
    p1 = word[0]^word[1]^word[3]^word[4]^word[6]^[8]^[10]
    p0 = ^(word)^p1^p2^p3^p4^p8
    mem[i*2+30] = [p0]+[p1]+[p2] + word[0]+[p4] + word[1:4]
    mem[i*2+31] = [p8]+ word[4:11]
```
```asm
      hclr            
      ldh          0   $15 
LOOP  hsr          0   $15   
      hsd          1   30    
      bl DONE           

      hsr          0   $15
      loadi        $14 
      hsd          1   1
      adds         $15

      hsr          0   $15
      loadi        $13    
      hclr            
      ldh          0   $12   
      ldh          0   $11   
      ldh          0   $10   
      ldh          0   $9    
      ldh          0   $8
      ldh          0   $6

      //D11
      hsr          0   $13   
      ldh          0   $7    
      hsd          2    
      rss          $7    
      hsr          0   $7    
      xors         $12
      xors         $11
      xors         $10
      xors         $9
      xors         $8
      hsr          0   $7
      adds         $6
      hsd          1
      lss          $6

      //D10
      hsr          0   $13   
      ldh          0   $7    
      hsd          1    
      rss          $7    
      hsr          0   $7    
      xors         $12
      xors         $11
      xors         $10
      xors         $8
      hsr          0   $7
      adds         $6
      hsd          1
      lss          $6

      //D9
      hsr          0   $13   
      ldh          0   $7    
      hsd          0    
      rss          $7    
      hsr          0   $7    
      xors         $12
      xors         $11
      xors         $9
      xors         $8
      hsr          0   $7
      adds         $6
      hsd          1
      lss          $6

      //D8
      hsr          0   $14   
      ldh          0   $7    
      hsd          7    
      rss          $7    
      hsr          0   $7    
      xors         $12
      xors         $11
      xors         $8
      hsr          0   $7
      adds         $6
      hsd          1
      lss          $6

      //D7
      hsr          0   $14   
      ldh          0   $7    
      hsd          6    
      rss          $7
      hsd          0   1
      ands         $7    
      hsr          0   $7    
      xors         $12
      xors         $10
      xors         $9
      xors         $8
      hsr          0   $7
      adds         $6
      hsd          1
      lss          $6

      //D6
      hsr          0   $14   
      ldh          0   $7    
      hsd          5    
      rss          $7 
      hsd          0   1
      ands         $7    
      hsr          0   $7    
      xors         $12
      xors         $10
      xors         $8
      hsr          0   $7
      adds         $6
      hsd          1
      lss          $6

      //D5
      hsr          0   $14   
      ldh          0   $7    
      hsd          4    
      rss          $7 
      hsd          0   1
      ands         $7    
      hsr          0   $7    
      xors         $12
      xors         $9
      xors         $8
      hsr          0   $7
      adds         $6
      hsd          1
      lss          $6

      //p8 and save first byte
      hsr          0   $12
      adds         $6
      hsr          1   $15
      hsd          0   30
      adds         $1
      hsr          0   $1
      savei        $6

      //D4
      hclr         
      ldh          $6
      hsr          0   $14   
      ldh          0   $7    
      hsd          3    
      rss          $7 
      hsd          0   1
      ands         $7    
      hsr          0   $7    
      xors         $11
      xors         $10
      xors         $9
      xors         $8
      hsr          0   $7
      adds         $6
      hsd          1
      lss          $6

      //D3
      hsr          0   $14   
      ldh          0   $7    
      hsd          2    
      rss          $7 
      hsd          0   1
      ands         $7    
      hsr          0   $7    
      xors         $12
      xors         $10
      xors         $8
      hsr          0   $7
      adds         $6
      hsd          1
      lss          $6

      //D2
      hsr          0   $14   
      ldh          0   $7    
      hsd          1    
      rss          $7 
      hsd          0   1
      ands         $7    
      hsr          0   $7    
      xors         $11
      xors         $9
      xors         $8
      hsr          0   $7
      adds         $6
      hsd          1
      lss          $6

      //p4
      hsr          0   $11
      adds         $6
      hsd          1
      lss          $6


      //D1
      hsr          0   $14   
      ldh          0   $7    
      hsd          0    
      rss          $7 
      hsd          0   1
      ands         $7    
      hsr          0   $7    
      xors         $10
      xors         $9
      xors         $8
      hsr          0   $7
      adds         $6
      hsd          1
      lss          $6

      //p2
      hsr          0   $10
      adds         $6
      hsd          1
      lss          $6

      //p1
      //p4
      hsr          0   $9
      adds         $6
      hsd          1
      lss          $6

      hsr          0    $10
      xors         $8
      hsr          0    $9
      xors         $8
      hsr          0   $8
      adds         $6

      hsr          1   $15
      hsd          0   29
      adds         $1
      hsr          0   $1
      savei        $6

      hds          0   1
      adds         $15
      jump LOOP          
DONE
```
### Program 2
```C
unsigned short mem[60];

void recoverMessage(unsigned int *originalMessage) {
  int i;
  unsigned int data, errorFlag, errorPos;

  for (i = 0; i < MAX_DATA; i++) {
    data = mem[30 + i];
    errorFlag = data >> 14; //isolating the ywo significant bit 
    errorPos = data & 0x3FFF;//set all bits of the result to 0 except the 14 bit

    if (errorFlag == 0x0000) { // no errors
      originalMessage[i] = errorPos;
    } else if (errorFlag == 0x4000) { // single bit error(14 bits)
      originalMessage[i] = errorPos ^ errorFlag;
    } else { // double bit error
      originalMessage[i] = ERROR_STATE;
    }
  }
}
```
```asm
      ; r[3] = i = 0
      hsd 3 0 
      ;r[4] = data = 0
      hsd 4 0
      ;r[5] = errorFlag = 0
      hsd 5 0
      ; r[6] = errorPos = 0
      hsd 6 0
      ;
      DEFINE MAX_DATA=15

LOOP  
      ; r[0] = r[3] = i
      hsr 0 $3
      ; [TODO] r[1] = MAX_DATA-1
      hsd 1 MAX_DATA-1
      ; if r[0] > r[1], DONE
      bg DONE
      ; r[1] = 30
      hsd 1 30
      ; r[1] = r[1] + r[0] = 30+i
      adds $1
      ; r[0] = r[1] = 30+i
      hsr 0 $1
      ; r[4] = data = mem[r[0]] = mem[30+i]
      loadi $4
      ; r[0] = 14
      hsd 0 14
      ; r[5] = errorFlag = r[4] = data
      hsr 5 $4
      ; r[5] = errorFlag = r[5] >> r[0] = data >> 14
      rss $5
      ; r[6] = errorPos = r[4] = data
      hsr 6 $4
      ; r[0] = 0x3FFF = 16383
      hsd 0 16383
      ; r[6] = errorPos = r[6] & r[0] = data & 0x3FFF
      ands $6
      ; r[0] = r[5] = errorFlag
      hsr 0 $5
      ; r[1] = 0
      hsd 1 0
      ; if r[0] != r[1], go to SINGLE
      bne SINGLE
      ; r[0] = r[3] = i
      hsr 0 $3
      ; mem[r[0]] mem[i] = r[6] = errorPos
      savei $6
      ; go to ITERATION
      jumpf ITERATION

SINGLE
      ; r[0] = r[5] = errorFlag
      hsr 0 $5
      ; r[1] = 0x4000 = 16384
      hsd 1 16384
      ; if r[0] != r[1], go to DOUBLE
      bne DOUBLE
      ; r[1] = r[6] = errorPos
      hsr 1 $6
      ; r[1] = r[1] ^ r[0] = errorPos ^ errorFlag
      xors $1
      ; r[0] = r[3] = i
      hsr 0 $3
      ; mem[r[0]] = mem[i] = r[1] = errorPos ^ errorFlag
      savei $1
      ; go to ITERATION
      jumpf ITERATION

DOUBLE
      ; r[0] = r[3] = i
      hsr 0 $3
      ; [TODO] mem[r[0]] = mem[i] = ERROR_STATE
      savei $ERROR_STATE 

ITERATION
      ; r[0] = 1
      hsd 0 1
      ; r[3] = r[3] + r[0] = i+1
      adds $3
      ; go to LOOP
      jumpf LOOP

DONE
```

### Program 3
```C
int program3(mem[32][8],pattern[8])
{
    int count = 0,byteCount=0,intersectCount=0;
    int flag=0;
    int i,j,k;

    for (i=0;i<32;i++) {
        for(j=0;j<8-5+1;j++){
            for (k=0;k<5;k++) {
                if(mem[i][j+k] != pattern[k]) 
                    break; 
            }
            if (k == 5){ 
                count++;
                flag=1;
            }
        }
        byteCount+=flag;
        flag=0;
    }
    for (i=0;i<31;i++) {
        for(j=0;j<8-5+1;j++){
            for (k=0;k<5;k++) {
                if((mem[i][4,7]+mem[i+1][0,3])[j+k] != pattern[k]) 
                    break; 
            }
            if (k == 5){ 
                intersectCount++; 
            }
        }
    }
    mem[33]=count;
    mem[34]=byteCount;
    mem[35]=count+intersectCount;
    
}
```
```
// count=$12=0
// byteCount=$13=0
// intersectCount=$14=0
// flag=$15=0

// i=$3=0
// loopi: 
// if i>31:go to loopiend

// j=$4=0
// loopj: 
// if j>3:go to loopjend

// k=$5=0
// loopk:
// if k>4:go to loopkend

// $6 = mem[i]
// $7 = pattern
// $8 = 8b1000_0000 >> j
// $9 = $8 >> k
// $10 = $6 & $9
// $11 = $7 & $8
// $0 = $10
// $1 = $11
// if $0!=$1: go to loopkend
// k+=1
// go to loopk
// loopkend:

// j+=1
// if k!=5: go to loopj
// count+=1
// flag=1
// go to loopj
// loopjend:

// byteCount+=flag;
// flag=0;
// i+=1
// go to loopi
// loopiend:

// mem[35]=count=$11
// mem[34]=byteCount=$12

// first loop ends
////////////////////////////////////////////////////////////////
// second loop begins

// i=$3=0
// loopi: 
// if i>30:go to loopiend

// j=$4=0
// loopj: 
// if j>3:go to loopjend

// k=$5=0
// loopk:
// if k>4:go to loopkend

// $6 = mem[i] << 4
// $7 = mem[i+1] >> 4
// $6 = $6 + $7
// $7 = pattern
// $8 = 8b1000_0000 >> j
// $9 = $8 >> k
// $10 = $6 & $9
// $11 = $7 & $8
// $0 = $10
// $1 = $11
// if $0!=$1: go to loopkend
// k+=1
// go to loopk
// loopkend:

// j+=1
// if k!=5: go to loopj
// intersectCount+=1
// go to loopj
// loopjend:

// i+=1
// go to loopi
// loopiend:

// mem[35]=count+intersectCount
```

```asm
; count=$12=0:
hsd 12 0
; byteCount=$13=0
hsd 13 0
; intersectCount=$14=0
hsd 14 0
; flag=$15=0
hsd 15 0     

; i=$3=0:
hsd 3 0
; loopi:            
Label: loopi
; if i>31:go to end
hsd 1 31
hsr 0 3
bg end

; j=$4=0:
hsd 4 0            
; loopj:            
Label: loopj
; if j>3:go to loopjend
hsd 1 3
hsr 0 4
bg loopjend

; k=$5=0
hsd 5 0
; loopk:
Label: loopk
; if k>4:go to loopkend
hsd 1 4
hsr 0 5
bg loopkend

; $6=mem[i]
hsr 0 3            
loadi 6          
; $7=pattern
hsd 0 32          
loadi 7         
; $8 = 8b1000_0000 >> j
hsd 8 128 ;==8b1000_0000
hsr 0 $4 
rss $8
; $9 = $8 >> k
hsr 9 8
hsr 0 5
rss $9   
; $10 = $6 & $9
hsr 10 6 
hsr 0 9 
ands 10  
; $11 = $7 & $8
hsr 11 8 
hsr 0 7 
ands 11  
; $0=$10
hsr 0 10
; $1=$11
hsr 1 11
; if $0!=$1: go to loopkend
bne loopkend

; k+=1
hsd 0 1           
adds 5
; go to loopk
jumpf loopk
; loopkend:
Label:loopkend

; j+=1
hsd 0 1           
adds 4
; if k!=5: go to loopj
hsd 0 5
hsr 1 5
bne loopj
; count+=1
hsd 0 1           
adds 12
; flag=1
hsd 15 1
; go to loopj
jumpf loopj
; loopjend:
Label:loopjend

; byteCount+=flag;
hsr 0 15
adds 13
; flag=0;
hsd 15 0
; i+=1:
hsd 0 1            
adds 3          
; go to loopi
jumpf loopi
; loopiend:
Label:loopiend

; mem[33]=count
hsd 0 33
savei 12
; mem[34]=byteCount
hsd 0 34
savei 13

; first loop ends
////////////////////////////////////////////////////////////////
; second loop begins

; count=$12=0:
hsd 12 0
; byteCount=$13=0
hsd 13 0
; intersectCount=$14=0
hsd 14 0
; flag=$15=0
hsd 15 0     

; i=$3=0:
hsd 3 0
; loopi:            
Label: loopi
; if i>31:go to end
hsd 1 31
hsr 0 3
bg end

; j=$4=0:
hsd 4 0            
; loopj:            
Label: loopj
; if j>3:go to loopjend
hsd 1 3
hsr 0 4
bg loopjend

; k=$5=0
hsd 5 0
; loopk:
Label: loopk
; if k>4:go to loopkend
hsd 1 4
hsr 0 5
bg loopkend

; $6=mem[i]
hsr 0 3            
loadi 6       
; $7 = mem[i+1]
hsr 1 3
hsd 0 1
adds 1
loadi 7
; $6 = $6 << 4
; $7 = $7 >> 4
hsd 0 4
lss $6
rss $7
; $6 = $6 + $7
hsr 0 7
adds 6   
; $7=pattern
hsd 0 32          
loadi 7         
; $8 = 8b1000_0000 >> j
hsd 8 128 ;==8b1000_0000
hsr 0 $4 
rss $8
; $9 = $8 >> k
hsr 9 8
hsr 0 5
rss $9   
; $10 = $6 & $9
hsr 10 6 
hsr 0 9 
ands 10  
; $11 = $7 & $8
hsr 11 8 
hsr 0 7 
ands 11  
; $0=$10
hsr 0 10
; $1=$11
hsr 1 11
; if $0!=$1: go to loopkend
bne loopkend

; k+=1
hsd 0 1           
adds 5
; go to loopk
jumpf loopk
; loopkend:
Label:loopkend

; j+=1
hsd 0 1           
adds 4
; if k!=5: go to loopj
hsd 0 5
hsr 1 5
bne loopj
; intersectCount+=1
hsd 0 1           
adds 14
; go to loopj
jumpf loopj
; loopjend:
Label:loopjend

; i+=1:
hsd 0 1            
adds 3          
; go to loopi
jumpf loopi
; loopiend:
Label:loopiend

; mem[35]=count+intersectCount
hsr 0 12
adds 14
hsd 0 35 
savei 14
```

```python
def program3():
  def next(location,index): #in-place modifying
    if (index == 7):
      index = 0
      location += 1
    else:
      index += 1
  
  int location = 0
  int index = 0
  int count = 0, byteCount=0, fullCount=0

  pattern = mem[32][3:7]
  byteCountEnabled = True
  while(location !=31 or index !=4):
    if(pattern[0] == mem[location][index]):
      currLoc = location # shallow copy
      currInd = index
      for i in range(1,5):
        if (i == 5):
          fullCount += 1
          if(location == currLoc):
            count+=1
            if(byteCountEnabled):
              byteCount += 1
              byteCountEnabled = False
        next(currLoc,currInd)
        if(pattern[i] != mem[currLoc][currInd]):
          break
    currLoc = location
    next(location,index)
    if(currLoc != location):
      byteCountEnabled = True
  
  mem[33] = count
  mem[34] = byteCount
  mem[35] = fullCount

```