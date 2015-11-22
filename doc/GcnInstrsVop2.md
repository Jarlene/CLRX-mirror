## GCN ISA VOP2/VOP3 instructions

VOP2 instructions can be encoded in the VOP2 encoding and the VOP3a/VOP3b encoding.
List of fields for VOP2 encoding:

Bits  | Name     | Description
------|----------|------------------------------
0-8   | SRC0     | First (scalar or vector) source operand
9-16  | VSRC1    | Second vector source operand
17-24 | VDST     | Destination vector operand
25-30 | OPCODE   | Operation code
31    | ENCODING | Encoding type. Must be 0

Syntax: INSTRUCTION VDST, SRC0, VSRC1

List of fields for VOP3A/VOP3B encoding (GCN 1.0/1.1):

Bits  | Name     | Description
------|----------|------------------------------
0-7   | VDST     | Vector destination operand
8-10  | ABS      | Absolute modifiers for source operands (VOP3A)
8-14  | SDST     | Scalar destination operand (VOP3B)
11    | CLAMP    | CLAMP modifier (VOP3A)
15    | CLAMP    | CLAMP modifier (VOP3B)
17-25 | OPCODE   | Operation code
26-31 | ENCODING | Encoding type. Must be 0b110100
32-40 | SRC0     | First (scalar or vector) source operand
41-49 | SRC1     | Second (scalar or vector) source operand
50-58 | SRC2     | Third (scalar or vector) source operand
59-60 | OMOD     | OMOD modifier. Multiplication modifier
61-63 | NEG      | Negation modifier for source operands

List of fields for VOP3A encoding (GCN 1.2):

Bits  | Name     | Description
------|----------|------------------------------
0-7   | VDST     | Destination vector operand
8-10  | ABS      | Absolute modifiers for source operands (VOP3A)
8-14  | SDST     | Scalar destination operand (VOP3B)
15    | CLAMP    | CLAMP modifier
16-25 | OPCODE   | Operation code
26-31 | ENCODING | Encoding type. Must be 0b110100
32-40 | SRC0     | First (scalar or vector) source operand
41-49 | SRC1     | Second (scalar or vector) source operand
50-58 | SRC2     | Third (scalar or vector) source operand
59-60 | OMOD     | OMOD modifier. Multiplication modifier
61-63 | NEG      | Negation modifier for source operands

Syntax: INSTRUCTION VDST, SRC0, SRC1 [MODIFIERS]

Modifiers:

* CLAMP - clamps destination floating point value in range 0.0-1.0
* MUL:2, MUL:4, DIV:2 - OMOD modifiers. Multiply destination floating point value by
2.0, 4.0 or 0.5 respectively
* -SRC - negate floating point value from source operand
* ABS(SRC) - apply absolute value to source operand

Negation and absolute value can be combined: `-ABS(V0)`. Modifiers CLAMP and
OMOD (MUL:2, MUL:4 and DIV:2) can be given in random order.

Limitations for operands:

* only one SGPR can be read by instruction. Multiple occurrences of this same
SGPR is allowed
* only one literal constant can be used, and only when a SGPR or M0 is not used in
source operands
* only SRC0 can holds LDS_DIRECT

VOP2 opcodes (0-63) are reflected in VOP3 in range: 256-319.
List of the instructions by opcode:

 Opcode     | Mnemonic (GCN1.0/1.1) | Mnemonic (GCN 1.2)
