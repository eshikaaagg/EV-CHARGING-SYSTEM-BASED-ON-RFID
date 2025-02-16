#include <SPI.h>
#include <MFRC522.h>

#define SS_PIN 21
#define RST_PIN 22
#define OUTPUT_PIN 2

MFRC522 rfid(SS_PIN, RST_PIN);

const String registeredUIDs[] = {"EBA4EA0A", "86EA3EF8"};
const int numRegisteredUIDs = sizeof(registeredUIDs) / sizeof(registeredUIDs[0]);

void setup() {
    Serial.begin(115200);
    delay(1000);

    SPI.begin();
    rfid.PCD_Init();

    byte version = rfid.PCD_ReadRegister(rfid.VersionReg);
    if (version == 0x00 || version == 0xFF) {
        Serial.println("ERROR: No RFID module detected. Check wiring!");
        while (true);
    }

    pinMode(OUTPUT_PIN, OUTPUT);
    digitalWrite(OUTPUT_PIN, LOW);
}

void loop() {
    if (rfid.PICC_IsNewCardPresent() && rfid.PICC_ReadCardSerial()) {
        String detectedUID = "";
        for (byte i = 0; i < rfid.uid.size; i++) {
            if (rfid.uid.uidByte[i] < 0x10) detectedUID += "0";
            detectedUID += String(rfid.uid.uidByte[i], HEX);
        }
        detectedUID.toUpperCase(); 

        bool isRegistered = false;
        for (int i = 0; i < numRegisteredUIDs; i++) {
            if (detectedUID == registeredUIDs[i]) {
                isRegistered = true;
                break;
            }
        }

        if (isRegistered) {
            Serial.println("Registered Vehicle ✅");
        } else {
            Serial.println("Not Registered 🚫");
        }

        digitalWrite(OUTPUT_PIN, HIGH);
        delay(2000);
        digitalWrite(OUTPUT_PIN, LOW);

        rfid.PICC_HaltA();
        rfid.PCD_StopCrypto1();
    }
}
