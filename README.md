# Flood-alert-system-using-Arduino-UNO-

include <SoftwareSerial.h>
#define sensorPin A0
#define greenLED 5
#define yellowLED 6
#define redLED 7
#define buzzer 8
SoftwareSerial sim800c(9, 10); // RX, TX for SIM800C
bool alertSent = false; // Prevent multiple calls
bool smsSent = false; // Prevent multiple SMS
void setup() {
pinMode(sensorPin, INPUT);
pinMode(greenLED, OUTPUT);
pinMode(yellowLED, OUTPUT);
pinMode(redLED, OUTPUT);
pinMode(buzzer, OUTPUT);
Serial.begin(9600);
sim800c.begin(115200); // Check if your module uses 9600 or 115200
delay(2000);
// Check module response
sim800c.println("AT");
delay(1000);
if (sim800c.available()) {
Serial.println(sim800c.readString());
}
{
void makeCall() {
sim800c.println("ATD+9344503856;"); // Replace with your phone number
delay(15000); // Wait 15 seconds for call
sim800c.println("ATH"); // Hang up
}
void sendSMS() {
Serial.println("Sending SMS...");
sim800c.println("AT+CMGF=1"); // Set SMS mode to text
delay(1000);
sim800c.println("AT+CMGS=\"+9344503856\""); // Replace with recipient's number delay(1000);
sim800c.print("WARNING: Water level is at medium level.");
delay(1000);
sim800c.write(26); // ASCII code for Ctrl+Z
Serial.println("SMS Sent!");
}
void loop() {
int waterLevel = analogRead(sensorPin);
Serial.println(waterLevel);
if (waterLevel 300) { // Low level
digitalWrite(greenLED, HIGH);
digitalWrite(yellowLED, LOW);
digitalWrite(redLED, LOW);
digitalWrite(buzzer, LOW);
alertSent = false; // Reset alert
smsSent = false; // Reset SMS alert
} else if (waterLevel>= 300 &&waterLevel<600) { // Medium level
digitalWrite(greenLED, LOW);
digitalWrite(yellowLED, HIGH);
digitalWrite(redLED, LOW);
digitalWrite(buzzer, LOW);
if (!smsSent) {
sendSMS();
smsSent = true; // Prevent multiple SMS
}
alertSent = false; // Reset call alert
}
else { // High water level (Flood alert)
digitalWrite(greenLED, LOW);
digitalWrite(yellowLED, LOW);