------------|----------------------|------------------------
 0 (0x0)    | V_CNDMASK_B32        | V_CNDMASK_B32
 1 (0x1)    | V_READLANE_B32       | V_ADD_F32
 2 (0x2)    | V_WRITELANE_B32      | V_SUB_F32
 3 (0x3)    | V_ADD_F32            | V_SUBREV_F32
 4 (0x4)    | V_SUB_F32            | V_MUL_LEGACY_F32
 5 (0x5)    | V_SUBREV_F32         | V_MUL_F32
 6 (0x6)    | V_MAC_LEGACY_F32     | V_MUL_I32_I24
 7 (0x7)    | V_MUL_LEGACY_F32     | V_MUL_HI_I32_I24
 8 (0x8)    | V_MUL_F32            | V_MUL_U32_U24
 9 (0x9)    | V_MUL_I32_I24        | V_MUL_HI_U32_U24
 10 (0xa)   | V_MUL_HI_I32_I24     | V_MIN_F32
 11 (0xb)   | V_MUL_U32_U24        | V_MAX_F32
 12 (0xc)   | V_MUL_HI_U32_U24     | V_MIN_I32
 13 (0xd)   | V_MIN_LEGACY_F32     | V_MAX_I32
 14 (0xe)   | V_MAX_LEGACY_F32     | V_MIN_U32
 15 (0xf)   | V_MIN_F32            | V_MAX_U32
 16 (0x10)  | V_MAX_F32            | V_LSHRREV_B32
 17 (0x11)  | V_MIN_I32            | V_ASHRREV_I32
 18 (0x12)  | V_MAX_I32            | V_LSHLREV_B32
 19 (0x13)  | V_MIN_U32            | V_AND_B32
 20 (0x14)  | V_MAX_U32            | V_OR_B32
 21 (0x15)  | V_LSHR_B32           | V_XOR_B32
 22 (0x16)  | V_LSHRREV_B32        | V_MAC_F32
 23 (0x17)  | V_ASHR_I32           | V_MADMK_F32
 24 (0x18)  | V_ASHRREV_I32        | V_MADAK_F32
 25 (0x19)  | V_LSHL_B32           | V_ADD_U32
 26 (0x1a)  | V_LSHLREV_B32        | V_SUB_U32
 27 (0x1b)  | V_AND_B32            | V_SUBREV_U32
 28 (0x1c)  | V_OR_B32             | V_ADDC_U32
 29 (0x1d)  | V_XOR_B32            | V_SUBB_U32
 30 (0x1e)  | V_BFM_B32            | V_SUBBREV_U32
 31 (0x1f)  | V_MAC_F32            | V_ADD_F16
 32 (0x20)  | V_MADMK_F32          | V_SUB_F16
 33 (0x21)  | V_MADAK_F32          | V_SUBREV_F16
 34 (0x22)  | V_BCNT_U32_B32       | V_MUL_F16
 35 (0x23)  | V_MBCNT_LO_U32_B32   | V_MAC_F16
 36 (0x24)  | V_MBCNT_HI_U32_B32   | V_MADMK_F16
 37 (0x25)  | V_ADD_I32            | V_MADAK_F16
 38 (0x26)  | V_SUB_I32            | V_ADD_U16
 39 (0x27)  | V_SUBREV_I32         | V_SUB_U16
 40 (0x28)  | V_ADDC_U32           | V_SUBREV_U16
 41 (0x29)  | V_SUBB_U32           | V_MUL_LO_U16
 42 (0x2a)  | V_SUBBREV_U32        | V_LSHLREV_B16
 43 (0x2b)  | V_LDEXP_F32          | V_LSHRREV_B16
 44 (0x2c)  | V_CVT_PKACCUM_U8_F32 | V_ASHRREV_I16
 45 (0x2d)  | V_CVT_PKNORM_I16_F32 | V_MAX_F16
 46 (0x2e)  | V_CVT_PKNORM_U16_F32 | V_MIN_F16
 47 (0x2f)  | V_CVT_PKRTZ_F16_F32  | V_MAX_U16
 48 (0x30)  | V_CVT_PK_U16_U32     | V_MAX_I16
 49 (0x31)  | V_CVT_PK_I16_I32     | V_MIN_U16
 50 (0x32)  | --                   | V_MIN_I16
 51 (0x33)  | --                   | V_LDEXP_F16

### Instruction set

Alphabetically sorted instruction list:

#### V_ADD_F32

