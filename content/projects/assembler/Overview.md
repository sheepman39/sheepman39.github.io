---
title: "Overview"
date: 2024-11-30T11:02:55-05:00
draft: true
weight: 1
author: "John Salata"
description: "A brief discription of the assembler project"
toc: true
---

# Overview
In the Fall 2024 semester, I took a Computer Structures class that covered a lot of the lower-level features of computers that we often take for granted.  Some of the topics that we covered include, but not limited to, assembly, linkers, loaders, and compiler design.  The big final project that we spent the entire semester on was to build an assembler for the SIC/XE instruction set. We could use any language or tool that we wanted to as long as all of the work was original.  For me, this was the first project I've worked on with no guardrails or language requirements.  This openness made it an interesting challenge and opportunity to work with new tools.

# Background Knowledge
## What is SIC/XE?
Each CPU has its own architecture that dictates exactly what instructions it can takes.  The two most common architectures are x86 and ARM.  SIC is a hypothetical instruction set that was designed to be easy to learn and demonstrate many concepts that are common in many real-world sets. SIC/XE, or SIC Extended, was developed as a way to expand the SIC instruction set and provide more hypothetical hardware to work with.  This [GeeksForGeeks](https://www.geeksforgeeks.org/sic-xe-architecture/) article does a good job of going into the more technical details about the instruction set and its capabilities.

## What is an assembler?
An assembler takes the instructions written in assembly and assembles them into a hexadecimal format.  Assembly is like an abstraction for binary code.  We can directly translate a line into binary code.  We usually represent binary code in hexadecimal format to make it easier to understand.

From each line's hexadecimal representation, we generate object files that include the name of the program, how long the program is, and each of the assembled instructions in various lines.  More complex programs will have more complex object files, but essentially they store all of the needed information to be loaded into memory and executed by the processor.

As an example, take this simple program written in the SIC/XE format.  Note that anything after the '.' is considered to be a comment.
```
	LDS	#3	 .Initialize Register S to 3
	LDT	#300 .Initialize Register T to 300
	LDX	#0	 .Initialize Index Register to 0
ADDLP	LDA	ALPHA,X	.Load Word from ALPHA into Register A
	ADD	BETA,X	.Add Word From BETA
	STA	GAMMA,X	.Store the Result in a work in GAMMA
	ADDR	S,X	.ADD 3 to INDEX value
	COMPR	X,T	.Compare new INDEX value to 300
	JLT	ADDLP	.Loop if INDEX value is less than 300
ALPHA	RESW	100
BETA	RESW	100
GAMMA	RESW	100
```

When assembled, it looks like this:
```
HOUTPUT00000000039d
T000000196d000375012c05000003a00d1ba1360fa25f9041A0153b2ff0
E000000
```

More specific details will come in later posts to explain all of the nuances in the input and output.  For now, it is mean to show some background info and our general goal with this project.

# Goals and Design Decisions
Our primary goals for this project are to build a fully functioning SIC/XE Assembler that can handle
- literals
- expressions
- macros
- functions
- program blocks
- control sections

Additionally, I wanted to set some additional goals to ensure that the project would be easy to work on as more features were added.  This included the SOLID principles and other concepts like:
- Single responsibility where each method has one purpose
- Open-close principle where base classes can be extended but not modified
- Liskov-substitution principle where child classes can be substituted for their parents
- Interface principle where many interfaces are better than one general purpose
- Dependency inversion principle where we depend on abstractions rather than concretions
- Minimal Exception safety where we do not guarantee valid results upon an error
- Clear Error Messages where when something does go wrong, a clear message is given to the user

The one risky philosophy I want to explain with this project is the choice to use minimal exception safety as the base for how we handle all exceptions.  The reason why we do not guarantee safety or validation is that this is an assembler where everything has to be just right or else nothing will be right at all.  I would rather have exceptions flow up and reported instead of having incorrect output.  This ensures that user's know immediately when their input does not work and-with clear error messages-can pinpoint where it failed.    