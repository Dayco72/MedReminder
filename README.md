
https://www.figma.com/design/9tVkp4A7gZgpwz61eVb0q5/Sin-t%C3%ADtulo?node-id=0-1&t=qnviUEI0n3VhQiyZ-1
¡Quedaron excelentes las vistas en Figma! La jerarquía visual, los 4 estados de la tarjeta y la estructura de los formularios se ven limpios y listos para llevarse a código nativo en Android Studio.

Aquí tienen el **plan estratégico de implementación**, los pasos a seguir para configurar el proyecto y la **división equitativa de código** entre tu compañero y tú.

---

## Plan Estratégico de Implementación

```
                   ┌──────────────────────────────────────────────┐
                   │   FASE 1: Configuración Base del Proyecto    │
                   │   (Estructura, SQLite y Recursos XML)    │
                   └──────────────────────┬───────────────────────┘
                                          │
            ┌─────────────────────────────┴─────────────────────────────┐
            ▼                                                           ▼
┌───────────────────────────────┐                           ┌───────────────────────────────┐
│     FASE 2A: DESARROLLADOR A  │                           │     FASE 2B: DESARROLLADOR B  │
│  Módulo de Registro, Cámara   │                           │  Dashboard, Grid de Botiquín  │
│        y Persistencia         │                           │      y Alertas de Alarmas     │
└───────────┬───────────────────┘                           └───────────┬───────────────────┘
            │                                                           │
            └─────────────────────────────┬─────────────────────────────┘
                                          ▼
                   ┌──────────────────────────────────────────────┐
                   │    FASE 3: Integración y Cierre de Ciclo     │
                   │   (Dialog de Confirmación y Pruebas SUS)    │
                   └──────────────────────────────────────────────┘

```

---

## Pasos Iniciales en Android Studio (Paso a Paso)

antes de dividirse las tareas, realicen este montaje inicial juntos en la rama `main` de Git:

1. **Crear el Proyecto:**
* Nombre: `BoxMed` (o el nombre que hayan elegido).
* Lenguaje: **Java**.
* SDK Mínimo: **API 26 (Android 8.0)** o superior.
* Plantilla: `Empty Views Activity`.


2. **Exportar Recursos de Figma:**
* Exporten los íconos de la barra inferior (Calendario, Botiquín, Más) y los íconos de la cámara/reloj como vectores `.svg`.
* En Android Studio, den clic derecho en `res/drawable` $\rightarrow$ **New** $\rightarrow$ **Vector Asset** e importen los SVG.


3. **Definir la Paleta de Colores en `res/values/colors.xml`:**
```xml
<resources>
    <color name="primary_blue">#007AFF</color>
    <color name="status_pending_bg">#E0F2FE</color>
    <color name="status_alert_stroke">#F59E0B</color>
    <color name="status_taken_bg">#D1FAE5</color>
    <color name="status_missed_stroke">#EF4444</color>
    <color name="cabinet_bg">#F1F5F9</color>
</resources>

```



---

## División Equitativa del Código (Trabajo en Equipo)

Para evitar conflictos de fusión (*merge conflicts*), cada desarrollador trabajará en una **rama independiente de Git** (`feature/registro-persistencia` y `feature/dashboard-alarmas`).

### Desarrollador A: Módulo de Registro, Captura y Persistencia

**Objetivo principal:** Hacer que la información y las fotos que ingresa el usuario se guarden correctamente de forma local.

1. **Crear el Modelo de Datos (`Medicamento.java`):**
* Atributos: `id`, `nombre`, `dosis`, `horaPrimeraToma`, `frecuencia`, `momentoDia`, `fotoPath`, `estado` (*PENDIENTE*, *TOMADA*, *OMITIDA*).


2. **Crear la Base de Datos (`SQLiteOpenHelper`):**
* Diseñar la tabla `medicamentos` y escribir los métodos CRUD: `insertarMedicamento()`, `obtenerMedicamentos()`, `actualizarEstadoMedicamento()`.


3. **Diseñar el Layout XML de Registro (`fragment_agregar_medicamento.xml`):**
* Maquetar los campos `EditText`, el selector de hora (`TimePicker`), los *chips* de momentos del día y el botón "Guardar en el Botiquín".


4. **Programar la Cámara y Almacenamiento Local (`AgregarMedicamentoFragment.java`):**
* Configurar el `Intent(MediaStore.ACTION_IMAGE_CAPTURE)` para abrir la cámara nativa del teléfono.
* Guardar el archivo de imagen en el directorio privado de la app (`getExternalFilesDir`) y registrar la ruta absoluta (`fotoPath`) en la base de datos.



---

### Desarrollador B: Módulo de Visualización (Dashboard) y Notificaciones

**Objetivo principal:** Construir el grid dinámico del botiquín y el sistema de avisos de tiempo en pantalla.

1. **Diseñar el Layout del Ítem y Dashboard (`item_compartimento.xml` y `fragment_botiquin.xml`):**
* Crear la tarjeta individual del compartimento con `CardView` y `ConstraintLayout`.
* Maquetar el contenedor del botiquín y el `RecyclerView` con un `GridLayoutManager` de **2 columnas**.


2. **Crear el Adaptador del Grid (`MedicamentoAdapter.java`):**
* Cargar dinámicamente el nombre, dosis, la imagen guardada (usando `BitmapFactory.decodeFile` o la librería *Glide*) y cambiar el estilo según el estado (*Pending*, *Alert*, *Taken*, *Missed*).


3. **Programar los Filtros por Momento del Día (`Chips`):**
* Filtrar los elementos del `RecyclerView` cuando el usuario presione "Todos", "Mañana", "Tarde" o "Noche".


4. **Configurar las Notificaciones (`AlarmManager` y `NotificationManager`):**
* Crear el `BroadcastReceiver` que escuche cuando el temporizador llegue a cero y dispare la notificación emergente en la barra de estado de Android.



---

## Fases de Integración y Cierre de Ciclo (Trabajo Conjunto)

Una vez que ambas ramas estén terminadas, realicen el *merge* a la rama principal y completen las últimas dos vistas:

1. **Modal de Confirmación de Toma (`DialogConfirmacion.java` / `dialog_confirmacion.xml`):**
* Cuando el usuario abra la app desde la notificación o toque una tarjeta en estado *Alerta*, mostrar el diálogo flotante con la foto ampliada del medicamento.
* Al presionar **"Marcar como Tomada"**, llamar al método de la base de datos de Desarrollador A para actualizar el estado a *TOMADA* y refrescar el `RecyclerView` de Desarrollador B.


2. **Vista de Historial (`HistorialFragment.java`):**
* Maquetar la lista simple que consulte los medicamentos del día ordenados por hora y muestre el resumen de adherencia.