Opcode VOP2: 3 (0x3) for GCN 1.0/1.1; 1 (0x1) for GCN 1.2  
Opcode VOP3a: 259 (0x103) for GCN 1.0/1.1; 257 (0x101) for GCN 1.2  
Syntax: V_ADD_F32 VDST, SRC0, SRC1  
Description: Add two FP value from SRC0 and SRC1 and store result to VDST.  
Operation:  
```
VDST = (FLOAT)SRC0 + (FLOAT)SRC1
```

#### V_ADD_I32, V_ADD_U32

Opcode VOP2: 37 (0x25) for GCN 1.0/1.1; 25 (0x19) for GCN 1.2  
Opcode VOP3b: 293 (0x125) for GCN 1.0/1.1; 281 (0x119) for GCN 1.2  
Syntax VOP2 GCN 1.0/1.1: V_ADD_I32 VDST, VCC, SRC0, SRC1  
Syntax VOP3b GCN 1.0/1.1: V_ADD_I32 VDST, SDST(2), SRC0, SRC1  
Syntax VOP2 GCN 1.2: V_ADD_U32 VDST, VCC, SRC0, SRC1  
Syntax VOP3b GCN 1.2: V_ADD_U32 VDST, SDST(2), SRC0, SRC1  
Description: Add SRC0 to SRC1 and store result to VDST and store carry flag to
SDST bit with number that equal to lane id. SDST is 64-bit.  
Operation:  
```
UINT64 temp = (UINT64)SRC0 + (UINT64)SRC1
VDST = temp
UINT64 mask = (1ULL<<LANEID)
SDST = (SDST&~mask) | ((temp >> 32) ? MASK : 0)
```

#### V_AND_B32

Opcode: VOP2: 27 (0x1b) for GCN 1.0/1.1; 19 (0x13) for GCN 1.2  
Opcode: VOP3a: 283 (0x11b) for GCN 1.0/1.1; 275 (0x113) for GCN 1.2  
Syntax: V_AND_B32 VDST, SRC0, SRC1  
Description: Do bitwise AND on SRC0 and SRC1 and store result to VDST.
CLAMP and OMOD modifier doesn't affect on result.  
Operation:  
```
VDST = SRC0 & SRC1
```

#### V_ASHR_I32

Opcode VOP2: 23 (0x17) for GCN 1.0/1.1  
Opcode VOP3a: 279 (0x117) for GCN 1.0/1.1  
Syntax: V_ASHR_I32 VDST, SRC0, SRC1  
Description: Arithmetic shift right SRC0 by (SRC1&31) bits and store result into VDST.  
Operation:  
```
VDST = (INT32)SRC0 >> (SRC1&31)
```

#### V_ASHRREV_I32

Opcode VOP2: 24 (0x18) for GCN 1.0/1.1; 16 (0x11) for GCN 1.2  
Opcode VOP3a: 280 (0x118) for GCN 1.0/1.1; 272 (0x111) for GCN 1.2  
Syntax: V_ASHRREV_I32 VDST, SRC0, SRC1  
Description: Arithmetic shift right SRC1 by (SRC0&31) bits and store result into VDST.  
Operation:  
```
VDST = (INT32)SRC1 >> (SRC0&31)
```

#### V_BCNT_U32_B32

Opcode VOP2: 34 (0x22) for GCN 1.0/1.1  
Opcode VOP3a: 290 (0x122) for GCN 1.0/1.1  
Syntax: V_BCNT_U32_B32 VDST, SRC0, SRC1  
Description: Count bits in SRC0, adds SSRC1, and store result to VDST.  
Operation:  
```
VDST = SRC1
for (UINT8 i = 0; i < 32; i++)
    VDST += ((1U<<i) & SRC0) != 0
```

#### V_BFM_B32

