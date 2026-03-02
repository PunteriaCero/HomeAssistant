# 🎬 Scripts ADB Mejorados - Referencia Completa

**Versión**: 3.0 - Con Retorno de Respuestas JSON  
**Última actualización**: 2024  
**Estado**: ✅ LISTO PARA PRODUCCIÓN

---

## 📋 Índice de Scripts

| Script | Función | Acciones | Status |
|--------|---------|----------|--------|
| `adb_device` | Gestión de dispositivos | connect, disconnect, list, status, health | ✅ v3.0 |
| `adb_info` | Información del dispositivo | info, current_app, installed_apps, logcat | ✅ v3.0 |
| `adb_volume` | Control de volumen | get, up, down, set, mute | ✅ v3.0 |
| `adb_media` | Control de reproducción | play, pause, exit | ✅ v3.0 |
| `adb_system` | Operaciones del sistema | screenshot, command | ✅ v3.0 |

---

## 🚀 Script: adb_device

**Gestión de dispositivos ADB**

### Acciones Disponibles

#### 1. CONNECT - Conectar a un dispositivo

```yaml
service: script.adb_device
data:
  action: "connect"
  device_ip: "192.168.0.163"
```

**Respuesta Retornada:**
```yaml
device_result:
  timestamp: "2024-03-01T15:30:45..."
  action: "connect"
  device_ip: "192.168.0.163"
  status: "completed"
  response: {
    "status": "connected",
    "ip": "192.168.0.163"
  }
```

#### 2. DISCONNECT - Desconectar un dispositivo

```yaml
service: script.adb_device
data:
  action: "disconnect"
  device_ip: "192.168.0.163"
```

#### 3. LIST - Listar dispositivos

```yaml
service: script.adb_device
data:
  action: "list"
```

**Respuesta:**
```yaml
device_result:
  response: [
    {
      "ip": "192.168.0.163",
      "connected": true,
      "model": "AFFT"
    }
  ]
  count: 1
```

#### 4. STATUS - Obtener estado

```yaml
service: script.adb_device
data:
  action: "status"
  device_ip: "192.168.0.163"
```

#### 5. HEALTH - Verificar salud de la API

```yaml
service: script.adb_device
data:
  action: "health"
```

---

## 🎬 Script: adb_media

**Control de reproducción de medios**

### Acciones Disponibles

#### 1. PLAY - Reproducir video

```yaml
service: script.adb_media
data:
  action: "play"
  device_ip: "192.168.0.163"
  video_url: "https://www.youtube.com/watch?v=VIDEO_ID"
```

**Respuesta Retornada:**
```yaml
media_result:
  timestamp: "2024-03-01T15:30:45..."
  action: "play"
  device_ip: "192.168.0.163"
  video_url: "https://www.youtube.com/watch?v=..."
  status: "completed"
  response: {
    "status": "playing",
    "app": "YouTube"
  }
```

#### 2. PAUSE - Pausar reproducción

```yaml
service: script.adb_media
data:
  action: "pause"
  device_ip: "192.168.0.163"
```

#### 3. EXIT - Cerrar aplicación

```yaml
service: script.adb_media
data:
  action: "exit"
  device_ip: "192.168.0.163"
```

---

## 🔊 Script: adb_volume

**Control de volumen del dispositivo**

### Acciones Disponibles

#### 1. GET - Obtener volumen actual

```yaml
service: script.adb_volume
data:
  action: "get"
  device_ip: "192.168.0.163"
```

**Respuesta:**
```yaml
volume_result:
  status: "completed"
  response: {
    "level": 8,
    "max": 15,
    "muted": false
  }
```

#### 2. UP - Aumentar volumen

```yaml
service: script.adb_volume
data:
  action: "up"
  device_ip: "192.168.0.163"
  steps: 2  # Opcional, default: 1
```

**Respuesta:**
```yaml
volume_result:
  steps: 2
  response: {
    "level": 10,
    "previous_level": 8
  }
```

#### 3. DOWN - Disminuir volumen

```yaml
service: script.adb_volume
data:
  action: "down"
  device_ip: "192.168.0.163"
  steps: 3
```

