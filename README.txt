unsigned long pulse_width;
int motorPin = 3;
int triggerPin = 2;
int monitorPin = 5;
int powerPin = 4;
int ledPin = 13;


void setup()
{
  Serial.begin(9600); // Start serial communications
  pinMode(ledPin,OUTPUT);
  pinMode(motorPin,OUTPUT);
  pinMode(triggerPin, OUTPUT); 
  pinMode(monitorPin, INPUT); 
  pinMode(powerPin, OUTPUT); 
  digitalWrite(powerPin,HIGH); //Turn sensor on
  digitalWrite(triggerPin , LOW); // Set trigger LOW for continuous read
}

void loop()
{
  delay(10);
  pulse_width = pulseIn(monitorPin, HIGH); // Count how long the pulse is high in microseconds
  
  if(pulse_width != 0)
  { 
    // If we get a reading that isn't zero, let's print it
    pulse_width = pulse_width/10; // 10usec = 1 cm of distance for LIDAR-Lite
    Serial.println(pulse_width);
    if(pulse_width > 100)
    {
      stopMotor();
      delay(1);
    }
    else
    {
      startMotor();
      delay(1);
    }
  // Print the distance
  }
  else
  { 
    // We read a zero which means we're locking up. 
    digitalWrite(powerPin,LOW);  // Turn off the sensor
    delay(1);  // Wait 1ms
    digitalWrite(powerPin,HIGH);  //Turn on the sensor
    delay(1);  //Wait 1ms for it to turn on.
  }
}

void stopMotor()
{
  digitalWrite(ledPin,LOW);
  analogWrite(motorPin,0);
}

void startMotor()
{
  digitalWrite(ledPin,HIGH);
  if(pulse_width>75&&pulse_width<=100)
  {
    analogWrite(motorPin,50);
  }
  if(pulse_width>50&&pulse_width<=75)
  {
    analogWrite(motorPin,100);
  }
  if(pulse_width>25&&pulse_width<=50)
  {
    analogWrite(motorPin,150);
  }
  if(pulse_width>=0&&pulse_width<=25)
  {
    analogWrite(motorPin,200);
  }
}