Opcode VOP2: 30 (0x1e) for GCN 1.0/1.1  
Opcode VOP3a: 286 (0x11e) for GCN 1.0/1.1  
Syntax: V_BFM_B32 VDST, SRC0, SRC1  
Description: Make 32-bit bitmask from (SRC1 & 31) bit that have length (SRC0 & 31) and
store it to VDST.  
Operation:  
```
VDST = ((1U << (SRC0&31))-1) << (SRC1&31)
```

#### V_CNDMASK_B32

Opcode VOP2: 0 (0x0) for GCN 1.0/1.1; 1 (0x0) for GCN 1.2  
Opcode VOP3a: 259 (0x100) for GCN 1.0/1.1; 256 (0x100) for GCN 1.2  
Syntax VOP2: V_CNDMASK_B32 VDST, SRC0, SRC1, VCC  
Syntax VOP3a: V_CNDMASK_B32 VDST, SRC0, SRC1, SSRC2(2)  
Description: If bit for current lane of VCC or SDST is set then store SRC1 to VDST,
otherwise store SRC0 to VDST. CLAMP and OMOD modifier doesn't affect on result.  
Operation:  
```
VDST = SSRC2&(1ULL<<LANEID) ? SRC1 : SRC0
```

#### V_LSHL_B32

Opcode VOP2: 25 (0x19) for GCN 1.0/1.1  
Opcode VOP3a: 281 (0x119) for GCN 1.0/1.1  
Syntax: V_LSHL_B32 VDST, SRC0, SRC1  
Description: Shift left SRC0 by (SRC1&31) bits and store result into VDST.  
Operation:  
```
VDST = SRC0 << (SRC1&31)
```

#### V_LSHLREV_B32

Opcode VOP2: 26 (0x1a) for GCN 1.0/1.1; 18 (0x12) for GCN 1.2  
Opcode VOP3a: 282 (0x11a) for GCN 1.0/1.1; 274 (0x112) for GCN 1.2  
Syntax: V_LSHLREV_B32 VDST, SRC0, SRC1  
Description: Shift left SRC1 by (SRC0&31) bits and store result into VDST.  
Operation:  
```
VDST = SRC1 << (SRC0&31)
```

#### V_LSHR_B32

Opcode VOP2: 21 (0x15) for GCN 1.0/1.1  
Opcode VOP3a: 277 (0x115) for GCN 1.0/1.1  
Syntax: V_LSHR_B32 VDST, SRC0, SRC1  
Description: Shift right SRC0 by (SRC1&31) bits and store result into VDST.  
Operation:  
```
VDST = SRC0 >> (SRC1&31)
```

#### V_LSHRREV_B32

Opcode VOP2: 22 (0x16) for GCN 1.0/1.1; 16 (0x10) for GCN 1.2  
Opcode VOP3a: 278 (0x116) for GCN 1.0/1.1; 272 (0x110) for GCN 1.2  
Syntax: V_LSHRREV_B32 VDST, SRC0, SRC1  
Description: Shift right SRC1 by (SRC0&31) bits and store result into VDST.  
Operation:  
```
VDST = SRC1 >> (SRC0&31)
```

#### V_MAC_F32

Opcode VOP2: 31 (0x1f) for GCN 1.0/1.1; 22 (0x16) for GCN 1.2  
Opcode VOP3a: 287 (0x11f) for GCN 1.0/1.1; 278 (0x116) for GCN 1.2  
Syntax: V_MAC_F32 VDST, SRC0, SRC1  
Description: Multiply FP value from SRC0 by FP value from SRC1 and add result to VDST.  
Operation:  
```
VDST = (FLOAT)SRC0 * (FLOAT)SRC1 + (FLOAT)VDST
```

#### V_MAC_LEGACY_F32

