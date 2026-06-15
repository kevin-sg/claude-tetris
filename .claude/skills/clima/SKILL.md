---
name: clima
description: >
  Consulta el clima actual del usuario en tiempo real usando wttr.in.
  Úsalo cuando el usuario pregunte por el tiempo, el clima, la temperatura,
  "qué tiempo hace", "cómo está el clima", "va a llover", "hace frío",
  "hace calor", "necesito paraguas", o invoque /clima.
  Si el usuario menciona una ciudad o ubicación, úsala como argumento.
  Actívalo proactivamente ante cualquier pregunta relacionada con condiciones
  meteorológicas o pronóstico del tiempo, incluso si no se pide explícitamente.
---

Cuando este skill se active, ejecuta inmediatamente el comando Bash para
obtener el clima y preséntalo de forma concisa en español.

## Pasos

1. **Determina la ubicación:**
   - Si el usuario pasó un argumento (ej: `/clima Mendoza`), usa `$ARGUMENTS` como ciudad
   - Si no hay argumento, usa `Buenos+Aires` como ciudad por defecto

2. **Ejecuta el comando:**
   - Con argumento: `curl -s "wttr.in/$ARGUMENTS?format=j1&lang=es"`
   - Sin argumento (por defecto): `curl -s "wttr.in/Buenos+Aires?format=j1&lang=es"`

3. **Extrae del JSON y presenta al usuario:**
   - Ubicación: `nearest_area[0].areaName[0].value` + país
   - Condición: `current_condition[0].weatherDesc[0].value`
   - Temperatura: `current_condition[0].temp_C` °C
   - Sensación térmica: `current_condition[0].FeelsLikeC` °C
   - Humedad: `current_condition[0].humidity` %
   - Viento: `current_condition[0].windspeedKmph` km/h
   - Pronóstico hoy — máx: `weather[0].maxtempC` / mín: `weather[0].mintempC`

4. **Fallback:** si curl falla o el JSON está malformado, ejecuta:
   `curl -s "wttr.in/${ARGUMENTS:-Buenos+Aires}?format=3"` y muestra la línea resultante tal cual.

## Formato de respuesta

Usa íconos y mantén la respuesta breve. Ejemplo:

```
📍 Santiago, Chile
🌤️ Parcialmente nublado · 18°C (sensación 16°C)
💧 Humedad: 72% · 💨 Viento: 15 km/h
📅 Hoy: mín 12°C / máx 22°C
```

No añadas texto introductorio ni de cierre — solo el reporte del clima.
