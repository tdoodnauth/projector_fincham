//Code for Epson projector to control projector screen and 
//Room lights
//=========================================================
//CONSTANTS
int SCREEN_DOWN_TIME = 28000;
int SCREEN_UP_TIME = 30000;
int BUTTON_PRESS_DELAY = 2000;

//DEFINE I/O PINS
//SCREEN
int SCREEN_UP_SW = 9;
int SCREEN_DOWN_SW = 2;
int SCREEN_STOP_SW = 8;

//PROJECTOR
int PROJECTOR_TRIGGER = 7;

//LIGHTS
int LIGHTS_BRIGHT_SW = 6;
int LIGHTS_ENTERTAIN_SW = 4;
int LIGHTS_RELAX_SW = 5;
int LIGHTS_OFF_SW = 3;

//========================================================
//STATE VARIABLE
enum STATE_VARIABLE
{ INITIALIZE = 0, // default power up state
  READY = 1,
  SCREEN_UP = 2, //moving screen up
  SCREEN_DOWN = 3, //moving screen down
  SCREEN_STOP_UP = 4, //screen has stopped at the top
  SCREEN_STOP_DOWN = 5, //screen has stopped at the bottom
};

STATE_VARIABLE systemState = INITIALIZE;


void setup()
{
  // put your setup code here, to run once:

Serial.begin(9600);

  //========================================================
  //DEFINE PIN MODES
  //SCREEN
  pinMode(SCREEN_UP_SW, OUTPUT);
  pinMode(SCREEN_DOWN_SW, OUTPUT);
  pinMode(SCREEN_STOP_SW, OUTPUT);

  //PROJECTOR
  pinMode(PROJECTOR_TRIGGER, INPUT);

  //LIGHTS
  pinMode(LIGHTS_BRIGHT_SW, OUTPUT);
  pinMode(LIGHTS_ENTERTAIN_SW, OUTPUT);
  pinMode(LIGHTS_RELAX_SW, OUTPUT);
  pinMode(LIGHTS_OFF_SW, OUTPUT);

  //INITIALIZE SWITCHES, ALL DEVICES ARE ACTIVE LOW, IDLE STATE, ALL DEVICES TO BE DRIVEN HIGH
  digitalWrite(SCREEN_UP_SW, HIGH);
  digitalWrite(SCREEN_DOWN_SW, HIGH);
  digitalWrite(SCREEN_STOP_SW, HIGH);

  digitalWrite(LIGHTS_BRIGHT_SW, HIGH);
  digitalWrite(LIGHTS_ENTERTAIN_SW, HIGH);
  digitalWrite(LIGHTS_RELAX_SW, HIGH);
  digitalWrite(LIGHTS_OFF_SW, HIGH);

  Serial.print(systemState);
}

void loop()
{
delay(5000);
  int Projector_status = digitalRead(PROJECTOR_TRIGGER); //read projector pin

  if (systemState == INITIALIZE) //Initialize, screen is in an unknown state
  {
    //Move screen up for x seconds and then stop
    moveScreenUp(); //Home the screen at the top
    systemState = SCREEN_STOP_UP;
  }
  else if (systemState == SCREEN_UP)
  {
    moveScreenUp(); //Home the screen at the top
    systemState = SCREEN_STOP_UP;
  }

  else if (systemState == SCREEN_DOWN)
  {
    moveScreenDown();
    systemState = SCREEN_STOP_DOWN;
  }

  else if (systemState == SCREEN_STOP_UP)
  {
    if (Projector_status == HIGH)
      systemState = SCREEN_DOWN;
  }

  else if (systemState == SCREEN_STOP_DOWN)
  {
    if (Projector_status == LOW)
    {
      systemState = SCREEN_UP;
    }
  }
  Serial.print(systemState);
}

void moveScreenUp() {

  //Turn the lights on

  digitalWrite(LIGHTS_BRIGHT_SW, LOW);
  delay(BUTTON_PRESS_DELAY);
  digitalWrite(LIGHTS_BRIGHT_SW, HIGH);

  digitalWrite(SCREEN_UP_SW, LOW);
  delay(BUTTON_PRESS_DELAY);
  digitalWrite(SCREEN_UP_SW, HIGH);

  //duplicate incase limit switch for screen down
  digitalWrite(SCREEN_UP_SW, LOW);
  delay(BUTTON_PRESS_DELAY);
  digitalWrite(SCREEN_UP_SW, HIGH);

  delay(SCREEN_UP_TIME);

  digitalWrite(SCREEN_STOP_SW, LOW);
  delay(BUTTON_PRESS_DELAY);
  digitalWrite(SCREEN_STOP_SW, HIGH);
}
void moveScreenDown()
{
  digitalWrite(LIGHTS_RELAX_SW, LOW);
  delay(BUTTON_PRESS_DELAY);
  digitalWrite(LIGHTS_RELAX_SW, HIGH);
  
  digitalWrite(SCREEN_DOWN_SW, LOW);
  delay(BUTTON_PRESS_DELAY);
  digitalWrite(SCREEN_DOWN_SW, HIGH);
  
  delay(SCREEN_DOWN_TIME);
  
  digitalWrite(SCREEN_STOP_SW, LOW);
  delay(BUTTON_PRESS_DELAY);
  digitalWrite(SCREEN_STOP_SW, HIGH);
}
