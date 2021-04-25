//elevator
//Have made use of Arduino Mega and stepper motor, 8 segment display, jumper wires, switches,etc.
#include <Stepper.h>
//Global variables
int CF=0; // Current floor
int DF=0; //destination floor
int DF0=0; // Destination floor boolean
int DF1=0; // Destination floor boolean
int DF2=0; // Destination floor boolean
int DF3=0; // Destination floor boolean
int Direction=1;
int move_to_request=1;
int time_count=25;//5000/200 (5000msec delay with each key press cycle taking 200ms : 5000/200: 25 keypress cycle wait before door closes)
// Motor
const int stepsPerRevolution = 200; //
int intersteps = 2222;
Stepper myStepper(stepsPerRevolution, 8,10,9,11);
//Requests
int GFU=0; //boolean
int FFU=0; //boolean
int FFD=0; //bolean
int SFU=0;//bolean
int SFD=0;//bolean
int TFD=0;//bolean
// Declare pins
// Seven segment pins
int pina = 22;
int pinb = 23;
int pinc = 24;
int pind = 25;
int pine = 26;
int pinf = 27;
int ping = 28;
// Buttons pins
int pinGF = 53;//connected to ground floor
int pinFF = 52;
int pinSF = 51;
int pinTF = 50;
int pinGFU = 49;
int pinFFU = 48;
int pinFFD = 47;
int pinSFU = 46;
int pinSFD = 45;
int pinTFD = 44;
// LED pins
int pinLedOpen = 30;
int pinLedClose = 31;
int pinLedUp = 32;
int pinLedDown = 33;
// buzzer pins
int pinBuzzer=2;
void initButtons() {
 pinMode(pinGF, INPUT);
 pinMode(pinFF, INPUT);
 pinMode(pinSF, INPUT);
 pinMode(pinTF, INPUT);
 pinMode(pinGFU, INPUT);
 pinMode(pinFFU, INPUT);
 pinMode(pinFFD, INPUT);
 pinMode(pinSFU, INPUT);
 pinMode(pinSFD, INPUT);
 pinMode(pinTFD, INPUT); }
int keyScan() {
 delay(200);
 if (digitalRead(pinGF) == HIGH)

{
 return 0;

}
 if (digitalRead(pinFF) == HIGH)

{
 return 1;

}
 if (digitalRead(pinSF) == HIGH)

{
 return 2;

}
 if (digitalRead(pinTF) == HIGH)

{
 return 3;

}
 if (digitalRead(pinGFU) == HIGH)

{
 return 4;

}
 if (digitalRead(pinFFU) == HIGH)

{
 return 5;

}

 if (digitalRead(pinFFD) == HIGH)

{
 return 6;

}

 if (digitalRead(pinSFU) == HIGH)

{
 return 7;

}

 if (digitalRead(pinSFD) == HIGH)

{
 return 8;

}

 if (digitalRead(pinTFD) == HIGH)

{
 return 9;
 }
else {
 return
-1;
}}
void disp_init() {
 // set pins as output
 pinMode(pina, OUTPUT);
 pinMode(pinb, OUTPUT);
 pinMode(pinc, OUTPUT);
 pinMode(pind, OUTPUT);
 pinMode(pine, OUTPUT);
 pinMode(pinf, OUTPUT);
 pinMode(ping, OUTPUT);

 // Indicator LEDs
 pinMode(pinLedOpen, OUTPUT);
 pinMode(pinLedClose, OUTPUT);
 pinMode(pinLedDown, OUTPUT);
 pinMode(pinLedUp, OUTPUT);

//Buzzer sound indicator
 pinMode(pinBuzzer,OUTPUT);
 // initialize the pins to HIGH <-- All of the off
 digitalWrite(pina, HIGH);
 digitalWrite(pinb, HIGH);
 digitalWrite(pinc, HIGH);
 digitalWrite(pind, HIGH);
 digitalWrite(pine, HIGH);
 digitalWrite(pinf, HIGH);
 digitalWrite(ping, HIGH);

 // Initialize LEDs to LOW
 digitalWrite(pinLedOpen, LOW);
 digitalWrite(pinLedClose, LOW);
 digitalWrite(pinLedUp, LOW);
 digitalWrite(pinLedDown, LOW);
 // Initialize buzzer to LOW
 digitalWrite(pinBuzzer, LOW);

 disp(0); }
