/* 
This is the bare minimum for initializing an AHT20 module and getting a reading each second.

BE AWARE: There are no safety measures or functionality checks implemented; I2c connectivity
checks(acknowledgements and CRC), timeouts, sensor calibration checks, data raedy checks for reducing
waiting time, and better memory handeling for efficiency are some of the improvements that can be made.

Ali.M
*/


#include <Wire.h>


void setup() {

  Wire.begin(); //Activating I2c
  Serial.begin(9600);

  delay(100); //Min 40ms for AHT20's warm-up
    Wire.beginTransmission(0x38); //Starting transmission to AHT20's adrress
    Wire.write(0xba); // Soft restart command
    Wire.endTransmission(); //Releasing the BUS
      
  delay(50); //Min 20ms after soft restart
    Wire.beginTransmission(0x38);
    Wire.write(0xbe); //Initializing command followed by callibration commands
    Wire.write(0x08);
    Wire.write(0x00);
    Wire.endTransmission();

}

unsigned long t=1000;

void loop() {

  if(t<millis()-1000) {

    t=millis();
    
    unsigned long raw[8];
    byte buff[8];

    delay(50); //Min 10ms after initializing
      Wire.beginTransmission(0x38);
      Wire.write(0xAC); //Triggering measurement commands
      Wire.write(0x33);
      Wire.write(0x00);
      Wire.endTransmission();
    

    delay(100); //Min 80ms for measurement proccess(can be less,bit7 of status byte:low=DataReady)
      Wire.requestFrom(0x38, 7);
      if(Wire.available()) {
        Wire.readBytes(buff,7); //Getting 7bytes from AHT20; 0:status(bit3:high=callibrated, bit7:high=busy)
        // 1;2;bit7,6,5,4 of 3:H(RH=H/2^20*100 %) bit3,2,1,0 of 3;4;5:T(C=T/2^20*200-50) 6:CRC 
      }
    
    for(int i=0;i<8;++i) {
      raw[i]=buff[i]; //Converting bytes to unsigned longs for calculations(int isn't enough)
    }

    float RH=(raw[1]*4096+raw[2]*16+raw[3]/16)/10485.76;
    float C=((raw[3]-(raw[3]/16)*16)*65536+raw[4]*256+raw[5])/5242.88-50; 
    // /16*16 gets rid of the last 4 bits and by subtracting the result from the byte you get the last 4

    Serial.print("RH= ");
    Serial.print(RH);
    Serial.println(" %");
    Serial.print("T= ");
    Serial.print(C);
    Serial.println(" C");

  }
}
