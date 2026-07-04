# ESPHome CYD Energy Distribution Panel - Technical Constraints

## Hardware Foundation (From Template)
- **Board:** `esp32dev` (ESP32-2432S028R / Cheap Yellow Display)
- **Display Driver:** `ili9xxx` (Model: ILI9341) via native ESPHome SPI.
- **SPI Pins:** CLK (GPIO14), MOSI (GPIO13), MISO (GPIO12).
- **Display Pins:** CS (GPIO15), DC (GPIO2).
- **Backlight:** LEDC PWM on GPIO21.
- **Resolution:** 320x240 (Landscape).

## Software Architecture: ESPHome Native Display Lambdas
1. **Data Ingestion:** Import Home Assistant sensors using the `homeassistant` platform.
2. **UI Framework:** Strictly use native ESPHome C++ drawing commands (`it.line`, `it.circle`, `it.printf`, `it.filled_circle`) inside the display `lambda`. Do not use LVGL.
3. **Layout Mapping (320x240):**
   - Top-Left: Low-Carbon / Grid Return
   - Top-Right: Solar
   - Bottom-Left: Grid Import
   - Bottom-Right: Home Consumption
4. **Animation Logic (C++):** - Since native lambdas execute sequentially on every display update, animations must be driven by a `static float` tick counter within the lambda.
   - Use the tick counter and basic math to interpolate X/Y coordinates for dots moving along the lines between nodes.
   - Adjust the speed (tick increment size) or hide the dots entirely based on the raw sensor values.
5. **Aesthetics:** Use a black background (`000000`) and standard Home Assistant entity colors (Orange for Solar, Blue for Grid, Red/Orange for Home, Green for Low-Carbon/Export).