#### 4. SET - Establecer nivel exacto

```yaml
service: script.adb_volume
data:
  action: "set"
  device_ip: "192.168.0.163"
  steps: 12  # Nivel de 0-15
```

#### 5. MUTE - Silenciar

```yaml
service: script.adb_volume
data:
  action: "mute"
  device_ip: "192.168.0.163"
```

---

## ℹ️ Script: adb_info

**Obtener información del dispositivo**

### Acciones Disponibles

#### 1. INFO - Información del dispositivo

```yaml
service: script.adb_info
data:
  action: "info"
  device_ip: "192.168.0.163"
```

**Respuesta:**
```yaml
info_result:
  response: {
    "model": "AFFT",
    "android_version": "5.1.1",
    "build": "LRX22H",
    "ip": "192.168.0.163"
  }
```

#### 2. CURRENT_APP - App actual

```yaml
service: script.adb_info
data:
  action: "current_app"
  device_ip: "192.168.0.163"
```

**Respuesta:**
```yaml
info_result:
  response: {
    "app_name": "YouTube",
    "package_name": "com.google.android.youtube"
  }
```

#### 3. INSTALLED_APPS - Apps instaladas

```yaml
service: script.adb_info
data:
  action: "installed_apps"
  device_ip: "192.168.0.163"
```

**Respuesta:**
```yaml
info_result:
  response: [
    {
      "name": "YouTube",
      "package": "com.google.android.youtube"
    },
    {
      "name": "Amazon Prime Video",
      "package": "com.amazon.veneziano"
    }
  ]
  count: 45
```

#### 4. LOGCAT - Logs del sistema

```yaml
service: script.adb_info
data:
  action: "logcat"
  device_ip: "192.168.0.163"
  lines: 30  # Opcional, default: 50
```

---

## 💻 Script: adb_system

**Operaciones del sistema**

### Acciones Disponibles

#### 1. SCREENSHOT - Captura de pantalla

```yaml
service: script.adb_system
data:
  action: "screenshot"
  device_ip: "192.168.0.163"
```

#### 2. COMMAND - Comando personalizado

```yaml
service: script.adb_system
data:
  action: "command"
  device_ip: "192.168.0.163"
  command: "pm list packages"
```

**Respuesta:**
```yaml
system_result:
  command: "pm list packages"
  response: [
    "package:com.google.android.youtube",
    "package:com.amazon.veneziano",
    ...
  ]
```

---

## 🔄 Usar Respuestas en Otros Scripts

### Opción 1: Capturar respuesta_variable

```yaml
- action: script.adb_volume
  data:
    action: "get"
    device_ip: "192.168.0.163"
  response_variable: volume_check

# Acceder a los resultados:
- action: notify.notify
  data:
    message: |
      Nivel actual: {{ volume_check.volume_result.response.level }}/15
      IP: {{ volume_check.volume_result.device_ip }}
```

### Opción 2: Filtrar en Automations

```yaml
- action: script.adb_media
  data:
    action: "play"
    device_ip: "192.168.0.163"
    video_url: "https://www.youtube.com/watch?v=..."
  response_variable: play_result

- choose:
  - conditions:
      - condition: template
        value_template: "{{ play_result.media_result.status == 'completed' }}"
    sequence:
      - action: notify.notify
        data:
          message: "✅ Video reproduciendo correctamente"
  default:
    - action: notify.notify
      data:
        message: "❌ Error al reproducir"
```

### Opción 3: Guardar en Variables Helper

```yaml
- action: script.adb_info
  data:
    action: "current_app"
    device_ip: "192.168.0.163"
  response_variable: app_check

- action: input_text.set_value
  target:
    entity_id: input_text.firestick_current_app
  data:
    value: "{{ app_check.info_result.response.app_name }}"
```

---

## 📊 Estructura Común de Respuesta

Todos los scripts retornan una estructura similar:

