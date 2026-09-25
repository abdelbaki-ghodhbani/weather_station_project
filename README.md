# Connected Weather Station

An IoT weather station built on an **STM32F401RE** that measures **temperature and humidity** with a **DHT11** sensor and uploads the readings to the **ThingSpeak** cloud through an **ESP8266** Wi-Fi module. You can then view the data as live charts from anywhere.

## How it works

```
DHT11 ──1-wire (PA1)──► STM32F401RE ──USART1/AT──► ESP8266 ──HTTP──► ThingSpeak cloud dashboard
```

1. The STM32 reads the DHT11 over its single-wire protocol, using microsecond delays for the timing.
2. It sends temperature and humidity to ThingSpeak as two fields in one HTTP `GET /update` request.
3. It uploads again about every 15 s, which stays within ThingSpeak's free-tier rate limit.

## Features

- DHT11 driver built on STM32 HAL with microsecond timing
- ESP8266 AT-command data logger that uploads several fields in one request
- Interrupt-driven UART ring buffer for reliable communication with the ESP module
- Project generated with STM32CubeMX (`.ioc` included) for easy reconfiguration

## Hardware

| Component | Connection |
|---|---|
| NUCLEO-F401RE | Main MCU |
| DHT11 | Data → PA1 |
| ESP8266 module | USART1 (PA9 TX / PA10 RX), 3.3 V |

## Getting started

1. Create a free channel on [ThingSpeak](https://thingspeak.com) with **2 fields** (Temperature, Humidity) and copy its **Write API Key**.
2. Open `tpe_weatherstation` in **STM32CubeIDE**.
3. In `Core/Src/main.c`, fill in:
   ```c
   #define WIFI_SSID          "YOUR_WIFI_SSID"
   #define WIFI_PASSWORD      "YOUR_WIFI_PASSWORD"
   #define THINGSPEAK_API_KEY "YOUR_THINGSPEAK_WRITE_KEY"
   ```
4. Build, flash and watch the charts update on your ThingSpeak channel.

## Tech stack

`Embedded C` · `STM32 HAL` · `STM32CubeMX` · `ESP8266` · `IoT` · `ThingSpeak` · `UART`

## Credits

The DHT11, UART ring buffer and ESPDataLogger modules are adapted from [ControllersTech](https://controllerstech.com) tutorials.

## Author

**Abdelbaki Ghodhbani** — [LinkedIn](https://www.linkedin.com/in/abdelbaki-ghodhbani) · [GitHub](https://github.com/abdelbaki-ghodhbani)
