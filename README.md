# Crash Detection System
# Aim
The objective of this project is to build an Arduino based crash detection system which can be incorporated into vehicles and phones using Accelerometer,GPS and GSM
# Description
The crash detection system is an Arduino based accident alert system using Accelerometer, GPS and GSM. Accelerometer detects the sudden change in motion(possibly due to an accident) and GSM module sends the alert signal to your Mobile Phone with the location of the crash(google map link), whose coordinates are obtained from the GPS module. The alert message also contains the speed of crash. This can also be used as a tracking system with few changes in software and hardware.
# Components required
1. Arduino Uno
2. GSM Module (SIM900A)
3. GPS Module (SIM28ML)
4. Accelerometer (ADXL335)
5. 16x2 LCD
6. Power Supply
7. Connecting Wires
8. 10 K-POT
9. Breadboard or PCB
10. Power supply 12v 1amp
# Working
GPS stands for Global Positioning System and is used to detect the Latitude and Longitude of any location on the Earth, with exact UTC time. GPS module is used to track the location of the crash in our project. This device receives the coordinates from the satellite for each and every second, with time and date.

The SIM900 is a complete Quad-band GSM/GPRS Module which can be embedded easily used by customer or hobbyist. SIM900 GSM Module provides an industry-standard interface. SIM900 delivers GSM/GPRS 850/900/1800/1900MHz performance for voice, SMS, Data with low power consumption.

AT means ATTENTION. This command is used to control GSM module. AT commands used for this project:
```
ATE0 For echo off
AT+CNMI=2,2,0,0,0  <ENTER>       Auto opened message Receiving.  (No need to open message)
ATD<Mobile Number>; <ENTER>      making a call (ATD+919610126059;\r\n)
AT+CMGF=1 <ENTER>                Selecting Text mode
AT+CMGS=”Mobile Number” <ENTER>  Assigning recipient’s mobile number
>>Now we can write our message
>>After writing message
Ctrl+Z  send message command (26 in decimal).
ENTER=0x0d in HEX
```

Accelerometer:
Pin Description of accelerometer:
1. Vcc         5 volt supply should connect at this pin.
2. X-OUT   This pin gives an Analog output in x direction
3. Y-OUT   This pin give an Analog Output in y direction
4. Z-OUT   This pin gives an Analog Output in z direction
5. GND      Ground
6. ST          This pin used for set sensitivity of sensor

# Circuit Diagram

<img width="487" alt="image" src="https://github.com/sasidharirl/bits-bots/assets/119321194/4d34903a-3f29-420b-8c53-cbbdf433d075">

