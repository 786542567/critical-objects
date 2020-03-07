# critical-objects

//transmitter code

 #include <SPI.h>
    #include <nRF24L01.h>
    #include <RF24.h>
    RF24 radio(9, 8);
    
    const byte addresses [][6] = {"00001", "00002"};  
    
    int button_pin = 2;
    int led_pin = 3;
    
    boolean button_state = 0;
    boolean button_state1 = 0;
    
    void setup() {
      pinMode(button_pin, INPUT);
      pinMode(led_pin, OUTPUT);
      radio.begin();                           
      radio.openWritingPipe(addresses[1]);    
      radio.openReadingPipe(1, addresses[0]); 
      radio.setPALevel(RF24_PA_MIN); 
    }
    void loop() 
    {  
      delay(5);
      radio.stopListening();                             
      button_state = digitalRead(button_pin);
      radio.write(&button_state, sizeof(button_state)); 
      delay(5);
      
      radio.startListening();                           
      while(!radio.available());                         
      radio.read(&button_state1, sizeof(button_state1)); 
      if (button_state1 == HIGH)
      {
        digitalWrite(led_pin, HIGH);
      }
      else
      {
        digitalWrite(led_pin, LOW);
      }
    }
    
  //reciever code
  
#include <SPI.h>
#include <nRF24L01.h>
#include <RF24.h>

RF24 radio(9, 8); // CE, CSN
const byte addresses [][6] = {"00001", "00002"};    
int button_pin = 2;
int led_pin = 3;

boolean button_state = 0;
boolean button_state1 = 0;


void setup() {
  pinMode(led_pin, OUTPUT);
  Serial.begin(9600);
  radio.begin();                            
  radio.openWritingPipe(addresses[0]);      
  radio.openReadingPipe(1, addresses[1]);  
  radio.setPALevel(RF24_PA_MIN);           
}

void loop() 
{
  delay(5);
  radio.startListening();                  
  if (radio.available())                     
  {
    radio.read(&button_state, sizeof(button_state));
    if(button_state == HIGH)
  {
     digitalWrite(led_pin, HIGH);
  }
  else
  {
     digitalWrite(led_pin, LOW);
  }
  delay(5);
  
  radio.stopListening();                          
  button_state1 = digitalRead(button_pin);
  radio.write(&button_state1, sizeof(button_state1));  
  }
}

