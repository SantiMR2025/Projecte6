# Guía Técnica Extensa por Fases

## Actividad T08 – Vigilancia y Auditoría de Sistemas

**Alumno:** Nº de lista 17  
**Servidor:** Windows Server 2025  
**Cliente:** Windows 11  
**Asignatura:** Sistemes Operatius en Xarxa

***

# FASE 1 – Preparación del Entorno

## Objetivo

Validar que todo el entorno de laboratorio está correctamente configurado antes de comenzar.

## Requisitos Previos

*   Servidor **Windows Server 2025** funcionando como **Controlador de Dominio**.
*   Cliente **Windows 11** unido al dominio.
*   Conexión y comunicación correcta entre cliente y servidor.
*   Usuario estándar creado (ej.: `almacen.user`).
*   Usuario administrador disponible.

## Verificación

*   Confirmar acceso al dominio desde Windows 11.
*   Confirmar acceso a:
    *   Server Manager
    *   Group Policy Management
    *   Event Viewer
    *   Task Manager

***

# FASE 2 – Monitorización del Servidor

## Objetivo

Analizar los recursos del servidor para comprobar si está saturado o funciona sin estrés.

## Pasos

### 1. Abrir el Administrador de tareas

En Windows Server:  
`CTRL + SHIFT + ESC`

### 2. Ir a la pestaña “Rendimiento”

Observar:

*   Uso de CPU
*   Uso de Memoria RAM
*   Actividad de discos y red

### 3. Realizar una captura

Debe verse CPU y RAM claramente.

### 4. Interpretación

Incluye en tu informe:

*   Si el uso es bajo → sistema estable
*   Si es alto → posible saturación
*   Añade tu interpretación técnica personal

***

# FASE 3 – Configuración de Auditoría de Seguridad (GPO)

## Objetivo

Activar las políticas necesarias para registrar accesos correctos y fallidos al sistema.

## Pasos

### 1. Abrir la consola de GPO

Ruta:

    Server Manager → Tools → Group Policy Management

### 2. Seleccionar la GPO

Usar:

    Default Domain Policy

### 3. Editar la GPO

Botón derecho → **Edit**

### 4. Activar auditoría

Ruta:

    Computer Configuration
    → Windows Settings
    → Security Settings
    → Local Policies
    → Audit Policy

Habilitar:

*   Audit logon events → **Success + Failure**
*   Audit account logon events → **Success + Failure**

### 5. Aplicar GPO en el cliente

En Windows 11:

    gpupdate /force

### 6. Captura requerida

Captura del editor de GPO con estas políticas activadas.

***

# FASE 4 – Simulación de Incidencias (Hacking Ético)

## Objetivo

Generar eventos reales de inicio fallido que luego se analizarán.

## Pasos

### 1. Cerrar sesión en Windows 11

### 2. Intentar iniciar sesión

Selecciona un usuario del dominio e introduce una contraseña incorrecta.

### 3. Repetir

Hazlo 3 o 4 veces seguidas.

### 4. Iniciar sesión correctamente

Usa ahora la contraseña correcta con `Administrator` o un usuario válido.

### Resultado esperado

El sistema genera:

*   Varios eventos **4625** (inicio fallido)
*   Un evento **4624** (inicio correcto)

***

# FASE 5 – Análisis Forense (Event Viewer)

## Objetivo

Localizar evidencia de los intentos fallidos y registrar el Event ID solicitado.

## Pasos

### 1. Abrir el Visor de Eventos

Ruta:

    Inicio → Event Viewer

o

    Server Manager → Tools → Event Viewer

### 2. Acceder al registro de seguridad

    Windows Logs → Security

### 3. Buscar eventos recientes

Filtrar por:

*   “Audit Failure” (fallos)
*   “Audit Success” (accesos correctos)

### 4. Abrir un evento fallido

Identificar:

*   Usuario que falló
*   Fecha y hora
*   Código de estado
*   IP origen (si existe)

### 5. Realizar captura

Debe verse el detalle completo del evento.

### 6. Identificación del Event ID

*   Inicio de sesión fallido → **4625**
*   Inicio de sesión correcto → **4624**

***

# FASE 6 – Preparación del Informe Final

## Lo que debes entregar

### 1. Monitorización

*   Captura del Administrador de Tareas
*   Interpretación técnica

### 2. Auditoría GPO

*   Captura de política con Success + Failure activados

### 3. Evidencia forense

*   Captura del Visor de Eventos mostrando el evento 4625

### 4. Respuesta técnica

    Event ID (error de inicio de sesión): 4625
