
# Informe Técnico – TransLògic S.A.

## T07: Administració Avançada i Seguretat Corporativa

# 1. Introducción

Este documento recoge todos los pasos realizados para la mejora y administración avanzada de la infraestructura Active Directory de **TransLògic S.A.**

El objetivo del proyecto es implementar:

*   Mejora de seguridad corporativa mediante GPO.
*   Instalación automática de software por departamentos.
*   Sistemas de movilidad como perfiles móviles.
*   Medidas de seguridad mediante redirección de carpetas.
*   Delegación de funciones para un técnico de soporte (Helpdesk).

Todos los procedimientos están basados exclusivamente en la documentación estudiada y en las prácticas realizadas.

***

# 2. Estructura final de OUs

Se diseñó la siguiente estructura para permitir una administración modular y segura:

    translogic.local
    └── OU_TransLogic_17
        ├── USERS_17
        │   ├── OU_Gestio_17
        │   ├── OU_Gerencia_17
        │   ├── OU_Magatzem_17
        │   └── OU_Admins_17
        └── COMPUTERS_17
            ├── OU_Clients_17
            └── OU_Portatils_17

Justificación técnica:

*   Separación entre usuarios y equipos.
*   Aplicación precisa de GPO por departamento.
*   Delegación de funciones sin exponer todo el dominio.
*   Simplificación de despliegue de software.

**Captura 1 – Vista de las OUs en ADUC**

***

# 3. Políticas de Seguridad y Contraseñas (GPO)

## 3.1. Política Global: Default Domain Policy

Requisito:  
Contraseña mínima de **8 caracteres**.

Ruta:

    Computer Configuration
    → Policies
    → Windows Settings
    → Security Settings
    → Account Policies
    → Password Policy

**Captura 2 – Configuración de longitud mínima de contraseña**

***

## 3.2. Política para Gerencia (OU\_Gerencia\_17)

Requisitos:

*   Contraseña mínima: **18 caracteres**
*   Caducidad: **28 días**
*   Complejidad: **Desactivada**

GPO creada:  
**GPO\_Gerencia\_Password\_17**

Ruta de ajustes idéntica a la Default Domain Policy, vinculada esta vez solo a Gerencia.

**Captura 3 – GPO de Gerencia configurada**

***

## 3.3. GPO Proactiva (Mejora de seguridad)

Propuesta implementada:  
**Bloqueo automático de pantalla en Magatzem**  
GPO: *GPO\_Magatzem\_BlocPantalla\_17*

Valores configurados:

*   Screen saver timeout: 300 segundos
*   Password protect the screen saver: Enabled
*   Force specific screen saver: Enabled

**Captura 4 – Ajustes del Screen Saver Timeout**

***

# 4. Automatización de Software

## 4.1. Repositorio compartido: soft

Ruta creada:

    E:\soft_17

Compartida como:

    \\DC\soft

Permisos:

*   Administrators / IT → Full Control
*   Usuarios → Read

**Captura 5 – Carpeta soft compartida y permisos**

***

## 4.2. Instalación asignada (7zip) – Gestió

GPO: **GPO\_7zip\_Gestio\_17**

Tipo: **Assigned**  
Paquete:

    \\DC\soft\7zip.msi

Resultado:  
Se instala automáticamente al iniciar sesión.

**Captura 6 – GPO de instalación de 7zip**

***

## 4.3. Instalación publicada (Firefox) – Gerencia

GPO: **GPO\_Firefox\_Gerencia\_17**

Tipo: **Published**  
Paquete:

    \\DC\soft\firefox.msi

Resultado:  
Los usuarios de Gerencia pueden instalarlo desde “Obtener programas”.

**Captura 7 – Firefox publicado en el Panel de Control**

***

## 4.4. Conversión de EXE a MSI (consulta técnica)

Si una aplicación solo dispone de `.exe`, debe generarse un `.msi` usando herramientas externas de empaquetado (repackager).  
Esto es necesario porque **GPO solo instala automáticamente archivos MSI**.

***

# 5. Perfils Mòbils (Roaming Profiles)

## 5.1. Carpeta perfils

Ruta creada:

    E:\perfils_17

Compartida como:

    \\DC\perfils

**Captura 8 – Carpeta perfils compartida**

***

## 5.2. Configuración en plantilla de usuario

Ruta usada en **Profile path**:

    \\DC\perfils\%username%

**Captura 9 – Template con ruta de perfil móvil**

***

## 5.3. Usuario de prueba: uprova17

Al iniciar sesión desde Windows 11 se crea:

    E:\perfils_17\uprova17.V6

**Captura 10 – Carpeta del perfil móvil (uprova17.V6)**

***

# 6. Redirección de carpetas (Folder Redirection)

## 6.1. Configuración aplicada a todo el dominio

Ruta objetivo configurada en la Default Domain Policy:

    \\DC\personal\%username%\Documents

Modo:  
Basic – Create a folder for each user under the root path

**Captura 11 – Configuración de redirección de Documents**

***

## 6.2. Verificación en Windows 11

Prueba funcional:

1.  Guardar archivo dentro de **Documentos**.
2.  Confirmar que aparece en el servidor en:
        E:\personal_17\Usuario\Documents

**Captura 12 – Fichero sincronizado en Documents redirigido**

***

# 7. Delegación de funciones (Helpdesk)

## 7.1. Creación del usuario adminOU17

Ubicación:

    OU_TransLogic_17 → USERS_17 → OU_Admins_17

***

## 7.2. Delegación sobre OU\_TransLogic\_17

Funciones delegadas:

*   Resetear contraseñas
*   Modificar pertenencia a grupos

No permitido:

*   Crear usuarios nuevos

**Captura 13 – Assistant Delegate Control con permisos asignados**

***

## 7.3. Verificación funcional

*   Reset password: **Funciona**
*   Modify group membership: **Funciona**
*   Crear nuevo usuario: **Acceso denegado**

**Captura 14 – Error al intentar crear usuario**

***

# 8. Verificación general con gpresult

Comando:

    gpresult /R

Comprobaciones:

*   Aparecen todas las GPO aplicadas al usuario
*   Redirección de Documentos activa
*   Instalación de software detectada
*   Política de contraseñas correcta

**Captura 15 – Output de gpresult**
