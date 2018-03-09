# RFID-Tag-Reader-using-Arduino-Uno


# Components:
1. Arduino UNO
2. RC522 module
3. Small Breadboard and Wires

# Connection
1. The GND of the UNO is connected to the negative (-) pin of the buzzer, GND pin of the LED stick and GND pin of the RFID Module

2. The 3.3V pin of the UNO is connected to the VCC pin of the LED stick and the 3.3V pin of the RFID Module

3. Pin 9-10-11-12-13 of the UNO are connected to pin RST-SDA-MOSI-MISO-SCK of the RFID Module


![arduino_uno_rfid_rc522](https://user-images.githubusercontent.com/18008644/37213103-41756366-23db-11e8-8786-af0675ff65e8.jpg)

#Library

Import .ZIP Library from following links-
# https://github.com/miguelbalboa/rfid
# https://github.com/PaulStoffregen/SPI

# Code

/*
 * -----------------------------------------------------------------------------------------
 *             MFRC522      Arduino       Arduino   Arduino    Arduino          Arduino
 *             Reader/PCD   Uno           Mega      Nano v3    Leonardo/Micro   Pro Micro
 * Signal      Pin          Pin           Pin       Pin        Pin              Pin
 * -----------------------------------------------------------------------------------------
 * RST/Reset   RST          9             5         D9         RESET/ICSP-5     RST
 * SPI SS      SDA(SS)      10            53        D10        10               10
 * SPI MOSI    MOSI         11 / ICSP-4   51        D11        ICSP-4           16
 * SPI MISO    MISO         12 / ICSP-1   50        D12        ICSP-1           14
 * SPI SCK     SCK          13 / ICSP-3   52        D13        ICSP-3           15
 * 
 * 8A 69 73 5B  CB 08 04 00  62 63 64 65  66 67 68 69 -  rfid tag serials
 */

#include <SPI.h>
#include <MFRC522.h>

#define RST_PIN     9       // 
#define SS_PIN      10      //

MFRC522 mfrc522(SS_PIN, RST_PIN);   // Create MFRC522 instance

void setup() {
    Serial.begin(9600);     // Initialize serial communications with the PC
    while (!Serial);        // Do nothing if no serial port is opened (added for Arduinos based on ATMEGA32U4)
    SPI.begin();            // Init SPI bus
    mfrc522.PCD_Init();     // Init MFRC522
    ShowReaderDetails();    // Show details of PCD - MFRC522 Card Reader details
    Serial.println(F("Scan PICC to see UID, type, and data blocks..."));
}

void loop() {
    // Look for new cards
    if ( ! mfrc522.PICC_IsNewCardPresent()) {
        return;
    }

    // Select one of the cards
    if ( ! mfrc522.PICC_ReadCardSerial()) {
        return;
    }

    // Dump debug info about the card; PICC_HaltA() is automatically called
    mfrc522.PICC_DumpToSerial(&(mfrc522.uid));
}

void ShowReaderDetails() {
    // Get the MFRC522 software version
    byte v = mfrc522.PCD_ReadRegister(mfrc522.VersionReg);
    Serial.print(F("MFRC522 Software Version: 0x"));
    Serial.print(v, HEX);
    if (v == 0x91)
        Serial.print(F(" = v1.0"));
    else if (v == 0x92)
        Serial.print(F(" = v2.0"));
    else
        Serial.print(F(" (unknown)"));
    Serial.println("");
    // When 0x00 or 0xFF is returned, communication probably failed
    if ((v == 0x00) || (v == 0xFF)) {
        Serial.println(F("WARNING: Communication failure, is the MFRC522 properly connected?"));
    }
}