# Code
```
void initModule(String cmd, char *res, int t)
{
  while(1)
  {
    Serial.println(cmd);
    Serial1.println(cmd);
    delay(100);
    while(Serial1.available()>0)
    {
       if(Serial1.find(res))
       {
        Serial.println(res);
        delay(t);
        return;
       }

       else
       {
        Serial.println("Error");
       }
    }
    delay(t);
  }
}
void setup() 
{
  Serial1.begin(9600);
  Serial.begin(9600);
  lcd.begin(16,2);  
  lcd.print("Accident Alert  ");
  lcd.setCursor(0,1);
  lcd.print("     System     ");
  delay(2000);
  lcd.clear();
  .... ......
  ...... .....
 lcd.print("Callibrating ");
  lcd.setCursor(0,1);
  lcd.print("Acceleromiter");
  for(int i=0;i<samples;i++)
  {
    xsample+=analogRead(x);
    ysample+=analogRead(y);
    zsample+=analogRead(z);
  }

  xsample/=samples;
  ysample/=samples;
  zsample/=samples;

  Serial.println(xsample);
  Serial.println(ysample);
  Serial.println(zsample);
void loop() 
{
    int value1=analogRead(x);
    int value2=analogRead(y);
    int value3=analogRead(z);

    int xValue=xsample-value1;
    int yValue=ysample-value2;
    int zValue=zsample-value3;
    
    Serial.print("x=");
    Serial.println(xValue);
    Serial.print("y=");
    Serial.println(yValue);
    Serial.print("z=");
    Serial.println(zValue);
    ..... .....
    ........ ...
```
```
#include<SoftwareSerial.h>

SoftwareSerial Serial1(2,3); //make RX arduino line is pin 2, make TX arduino line is pin 3.

SoftwareSerial gps(10,11);

#include<LiquidCrystal.h>

LiquidCrystal lcd(4,5,6,7,8,9);


#define x A1

#define y A2

#define z A3


int xsample=0;

int ysample=0;

int zsample=0;


#define samples 10


#define minVal -50

#define MaxVal 50


int i=0,k=0;

int  gps_status=0;

float latitude=0; 

float logitude=0;                       

String Speed="";

String gpsString="";

char *test="$GPRMC";


void initModule(String cmd, char *res, int t)

{

  while(1)

  {

    Serial.println(cmd);

    Serial1.println(cmd);

    delay(100);

    while(Serial1.available()>0)

    {

       if(Serial1.find(res))

       {

        Serial.println(res);

        delay(t);

        return;

       }


       else

       {

        Serial.println("Error");

       }

    }

    delay(t);

  }

}



void setup() 

{

  Serial1.begin(9600);

  Serial.begin(9600);

  lcd.begin(16,2);  

  lcd.print("Accident Alert  ");

  lcd.setCursor(0,1);

  lcd.print("     System     ");

  delay(2000);

  lcd.clear();

  lcd.print("Initializing");

  lcd.setCursor(0,1);

  lcd.print("Please Wait...");

  delay(1000);

  

  Serial.println("Initializing....");

  initModule("AT","OK",1000);

  initModule("ATE1","OK",1000);

  initModule("AT+CPIN?","READY",1000);  

  initModule("AT+CMGF=1","OK",1000);     

  initModule("AT+CNMI=2,2,0,0,0","OK",1000);  

  Serial.println("Initialized Successfully");

  lcd.clear();

  lcd.print("Initialized");

  lcd.setCursor(0,1);

  lcd.print("Successfully");

  delay(2000);

  lcd.clear();

  lcd.print("Callibrating ");

  lcd.setCursor(0,1);

  lcd.print("Acceleromiter");

  for(int i=0;i<samples;i++)

  {

    xsample+=analogRead(x);

    ysample+=analogRead(y);

    zsample+=analogRead(z);

  }


  xsample/=samples;

  ysample/=samples;

  zsample/=samples;


  Serial.println(xsample);

  Serial.println(ysample);

  Serial.println(zsample);

  delay(1000);

  

  lcd.clear();

  lcd.print("Waiting For GPS");

  lcd.setCursor(0,1);

  lcd.print("     Signal    ");

  delay(2000);

  gps.begin(9600);

  get_gps();

  show_coordinate();

  delay(2000);

  lcd.clear();

  lcd.print("GPS is Ready");

  delay(1000);

  lcd.clear();

  lcd.print("System Ready");

  Serial.println("System Ready..");

}


void loop() 

{

    int value1=analogRead(x);

    int value2=analogRead(y);

    int value3=analogRead(z);


    int xValue=xsample-value1;

    int yValue=ysample-value2;

    int zValue=zsample-value3;

    

    Serial.print("x=");

    Serial.println(xValue);

    Serial.print("y=");

    Serial.println(yValue);

    Serial.print("z=");

    Serial.println(zValue);


    if(xValue < minVal || xValue > MaxVal  || yValue < minVal || yValue > MaxVal  || zValue < minVal || zValue > MaxVal)

    {

      get_gps();

      show_coordinate();

      lcd.clear();

      lcd.print("Sending SMS ");

      Serial.println("Sending SMS");

      Send();

      Serial.println("SMS Sent");

      delay(2000);

      lcd.clear();

      lcd.print("System Ready");

    }       

}


void gpsEvent()

{

  gpsString="";

  while(1)

  {

   while (gps.available()>0)            //Serial incoming data from GPS

   {

    char inChar = (char)gps.read();

     gpsString+= inChar;                    //store incoming data from GPS to temparary string str[]

     i++;

    // Serial.print(inChar);

     if (i < 7)                      

     {

      if(gpsString[i-1] != test[i-1])         //check for right string

      {

        i=0;

        gpsString="";

      }

     }

    if(inChar=='\r')

    {

     if(i>60)

     {

       gps_status=1;

       break;

     }

     else

     {

       i=0;

     }

    }

  }

   if(gps_status)

    break;

  }

}


void get_gps()

{

  lcd.clear();

  lcd.print("Getting GPS Data");

  lcd.setCursor(0,1);

  lcd.print("Please Wait.....");

   gps_status=0;

   int x=0;

   while(gps_status==0)

   {

    gpsEvent();

    int str_lenth=i;

    coordinate2dec();

    i=0;x=0;

    str_lenth=0;

   }

}


void show_coordinate()

{

    lcd.clear();

    lcd.print("Lat:");

    lcd.print(latitude);

    lcd.setCursor(0,1);

    lcd.print("Log:");

    lcd.print(logitude);

    Serial.print("Latitude:");

    Serial.println(latitude);

    Serial.print("Longitude:");

    Serial.println(logitude);

    Serial.print("Speed(in knots)=");

    Serial.println(Speed);

    delay(2000);

    lcd.clear();

    lcd.print("Speed(Knots):");

    lcd.setCursor(0,1);

    lcd.print(Speed);

}


void coordinate2dec()

{

  String lat_degree="";

    for(i=20;i<=21;i++)         

      lat_degree+=gpsString[i];

      

  String lat_minut="";

     for(i=22;i<=28;i++)         

      lat_minut+=gpsString[i];


  String log_degree="";

    for(i=32;i<=34;i++)

      log_degree+=gpsString[i];


  String log_minut="";

    for(i=35;i<=41;i++)

      log_minut+=gpsString[i];

    

    Speed="";

    for(i=45;i<48;i++)          //extract longitude from string

      Speed+=gpsString[i];

      

     float minut= lat_minut.toFloat();

     minut=minut/60;

     float degree=lat_degree.toFloat();

     latitude=degree+minut;

     

     minut= log_minut.toFloat();

     minut=minut/60;

     degree=log_degree.toFloat();

     logitude=degree+minut;

}


void Send()

{ 

   Serial1.println("AT");

   delay(500);

   serialPrint();

   Serial1.println("AT+CMGF=1");

   delay(500);

   serialPrint();

   Serial1.print("AT+CMGS=");

   Serial1.print('"');

   Serial1.print("9821757249");    //mobile no. for SMS alert

   Serial1.println('"');

   delay(500);

   serialPrint();

   Serial1.print("Latitude:");

   Serial1.println(latitude);

   delay(500);

   serialPrint();

   Serial1.print(" longitude:");

   Serial1.println(logitude);

   delay(500);

   serialPrint();

   Serial1.print(" Speed:");

   Serial1.print(Speed);

   Serial1.println("Knots");

   delay(500);

   serialPrint();

   Serial1.print("http://maps.google.com/maps?&z=15&mrt=yp&t=k&q=");

   Serial1.print(latitude,6);

   Serial1.print("+");              //28.612953, 77.231545   //28.612953,77.2293563

   Serial1.print(logitude,6);

   Serial1.write(26);

   delay(2000);

   serialPrint();

}


void serialPrint()

{

  while(Serial1.available()>0)

  {

    Serial.print(Serial1.read());

  }

}
```

