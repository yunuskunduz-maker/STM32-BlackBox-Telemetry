# STM32 Based SPI Telemetry System Verified with Logic Analyzer 🚀

## 📋 Proje Özeti (Project Overview)
Bu proje, **STM32F103 (Nucleo-64)** mikrodenetleyicisi kullanılarak geliştirilmiş, gömülü sistemler için bir **SPI tabanlı telemetri vericisi** prototipidir.

Sistem, dış dünyadan aldığı analog verileri (Potansiyometre) okur, bunları özel bir veri yapısı (struct) içinde paketler ve **SPI (Serial Peripheral Interface)** protokolü üzerinden yüksek hızda iletir. Projenin güvenilirliği ve veri bütünlüğü, **Logic Analyzer (Lojik Analizör)** kullanılarak sinyal seviyesinde doğrulanmıştır.

## 🛠️ Kullanılan Donanım ve Yazılım (Tech Stack)
* **MCU:** STM32 Nucleo-F103RB (ARM Cortex-M3)
* **Haberleşme:** SPI (Full Duplex Mode)
* **Sensör:** 10K Potansiyometre (Analog Giriş - ADC)
* **Doğrulama Aracı:** 24MHz 8-Channel Logic Analyzer & Sigrok PulseView
* **IDE:** STM32CubeIDE

## 📈 Geliştirme ve Doğrulama Süreci (Development Process)
Bu proje, sadece kod yazmaktan ibaret olmayıp, adım adım donanım doğrulama metodolojisi (Iterative Hardware Verification) izlenerek geliştirilmiştir:

### 1. Aşama: Sinyal Doğrulaması (Signal Verification) 📡
İlk olarak STM32'nin SPI çevre birimi (Peripheral) ayağa kaldırıldı. Rastgele veri yerine, bilinen test desenleri (`0xAA`) gönderilerek Logic Analyzer üzerinde **MOSI (Data)** ve **SCK (Clock)** hatlarının zamanlaması doğrulandı. Clock Polarity (CPOL) ve Phase (CPHA) ayarları analizör ile optimize edildi.

### 2. Aşama: Donanım El Sıkışması (Hardware Handshake) 🤝
Sisteme **nRF24L01** kablosuz modülü entegre edildi. Modülün `STATUS` register'ı okunarak **MISO (Master In Slave Out)** hattının çalışırlığı test edildi. Logic Analyzer ile modülden gelen cevaplar (`0x0E` vb.) yakalanarak fiziksel bağlantının sağlamlığı onaylandı.

### 3. Aşama: Canlı Telemetri (Live Data Telemetry) 🎛️
Sisteme analog sensör (Potansiyometre) entegre edildi. ADC üzerinden okunan ham veri, buton durumu ve paket sayacı ile birleştirilerek profesyonel bir veri paketi oluşturuldu:

```c
typedef struct {
  uint16_t pot_val;   // 12-bit ADC Değeri (0-4095)
  uint8_t  btn_state; // Buton Durumu
  uint8_t  counter;   // Paket Sayacı (Canlılık Testi)
} TelemetryPacket;
