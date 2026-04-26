# LAB 5 - APK Analysis: UnCrackable Level 2

## Overview

This repository contains the static analysis of the OWASP UnCrackable Level 2 Android application. The challenge involves reverse engineering an APK that hides its validation logic inside a native library (libfoo.so), requiring analysis beyond standard Java decompilation.

## Objective

Find the hidden secret string that validates successfully in the application by:
- Decompiling Java code with JADX
- Extracting and analyzing the native library with Ghidra
- Decoding the obfuscated secret

## Tools Used

| Tool | Purpose |
|------|---------|
| JADX GUI | Java decompilation |
| Ghidra | Native library analysis |
| Python | Hexadecimal decoding and string manipulation |
| adb | APK installation and interaction |

## Result

**Secret Found:** `Thanks for all the fish`

## Findings Summary

### Critical Vulnerabilities Discovered:

**1. Secret Stored in Native Library (Critical)**
- Secret hidden as reversed hexadecimal ASCII in libfoo.so
- Extracted via strncmp analysis in Ghidra

**2. Weak Anti-Debugging Protection (Critical)**
- fork/ptrace based protection in native code
- Easily bypassed with Frida or APK patching

**3. Insufficient Root Detection (Medium)**
- Client-side root checks for su binaries and system properties
- Bypassable with Magisk Hide

## Analysis Process

### Step 1: Java Decompilation
jadx-gui UnCrackable-Level2.apk

- Located MainActivity to CodeCheck class
- Found native method: CodeCheck.bar()
- Identified library loading: System.loadLibrary("foo")

### Step 2: Native Library Extraction
unzip UnCrackable-Level2.apk -d extracted/
ls -R extracted/lib/ 

Found: lib/x86/libfoo.so

### Step 3: Ghidra Analysis
- Imported libfoo.so into Ghidra
- Found JNI function: Java_sg_vantagepoint_uncrackable2_CodeCheck_bar
- Located strncmp call comparing user input to secret

### Step 4: Secret Decoding
```python
hex_data = "6873696620656874206c6c6120726f6620736b6e616854"
ascii_result = bytes.fromhex(hex_data).decode("ascii")
secret = ascii_result[::-1]
print(secret)
```

Result: Thanks for all the fish


EL YAMANI OMAYMA
