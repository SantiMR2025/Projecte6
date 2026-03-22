# Guía Técnica – Actividad T08

## Vigilancia y Auditoría de Sistemas

**Alumno:** Nº de lista 17  
**Entorno:** Windows Server 2025 + Windows 11  
**Asignatura:** Sistemes Operatius en Xarxa

***

# 1. Introducción

En un entorno empresarial como TransLògic S.A., la integridad y seguridad del sistema es crítica.  
El objetivo de esta actividad es:

*   Monitorizar el estado del servidor.
*   Configurar la auditoría de seguridad mediante GPO.
*   Simular intentos de acceso fallidos.
*   Analizar la evidencia en el Visor de eventos.

La guía supone que el servidor ya está configurado como **Controlador de Dominio**, tal como se trabaja en el PDF, y que el equipo cliente **Windows 11 está unido al dominio**.

***

# 2. Monitorización de Recursos del Servidor

## 2.1. Objetivo

Comprobar si el servidor tiene suficiente capacidad de CPU y memoria para soportar la carga actual.

## 2.2. Procedimiento

1.  En **Windows Server 2025**, abrir el Administrador de tareas:  
    **CTRL + SHIFT + ESC**
2.  Ir a la pestaña **Rendimiento**.
3.  Observar:
    *   Uso de CPU
    *   Uso de RAM
4.  Tomar la **captura de pantalla**.

## 2.3. Interpretación (modelo)

*   CPU por debajo del 50%: servidor funcionando sin estrés.
*   RAM con espacio libre suficiente: rendimiento adecuado.
*   Picos altos frecuentes: podría indicar necesidad de optimización.

**Captura aquí:**  
`ruta_de_tu_captura`

***

# 3. Configuración de Auditoría de Seguridad (GPO)

Basado en las directrices del PDF sobre configuración de GPO y seguridad.  
Las GPO permiten administrar el dominio de forma centralizada y configurar automáticamente los equipos.  
El PDF remarca que se gestionan desde **Group Policy Management**, accesible desde Server Manager.

## 3.1. Abrir la consola de GPO

Ruta según el PDF:  
**Server Manager → Tools → Group Policy Management**  
(El PDF muestra esta ruta claramente en varias capturas)

## 3.2. Seleccionar la política adecuada

Normalmente se usa:  
**Default Domain Policy**

## 3.3. Editar la GPO

Ruta dentro del editor (coincide con la estructura del PDF):

    Computer Configuration
    → Windows Settings
    → Security Settings
    → Local Policies
    → Audit Policy

## 3.4. Activar:

*   Audit logon events → **Success** + **Failure**
*   Audit account logon events → **Success** + **Failure**

Esto permite registrar:

*   Quién entra correctamente
*   Quién intenta entrar sin permiso

## 3.5. Aplicar las políticas en el cliente

En **Windows 11**, ejecutar:

    gpupdate /force

## 3.6. Captura requerida

`ruta_de_tu_captura`

***

# 4. Simulación de Intentos de Intrusión (Hacking Ético)

## 4.1. Objetivo

Generar eventos reales de inicio de sesión fallido que el sistema registrará gracias a la auditoría configurada.

## 4.2. Procedimiento

1.  Cerrar sesión en Windows 11.
2.  Intentar iniciar sesión con un usuario válido del dominio  
    (por ejemplo: usuario de almacén).
3.  Introducir una contraseña **incorrecta adrede**.
4.  Repetir 3–4 veces seguidas.
5.  Finalmente iniciar sesión **correctamente** con Administrator.

Esto generará:

*   Eventos 4625 (fallidos)
*   Evento 4624 (correcto)

***

# 5. Análisis Forense en el Visor de Eventos

El PDF utiliza ampliamente la administración mediante herramientas del Server Manager, incluyendo el **Visor de Eventos**.

## 5.1. Abrir el Visor de Eventos

Ruta:  
**Server Manager → Tools → Event Viewer**

## 5.2. Ubicación del registro

    Windows Logs → Security

## 5.3. Buscar eventos de logon fallido

Selecciona los eventos correspondientes a los intentos fallidos generados en la simulación.

## 5.4. Detalles importantes del evento

Dentro de un evento de fallo (Event ID 4625) revisa:

*   Usuario que lo intentó
*   Hora
*   Tipo de inicio de sesión
*   Código de error
*   IP origen (si aplica)

## 5.5. Captura requerida

`ruta_de_tu_captura`

***

# 6. Respuesta técnica solicitada en la actividad

## ¿Cuál es el Event ID de un inicio de sesión fallido?

**Event ID: 4625**

## ¿Cuál es el Event ID de un inicio de sesión correcto?

**Event ID: 4624**

Ambos forman parte del conjunto de registros de auditoría generados cuando se activan las opciones de Success/Failure.


