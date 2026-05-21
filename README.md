# IoT MQTT TLS (ESP32)

Proyecto base para ESP32 con MQTT seguro (TLS), provisión Wi‑Fi por portal AP, y actualizaciones OTA vía GitHub Actions.

## 🚀 Quick Start

### Requisitos
- **PlatformIO**: extensión de VS Code o CLI (`pip install platformio`)
- **Python 3.7+**
- **ESP32** conectado por USB

### Configuración inicial

1. **Fork y clonar el repositorio**
   ```bash
   git clone https://github.com/<tu-usuario>/iot-mqtt-tls.git
   cd iot-mqtt-tls
   ```

2. **Habilitar GitHub Actions** (si usarás OTA)
   - Ve a tu fork en GitHub → tab **Actions**
   - Haz clic en **"I understand my workflows…"** → **Enable**
   - Configura los Secrets (ver [SECRETS_SETUP.md](SECRETS_SETUP.md))

3. **Crear archivo `.env`** en la raíz del proyecto:
   ```ini
   COUNTRY=colombia
   STATE=valle
   CITY=tulua
   MQTT_SERVER=mqtt.tu-dominio.com
   MQTT_PORT=8883
   MQTT_USER=miuser
   MQTT_PASSWORD=supersecreto
   WIFI_SSID=MiWiFiInicial
   WIFI_PASSWORD=MiPassInicial
   ```
   > **Nota**: `ROOT_CA` usa el valor por defecto del código. Si necesitas cambiarlo, edita `src/secrets.cpp`. `WIFI_SSID/PASSWORD` son opcionales (se puede configurar luego por el portal AP).

4. **Compilar y subir al ESP32**
   
   **Opción simple (recomendada):**
   ```bash
   python scripts/build_with_env.py upload
   ```
   
   **Opción manual:**
   ```bash
   set -a && source .env && set +a
   pio run -t upload
   ```

5. **Configurar Wi‑Fi** (primera vez o para cambiar)
   - El dispositivo crea un AP: busca `ESP32-Setup-XXXXXX` y conéctate
   - Abre el navegador en `http://192.168.4.1`
   - Ingresa SSID y contraseña → **Guardar**
   - El dispositivo se reinicia y se conecta automáticamente
   - Las credenciales se guardan en NVS (persisten tras OTA)

   **Para reconfigurar**: mantén presionado el botón **BOOT (GPIO0)** durante 3+ segundos al encender.

✅ **Listo**: el dispositivo se conecta a Wi‑Fi y MQTT. Las credenciales persisten tras actualizaciones OTA.

## 📦 Actualización OTA (opcional)

Para enviar actualizaciones OTA a los dispositivos:

1. **Commit y push** de tus cambios:
   ```bash
   git add .
   git commit -m "feat: mejora X"
   git push origin main
   ```

2. **Crear tag de versión** (formato `vX.Y.Z`):
   ```bash
   git tag v1.2.0
   git push origin v1.2.0
   ```

3. GitHub Actions automáticamente:
   - Compila el firmware
   - Sube el binario a S3 con nombre `firmware_v1.2.0.bin`
   - Publica mensaje MQTT al tópico OTA definido en `src/libota.h` (por defecto: `dispositivo/device1/ota`)

Los dispositivos suscritos recibirán la actualización automáticamente.

## 🔧 Troubleshooting

| Problema | Solución |
|----------|----------|
| Portal no abre | Escribe manualmente `http://192.168.4.1` y desactiva datos móviles |
| No aparece el AP | Apaga/enciende el dispositivo o mantén BOOT 3+ s al encender |
| Error ROOT_CA | Asegúrate de que está en una sola línea con `\n` entre líneas |
| OTA no llega | Revisa que los Secrets de GitHub estén configurados y que el dispositivo esté suscrito al tópico OTA |

## 📚 Documentación

### Configuración básica
- **[SECRETS_SETUP.md](SECRETS_SETUP.md)** - Configurar `.env` y GitHub Secrets
- **[WIFI_SETUP.md](WIFI_SETUP.md)** - Guía detallada del portal de configuración Wi‑Fi
- **[WINDOWS_SETUP.md](WINDOWS_SETUP.md)** - Instrucciones específicas para Windows

### Funcionalidades avanzadas
- **[OTA_SETUP.md](OTA_SETUP.md)** - Guía completa de actualizaciones OTA

### Infraestructura
- **Configuración EMQX ACL** - Consulta la Wiki del repositorio para la configuración completa de Access Control List en EMQX. La documentación extensa de ACL (múltiples métodos y ejemplos) está disponible en la Wiki de GitHub para facilitar su actualización.

## 🏗️ Estructura del proyecto

```
├── src/              # Código fuente
│   ├── main.cpp      # Punto de entrada
│   ├── libiot.*      # Cliente MQTT con TLS
│   ├── libwifi.*     # Gestión Wi‑Fi
│   ├── libota.*      # Actualizaciones OTA
│   ├── libprovision.* # Portal de configuración AP
│   └── libstorage.*  # Persistencia en NVS
├── scripts/          # Scripts de build
├── .github/workflows/ # GitHub Actions
└── platformio.ini    # Configuración PlatformIO
```

## 📝 Licencias

Este proyecto está bajo la licencia MIT. Ver [LICENSE](LICENSE) para más detalles.
