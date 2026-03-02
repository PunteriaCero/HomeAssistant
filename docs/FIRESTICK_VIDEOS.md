# Fire Stick - Control de Videos y Volumen

## 📺 Descripción General - Scripts Consolidados

⚠️ **CAMBIOS IMPORTANTES**: Los scripts específicos del Fire Stick han sido consolidados en scripts genéricos reutilizables.

### Scripts Anteriores (DEPRECATED):
- ~~`firestick_play_video`~~ → Usar `adb_media` (acción: play)
- ~~`firestick_morning_video`~~ → Usar `adb_media` + `adb_volume`

### Scripts Genéricos (RECOMENDADOS):
1. **`adb_media`** - Controlar reproducción de videos (play, pause, exit)
2. **`adb_volume`** - Ajustar volumen (up, down, set, get, mute)
3. **`adb_device`** - Gestionar conexión del dispositivo

---

## 1️⃣ Script: `adb_media` - Reproducción de Videos

### Descripción
Script genérico para reproducir, pausar o salir de aplicaciones en cualquier dispositivo Android conectado vía ADB.

### Uso Manual
Desde Home Assistant Developer Tools → Services:

```yaml
action: script.adb_media
data:
  action: "play"
  device_ip: "192.168.0.163"
  video_url: "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
```

### Parámetros
| Parámetro | Tipo | Requerido | Valores | Ejemplo |
|-----------|------|-----------|---------|---------|
| `action` | texto | ✅ Sí | play, pause, exit | "play" |
| `device_ip` | texto | ✅ Sí | IP válida | "192.168.0.163" |
| `video_url` | texto | ⚠️ Solo si action="play" | URL | "https://www.youtube.com/watch?v=..." |

### Respuesta Capturada
```json
{
  "timestamp": "2024-01-15T10:30:00+00:00",
  "action": "play",
  "device_ip": "192.168.0.163",
  "status": "completed",
  "response": {
    "message": "Video started successfully",
    "event": "video_started"
  },
  "error": null
}
```

### Flujo de Ejecución (para action="play")
1. **Conectar** al dispositivo Android
2. **Iniciar** YouTube (u aplicación compatible)
3. **Introducir** la URL del video
4. **Esperar** el inicio del video
5. **Capturar** respuesta JSON del endpoint
6. **Notificar** el estado

### Ejemplo: Reproducir Video Matutino
```yaml
- action: script.adb_media
  data:
    action: "play"
    device_ip: "192.168.0.163"
    video_url: "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
  response_variable: morning_video

- action: notify.notify
  data:
    title: "🎬 Video iniciado"
    message: "Estado: {{ morning_video.media_result.status }}"
```

---

## 2️⃣ Script: `adb_volume` - Control de Volumen

### Descripción
Script genérico para controlar el volumen en cualquier dispositivo Android.

### Uso Manual
Desde Home Assistant Developer Tools → Services:

```yaml
action: script.adb_volume
data:
  action: "up"
  device_ip: "192.168.0.163"
  steps: 3
```

### Parámetros
| Parámetro | Tipo | Requerido | Valores | Ejemplo |
|-----------|------|-----------|---------|---------|
| `action` | texto | ✅ Sí | get, up, down, set, mute | "up" |
| `device_ip` | texto | ✅ Sí | IP válida | "192.168.0.163" |
| `steps` | número | ⚠️ Para up/down | 1-15 | 3 |
| `level` | número | ⚠️ Para set | 0-15 | 8 |

### Acciones Disponibles
- **get** - Obtener nivel actual de volumen
- **up** - Aumentar volumen (especificar steps)
- **down** - Disminuir volumen (especificar steps)
- **set** - Establecer volumen a nivel específico (especificar level)
- **mute** - Silenciar dispositivo

### Ejemplo: Aumentar Volumen 3 Pasos
```yaml
- action: script.adb_volume
  data:
    action: "up"
    device_ip: "192.168.0.163"
    steps: 3
  response_variable: volume_result

- action: notify.notify
  data:
    message: "Volumen: {{ volume_result.volume_result.current_level }}"
```

---

## 📋 Ejemplo Completo: Automation Matutina (Consolidada)

Esta es la forma correcta de implementar la reproducción de video matutino con los nuevos scripts genéricos:

```yaml
- id: 'firestick_daily_morning_video'
  alias: "Fire Stick - Reproducir video a las 6 AM"
  triggers:
    - trigger: time
      at: "06:00:00"
  actions:
    # 1. Conectar dispositivo
    - action: script.adb_device
      data:
        action: "connect"
        device_ip: "192.168.0.163"
    
    - delay:
        seconds: 2
    
    # 2. Reproducir video
    - action: script.adb_media
      data:
        action: "play"
        device_ip: "192.168.0.163"
        video_url: "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
      response_variable: morning_video
    
    - delay:
        seconds: 1
    
    # 3. Aumentar volumen
    - action: script.adb_volume
      data:
        action: "up"
        device_ip: "192.168.0.163"
        steps: 3
    
    # 4. Notificar
    - action: notify.notify
      data:
        title: "☀️ Buenos días"
        message: "Video iniciado: {{ morning_video.media_result.status }}"
  
  mode: single
```

