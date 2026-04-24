# Implementación de Vuelo de Waypoints - VERSIÓN MEJORADA

## Cambios Realizados

Se ha añadido una nueva pestaña **"Vuelo"** a Form1 que permite ejecutar un plan de vuelo basado en waypoints con visualización avanzada.

### Estructura del Formulario

El formulario ahora tiene dos pestañas:

1. **Pestaña "Control"** - Contiene todos los controles originales (despegar, aterrizar, movimiento manual, telemetría, etc.)
2. **Pestaña "Vuelo"** - Nueva pestaña para la ejecución de planes de vuelo con waypoints

### Funcionalidades de la Pestaña "Vuelo"

#### Componentes:
- **Mapa GMap.NET**: Visualiza los waypoints cargados con números
- **Marcadores de Waypoints**: Círculos azules con número en el interior (WP 1, WP 2, etc.)
- **Dron Virtual**: Círculo rojo que representa la posición actual del dron
- **Rastro de Vuelo**: Puntos amarillos que trazan el recorrido durante la misión
- **Botón "Cargar Waypoints desde archivo"**: Carga un archivo .txt con coordenadas
- **Lista de Waypoints**: Muestra todos los waypoints cargados
- **Botón "Iniciar Vuelo"**: Comienza la ejecución del plan
- **Botón "Detener Vuelo"**: Detiene el vuelo en curso

### Formato del Archivo de Waypoints

El archivo debe ser un archivo de texto (.txt) con las coordenadas en formato:

```
latitud, longitud
latitud, longitud
...
```

Ejemplo (ver `waypoints_example.txt`):
```
41.27, 1.98
41.271, 1.981
41.272, 1.982
```

También acepta espacios sin comas como separador:
```
41.27 1.98
41.271 1.981
```

### Cómo Funciona

1. **Cargar waypoints**: 
   - Click en "Cargar Waypoints desde archivo"
   - Seleccionar un archivo .txt con coordenadas
   - Los waypoints aparecerán en el mapa (marcadores azules con números) y en la lista

2. **Iniciar vuelo**:
   - Click en "Iniciar Vuelo"
   - El dron se dirigirá al primer waypoint usando `dron.IrAlPunto()`
   - Se visualiza el dron como un círculo rojo en el mapa
   - Se dibuja un rastro amarillo del recorrido realizado
   - Cuando llega a un waypoint (distancia < 0.5m), automáticamente se dirige al siguiente
   - La altitud se mantiene fija en 5 metros

3. **Detener vuelo**:
   - Click en "Detener Vuelo" para parar el plan en cualquier momento
   - El dron recibe comando "Stop"
   - El rastro se borra automáticamente

### Visualización del Mapa

**Elementos visuales**:
- 🔵 **Marcadores Azules con Números**: Waypoints (1, 2, 3, etc.)
- 🔴 **Círculo Rojo**: Posición actual del dron
- ➡️ **Línea negra en dron**: Indica heading/orientación del dron
- ⬜ **Puntos Amarillos**: Rastro del recorrido

### Variables y Constantes

- `FLIGHT_ALTITUDE = 5f`: Altitud de vuelo fija (en metros)
- `WAYPOINT_ARRIVAL_THRESHOLD = 0.000005`: Distancia mínima para considerar que se llegó al waypoint (~0.5m)
- `waypointTimer`: Timer que verifica cada 500ms si se ha llegado al waypoint actual
- `flightTrace`: Lista que almacena todos los puntos del recorrido
- `flightTraceOverlay`: Overlay para dibujar el rastro
- `flightDroneOverlay`: Overlay para el marcador del dron

### Métodos Nuevos en Form1.cs

- `InicializarMapaVuelo()`: Configura el mapa de la pestaña Vuelo con 3 overlays
- `loadWaypointsBtn_Click()`: Carga waypoints desde archivo con marcadores numerados
- `startFlightBtn_Click()`: Inicia el plan de vuelo
- `stopFlightBtn_Click()`: Detiene el plan de vuelo y limpia rastro
- `WaypointTimer_Tick()`: Verifica progreso, actualiza dron, dibuja rastro cada 500ms

### Nuevos Marcadores Personalizados (MapMarkers.cs)

- **WaypointMarker**: Círculo azul con número blanco del waypoint
- **TraceMarker**: Pequeño punto amarillo para el rastro

### Método Utilizado del Dron

Se utiliza la función existente en la librería `csDronLink`:
```csharp
dron.IrAlPunto((float)latitud, (float)longitud, (float)altitud)
```

### Overlays del Mapa de Vuelo

El mapa de vuelo tiene 3 capas (overlays):
1. **flightTraceOverlay** (abajo): Rastro amarillo
2. **flightWaypointsOverlay** (medio): Waypoints numerados
3. **flightDroneOverlay** (arriba): Marcador del dron

### Notas Importantes

- La altitud siempre es 5 metros. Si en futuro se necesita altitudes variables, se puede leer del archivo.
- El dron debe estar despegado antes de iniciar el plan de vuelo
- El umbral de llegada es de ~0.5 metros (0.000005 grados)
- El sistema verifica cada 500ms si se ha llegado al waypoint actual
- El rastro se dibuja automáticamente conforme avanza el dron
- El rastro se limpia cuando se detiene o completa el plan
- El marcador del dron muestra la orientación (heading) actual mediante una línea negra
- Cada waypoint tiene un número visible para fácil identificación
