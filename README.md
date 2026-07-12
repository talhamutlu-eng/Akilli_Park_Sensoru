# Akilli_Park_Sensoru
# ESP32 Tabanlı Akıllı Park Sensörü Sistemi

Bu proje, **ESP32** mikrodenetleyicisi ve **HC-SR04 ultrasonik mesafe sensörü** kullanılarak tasarlanmış, görsel ve işitsel geri bildirim sağlayan akıllı bir park yardım sistemidir. Sistem, nesnelerin yakınlığına göre dinamik olarak tepki vermektedir.

## 🚀 Sistem Özellikleri
* **Mesafe Ölçümü:** HC-SR04 ultrasonik sensör ile 2 cm ile 400 cm arasındaki engeller yüksek hassasiyetle tespit edilir.
* **Görsel Geri Bildirim:** 16x2 I2C LCD ekran üzerinde anlık mesafe verisi (cm cinsinden) yazdırılır. Ayrıca Yeşil, Sarı ve Kırmızı LED'ler ile kademeli uyarı verilir.
* **İşitsel Geri Bildirim (Buzzer):** Mesafe azaldıkça bip seslerinin frekansı ve sıklığı dinamik algoritma ile hızlanarak sürücüyü uyarır.

## 🎛️ Donanım Bağlantıları (Pinout)
* **HC-SR04 Tetikleme (Trig):** GPIO 23
* **HC-SR04 Yankı (Echo):** GPIO 4
* **Buzzer Girişi:** GPIO 13
* **Yeşil LED:** GPIO 19
* **Sarı LED:** GPIO 18
* **Kırmızı LED:** GPIO 5
* **I2C LCD Ekran:** Standart SDA/SCL Pinleri

---

## 💻 Proje Kodu (C++)
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);

const int trigPin   = 23;
const int echoPin   = 4;
const int buzzerPin = 13;
const int ledGreen  = 19;
const int ledYellow = 18;
const int ledRed    = 5;

void setup() {
  pinMode(trigPin,   OUTPUT);
  pinMode(echoPin,   INPUT);
  pinMode(buzzerPin, OUTPUT);
  pinMode(ledGreen,  OUTPUT);
  pinMode(ledYellow, OUTPUT);
  pinMode(ledRed,    OUTPUT);

  lcd.init();
  lcd.backlight();
  lcd.setCursor(0, 0);
  lcd.print("TALHA MUTLU");
}

void loop() {

  // -- 1. Ölçüm --
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  long duration = pulseIn(echoPin, HIGH, 30000); // 30ms timeout
  float distance = (duration * 0.0343) / 2;

  // Hatalı okuma filtresi
  if (distance <= 0 || distance > 400) return;

  // -- 2. LCD --
  lcd.setCursor(0, 1);
  lcd.print("Mesafe: ");
  lcd.print(distance, 1);
  lcd.print(" cm    ");

  // -- 3. LED + Buzzer Mantığı --

  if (distance > 30) {
    // YEŞİL bölge — seyrek ama hissedilir bip (400ms aralık)
    digitalWrite(ledGreen,  HIGH);
    digitalWrite(ledYellow, LOW);
    digitalWrite(ledRed,    LOW);

    tone(buzzerPin, 1000);
    delay(60);           // bip süresi biraz uzatıldı
    noTone(buzzerPin);
    delay(400);          // 1200ms'den 400ms'e düşürüldü

  } else if (distance <= 30 && distance > 15) {
    // SARI bölge — yaklaştıkça hızlanan bip
    digitalWrite(ledGreen,  LOW);
    digitalWrite(ledYellow, HIGH);
    digitalWrite(ledRed,    LOW);

    tone(buzzerPin, 1000);
    delay(80);
    noTone(buzzerPin);
    delay((int)(distance * 10)); // 15cm->150ms, 30cm->300ms

  } else {
    // KIRMIZI bölge — seri / sürekli bip
    digitalWrite(ledGreen,  LOW);
    digitalWrite(ledYellow, LOW);
    digitalWrite(ledRed,    HIGH);

    tone(buzzerPin, 1000);
    delay(60);
    noTone(buzzerPin);

    if (distance <= 8) {
      delay(30);           // 8cm altı: neredeyse sürekli bip
    } else {
      delay((int)(distance * 6));
    }
  }
}