```yaml
{script_name}_result:
  timestamp: "2024-03-01T15:30:45.123456"  # ISO format
  action: "acción_realizada"                # Nombre de la acción
  device_ip: "192.168.0.163"                # IP del dispositivo
  status: "completed" | "error"             # Estado
  response: {}                              # Respuesta JSON del endpoint
  error: null | "descripción_error"         # Error si aplica
  # Campos opcionales según la acción:
  command: "..."                            # Para system command
  steps: 2                                  # Para volume up/down
  level: 12                                 # Para volume set
  video_url: "..."                          # Para media play
  count: 45                                 # Para listas
  lines: 30                                 # Para logcat
```

---

## 🎯 Ejemplos de Uso Completo

### Ejemplo 1: Reproducir video y ajustar volumen

```yaml
# Reproducir Video
- action: script.adb_media
  data:
    action: "play"
    device_ip: "192.168.0.163"
    video_url: "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
  response_variable: play_result

# Esperar a que inicie
- delay:
    seconds: 2

# Ajustar volumen a nivel 10
- action: script.adb_volume
  data:
    action: "set"
    device_ip: "192.168.0.163"
    steps: 10
  response_variable: volume_result

# Notificar resultado
- action: notify.notify
  data:
    message: |
      Video: {{ play_result.media_result.status }}
      Volumen: {{ volume_result.volume_result.response.level }}/15
```

### Ejemplo 2: Chequear dispositivo y reportar

```yaml
- action: script.adb_device
  data:
    action: "status"
    device_ip: "192.168.0.163"
  response_variable: device_check

- action: script.adb_info
  data:
    action: "current_app"
    device_ip: "192.168.0.163"
  response_variable: app_check

- action: notify.notify
  data:
    title: "📊 Reporte del Fire Stick"
    message: |
      Estado: {{ device_check.device_result.response.status }}
      App: {{ app_check.info_result.response.app_name }}
      IP: {{ device_check.device_result.device_ip }}
      Timestamp: {{ device_check.device_result.timestamp }}
```

### Ejemplo 3: Automatización condicional

```yaml
- action: script.adb_info
  data:
    action: "current_app"
    device_ip: "192.168.0.163"
  response_variable: current_app

- choose:
  - conditions:
      - condition: template
        value_template: "{{ 'YouTube' in current_app.info_result.response.app_name }}"
    sequence:
      # Si está en YouTube, aumentar volumen
      - action: script.adb_volume
        data:
          action: "up"
          device_ip: "192.168.0.163"
          steps: 2
  - conditions:
      - condition: template
        value_template: "{{ 'Plex' in current_app.info_result.response.app_name }}"
    sequence:
      # Si está en Plex, mantener volumen bajo
      - action: script.adb_volume
        data:
          action: "set"
          device_ip: "192.168.0.163"
          steps: 7
```

---

## 🔧 Manejo de Errores

### Validación de respuestas

```yaml
- action: script.adb_media
  data:
    action: "play"
    device_ip: "192.168.0.163"
    video_url: "https://www.youtube.com/watch?v=..."
  response_variable: play_result

- choose:
  - conditions:
      - condition: template
        value_template: "{{ play_result.media_result.status == 'error' }}"
    sequence:
      - action: notify.notify
        data:
          title: "❌ Error"
          message: "{{ play_result.media_result.error }}"
  - conditions:
      - condition: template
        value_template: "{{ play_result.media_result.status == 'completed' }}"
    sequence:
      - action: notify.notify
        data:
          title: "✅ Éxito"
          message: "Video reproduciendo"
```

---

## 📞 Referencia Rápida

```bash
# Reproducir video
service: script.adb_media
data: {action: "play", device_ip: "192.168.0.163", video_url: "URL"}

# Volumen
service: script.adb_volume
data: {action: "set", device_ip: "192.168.0.163", steps: 10}

# Info del dispositivo
service: script.adb_info
data: {action: "current_app", device_ip: "192.168.0.163"}

# Gestión
service: script.adb_device
data: {action: "status", device_ip: "192.168.0.163"}

# Sistema
service: script.adb_system
data: {action: "screenshot", device_ip: "192.168.0.163"}
```

---

**Estado**: ✅ Todos los scripts mejorados a v3.0  
**Última actualización**: 2024  
**Compatibilidad**: Home Assistant 2024+  
**Próxima mejora**: Logging centralizado de respuestas