void disp(int dig) {
 switch (dig) {
 case 0:
 digitalWrite(pina, LOW);
 digitalWrite(pinb, LOW);
 digitalWrite(pinc, LOW);
 digitalWrite(pind, LOW);
 digitalWrite(pine, LOW);
 digitalWrite(pinf, LOW);
 digitalWrite(ping, HIGH);
 break;
 case 1:
 digitalWrite(pina, HIGH);
 digitalWrite(pinb, LOW);
 digitalWrite(pinc, LOW);
 digitalWrite(pind, HIGH);
 digitalWrite(pine, HIGH);
 digitalWrite(pinf, HIGH);
 digitalWrite(ping, HIGH);
 break;
 case 2:
 digitalWrite(pina, LOW);
 digitalWrite(pinb, LOW);
 digitalWrite(pinc, HIGH);
 digitalWrite(pind, LOW);
 digitalWrite(pine, LOW);
 digitalWrite(pinf, HIGH);
 digitalWrite(ping, LOW);
 break;
 case 3:
 digitalWrite(pina, LOW);
 digitalWrite(pinb, LOW);
 digitalWrite(pinc, LOW);
 digitalWrite(pind, LOW);
 digitalWrite(pine, HIGH);
 digitalWrite(pinf, HIGH);
 digitalWrite(ping, LOW);
 default:
 break;

}
}
void Up(int i) {
 if(i==1)
 {
 //lift going upwards
 digitalWrite(pinLedUp, HIGH);
 digitalWrite(pinLedDown, LOW);

}
 else if(i==0)

{
 //lift at rest
 digitalWrite(pinLedUp, LOW);
 digitalWrite(pinLedDown, LOW);

}else

{
 //lift moving downwards
 digitalWrite(pinLedUp, LOW);
 digitalWrite(pinLedDown, HIGH);

}
}
void Open(int i) {
 if(i==1)
 {
 //door opening
 digitalWrite(pinLedOpen, HIGH);
 digitalWrite(pinLedClose, LOW);
 digitalWrite(pinBuzzer, LOW);

}
 else

{
 //door closing
 digitalWrite(pinLedOpen, LOW);
 digitalWrite(pinLedClose, HIGH);
 digitalWrite(pinBuzzer,HIGH);

}
}
//set next destination
void set_destination(){
 if(Direction==1){
 if(CF==0){
 if(DF1==1 || FFU==1){
 DF=1;
 }else if(DF2==1 || SFU==1){
 DF=2;
 }else if(DF3==1 || TFD==1){
 DF=3;

}
 }else if(CF==1){
 if(DF2==1 || SFU==1){
 DF=2;
 }else if(DF3==1 || TFD==1){
 DF=3;

}
 }else if(CF==2){
 if(DF3==1 || TFD==1){
 DF=3;

}
 }
 }else if(Direction==2){
 if(CF==3){
 if(DF2==1 || SFD==1){
 DF=2;
 }else if(DF1==1 || FFD==1){
 DF=1;
 }else if(DF0==1 || GFU==1){
 DF=0;

}
 }else if(CF==2){
 if(DF1==1 || FFD==1){
 DF=1;
 }else if(DF0==1 || GFU==1){
 DF=0;

}
 }else if(CF==1){
 if(DF0==1 || GFU==1){
   DF=0;
 }
 }
 }
 //attending requests if no destinations set
 if(move_to_request==1){
 if(DF0==0 && DF1==0 && DF2==0 && DF3==0){
 if(GFU==1){
 move_to_request_floor(0);
 }else if(FFU==1){
 move_to_request_floor(1);
 }else if(FFD==1){
 move_to_request_floor(2);
 }else if(SFU==1){
 move_to_request_floor(3);
 }else if(SFD==1){
 move_to_request_floor(4);
 }else if(TFD==1){
 move_to_request_floor(5);
 }
 }
 }
}//end of set destination method
void move_to_request_floor(int request){
 if(DF0==0 && DF1==0 && DF2==0 && DF3==0 && move_to_request==1){
 int des;
 switch(request){
 case 0:
 des=0;
 if(CF==des){
 //OPENING DOOR
 Up(0);// at rest for opening door
 Open(1);//opening door
 //delay
 move_to_request=0;
 do{
 unsigned int count=0;
 while(count<time_count){
 get_key_press();
 count++;
 }
 }while(DF0==0 && DF1==0 && DF2==0 && DF3==0);
 // simulates door opening
 Open(0);//closing the door
 }else{
 DF=CF-1;
 Direction=2;
 }
 break;
 case 1:
 case 2:
 des=1;
 if(CF==des){
 //OPENING DOOR
 Up(0);// at rest for opening door
 Open(1);//opening door
 move_to_request=0;
 do{
 unsigned int count=0;
 while(count<time_count){
 get_key_press();
 count++;
 }
 }while(DF0==0 && DF1==0 && DF2==0 && DF3==0);
   // simulates door opening
 Open(0);//closing the door //open
 }else if(CF<des){
 DF=CF+1;
 Direction=1;// lift moves up
 }else{
 DF=CF-1;
 Direction=2;//lift moves down
 }
 break;
 case 3:
 case 4:
 des=2;
 if(CF==des){
 //OPENING DOOR
 Up(0);// at rest for opening door
 Open(1);//opening door
 move_to_request=0;
 // simulates door opening
 do{
 unsigned int count=0;
 while(count<time_count){
 get_key_press();
 count++;
 }
 }while(DF0==0 && DF1==0 && DF2==0 && DF3==0);
 Open(0);//closing the door //open
 }else if(CF<des){
 DF=CF+1;
 Direction=1;
 }else{
 DF=CF-1;
 Direction=2;
 }
 break;
 case 5:
 des=3;
 if(CF==des){
 //OPENING DOOR
 Up(0);// at rest for opening door
 Open(1);//opening door
 move_to_request=0;
 // simulates door opening
 do{
 unsigned int count=0;
 while(count<time_count){
 get_key_press();
 count++;
 }
 }while(DF0==0 && DF1==0 && DF2==0 && DF3==0);
 Open(0);//closing the door //open
 }else if(CF<des){
 DF=CF+1;
 Direction=1;
 }
 break;
 }
 }
}
void setup() {
 //stepper motor speed set
 myStepper.setSpeed(120);
 //destination and current floor are equal
 DF = CF = 0;
  disp_init();
 initButtons();
 Serial.begin(9600);
}
void get_key_press(){
int key;
delay(5);
key = keyScan();
switch(key)
{
 //cab key select
 case 0://destination GF
 if(CF>0 && Direction==2 && (TFD==1 || SFD==1 || FFD==1)){
 DF0=1; //dest to groundflo allowed
 set_destination();
 }else{
 if(Direction==1 && CF>0 && (FFD==1 || SFD==1)){
 DF0=1; //dest to thirdflo allowed
 Direction=2;
 set_destination();
 }
 }
 break;
 case 1:
 if((CF<1 && Direction==1 && GFU==1) || (CF>1 && Direction==2 && (TFD==1 || SFD==1))){
 DF1=1;//dest to firstflo allowed
 set_destination();
 }else{//turning point
 if((CF<1 && Direction==2 && GFU==1) || (CF>1 && Direction==1 && (TFD==1 || SFD==1))){
 DF1=1; //dest to secondflo allowed
 if(Direction==1){
 Direction=2;
 }else if(Direction==2){
 Direction=1;
 }
 set_destination();
 }
 }
 break;
 case 2:
 if((CF<2 && Direction==1 && (FFU==1 || GFU==1)) || (CF>2 && Direction==2 && TFD==1)){
 DF2=1;//dest to secondflo allowed
 set_destination();
 }else{//turning point
 if((CF<2 && Direction==2 && (FFU==1 || GFU==1)) || (CF>2 && Direction==1 && TFD==1)){
 DF2=1; //dest to secondflo allowed
 if(Direction==1){
 Direction=2;
 }else if(Direction==2){
 Direction=1;
 }
 set_destination();
 }
 }
 break;
 case 3:
 if((CF<3 && Direction==1 && (GFU==1 || FFU==1 || SFU==1))){
 DF3=1; //dest to thirdflo allowed

 set_destination();
 }else{//turning point
 if(Direction==2 && CF<3 && (FFU==1 || SFU==1)){
 DF3=1; //dest to thirdflo allowed
   Direction=1;
 set_destination();
 }
 }
 break;
 case 4:
 // GFU=1;
 if((CF > 0 && Direction==2) || (CF==0 && Direction==1) || (DF0==0 && DF1==0 && DF2==0 && DF3==0)){
 GFU=1;
 move_to_request_floor(0);
 }
 break;
 case 5:
 if((CF<= 1 && Direction==1) || (DF0==0 && DF1==0 && DF2==0 && DF3==0)){
 FFU=1;
 move_to_request_floor(1);
 }
 break;
 case 6:
 if((CF >= 1 && Direction==2) || (DF0==0 && DF1==0 && DF2==0 && DF3==0)){
 FFD= 1;
 move_to_request_floor(2);
 }
// Serial.print("DF = ");Serial.print(key);Serial.println("");
 break;
 case 7:
 //SFU=1;
 if((CF <= 2 && Direction==1) || (DF0==0 && DF1==0 && DF2==0 && DF3==0)){
 SFU= 1;
 move_to_request_floor(3);
 }
 break;
 case 8:
 // SFD=1;
 if((CF >= 2 && Direction==2) || (DF0==0 && DF1==0 && DF2==0 && DF3==0)){
 SFD= 1;
 move_to_request_floor(4);
 }
 break;
 case 9:
 // TFD=1;
 if((CF< 3 && Direction==1) || (CF==3 && Direction==2) || (DF0==0 && DF1==0 && DF2==0 && DF3==0)){
 TFD=1;
 move_to_request_floor(5);
 }
 break;
 default:
 break;
}//end of switch()
}// end of get_key_press
void set_lift_motion(){
if(DF==CF){
 Serial.print("IDLE @ ");Serial.println(CF);
}else if(DF>CF)//up movements
 {
 Serial.println("NTMU");
 int flr =1; //(DF - CF);
 int l = 2222 * flr;
 if((CF==0 && GFU==1) || (CF==1 && FFU==1) || (CF==2 && SFU==1)){
 Up(0);// at rest for opening door
 Open(1);//opening door
 do{
 unsigned int count=0;
   while(count<time_count){
 get_key_press();
 count++;
 }
 }while(DF0==0 && DF1==0 && DF2==0 && DF3==0);
 //clearing a served request
 if(CF==0){
 GFU=0;
 }else if(CF==1){
 FFU=0;
 }else if(CF==2){
 SFU=0;
 }
 // simulates door opening
 Open(0);//closing the door
 }
 Up(Direction);//moving up display
 disp(CF);
 delay(400);
 // Move motor a floor up
 myStepper.step(intersteps * flr);
 //delay(l);
 CF = CF+1;//DF;
 disp(CF);
 delay(400);
 if((CF==1 && DF1==1) || (CF==2 && DF2==1) || (CF==3 && DF3==1)){
 Up(0);// at rest for opening door
 Open(1);//opening door
 //clearing a served request
 if(CF==1){
 DF1=0;
 }else if(CF==2){
 DF2=0;
 }else if(CF==3){
 DF3=0;
 }
 if(DF0==0 && DF1==0 && DF2==0 && DF3==0){
 move_to_request=1;
 }else{
 move_to_request=0;
 }
 // simulates door opening
 do{
 unsigned int count=0;
 while(count<time_count){
 get_key_press();
 count++;
 }
 }while(DF0==0 && DF1==0 && DF2==0 && DF3==0 && GFU==0 && FFD==0 && FFU==0 && SFU==0 && TFD==0);
 Open(0);//closing the door
 }
 if(CF==3){
 Direction=2;
 }
 //Setting the next destination
 set_destination();
 }else if(DF<CF) // down movements
 {
 Serial.println("NTMD");
 int flr =-1; //(DF - CF);
 int l = 2222 * flr;
 move_to_request=1;
 if((CF==1 && FFD==1) || (CF==2 && SFD==1) || (CF==3 && TFD==1)){
 Up(0);// at rest for opening door
 Open(1);//opening door
   do{
 unsigned int count=0;
 while(count<time_count){
 get_key_press();
 count++;
 }
 }while(DF0==0 && DF1==0 && DF2==0 && DF3==0);
 //clearing a served request
 if(CF==1){
 FFD=0;
 }else if(CF==2){
 SFD=0;
 }else if(CF==3){
 TFD=0;
 }
 //delay
 //delay(3000); // simulates door opening
 Open(0);//closing the door
 }
 //down moving
 Up(Direction);//moving up display
 disp(CF);
 delay(400);
 // Move motor
 myStepper.step(intersteps * flr);
 //delay(l);
 CF = CF-1;
 disp(CF);
 delay(400);
 if((CF==0 && DF0==1) || (CF==1 && DF1==1) || (CF==2 && DF2==1)){
 Up(0);// at rest for opening door
 Open(1);//opening door
 //clearing a served request
 if(CF==0){
 DF0=0;
 }else if(CF==1){
 DF1=0;
 }else if(CF==2){
 DF2=0;
 }
 if(DF0==0 && DF1==0 && DF2==0 && DF3==0){
 move_to_request=1;
 }else{
 move_to_request=0;
 }
 do{
 unsigned int count=0;
 while(count<time_count){
 get_key_press();
 count++;
 }
 }while(DF0==0 && DF1==0 && DF2==0 && DF3==0 && GFU==0 && FFD==0 && FFU==0 && SFU==0 && TFD==0);
 //delay
 //delay(3000); // simulates door opening
 Open(0);//closing the door
 }
 //direction change on ground
 if(CF==0){
 Direction=1;
 }
 //Setting the next destination
 set_destination();

 }
}//end of set_lift_motion
void loop() {
 get_key_press();
 // Lift Logic
 set_lift_motion();
}// end of loop() method
