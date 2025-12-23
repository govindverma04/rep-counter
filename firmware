// ================= PIN DEFINITIONS =================

// 7-segment pins: A B C D E F G
int segPins[7] = {13, 12, 14, 27, 26, 25, 33};

// Digit select pins (COMMON ANODE)
int digitPins[2] = {32, 15};

// Ultrasonic sensor
#define TRIG_PIN 5
#define ECHO_PIN 18

// ================= DIGIT MAP =================
// Order: A B C D E F G
byte digitMap[10][7] = {
  {1,1,1,1,1,1,0}, // 0
  {0,1,1,0,0,0,0}, // 1
  {1,1,0,1,1,0,1}, // 2
  {1,1,1,1,0,0,1}, // 3
  {0,1,1,0,0,1,1}, // 4
  {1,0,1,1,0,1,1}, // 5
  {1,0,1,1,1,1,1}, // 6
  {1,1,1,0,0,0,0}, // 7
  {1,1,1,1,1,1,1}, // 8
  {1,1,1,1,0,1,1}  // 9
};

// ================= GLOBAL VARIABLES =================

volatile int displayValue = 0;
volatile bool currentDigit = false;

int repCount = 0;
bool zoneLocked = false;

float lastValidDistance = -1;
int stableCount = 0;

const int STABLE_REQUIRED = 2;   // number of stable readings required

hw_timer_t *timer = NULL;

// ================= TIMER ISR =================

void IRAM_ATTR onTimer() {
  // turn OFF both digits
  digitalWrite(digitPins[0], LOW);
  digitalWrite(digitPins[1], LOW);

  int value = displayValue;
  int digitVal = currentDigit ? value % 10 : value / 10;

  // COMMON ANODE → inverted segment logic
  for (int i = 0; i < 7; i++) {
    digitalWrite(segPins[i], !digitMap[digitVal][i]);
  }

  digitalWrite(digitPins[currentDigit], HIGH);
  currentDigit = !currentDigit;
}

// ================= ULTRASONIC READ =================

float readDistanceStable() {
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  long duration = pulseIn(ECHO_PIN, HIGH, 50000); // 50 ms timeout

  if (duration == 0) return -1;

  float cm = duration * 0.034 / 2;

  if (cm < 5 || cm > 400) return -1;

  return cm;
}

// ================= SETUP =================

void setup() {
  Serial.begin(115200);
  delay(500);
  Serial.println("ESP32 Rep Counter Started");

  // segment pins
  for (int i = 0; i < 7; i++) {
    pinMode(segPins[i], OUTPUT);
    digitalWrite(segPins[i], HIGH); // OFF
  }

  // digit pins
  for (int i = 0; i < 2; i++) {
    pinMode(digitPins[i], OUTPUT);
    digitalWrite(digitPins[i], LOW); // OFF
  }

  // ultrasonic pins
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);

  // hardware timer (ESP32 core 3.x)
  timer = timerBegin(1000000);        // 1 MHz clock
  timerAttachInterrupt(timer, &onTimer);
  timerAlarm(timer, 1000, true, 0);   // 1 ms refresh
}

// ================= LOOP =================

void loop() {
  static unsigned long lastRead = 0;

  if (millis() - lastRead >= 250) {   // slow reading
    lastRead = millis();

    float dist = readDistanceStable();

    Serial.print("Raw: ");
    Serial.print(dist);
    Serial.print(" cm | ");

    if (dist > 0) {
      lastValidDistance = dist;
      stableCount++;
      Serial.print("VALID ");
    } else {
      Serial.print("IGNORED ");
    }

    // -------- only act on stable readings --------
    if (stableCount >= STABLE_REQUIRED) {
      Serial.print("| STABLE: ");
      Serial.print(lastValidDistance);
      Serial.print(" cm | ");

      // ACTIVE ZONE (50–100 cm)
      if (lastValidDistance >= 30 && lastValidDistance <= 80) {
        if (!zoneLocked) {
          repCount++;
          if (repCount > 99) repCount = 0;
          zoneLocked = true;
          Serial.print("COUNT++ ");
        } else {
          Serial.print("LOCKED ");
        }
      }
      // EXIT ZONE (>130 cm)
      else if (lastValidDistance > 110) {
        zoneLocked = false;
        Serial.print("FREE ");
      }

      stableCount = 0; // reset after decision
    }

    Serial.print("| Reps: ");
    Serial.println(repCount);

    displayValue = repCount;
  }
}
