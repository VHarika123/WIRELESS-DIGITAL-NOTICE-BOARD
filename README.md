Wireless Digital Notice Board using LPC2148
📌 Project Overview
The Wireless Digital Notice Board replaces traditional notice boards with a dynamic, remotely updatable digital display. It is built around the LPC2148 ARM7 microcontroller and a dot matrix LED display, with messages sent wirelessly via Bluetooth (HC-05).

Messages are authenticated using a passkey-based input format before being displayed. The latest valid message is saved into EEPROM (AT24C256) so it remains available even after reset or power loss.

Block Diagram
<img width="950" height="578" alt="Screenshot 2025-09-14 193725" src="https://github.com/user-attachments/assets/9974cd9f-6e00-442c-af54-31483baa53ee" />

🚀 Features
Wireless message updates using Bluetooth
Supports scrolling messages across multiple dot matrix displays
Messages stored in EEPROM for persistence
Passkey-based authentication for security
Modular design using shift registers (74HC164) and latches (74HC573)
Developed in Embedded C (Keil) and programmed with Flash Magic
🛠️ Hardware Requirements
Component	Description
LPC2148	ARM7 Microcontroller (controller of system)
8x8 Dot Matrix LED (x4)	Display modules for scrolling text
74HC573 (Latch)	Controls dot matrix row selection
74HC164 (Shift Register)	Serial-to-parallel conversion for columns
AT24C256 (EEPROM)	Stores messages persistently
HC-05 Bluetooth Module	Wireless communication from mobile device
DB-9 Cable / USB-UART	For UART programming/debugging
💻 Software Requirements
Keil uVision (C Compiler) – Embedded C programming
Flash Magic – Programming tool for LPC2148
Bluetooth Terminal (Android app) – Sending messages wirelessly
⚙️ Implementation Workflow
1.Initialize Project – Create project in Keil, configure startup and libraries.

2.Basic Test – Display a single character on one dot matrix.

3.Multi-Display Handling – Extend to display 4 characters simultaneously.

4.Scrolling Logic – Implement text scrolling across displays.

void scrollMessage(char *msg) { for(int i=0; i<strlen(msg); i++) { displayChar(msg[i]); delay(); } }

5.EEPROM Integration – Save and retrieve messages persistently.

EEPROM_WriteByte(addr, data); char data = EEPROM_ReadByte(addr);

6.UART Test – Verify communication by sending and receiving test data.

7.Bluetooth Setup – Pair HC-05 with phone, send message via Bluetooth Terminal.

8.Passkey Security – Only accept messages in valid format (see below).

9.Main Loop – Continuously read message from EEPROM and scroll it.

If new message received → authenticate → update EEPROM → display.
🔒 Security Mechanism
To prevent unauthorized access, every message must follow this input format:

@$

@ → Start of message

→ 3-digit authentication code (default: 153)

→ Text to display

$ → End of message

Example
Input via Bluetooth Parsed Output Displayed on LED @153hello$ Passkey = 153, Message = "hello" hello @153Meeting at 10AM$ Passkey = 153, Message = "Meeting at 10AM" Meeting at 10AM (scrolling) @999Hi$ Wrong passkey ❌ Ignored

🔑 Implementation Snippet
void processInput(char *input) { if(input[0] == '@' && input[strlen(input)-1] == '$') { char passkey[4]; strncpy(passkey, input+1, 3); // extract passkey passkey[3] = '\0';

    if(strcmp(passkey, "153") == 0) {  // check passkey
        char message[100];
        strncpy(message, input+4, strlen(input)-5);
        message[strlen(input)-5] = '\0';
        
        saveMessageToEEPROM(message);
        scrollMessage(message);
    } else {
        // Invalid passkey → ignore
    }
}
}

🖥️ Sample Input / Output
Input	Output
@153Welcome$	Welcome
@153Project Completed Successfully$	Project Completed Successfully (scrolling)
No message stored	Waiting for message
🚀 Future Enhancements
Upgrade Bluetooth → Wi-Fi/IoT for remote cloud updates

Control multiple boards in sync

Replace LED matrix with LCD/TFT display

Web-based admin dashboard

Add SMS/voice command support

📢 Conclusion
This project successfully demonstrates a secure, wireless, and persistent digital notice board using LPC2148 + Bluetooth + EEPROM. It combines embedded C programming, wireless communication, and hardware interfacing to create a real-world IoT-ready system.
