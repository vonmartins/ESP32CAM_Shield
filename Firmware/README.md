# Firmware — Cámara de vigilancia ESP32-CAM + Home Assistant

Firmware basado en **ESPHome** para una **AI-Thinker ESP32-CAM** (ESP32 + OV2640),
integrada en Home Assistant mediante la **API nativa** (auto-discovery, sin MQTT).

## Qué hace

- Expone la cámara como entidad `camera.` en HA → **stream en directo desde HA**.
- Servidor web en la placa: stream MJPEG (`:8080`) y snapshot (`:8081`).
- LED de flash blanco (GPIO4) regulable desde HA + LED rojo de estado (GPIO33).
- Sensores: señal WiFi, uptime, temperatura interna, IP, SSID, versión, estado.
- Botón de reinicio y actualizaciones OTA por WiFi.

## Requisitos

- [ESPHome](https://esphome.io/) instalado (CLI o add-on de HA):
  ```bash
  pip install esphome
  ```

## Puesta en marcha

1. **Crea tu `secrets.yaml`:**
   ```bash
   cp secrets.yaml.example secrets.yaml
   ```
   Edítalo con tu WiFi. Genera la clave de la API y el password OTA
   (ver comentarios dentro del archivo). `secrets.yaml` está en `.gitignore`.

2. **Primer flasheo por USB** (conecta la placa por USB):
   ```bash
   esphome run esp32cam-surveillance.yaml
   ```
   Elige el puerto serie cuando lo pregunte. A partir de aquí las actualizaciones
   ya van por **OTA** (WiFi), no hace falta volver a enchufar el USB.

3. **En Home Assistant:** aparece sola en *Ajustes → Dispositivos y servicios*
   como dispositivo ESPHome nuevo. Pulsa **Configurar**. Ya tendrás la tarjeta
   de cámara con el stream.

## Ajustes útiles

En `esp32cam-surveillance.yaml`, bloque `esp32_camera:`:

- `resolution`: sube a `1024x768` o `1280x1024` si quieres más detalle
  (a costa de fluidez y ancho de banda).
- `jpeg_quality`: 10–63; **más bajo = mejor calidad**.
- `max_framerate`: fps máximos cuando alguien está mirando.
- `vertical_flip` / `horizontal_mirror`: si montas la cámara del revés.

## Notas de hardware

- Pinout configurado para la **AI-Thinker ESP32-CAM** (`board: esp32dev`).
- El flasheo se hace con la placa base **ESP32-CAM-MB** (USB). Tiene auto-reset,
  así que normalmente **no hace falta modo boot**. Si `esptool` no conecta:
  mantén pulsado **IO0/BOOT**, pulsa **RST**, suelta IO0, y reintenta.
- LED de flash blanco en **GPIO4** (ojo: comparte pin con el bus de la microSD;
  si algún día usas la SD, tenlo en cuenta).
- La PSRAM (4MB) la habilita sola el componente de cámara.
