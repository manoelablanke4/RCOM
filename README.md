First project - Computer Network
=====================================

## Project Description

This project implements a **reliable point-to-point communication system over a serial link (RS-232)** using the C programming language. It was developed in the context of the **RCOM (Computer Networks)** course to explore how reliable data transfer can be achieved at the **Data Link Layer** over an unreliable physical medium.

The system configures and manages a serial port using **non-canonical mode**, handling low-level details such as baud rate configuration, timeouts, and signal control. On top of the physical connection, a custom **data link layer protocol** is implemented to ensure correctness and reliability.

Key mechanisms include **frame delimitation**, **error detection**, and a **Stop-and-Wait Automatic Repeat reQuest (ARQ)** strategy. Data frames are acknowledged by the receiver, and lost or corrupted frames are retransmitted after a timeout. This allows the system to reliably transmit data even in the presence of communication errors.

The project supports the **transfer of files between two endpoints**, where one node acts as a sender and the other as a receiver. The receiver reconstructs the transmitted file using the received frames, ensuring data integrity and correct ordering.

Through this project, core networking concepts such as **layered protocol design**, **flow control**, **error handling**, and **serial communication** are explored in a practical and hands-on manner.

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
