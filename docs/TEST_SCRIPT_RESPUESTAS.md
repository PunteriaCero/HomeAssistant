# 🧪 Script de Prueba Mejorado - test_adb_endpoints

**Versión**: 2.1 - Con Captura de Respuestas JSON  
**Última actualización**: 2024  
**Estado**: ✅ LISTO PARA USAR

---

## 📋 Descripción

El script `test_adb_endpoints` ha sido mejorado para **capturar y retornar todas las respuestas JSON** de cada endpoint de la API ADB Control v1.2.0.

### ✨ Mejoras Implementadas

#### Antes (v2.0)
- Solo mostraba respuestas en notificaciones
- Las respuestas no eran reutilizables
- Información limitada al formato `.content`

#### Ahora (v2.1)
- ✅ Captura respuestas JSON completas
- ✅ Retorna estructura de datos completa: `test_results`
- ✅ Cada test almacena: nombre, endpoint, status, response
- ✅ Resumen automático: total, passed, failed
- ✅ Timestamp de ejecución completo
- ✅ Compatible con otros scripts que necesiten los resultados

---

## 🚀 Cómo Usar

### Ejecución Manual (Developer Tools)

```yaml
service: script.test_adb_endpoints
data:
  device_ip: "192.168.0.163"
```

### Resultado

El script ejecutará:
1. **12 tests** en paralelo/secuencial
2. **Mostrará notificaciones** con cada resultado
3. **Retornará estructura** con todas las respuestas

---

## 📊 Estructura de Respuesta

### Formato Completo Retornado

```yaml
test_results:
  timestamp: "2024-03-01T15:30:45.123456"
  device_ip: "192.168.0.163"
  summary:
    total: 15
    passed: 15
    failed: 0
  tests:
    - name: "API Health Check"
      endpoint: "GET /"
      status: "passed"
      response: {
        status: "online",
        uptime: 12345
      }
    
    - name: "List Devices"
      endpoint: "GET /devices"
      status: "passed"
      response: [
        {
          ip: "192.168.0.163",
          connected: true,
          model: "AFFT"
        }
      ]
    
    - name: "Connect Device"
      endpoint: "POST /devices/connect"
      status: "passed"
      device_ip: "192.168.0.163"
      response: {
        status: "connected",
        ip: "192.168.0.163"
      }
    
    # ... más tests aquí
```

---

## 🔄 Acceder a las Respuestas

### Opción 1: En Notificaciones (Automático)

Cada endpoint se notifica con su respuesta JSON completa:

```
✅ TEST 1: DEVICE MANAGEMENT
📋 Devices: {"ip": "192.168.0.163", "connected": true}
🔌 Connect: {"status": "connected", "ip": "192.168.0.163"}
```

### Opción 2: Desde Otro Script

Captura la respuesta y úsala:

```yaml
- action: script.test_adb_endpoints
  data:
    device_ip: "192.168.0.163"
  response_variable: adb_test_results

# Ahora puedes acceder a los resultados:
- action: notify.notify
  data:
    message: |
      Total Tests: {{ adb_test_results.test_results.summary.total }}
      Passed: {{ adb_test_results.test_results.summary.passed }}
      Failed: {{ adb_test_results.test_results.summary.failed }}
```

### Opción 3: Acceder a Test Específico

```yaml
- variables:
    first_test: "{{ adb_test_results.test_results.tests[0] }}"
    device_info_test: "{{ adb_test_results.test_results.tests | selectattr('name', 'equalto', 'Device Info') | first }}"

- action: notify.notify
  data:
    message: |
      Nombre: {{ first_test.name }}
      Endpoint: {{ first_test.endpoint }}
      Status: {{ first_test.status }}
      Response: {{ first_test.response | to_json }}
```

### Opción 4: Guardar en Input Select / Input Text

```yaml
- action: input_text.set_value
  target:
    entity_id: input_text.adb_test_results
  data:
    value: "{{ adb_test_results.test_results | to_json }}"
```

---

## 📈 Casos de Uso

### 1. Validar Función Específica

```yaml
- action: script.test_adb_endpoints
  data:
    device_ip: "192.168.0.163"
  response_variable: results

- variables:
    volume_tests: "{{ results.test_results.tests | selectattr('endpoint', 'contains', 'volume') | list }}"

- action: notify.notify
  data:
    message: |
      Pruebas de Volumen: {{ volume_tests | length }}
      {% for test in volume_tests %}
      • {{ test.name }}: {{ test.status }}
      {% endfor %}
```

### 2. Automatización de Monitoreo

```yaml
- id: 'weekly_adb_health_check'
  alias: "Chequeo Semanal de Salud ADB"
  triggers:
    - trigger: time
      at: "03:00:00"
  actions:
    - action: script.test_adb_endpoints
      data:
        device_ip: "192.168.0.163"
      response_variable: health_check
    
    - choose:
      - conditions:
          - condition: template
            value_template: "{{ health_check.test_results.summary.failed > 0 }}"
        sequence:
          - action: notify.notify
            data:
              title: "⚠️ ADB Health Check Failed"
              message: "{{ health_check.test_results.summary.failed }} tests falló"
```

### 3. Logging de Histórico

