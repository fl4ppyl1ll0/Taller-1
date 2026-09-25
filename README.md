# Taller 01 — Sistema de Control del Grupo POO

## Descripción del proyecto

Durante el primer semestre de 2026, el grupo de WhatsApp del curso de POO
perdió el control de acceso: el link circuló libremente y terminaron
ingresando personas de otros cursos. Este proyecto es el software que los
ayudantes usarán para que esto no se repita, controlando el ingreso solo
a quienes están oficialmente inscritos en un paralelo del curso (C1 o C2).

A partir de dos archivos de entrada:

- **`Alumnos.txt`**: lista oficial de alumnos inscritos (nombre, apellido,
  RUT y paralelo).
- **`Solicitudes.txt`**: personas que hicieron clic en el link e
  intentaron ingresar al grupo (nombre y apellido).

el sistema permite:

- **Filtrar automáticamente** las solicitudes de ingreso contra la lista
  oficial, separando admitidos de rechazados.
- **Inscribir manualmente** a rezagados (por nombre completo o por RUT).
- **Administrar el curso** durante el período de inscripciones: cambiar
  el paralelo de un alumno, eliminarlo o inscribir uno nuevo, con
  persistencia de los cambios en `Alumnos.txt`.
- **Generar reportes** versionados de los miembros por paralelo y de los
  rechazados.
- **Calcular estadísticas** del proceso de filtrado (tasa de rechazo,
  tasa de admisión, distribución por paralelo, entre otras).

El taller se desarrolla **sin Programación Orientada a Objetos** (una
única clase con métodos y variables estáticas) y **sin colecciones
dinámicas** (solo vectores estáticos de capacidad fija), usando
únicamente `Scanner`, `File`, `FileWriter`, `BufferedWriter` e
`IOException`.

## Integrante
-Florencia Lillo - 21.950.521-3 - ITI - github: https://github.com/fl4ppyl1ll0

## Estructura del proyecto

```
taller01_grupo_poo/
├── README.md
├── Alumnos.txt          # lista oficial del curso (se sobreescribe al usar la opcion 4)
├── Solicitudes.txt      # solicitudes de ingreso (dato de ejemplo)
├── Reportes/            # se crea automaticamente al generar el primer reporte
└── src/
    └── taller01/               <- paquete unico del proyecto
        └── Main.java           <- clase unica del proyecto (sin POO)
```

### Paquete `taller01`

Único paquete del proyecto.

### Clase `Main`

Única clase del proyecto (el taller prohíbe usar POO, por lo que no
existen otras clases ni objetos: todo son métodos y variables
`static` dentro de `Main`). Sus responsabilidades están agrupadas en:

| Grupo de métodos | Responsabilidad |
|---|---|
| `main`, `mostrarMenuPrincipal` | Bucle del menú principal y despacho a cada opción. |
| `cargarArchivos`, `cargarAlumnosDesdeArchivo`, `cargarSolicitudesDesdeArchivo` | Opción 1: lectura de `Alumnos.txt` y `Solicitudes.txt` hacia los vectores estáticos. |
| `procesarSolicitudes` | Opción 2: filtrado automático de solicitudes contra la lista de alumnos. |
| `inscripcionManual`, `inscribirManualPorNombre`, `inscribirManualPorRut`, `admitirPorInscripcionManual` | Opción 3: inscripción manual de rezagados por nombre o por RUT. |
| `administracionCurso`, `cambiarParalelo`, `eliminarAlumno`, `inscribirAlumnoNuevo`, `guardarAlumnos` | Opción 4: administración del curso y persistencia en `Alumnos.txt`. |
| `generarReportes`, `generarReporteParalelo`, `generarReporteRechazados`, `siguienteArchivoVersionado` | Opción 5: generación de reportes versionados en `Reportes/`. |
| `analisisEstadistico`, `contarSolicitudesDuplicadas`, `apellidoMasRepetido` | Opción 6: cálculo y despliegue de estadísticas. |
| `buscarAlumnoPorNombre`, `buscarAlumnoPorRut`, `esMiembro`, `agregarMiembro`, `agregarRechazado` | Funciones auxiliares de búsqueda y manipulación de los vectores. |
| `leerEntero`, `leerTexto` | Lectura robusta de datos por consola (nunca deja caer el programa). |

### Vectores estáticos principales (estado del programa)

- `alNombre / alApellido / alRut / alParalelo` (+ `alCount`): lista
  oficial de alumnos del curso.