---

## 🔄 Migración desde Scripts Antiguos

### Si usabas `firestick_play_video`:
**ANTES:**
```yaml
- action: script.firestick_play_video
  data:
    video_url: "https://www.youtube.com/watch?v=VIDEO_ID"
```

**AHORA:**
```yaml
- action: script.adb_media
  data:
    action: "play"
    device_ip: "192.168.0.163"
    video_url: "https://www.youtube.com/watch?v=VIDEO_ID"
```

### Si usabas `firestick_morning_video`:
**ANTES:**
```yaml
- action: script.firestick_morning_video
```

**AHORA:**
```yaml
- action: script.adb_device
  data:
    action: "connect"
    device_ip: "192.168.0.163"

- delay:
    seconds: 2

- action: script.adb_media
  data:
    action: "play"
    device_ip: "192.168.0.163"
    video_url: "https://www.youtube.com/watch?v=dQw4w9WgXcQ"

- delay:
    seconds: 1

- action: script.adb_volume
  data:
    action: "up"
    device_ip: "192.168.0.163"
    steps: 3
```

---

## 🎯 Ventajas de la Consolidación

✅ **Reutilizable** - Los scripts genéricos funcionan con cualquier dispositivo Android, no solo Fire Stick
✅ **Mantenible** - Una sola versión de cada script a mantener
✅ **Flexible** - Parámetros configurables para diferentes casos de uso
✅ **Profesional** - Captura de respuestas JSON para debugging
✅ **Escalable** - Fácil agregar nuevos dispositivos sin duplicar código

---

## 📚 Documentación Relacionada

- [ACTL_SCRIPTS_MEJORADOS.md](ACTL_SCRIPTS_MEJORADOS.md) - Referencia completa de todos los scripts
- [ANALISIS_COBERTURA_ADB_API.md](ANALISIS_COBERTURA_ADB_API.md) - Detalles de endpoints API
- automations.yaml - Ver `firestick_daily_morning_video` como ejemplo



### Cambiar la Hora de Reproducción
1. Abre `config/automations.yaml`
2. Busca `firestick_daily_morning_video`
3. Cambia el trigger:

```yaml
triggers:
  - trigger: time
    at: "HH:MM:SS"  # Ejemplo: "07:30:00" para 7:30 AM
```

4. Recarga automations

### Cambiar el Nivel de Volumen
En `firestick_morning_video`, modifica:

```yaml
- action: rest_command.adb_volume_up
  data:
    device_ip: "192.168.0.163"
    steps: 3  # Cambiar a 1-5 según necesites
```

---

## 🔧 URLs Soportadas

### YouTube
```
https://www.youtube.com/watch?v=VIDEO_ID
```

### YouTube Shorts
```
https://www.youtube.com/shorts/VIDEO_ID
```

### YouTube Playlist
```
https://www.youtube.com/playlist?list=PLAYLIST_ID
```

### Otras plataformas de streaming
Verifica si el Fire Stick tiene la app instalada y la URL es deeplink compatible.

---

## 📋 Video IDs Útiles

| Descripción | Video ID |
|-------------|----------|
| Rick Roll | `dQw4w9WgXcQ` (actual) |
| Música relajante | `jgpIP5sNyQA` |
| Noticias de hoy | Busca en YouTube |
| Podcast favorito | Busca en YouTube |

---

## ⚙️ REST Commands Requeridos

Estos scripts dependen de los siguientes `rest_command` definidos en `config/configuration.yaml`:

- `adb_connect_device` - Conecta al Fire Stick (puerto 5555)
- `adb_play_video` - Reproduce un video desde una URL
- `adb_volume_up` - Aumenta el volumen



Más info en: [config/configuration.yaml](../configuration.yaml)

---

## 🧪 Testing

### Test Manual de Reproducción
```yaml
service: script.firestick_play_video
data:
  video_url: "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
```

### Test de la Automation Matutina
1. Cambia la hora del trigger a 2 minutos en el futuro (en `automations.yaml`)
2. Recarga automations: Settings → Developer Tools → YAML → Automations
3. Observa las notificaciones a la hora indicada

### Ver Logs
Home Assistant → Developer Tools → Logs
Busca: "firestick" o "adb"

---

## 🐛 Solución de Problemas

| Problema | Solución |
|----------|----------|
| "Video no comienza a reproducirse" | Verifica que FireStick 192.168.0.163 esté conectado a ADB (puerto 5555) |
| "Notificación no aparece" | Verifica que `notify.notify` esté configurado (debe haber al menos un notifier) |
| "Volumen no cambia" | Intenta aumentar `steps` a 5 o después del video se reprododuzca |
| "Script no se ejecuta" | Recarga `scripts.yaml`: Settings → Developer Tools → YAML → Scripts |
| "Automation no dispara" | Verifica que la hora en Home Assistant sea correcta (Settings → System → General) |

---

## 📞 Contacto & Actualizaciones

- **Última actualización**: 2024
- **Fire Stick IP**: 192.168.0.163
- **ADB Server**: 192.168.0.251:9123
- **Puerto ADB**: 5555

Para más info sobre endpoints ADB: http://192.168.0.251:9123/docs
