markdown
# Writeup: hard_crackme (Reverse Engineering)

## 1. Challenge Overview
* Category: Reverse Engineering
* Target File: hard_crackme (64-bit LSB PIE ELF executable, dynamically linked, stripped)

---

## 2. Initial Reconnaissance
First, check the binary properties using the file command:

```bash
file hard_crackme

```

* Output:

```text
hard_crackme: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=..., for GNU/Linux 3.2.0, stripped

```

* Key Observations: The binary is stripped, and built as PIE (Position Independent Executable).

---

## 3. Ghidra Decompilation

Opening the binary in Ghidra and analyzing the main logic reveals the following C code:

```c
__isoc23_scanf(&DAT_00102047, local_56 + 0xe);

local_56[0]  = 0x26;
local_56[1]  = 0x20;
local_56[2]  = 0x25;
local_56[3]  = 0x30;
local_56[4]  = 0x27;
local_56[5]  = 0x26;
local_56[6]  = 0x30;
local_56[7]  = 0x36;
local_56[8]  = 0x27;
local_56[9]  = 0x30;
local_56[10] = 0x21;
local_56[11] = 0x64; 
local_56[12] = 0x67;
local_56[13] = 0x66;

local_10 = 0xe; 

for (local_c = 0; local_c < local_10; local_c = local_c + 1) {
    local_88[local_c] = local_56[local_c] ^ 0x55;
}
local_88[local_10] = 0;

iVar1 = strcmp((char *)(local_56 + 0xe), (char *)local_88);

```

### Logic Analysis:

1. The binary takes user input via scanf.
2. It initializes a 14-byte encrypted array.
3. It applies a XOR operation to each byte using the key 0x55.
4. It compares the result with user input using strcmp.

---

## 4. Solver Script

To extract the flag, use the following Python script:

```python
#!/usr/bin/env python3

encrypted_bytes = [
    0x26, 0x20, 0x25, 0x30, 0x27, 0x26, 0x30, 0x36, 0x27, 0x30, 0x21, 0x64, 0x67, 0x66
]

key = 0x55
flag = "".join([chr(b ^ key) for b in encrypted_bytes])

print(f"Decrypted Flag: {flag}")

```

Run the script in the terminal:

```bash
python3 solve.py

```

---

## 5. Conclusion

* Takeaway: Even if an ELF binary is stripped, identifying static data structures and simple XOR operations in Ghidra enables efficient static analysis.

```

```