Opcode VOP2: 6 (0x6) for GCN 1.0/1.1  
Opcode VOP3a: 262 (0x106) for GCN 1.0/1.1  
Syntax: V_MAC_LEGACY_F32 VDST, SRC0, SRC1  
Description: Multiply FP value from SRC0 by FP value from SRC1 and add result to VDST.
If one of value is 0.0 then always do not change VDST (do not apply IEEE rules for 0.0*x).  
Operation:  
```
if ((FLOAT)SRC0!=0.0 && (FLOAT)SRC1!=0.0)
    VDST = (FLOAT)SRC0 * (FLOAT)SRC1 + (FLOAT)VDST
```

#### V_MADMK_F32

Opcode: VOP2: 32 (0x20) for GCN 1.0/1.1; 23 (0x17) for GCN 1.2  
Opcode: VOP3a: 288 (0x120) for GCN 1.0/1.1; 279 (0x117) for GCN 1.2  
Syntax: V_MADMK_F32 VDST, SRC0, FLOATLIT, SRC1  
Description: Multiply FP value from SRC0 with the constant literal FLOATLIT and add
FP value from SRC1; and store result to VDST. Constant literal follows
after instruction word.  
Operation:
```
VDST = (FLOAT)SRC0 * (FLOAT)FLOATLIT + (FLOAT)SRC1
```

#### V_MADAK_F32

Opcode: VOP2: 33 (0x21) for GCN 1.0/1.1; 24 (0x18) for GCN 1.2  
Opcode: VOP3a: 289 (0x121) for GCN 1.0/1.1; 280 (0x118) for GCN 1.2  
Syntax: V_MADAK_F32 VDST, SRC0, SRC1, FLOATLIT  
Description: Multiply FP value from SRC0 with FP value from SRC1 and add
the constant literal FLOATLIT; and store result to VDST. Constant literal follows
after instruction word.  
Operation:
```
VDST = (FLOAT)SRC0 * (FLOAT)SRC1 + (FLOAT)FLOATLIT
```

#### V_MAX_F32

Opcode VOP2: 16 (0x10) for GCN 1.0/1.1; 11 (0xb) for GCN 1.2  
Opcode VOP3a: 272 (0x110) for GCN 1.0/1.1; 267 (0x10b) for GCN 1.2  
Syntax: V_MAX_F32 VDST, SRC0, SRC1  
Description: Choose largest floating point value from SRC0 and SRC1,
and store result to VDST.  
Operation:  
```
VDST = (FLOAT)SRC0>(FLOAT)SRC1 ? (FLOAT)SRC0 : (FLOAT)SRC1
```

#### V_MAX_I32

Opcode VOP2: 18 (0x12) for GCN 1.0/1.1; 11 (0xd) for GCN 1.2  
Opcode VOP3a: 274 (0x112) for GCN 1.0/1.1; 267 (0x10d) for GCN 1.2  
Syntax: V_MAX_I32 VDST, SRC0, SRC1  
Description: Choose largest signed value from SRC0 and SRC1, and store result to VDST.  
Operation:  
```
VDST = (INT32)SRC0>(INT32)SRC1 ? SRC0 : SRC1
```

#### V_MAX_LEGACY_F32

Opcode VOP2: 14 (0xe) for GCN 1.0/1.1  
Opcode VOP3a: 270 (0x10e) for GCN 1.0/1.1  
Syntax: V_MAX_LEGACY_F32 VDST, SRC0, SRC1  
Description: Choose largest floating point value from SRC0 and SRC1,
and store result to VDST. If SSRC1 is NaN value then store NaN value to VDST
(legacy rules for handling NaNs).  
Operation:  
```
if ((FLOAT)SRC1!=NaN)
    VDST = (FLOAT)SRC0>(FLOAT)SRC1 ? (FLOAT)SRC0 : (FLOAT)SRC1
else
    VDST = NaN
```

#### V_MAX_U32

Opcode VOP2: 20 (0x14) for GCN 1.0/1.1; 13 (0xf) for GCN 1.2  
Opcode VOP3a: 276 (0x114) for GCN 1.0/1.1; 269 (0x10f) for GCN 1.2  
Syntax: V_MAX_U32 VDST, SRC0, SRC1  
Description: Choose largest unsigned value from SRC0 and SRC1, and store result to VDST.  
Operation:  
```
VDST = SRC0>SRC1 ? SRC0 : SRC1
```

