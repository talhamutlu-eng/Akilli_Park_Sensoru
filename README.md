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