- `solNombre / solApellido / solProcesada` (+ `solCount`): solicitudes de
  ingreso leídas desde archivo.
- `miNombre / miApellido / miRut / miParalelo` (+ `miCount`): miembros
  admitidos al grupo.
- `rechDescripcion` (+ `rechCount`): solicitudes/inscripciones
  rechazadas, ya formateadas para el reporte.

## Instrucciones de ejecución

1. Clonar el repositorio:
   ```bash
   git clone <URL-del-repositorio>
   cd taller01_grupo_poo
   ```

2. Compilar (requiere JDK 11+; se probó con OpenJDK 21):
   ```bash
   javac -d bin src/taller01/Main.java
   ```

3. Ejecutar **desde la carpeta raíz del proyecto** (para que
   `Alumnos.txt` y `Solicitudes.txt` se encuentren en el directorio de
   trabajo actual):
   ```bash
   java -cp bin taller01.Main
   ```

   Alternativamente, sin carpeta `bin`:
   ```bash
   javac src/taller01/Main.java
   java -cp src taller01.Main
   ```

   > Importante: los archivos `Alumnos.txt` y `Solicitudes.txt` se leen y
   > escriben en el **directorio desde el cual se ejecuta `java`**, no en
   > el directorio del `.class`. Por eso se recomienda ejecutar el
   > programa parado en la raíz del proyecto (donde están ambos `.txt`).

### Formato de los archivos de entrada

**Alumnos.txt** (`nombre;apellido;rut;paralelo`, paralelo = `C1` o `C2`):
```
Martin;Droguett;20345678-9;C1
Luis;Pineda;19876543-2;C2
```

**Solicitudes.txt** (`nombre-apellido`):
```
Martin-Droguett
Miguel-Perez
```

Líneas vacías, mal formadas, con campos en blanco o con un paralelo
distinto de `C1`/`C2` se omiten con un aviso por consola; el programa
nunca se cae por esto. Si algún archivo no existe, se informa y se
continúa con 0 registros para ese archivo.

### Menú principal

```
===== Sistema de Control del Grupo POO =====
1) Cargar archivos (Alumnos y Solicitudes)
2) Procesar solicitudes (Filtrado automatico)
3) Inscripcion manual al grupo
4) Administracion del curso
5) Generar reportes
6) Analisis estadistico
7) Salir
```

1. **Cargar archivos**: lee `Alumnos.txt` y `Solicitudes.txt` en vectores
   estáticos.
2. **Procesar solicitudes**: compara cada solicitud (nombre + apellido,
   sin distinguir mayúsculas) contra la lista de alumnos. Admite,
   rechaza o detecta que la persona ya es miembro del grupo. Se puede
   volver a ejecutar sin duplicar resultados: solo procesa solicitudes
   que aún no fueron procesadas.
3. **Inscripción manual**: permite inscribir a un rezagado por nombre
   completo o por RUT. Si no pertenece al curso, se registra en
   rechazados (con el mensaje especial de "solo RUT" cuando corresponde).
4. **Administración del curso**: cambiar el paralelo de un alumno,
   eliminarlo del curso o inscribir uno nuevo. Todo cambio se refleja en
   memoria y se persiste reescribiendo `Alumnos.txt`.
5. **Generar reportes**: crea `ReporteC1-VX.txt`, `ReporteC2-VX.txt` y
   `Rechazados-VX.txt` dentro de `Reportes/`, sin sobrescribir versiones
   anteriores (cada tipo de reporte lleva su propio contador de versión).
6. **Análisis estadístico**: porcentaje de solicitudes rechazadas, tasa
   de admisión, distribución por paralelo, cantidad de rechazos "solo
   RUT", inscripciones manuales vs. por archivo, solicitudes duplicadas
   en el archivo y el apellido más repetido en la lista de alumnos.
7. **Salir**.

### Control de errores

El programa está diseñado para no caerse ante:

- Opciones de menú inválidas (letras, vacío, fuera de rango).
- Intentar procesar, inscribir, administrar o reportar antes de cargar
  los archivos.
- Archivos de entrada inexistentes o con líneas mal formadas.
- RUT o paralelo inválidos, o campos vacíos.
- Alcanzar la capacidad máxima de los vectores (100 alumnos/solicitudes,
  200 rechazados combinados).
- Personas que intentan ingresar dos veces (no se admiten duplicados).
