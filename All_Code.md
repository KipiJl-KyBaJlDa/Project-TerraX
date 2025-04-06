#include <SoftwareSerial.h>
#include<DHT.h>

DHT dht(13, DHT11);
SoftwareSerial bt(2, 3);  //TX , RX


int PWMA = 10;
int AIN1 = 8;
int AIN2 = 9;
int BIN1 = 6;
int BIN2 = 7;
int PWMB = 5;
int SPEED = 140;
char value = 'S';

int Frenel = 11;
int all_Diods = 12;
int light_Sensor = 0;
int gas = 1;
String Gas_Text = "";
String Light_Text = "";
String Frenel_Text = "";
String ALL_INFO = "";
int LIGHT = 0;


void setup() {
  for (int i = 5; i < 11; i++) {
    pinMode(i, OUTPUT);
  }
  pinMode(Frenel, INPUT);
  pinMode(all_Diods, OUTPUT);
  digitalWrite(all_Diods, LOW);
  Serial.begin(9600);
  bt.begin(9600);
  dht.begin();
}

void loop() {
  LIGHT = analogRead(light_Sensor);
  Serial.println(analogRead(light_Sensor));
  if (LIGHT < 180) {
    digitalWrite(all_Diods, HIGH);
    Light_Text = "ON_light";
  }
  else {
    digitalWrite(all_Diods, LOW);
    Light_Text = "OFF_light";
  }
  
  if (bt.available()) {
    value = bt.read();
    Serial.println(value);
    if (value == 'S') {
      STP();
    } else if (value == 'F') {
      FORWARD();
    } else if (value == 'B') {
      BACKWARD();
    } else if (value == 'L') {
      LEFT();
    } else if (value == 'R') {
      RIGHT();
    } else if (value == 'I') {
      Info();
    }
  }
}
void Info() {
  //        Тестові команди( чи все працює )
  //  Serial.println("HUMI: " + String(dht.readHumidity()));
  //  Serial.println("TEMP: " + String(dht.readTemperature()));
  //  Serial.println("Gas: " + String(analogRead(gas)));
  //    Serial.println("Light: " + String(analogRead(light_Sensor)));
  //  Serial.println("Frenel: " + String(digitalRead(Frenel)));
  //  digitalWrite(all_Diods, HIGH);

  float HUMI = dht.readHumidity();
  float TEMP = dht.readTemperature();
  int GAS = analogRead(gas);
  int FRENEL = digitalRead(Frenel);

  if (GAS > 220) {
    Gas_Text = "Air_normal👍";;
  }
  else {
    Gas_Text = "Air_pollution👎";
  }

  if (FRENEL) {
    Frenel_Text = "YES👽";
  }
  else {
    Frenel_Text = "NO";
  }

  ALL_INFO = String(HUMI) + " " + String(TEMP) + " " + Gas_Text + " " + Light_Text + " " + Frenel_Text;
  char ALL_INFO_C[ALL_INFO.length() + 1];
  ALL_INFO.toCharArray(ALL_INFO_C, ALL_INFO.length() + 1);
  bt.write(ALL_INFO_C);
}

void FORWARD() {
  digitalWrite(AIN1, HIGH);
  digitalWrite(AIN2, LOW);
  digitalWrite(BIN1, HIGH);
  digitalWrite(BIN2, LOW);
  analogWrite(PWMA, SPEED);
  analogWrite(PWMB, SPEED);
}
void BACKWARD() {
  digitalWrite(AIN1, LOW);
  digitalWrite(AIN2, HIGH);
  digitalWrite(BIN1, LOW);
  digitalWrite(BIN2, HIGH);
  analogWrite(PWMA, SPEED);
  analogWrite(PWMB, SPEED);
}
void LEFT() {
  digitalWrite(AIN1, HIGH);
  digitalWrite(AIN2, LOW);
  digitalWrite(BIN1, LOW);
  digitalWrite(BIN2, HIGH);
  analogWrite(PWMA, SPEED);
  analogWrite(PWMB, SPEED);
}
void RIGHT() {
  digitalWrite(AIN1, LOW);
  digitalWrite(AIN2, HIGH);
  digitalWrite(BIN1, HIGH);
  digitalWrite(BIN2, LOW);
  analogWrite(PWMA, SPEED);
  analogWrite(PWMB, SPEED);
}
void STP() {
  digitalWrite(AIN1, LOW);
  digitalWrite(AIN2, LOW);
  digitalWrite(BIN1, LOW);
  digitalWrite(BIN2, LOW);
  analogWrite(PWMA, 0);
  analogWrite(PWMB, 0);
}