#### V_MBCNT_HI_U32_B32

Opcode VOP2: 36 (0x24) for GCN 1.0/1.1  
Opcode VOP3a: 292 (0x124) for GCN 1.0/1.1  
Syntax: V_MBCNT_HI_U32_B32 VDST, SRC0, SRC1  
Description: Make mask for all lanes ending at current lane,
get from that mask higher 32-bits, use it to mask SSRC0,
count bits in that value, and store result to VDST.  
Operation:  
```
UINT32 MASK = ((1ULL << (LANEID-32)) - 1ULL) & SRC0
VDST = SRC1
for (UINT8 i = 0; i < 32; i++)
    VDST += ((1U<<i) & MASK) != 0
```

#### V_MBCNT_LO_U32_B32

Opcode VOP2: 35 (0x23) for GCN 1.0/1.1  
Opcode VOP3a: 291 (0x123) for GCN 1.0/1.1  
Syntax: V_MBCNT_LO_U32_B32 VDST, SRC0, SRC1  
Description: Make mask for all lanes ending at current lane,
get from that mask lower 32-bits, use it to mask SSRC0,
count bits in that value, and store result to VDST.  
Operation:  
```
UINT32 MASK = ((1ULL << LANEID) - 1ULL) & SRC0
VDST = SRC1
for (UINT8 i = 0; i < 32; i++)
    VDST += ((1U<<i) & MASK) != 0
```

#### V_MIN_F32

Opcode VOP2: 15 (0xf) for GCN 1.0/1.1; 10 (0xa) for GCN 1.2  
Opcode VOP3a: 271 (0x10f) for GCN 1.0/1.1; 266 (0x10a) for GCN 1.2  
Syntax: V_MIN_F32 VDST, SRC0, SRC1  
Description: Choose smallest floating point value from SRC0 and SRC1,
and store result to VDST.  
Operation:  
```
VDST = (FLOAT)SRC0<(FLOAT)SRC1 ? (FLOAT)SRC0 : (FLOAT)SRC1
```

#### V_MIN_I32

Opcode VOP2: 17 (0x11) for GCN 1.0/1.1; 12 (0xc) for GCN 1.2  
Opcode VOP3a: 273 (0x111) for GCN 1.0/1.1; 268 (0x10c) for GCN 1.2  
Syntax: V_MIN_I32 VDST, SRC0, SRC1  
Description: Choose smallest signed value from SRC0 and SRC1, and store result to VDST.  
Operation:  
```
VDST = (INT32)SRC0<(INT32)SRC1 ? SRC0 : SRC1
```

#### V_MIN_LEGACY_F32

Opcode VOP2: 13 (0xd) for GCN 1.0/1.1  
Opcode VOP3a: 269 (0x10d) for GCN 1.0/1.1  
Syntax: V_MIN_LEGACY_F32 VDST, SRC0, SRC1  
Description: Choose smallest floating point value from SRC0 and SRC1,
and store result to VDST. If SSRC1 is NaN value then store NaN value to VDST
(legacy rules for handling NaNs).  
Operation:  
```
if ((FLOAT)SRC1!=NaN)
    VDST = (FLOAT)SRC0<(FLOAT)SRC1 ? (FLOAT)SRC0 : (FLOAT)SRC1
else
    VDST = NaN
```

#### V_MIN_U32

Opcode VOP2: 19 (0x13) for GCN 1.0/1.1; 14 (0xe) for GCN 1.2  
Opcode VOP3a: 275 (0x113) for GCN 1.0/1.1; 270 (0x10e) for GCN 1.2  
Syntax: V_MIN_U32 VDST, SRC0, SRC1  
Description: Choose smallest unsigned value from SRC0 and SRC1, and store result to VDST.  
Operation:  
```
VDST = SRC0<SRC1 ? SRC0 : SRC1
```

