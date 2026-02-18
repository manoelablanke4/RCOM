# Reliable Serial Communication System (Data Link Layer Implementation)

## Overview

This project implements a reliable point-to-point communication system over an RS-232 serial link in C, developed within the Computer Networks (RCOM) course.

The objective was to design and implement a custom Data Link Layer protocol capable of ensuring reliable file transmission over an unreliable physical medium.

The implementation operates close to the hardware layer, requiring manual handling of framing, timeouts, retransmissions, and error control mechanisms.

---

## System Architecture

The system follows a layered architecture:

- **Physical Layer** – RS-232 serial communication  
- **Data Link Layer** – Custom reliable protocol  
- **Application Layer** – File segmentation and reconstruction  

The serial port is configured using non-canonical mode, allowing fine-grained control over:

- Baud rate
- Timeout behavior
- Signal handling
- Blocking/non-blocking reads
- Error detection

---

## Protocol Design

The protocol ensures reliability using a Stop-and-Wait Automatic Repeat reQuest (ARQ) strategy, where only one frame is transmitted at a time and must be acknowledged before sending the next.

### Key Mechanisms

- Frame delimitation using flag bytes  
- Byte stuffing to prevent misinterpretation of control flags inside payload  
- Sequence numbering for frame tracking  
- Acknowledgment frames (RR/REJ)  
- Timeout and retransmission logic  
- Error detection using BCC fields  

These mechanisms ensure correctness even under:

- Frame corruption  
- Packet loss  
- Artificial noise injection  
- Cable disconnections  

---

## Protocol Phases

### 1. Connection Establishment

The transmitter initiates communication by sending a SET control frame.  
The receiver responds with UA, confirming synchronization.

### 2. Data Transfer

- The file is segmented into packets.
- Each packet is encapsulated into a frame.
- Frames are transmitted sequentially.
- The receiver validates integrity and responds with:
  - RR (Receive Ready) – Frame accepted  
  - REJ (Reject) – Frame corrupted, retransmission required  

The Stop-and-Wait mechanism guarantees ordered and reliable delivery.

### 3. Connection Termination

After successful transmission:

- Both endpoints exchange DISC frames.
- A final UA confirms proper session termination.

---

## Interface (Application ↔ Link Layer)

The protocol exposes a clean API to the application layer:

- `llopen()` – Establishes the serial connection and performs handshake  
- `llwrite()` – Encapsulates and transmits data frames  
- `llread()` – Receives, validates, and extracts data  
- `llclose()` – Closes the connection and reports transmission statistics  

This separation reinforces proper layered architecture design.

---

## Testing and Fault Tolerance

The project includes a virtual cable simulator that allows:

- Injecting random noise  
- Simulating cable disconnections  
- Testing retransmission robustness  

Validation is performed by:

- Comparing transmitted and received files using `diff`  
- Monitoring retransmission counts  
- Evaluating protocol behavior under stress conditions  

---

## Technical Highlights

- Low-level UNIX serial port programming  
- Use of `termios` for device configuration  
- Signal-based timeout handling (`alarm()`)  
- Finite-state machine implementation for frame parsing  
- Robust error handling and retransmission logic  
- Clean separation between protocol and application layers  

---

## Development Environment

- Language: C  
- Operating System: Linux  
- Communication: RS-232  
- Tools: Makefile, diff, virtual cable simulator  

---

## Learning Outcomes

Through this project, the following concepts were explored in depth:

- Reliable data transfer over unreliable channels  
- Flow control and error recovery mechanisms  
- Framing and byte stuffing techniques  
- Layered protocol architecture  
- Systems programming and low-level device interaction  

Project Structure
-----------------

- bin/: Compiled binaries.
- src/: Source code for the implementation of the link-layer and application layer protocols. Students should edit these files to implement the project.
- include/: Header files of the link-layer and application layer protocols. These files must not be changed.
- cable/: Virtual cable program to help test the serial port. This file must not be changed.
- main.c: Main file. This file must not be changed.
- Makefile: Makefile to build the project and run the application.
- penguin.gif: Example file to be sent through the serial port.

Instructions to Run the Project
-------------------------------

1. Edit the source code in the src/ directory.
2. Compile the application and the virtual cable program using the provided Makefile.
3. Run the virtual cable program (either by running the executable manually or using the Makefile target):
	$ sudo ./bin/cable_app
	$ sudo make run_cable

4. Test the protocol without cable disconnections and noise
	4.1 Run the receiver (either by running the executable manually or using the Makefile target):
		$ ./bin/main /dev/ttyS11 rx penguin-received.gif
		$ make run_tx

	4.2 Run the transmitter (either by running the executable manually or using the Makefile target):
		$ ./bin/main /dev/ttyS10 tx penguin.gif
		$ make run_rx

	4.3 Check if the file received matches the file sent, using the diff Linux command or using the Makefile target:
		$ diff -s penguin.gif penguin-received.gif
		$ make check_files

5. Test the protocol with cable disconnections and noise
	5.1. Run receiver and transmitter again
	5.2. Quickly move to the cable program console and press 0 for unplugging the cable, 2 to add noise, and 1 to normal
	5.3. Check if the file received matches the file sent, even with cable disconnections or with noise
