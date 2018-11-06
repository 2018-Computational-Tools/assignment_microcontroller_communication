# Microcontrollers Communication

## Exercise #7: Serial communication

Start by creating a new project (e.g. clone this repository, open it in the Particle ID, and use the **Start new project** button to turn it into a project).

### Setup the circuit

Connect the RS232 circuit to your microcontroller VIN, GND, TX, RX. Connect the two DB9 plugs with a DB9 cable.

### Add the code

- modify your program as follows:
  - **outside** of your setup and loop functions:
    ```C++
    // combined message
    String temp_message = "";

    // tx is transmitter, rx is receiver
    void mirror_serial(Stream &tx, Stream &rx) {
      while (tx.available()) {
        byte c = tx.read();

        if (c == 0) {
          // do nothing when 0 character encountered
        } else if (c >= 32 && c <= 126) {
          // regular ASCII range
          temp_message += (char) c;
          tx.print((char) c); // bounce back to transmitter
          rx.print((char) c);
        } else  if (c == 13) {
          // return character received
          tx.println(); // bounce back to transmitter
          rx.println();
          rx.print("Whole line: ");
          rx.println(temp_message);
          temp_message = "";
        } else {
          // unknown character (127=backspace, 27=esc, etc.)
          rx.println();
          rx.print("Unknown int: ");
          rx.println(c);
        }
      }
    }
    ```
  - in the **setup** function:
    ```C++
    Serial.begin(9600);
    Serial1.begin(19200, SERIAL_8N1);
    Serial.println("Started up");
    ```
  - in the **loop** function:
    ```C++
    mirror_serial(Serial, Serial1);
    mirror_serial(Serial1, Serial);
    ```
- compile & flash the new program
- start your serial monitor with the **Show Serial Monitor** button in the Particle IDE and hit **Connect**
- start typing in the serial monitor to send messages