#### V_MUL_LEGACY_F32

Opcode VOP2: 7 (0x7) for GCN 1.0/1.1; 5 (0x4) for GCN 1.2  
Opcode VOP3a: 263 (0x107) for GCN 1.0/1.1; 260 (0x104) for GCN 1.2  
Syntax: V_MUL_LEGACY_F32 VDST, SRC0, SRC1  
Description: Multiply FP value from SRC0 by FP value from SRC1 and store result to VDST.
If one of value is 0.0 then always store 0.0 to VDST (do not apply IEEE rules for 0.0*x).  
Operation:  
```
if ((FLOAT)SRC0!=0.0 && (FLOAT)SRC1!=0.0)
    VDST = (FLOAT)SRC0 * (FLOAT)SRC1
else
    VDST = 0.0
```

#### V_MUL_F32

Opcode VOP2: 8 (0x8) for GCN 1.0/1.1; 5 (0x5) for GCN 1.2  
Opcode VOP3a: 264 (0x108) for GCN 1.0/1.1; 261 (0x105) for GCN 1.2  
Syntax: V_MUL_F32 VDST, SRC0, SRC1  
Description: Multiply FP value from SRC0 by FP value from SRC1 and store result to VDST.  
Operation:  
```
VDST = (FLOAT)SRC0 * (FLOAT)SRC1
```

#### V_MUL_HI_I32_24

Opcode VOP2: 10 (0xa) for GCN 1.0/1.1; 7 (0x7) for GCN 1.2  
Opcode VOP3a: 266 (0x10a) for GCN 1.0/1.1; 263 (0x107) for GCN 1.2  
Syntax: V_MUL_HI_I32_24 VDST, SRC0, SRC1  
Description: Multiply 24-bit signed integer value from SRC0 by 24-bit signed value from SRC1
and store higher 16-bit of the result to VDST with sign extension.
Any modifier doesn't affect on result.  
Operation:  
```
INT32 V0 = (INT32)((SRC0&0x7fffff) | (SSRC0&0x800000 ? 0xff800000 : 0))
INT32 V1 = (INT32)((SRC1&0x7fffff) | (SSRC1&0x800000 ? 0xff800000 : 0))
VDST = ((INT64)V0 * V1)>>32
```

#### V_MUL_HI_U32_U24

Opcode VOP2: 12 (0xc) for GCN 1.0/1.1; 9 (0x9) for GCN 1.2  
Opcode VOP3a: 268 (0x10c) for GCN 1.0/1.1; 265 (0x109) for GCN 1.2  
Syntax: V_MUL_HI_U32_U24 VDST, SRC0, SRC1  
Description: Multiply 24-bit unsigned integer value from SRC0 by 24-bit unsigned value
from SRC1 and store higher 16-bit of the result to VDST.
Any modifier doesn't affect to result.  
Operation:  
```
VDST = ((UINT64)(SRC0&0xffffff) * (UINT32)(SRC1&0xffffff)) >> 32
```

#### V_MUL_I32_I24

Opcode VOP2: 9 (0x9) for GCN 1.0/1.1; 6 (0x6) for GCN 1.2  
Opcode VOP3a: 265 (0x109) for GCN 1.0/1.1; 262 (0x106) for GCN 1.2  
Syntax: V_MUL_I32_I24 VDST, SRC0, SRC1  
Description: Multiply 24-bit signed integer value from SRC0 by 24-bit signed value from SRC1
and store result to VDST. Any modifier doesn't affect to result.  
Operation:  
```
INT32 V0 = (INT32)((SRC0&0x7fffff) | (SSRC0&0x800000 ? 0xff800000 : 0))
INT32 V1 = (INT32)((SRC1&0x7fffff) | (SSRC1&0x800000 ? 0xff800000 : 0))
VDST = V0 * V1
```