```yaml
- action: script.test_adb_endpoints
  data:
    device_ip: "192.168.0.163"
  response_variable: test_run

- action: system_log.write
  data:
    level: info
    logger: adb_api_tests
    message: |
      ADB Test Results - {{ test_run.test_results.timestamp }}
      Device: {{ test_run.test_results.device_ip }}
      Passed: {{ test_run.test_results.summary.passed }}/{{ test_run.test_results.summary.total }}
      Tests: {{ test_run.test_results.tests | map(attribute='name') | join(', ') }}
```

---

## 📝 Detalles Técnicos

### Captura de Respuestas

Cada test captura:

```yaml
- name: "Test Name"           # Nombre descriptivo
  endpoint: "METHOD /path"    # Endpoint de la API
  status: "passed"            # passed | failed
  response: {}                # Respuesta JSON completa
  # Campos opcionales según test:
  device_ip: "192.168.0.163"
  level: 8                    # Para volume set
  command: "pm list packages" # Para custom command
  count: 15                   # Para cantidad de items
```

### Manejo de Errores

Si un endpoint falla:

```yaml
- name: "Unable to Connect"
  endpoint: "POST /devices/connect"
  status: "failed"
  response: {
    error: "Connection refused",
    code: 503
  }
```

---

## 🔧 Ejemplos de Filtrado

### Obtener Solo Respuestas Exitosas

```yaml
{{ test_results.tests | selectattr('status', 'equalto', 'passed') | list }}
```

### Obtener Tests de Volumen

```yaml
{{ test_results.tests | selectattr('endpoint', 'contains', 'volume') | list }}
```

### Obtener Respuesta Específica

```yaml
{{ test_results.tests | selectattr('name', 'equalto', 'Device Info') | map(attribute='response') | first }}
```

### Contar por Categoría

```yaml
Device Tests: {{ test_results.tests | selectattr('endpoint', 'contains', 'device') | list | length }}
Volume Tests: {{ test_results.tests | selectattr('endpoint', 'contains', 'volume') | list | length }}
Playback Tests: {{ test_results.tests | selectattr('endpoint', 'contains', 'play') | list | length }}
```

---

## 📋 Tests Incluidos (15 Total)

| # | Nombre | Endpoint | Método | Captura |
|---|--------|----------|--------|---------|
| 0 | API Health Check | GET / | GET | ✅ |
| 1 | List Devices | GET /devices | GET | ✅ |
| 2 | Connect Device | POST /devices/connect | POST | ✅ |
| 3 | Device Status | GET /status | GET | ✅ |
| 4 | Device Info | GET /device/info | GET | ✅ |
| 5 | Current App | GET /device/current-app | GET | ✅ |
| 6 | Installed Apps | GET /device/installed-apps | GET | ✅ |
| 6B | Logcat | GET /device/logcat | GET | ✅ |
| 7 | Volume Current | GET /device/volume/current | GET | ✅ |
| 8 | Volume Up | POST /device/volume/increase | POST | ✅ |
| 9 | Volume Down | POST /device/volume/decrease | POST | ✅ |
| 10 | Volume Mute | POST /device/volume/mute | POST | ✅ |
| 11 | Play Video | POST /play | POST | ✅ |
| 12 | Pause Video | POST /stop | POST | ✅ |
| 13 | Exit App | POST /exit | POST | ✅ |
| 14 | Volume Set | POST /device/volume/set | POST | ✅ |
| 15 | Screenshot | GET /screenshot | GET | ✅ |
| 16 | Custom Command | POST /command | POST | ✅ |
| 17 | Disconnect | POST /devices/disconnect | POST | ✅ |

**Total**: 19 endpoints cubiertos con 100% de captura de respuestas JSON

---

## 🎯 Ventajas de la Versión 2.1

| Aspecto | Antes | Ahora |
|--------|-------|-------|
| **Captura de Respuestas** | ❌ Solo texto | ✅ JSON completo |
| **Reutilización** | ❌ No | ✅ Sí, para otros scripts |
| **Análisis de Datos** | ❌ Manual | ✅ Automático con filtros |
| **Resumen Estadístico** | ❌ Básico | ✅ Detallado |
| **Timestamp** | ❌ Hora simple | ✅ ISO completo |
| **Integración** | ❌ Limitada | ✅ Total con automations |

---

## 🐛 Solución de Problemas

### "La respuesta no aparece en la notificación"
- Verifica que `notify.notify` esté configurado
- Revisa los logs: Settings → Developer Tools → Logs

### "No puedo acceder a response_variable en otro script"
- Asegúrate de capturar: `response_variable: mi_variable`
- El script debe invocar con `action: script.test_adb_endpoints`

### "Las respuestas JSON están vacías"
- Verifica que el endpoint esté retornando datos
- Revisa http://192.168.0.251:9123/docs
- Prueba manualmente el endpoint desde PowerShell

---

## 📞 Referencias

- **API Documentation**: http://192.168.0.251:9123/docs
- **ADB Server**: 192.168.0.251:9123
- **Test Device**: 192.168.0.163
- **Script Location**: config/scripts.yaml

---

**Última actualización**: 2024  
**Versión**: 2.1.0  
**Compatibilidad**: Home Assistant 2024+  
**Estado**: ✅ Producción
