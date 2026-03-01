# Fire Stick - Scripts de Reproducción de Videos

## 📺 Descripción General

Se han creado dos scripts para controlar la reproducción de videos en el Fire Stick (192.168.0.163):

1. **`firestick_play_video`** - Script flexible para reproducir cualquier video
2. **`firestick_morning_video`** - Script específico para la automation matutina

---

## 1️⃣ Script: `firestick_play_video`

### Descripción
Reproduce un video en el Fire Stick desde cualquier URL. Acepta la URL como parámetro.

### Uso Manual
Desde Home Assistant Developer Tools → Services:

```yaml
service: script.firestick_play_video
data:
  video_url: "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
```

### Parámetros
| Parámetro | Tipo | Requerido | Ejemplo | Descripción |
|-----------|------|-----------|---------|-------------|
| `video_url` | texto | ✅ Sí | `https://www.youtube.com/watch?v=...` | URL del video a reproducir |

### Flujo de Ejecución
1. **Conectar** al Fire Stick (IP: 192.168.0.163)
2. **Esperar** 2 segundos
3. **Reproducir** el video desde la URL
4. **Notificar** el estado con los detalles

### Ejemplo de Automatización
```yaml
- id: 'custom_video_play'
  alias: "Reproducir video personalizado"
  triggers:
    - trigger: state
      entity_id: input_select.video_playlist
      to: "Mi Video Favorito"
  actions:
    - action: script.firestick_play_video
      data:
        video_url: "https://www.youtube.com/watch?v=VIDEO_ID"
```

---

## 2️⃣ Script: `firestick_morning_video`

### Descripción
Script específico para la automation matutina. Reproduceun video predefinido y aumenta el volumen automáticamente.

### Uso Manual
Desde Home Assistant Developer Tools → Services:

```yaml
service: script.firestick_morning_video
```

### Parámetros
No requiere parámetros. El video y volumen están predefinidos.

### Flujo de Ejecución
1. **Conectar** al Fire Stick
2. **Esperar** 2 segundos
3. **Reproducir** video: `https://www.youtube.com/watch?v=dQw4w9WgXcQ`
4. **Aumentar volumen** 3 pasos
5. **Notificar** con mensaje de buenos días

### Automation Asociada
La automation `firestick_daily_morning_video` utiliza este script:

```yaml
triggers:
  - trigger: time
    at: "06:00:00"
actions:
  - action: script.firestick_morning_video
```

Se ejecuta **todos los días a las 6:00 AM**.

---

## 🎯 Personalización

### Cambiar el Video Matutino
1. Abre `config/scripts.yaml`
2. Busca `firestick_morning_video`
3. Cambia la URL en `video_url`:

```yaml
- action: rest_command.adb_play_video
  data:
    device_ip: "192.168.0.163"
    video_url: "https://www.youtube.com/watch?v=TU_VIDEO_ID"  # ← Cambiar aquí
```

4. Recarga automations
5. ¡Listo! El próximo día a las 6 AM reproducirá el nuevo video

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