#### V_MUL_U32_U24

Opcode VOP2: 11 (0xb) for GCN 1.0/1.1; 8 (0x8) for GCN 1.2  
Opcode VOP3a: 267 (0x10b) for GCN 1.0/1.1; 264 (0x108) for GCN 1.2  
Syntax: V_MUL_U32_U24 VDST, SRC0, SRC1  
Description: Multiply 24-bit unsigned integer value from SRC0 by 24-bit unsigned value
from SRC1 and store result to VDST. Any modifier doesn't affect to result.  
Operation:  
```
VDST = (UINT32)(SRC0&0xffffff) * (UINT32)(SRC1&0xffffff)
```

#### V_OR_B32

Opcode: VOP2: 28 (0x1c) for GCN 1.0/1.1; 20 (0x14) for GCN 1.2  
Opcode: VOP3a: 284 (0x11c) for GCN 1.0/1.1; 276 (0x114) for GCN 1.2  
Syntax: V_OR_B32 VDST, SRC0, SRC1  
Description: Do bitwise OR operation on SRC0 and SRC1 and store result to VDST.
CLAMP and OMOD modifier doesn't affect on result.  
Operation:  
```
VDST = SRC0 | SRC1
```

#### V_READLANE_B32

Opcode VOP2: 1 (0x1) for GCN 1.0/1.1  
Opcode VOP3a: 257 (0x101) for GCN 1.0/1.1  
Syntax: V_READLANE_B32 SDST, VSRC0, SSRC1  
Description: Copy one VSRC0 lane value to one SDST. Lane (thread id) choosen from SSRC1&63.
SSRC1 can be SGPR or M0. Ignores EXEC mask.  
Operation:  
```
SDST = VSRC0[SSRC1 & 63]
```

#### V_SUB_F32

Opcode VOP2: 4 (0x4) for GCN 1.0/1.1; 2 (0x2) for GCN 1.2  
Opcode VOP3a: 260 (0x104) for GCN 1.0/1.1; 258 (0x102) for GCN 1.2  
Syntax: V_SUB_F32 VDST, SRC0, SRC1  
Description: Subtract FP value from SRC0 and FP value from SRC1 and store result to VDST.  
Operation:  
```
VDST = (FLOAT)SRC0 - (FLOAT)SRC1
```

#### V_SUBREV_F32

Opcode VOP2: 5 (0x5) for GCN 1.0/1.1; 2 (0x3) for GCN 1.2  
Opcode VOP3a: 261 (0x105) for GCN 1.0/1.1; 259 (0x103) for GCN 1.2  
Syntax: V_SUBREV_F32 VDST, SRC0, SRC1  
Description: Subtract FP value from SRC1 and FP value from SRC0 and store result to VDST.  
Operation:  
```
VDST = (FLOAT)SRC1 - (FLOAT)SRC0
```

#### V_XOR_B32

Opcode: VOP2: 29 (0x1d) for GCN 1.0/1.1; 21 (0x15) for GCN 1.2  
Opcode: VOP3a: 285 (0x11d) for GCN 1.0/1.1; 277 (0x115) for GCN 1.2  
Syntax: V_OR_B32 VDST, SRC0, SRC1  
Description: Do bitwise XOR operation on SRC0 and SRC1 and store result to VDST.
CLAMP and OMOD modifier doesn't affect on result.  
Operation:  
```
VDST = SRC0 ^ SRC1
```

#### V_WRITELANE_B32

Opcode VOP2: 2 (0x2) for GCN 1.0/1.1  
Opcode VOP3a: 258 (0x102) for GCN 1.0/1.1  
Syntax: V_WRITELANE_B32 VDST, VSRC0, SSRC1  
Description: Copy SGPR to one lane of VDST. Lane choosen (thread id) from SSRC1&63.
SSRC1 can be SGPR or M0. Ignores EXEC mask.  
Operation:  
```
VDST[SSRC1 & 63] = SSRC0
```