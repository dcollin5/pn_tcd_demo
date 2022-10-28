#include <math.h>;

 
const int B = 4275;               // B value of the thermistor
const int R0 = 100000;            // R0 = 100k
const int pinTempSensor = A0;     // Grove - Temperature Sensor connect to A0
 
//#if defined(ARDUINO_ARCH_AVR)
//#define debug  Serial
//#elif defianed(ARDUINO_ARCH_SAMD) ||  defined(ARDUINO_ARCH_SAM)
//#define debug  SerialUSB
//#else
//#define debug  Serial
//#endif

#include <LoRaWan.h>

//unsigned char buffer[128] = {0xef, 0xff, 0x55, 3, 4, 5, 6, 7, 8, 9,};
unsigned char data[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 0xA,};
char buffer[256];

void setup(void)
{
//    SerialUSB.begin(115200);
//    while(!SerialUSB);
//    digitalWrite(OUTPUT_PIN, HIGH);
//    digitalWrite(OUTPUT_PIN, HIGH);

    
    lora.init();

    memset(buffer, 0, 256);

    lora.getVersion(buffer, 256, 1);

//    SerialUSB.print(buffer); 

    memset(buffer, 0, 256);
    lora.getId(buffer, 256, 1);
//    SerialUSB.print(buffer);

    //REPLACE THESE VALUES:
//    lora.setId(char *DevAddr, char *DevEUI, char *AppEUI);
//    lora.setKey(char *NwkSKey, char *AppSKey, char *AppKey);00DBBF7C763121C5
    lora.setId("2701141E", "70B3D57ED8000D13", "00DBBF7C763121C5");
    lora.setKey("EC9F5B199255BEE55D03D8A9CA607C2B", "EC9F5B199255BEE55D03D8A9CA607C2B", "EC9F5B199255BEE55D03D8A9CA607C2B");

    lora.setDeciveMode(LWABP);
//    lora.setDeciveMode(LWOTAA);
    lora.setDataRate(DR0, EU868);
 
    lora.setChannel(0, 868.1);
    lora.setChannel(1, 868.3);
    lora.setChannel(2, 868.5);
    lora.setChannel(3, 867.1);
    lora.setChannel(4, 867.3);
    lora.setChannel(5, 867.5);
    lora.setChannel(6, 867.7);
    lora.setReceiceWindowFirst(0, 868.1);
    lora.setReceiceWindowSecond(869.5, DR3);

    lora.setDutyCycle(false);
    lora.setJoinDutyCycle(false);
    lora.setPower(30);
//    while(!lora.setOTAAJoin(JOIN)){
//      SerialUSB.println("Trying to Join network");
//    }
}

//void(* resetFunc) (void) = 0; //declare reset function @ address 0

void loop(void)
{

    bool result = false;
    int a = analogRead(pinTempSensor);
 
    float R = 1023.0/a-1.0;
    R = R0*R;
 
    float temperature = 1.0/(log(R/R0)/B+1/298.15)-273.15; // convert to temperature via datasheet

    char myTemp[8];     // empty string
    dtostrf(temperature, 6, 2, myTemp);
 
    Serial.print("temperature = ");
    Serial.println(temperature);

 //   SerialUSB.println("Send string packet to Pervasive Nation NOC.");
    //This sketch will broadcast a string to Pervasive Nation Network
     
    result = lora.transferPacket(myTemp, 8);
    
    if(result)
    {
        short length;
        short rssi;
        SerialUSB.print("Result is: ");
        memset(buffer, 0, 256);
        length = lora.receivePacket(buffer, 256, &rssi);
        SerialUSB.print("Length2 is: ");
        SerialUSB.println(length);
        if(length > 0)
        {
            SerialUSB.print("Length is: ");
            SerialUSB.println(length);
            SerialUSB.print("RSSI is: ");
            SerialUSB.println(rssi);
            SerialUSB.print("Data is: ");
            for(unsigned char i = 0; i < length; i ++)
            {
                SerialUSB.print("0x");
                SerialUSB.print(buffer[i], HEX);
                SerialUSB.print(" ");
            }
            SerialUSB.println();
        } else {
            Serial.println("Arduino will be reset after 5 seconds");
            delay(5000);
//            resetFunc();  //call reset
       //     resetDevice();

          //SerialUSB.print("Resetting device... ");
          //lora.setDeviceReset();
        }
        SerialUSB.print("Sleep for 60000 ms (1minutes) ");
        delay(30000);
    } 
}
