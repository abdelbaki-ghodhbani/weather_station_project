# Connected Weather Station

An IoT weather station built on the **NUCLEO-F401RE** board (STM32F401RE). It measures **temperature and humidity** with a **DHT11** sensor and uploads the readings to the **ThingSpeak** cloud through an **ESP8266** Wi-Fi module, so you can follow the data as live charts from anywhere.

> The project is generated with **STM32CubeMX / STM32CubeIDE** and uses the **STM32 HAL**. The pin and clock configuration (`tpe_weatherstation.ioc`) targets the **NUCLEO-F401RE**. For another board, recreate the `.ioc` for that MCU, keeping the same pin functions.

## How it works

```
DHT11 ──single-wire (PA1)──► STM32F401RE ──USART1 / AT──► ESP8266 ──HTTP──► ThingSpeak cloud dashboard
```

1. The STM32 reads the DHT11 over its single-wire protocol. The microsecond timing uses the Cortex-M4 **DWT cycle counter**.
2. The ESP8266 joins the Wi-Fi network in station mode (`AT+CWMODE=1`, `AT+CWJAP`).
3. Temperature (°C) and humidity (%RH) go to ThingSpeak as **field1** and **field2** in one HTTP `GET /update` request (`AT+CIPSTART` / `AT+CIPSEND`).
4. The station uploads again about every **16 s**, which stays within ThingSpeak's free-tier limit of one update every 15 s.

## Hardware configuration (NUCLEO-F401RE)

| Parameter | Value |
|---|---|
| System clock | **84 MHz**: PLL from the 8 MHz HSE bypass supplied by the ST-LINK (M = 4, N = 84, P = 2) |
| DHT11 data | **PA1** (GPIO, switched between output and input by the driver) |
| ESP8266 | **USART1**: PA9 TX / PA10 RX, 115200 baud 8N1, interrupt-driven ring buffer |
| Debug UART | USART2: PA2 / PA3 (ST-LINK virtual COM port), 115200 baud |

| Component | Connection |
|---|---|
| DHT11 | VCC 3.3 V, DATA → PA1 (with a pull-up), GND |
| ESP8266 module | TX → PA10, RX → PA9, VCC and EN → 3.3 V, GND |

## Getting started

1. Create a free channel on [ThingSpeak](https://thingspeak.com) with **2 fields** (Temperature, Humidity) and copy its **Write API Key**.
2. Open `tpe_weatherstation` in **STM32CubeIDE** (*File → Import → Existing Projects into Workspace*).
3. In `Core/Src/main.c`, fill in:
   ```c
   #define WIFI_SSID          "YOUR_WIFI_SSID"
   #define WIFI_PASSWORD      "YOUR_WIFI_PASSWORD"
   #define THINGSPEAK_API_KEY "YOUR_THINGSPEAK_WRITE_KEY"
   ```
4. Build, flash to the NUCLEO-F401RE and watch the charts update on your ThingSpeak channel.

## Tech stack

`Embedded C` · `STM32 HAL` · `STM32CubeMX` · `DWT timing` · `ESP8266 AT commands` · `UART interrupts` · `IoT` · `ThingSpeak`

## Credits

The DHT11, UART ring buffer and ESPDataLogger modules are adapted from [ControllersTech](https://controllerstech.com) tutorials.

## Author

**Abdelbaki Ghodhbani**, Embedded Software Engineer: [LinkedIn](https://www.linkedin.com/in/abdelbaki-ghodhbani) · [GitHub](https://github.com/abdelbaki-ghodhbani)
