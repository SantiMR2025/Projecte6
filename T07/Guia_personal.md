# FASE 1 – DISEÑO Y PREPARACIÓN DE LA ESTRUCTURA DE UNIDADES ORGANIZATIVAS (OU)  

### Objetivo de la fase  

Definir y justificar la estructura de OUs que se utilizará a lo largo de toda la práctica del cliente TransLògic S.A., basándonos estrictamente en la información del PDF (directivas de grupo, herencia, ámbito y filtrado). 

### Importante
Toda la estructura deberá pensarse para que:  
– Las GPO se apliquen correctamente según la jerarquía explicada en el PDF (primero dominio, luego OU específicas).  
– La organización facilite el despliegue de software por departamento.  
– Se pueda delegar control sin comprometer la seguridad.  

---

### 1.1. Justificación técnica (basado en el PDF)

Según el material, las directivas se aplican sobre:  
– Todo el dominio  
– OUs  
– Y siguen una jerarquía:  
Directivas locales → Default Domain Policy → OUs específicas.  

Esto significa que, si queremos políticas distintas para cada grupo (gestio, gerencia, etc.), deben estar claramente separadas en OUs que contengan sus usuarios.  
Además, para despliegue de software (PDF, páginas 22–38), las políticas se aplican sobre usuarios o equipos, así que conviene separar OUs de usuarios y equipos.  
---

### 1.2. Estructura final recomendada para TransLògic (tu número de lista incluido)

Dominio: translogic.local  
OUs raíz:  
OU\_TransLogic\_17 (OU principal) • USERS\_17  
– OU\_Gestio\_17  
– OU\_Gerencia\_17  
– OU\_Magatzem\_17  
– OU\_Admins\_17 (donde estará adminOU)  
• COMPUTERS\_17  
– OU\_Clients\_17  
– OU\_Portatils\_17  
Justificación  
– USERS\_17 contiene todos los usuarios, divididos por departamento, para aplicar políticas y software distintas.  
– COMPUTERS\_17 separa ordenadores (fijos y portátiles) porque algunas GPO pueden ser por equipo (seguridad, mapa de unidades, etc.).  
– OU\_Admins\_17 permite delegar permisos de forma precisa sobre la OU principal, sin dar acceso al dominio entero.  
---

1. Cómo crear estas OUs en Windows Server (explicado desde cero)  
2. Abrir el panel Server Manager.  
3. Ir a Tools → Active Directory Users and Computers.  
4. En el árbol de la izquierda, seleccionar el dominio translogic.local.  
5. Clic derecho → New → Organizational Unit.  
6. Crear OU\_TransLogic\_17 (marcar "Protect container from accidental deletion").  
7. Dentro de OU\_TransLogic\_17:  
   – Crear USERS\_17 y COMPUTERS\_17.  
8. Dentro de USERS\_17:  
   – Crear OU\_Gestio\_17  
   – Crear OU\_Gerencia\_17  
   – Crear OU\_Magatzem\_17  
   – Crear OU\_Admins\_17  
9. Dentro de COMPUTERS\_17:  
   – Crear OU\_Clients\_17  
   – Crear OU\_Portatils\_17

---

### 1. Resultado esperado

Cuando abras Active Directory Users and Computers deberías ver:  
translogic.local  
└── OU\_TransLogic\_17  
├── USERS\_17  
│   ├── OU\_Gestio\_17  
│   ├── OU\_Gerencia\_17  
│   ├── OU\_Magatzem\_17  
│   └── OU\_Admins\_17  
└── COMPUTERS\_17  
├── OU\_Clients\_17  
└── OU\_Portatils\_17  
Si esta estructura coincide, todo el resto de GPO funcionará sin conflictos.

FASE 2 – POLÍTICAS DE SEGURIDAD Y CONTRASEÑAS (GPO)  
Objetivo de la fase  
Configurar tres políticas distintas usando GPO, siguiendo exactamente el modelo explicado en el PDF:

1. Modificar la Default Domain Policy para todo el dominio.  
2. Crear una GPO específica para la OU\_Gerencia\_17.  
3. Crear una tercera GPO “proactiva” de seguridad.

Basado en las secciones de directivas de contraseña del PDF, páginas 7–14.  
---

2.1. Política Global: Modificar Default Domain Policy  
Esta política se aplica a **todo el dominio**, ya que las políticas de contraseña deben aplicarse desde allí (PDF, página 7).  
Requisito:  
Contraseña mínima: **8 caracteres**.  
Pasos:

1. En el servidor, abrir:  
   Server Manager \> Tools \> Group Policy Management.  
2. En el árbol izquierdo:  
   Domains \> translogic.local \> Default Domain Policy.  
3. Clic derecho → Edit.  
4. Ir a:  
   Computer Configuration  
   Policies  
   Windows Settings  
   Security Settings  
   Account Policies  
   Password Policy  
5. Modificar:  
   Minimum password length → **8 caracteres**.

Este ajuste se corresponde con lo mostrado en el PDF (páginas 9–11).  
Nota importante  
La Default Domain Policy siempre se usa para contraseña general; cambiarla en otro sitio puede causar conflictos.  
---

2.2. Política específica para Gerencia (OU\_Gerencia\_17)  
Requisitos:  
Contraseña mínima: 18 caracteres.  
Caducidad cada 28 días.  
No activar complejidad.  
Según PDF (páginas 9–12), las contraseñas largas aumentan seguridad y se pueden aplicar mediante GPO a una OU concreta.  
Pasos:

1. En Group Policy Management, ubicarse en:  
   OU\_TransLogic\_17 → USERS\_17 → OU\_Gerencia\_17.  
2. Clic derecho sobre OU\_Gerencia\_17 → Create a GPO in this domain, and Link it here.  
3. Nombre sugerido:  
   GPO\_Gerencia\_Password\_17  
4. Editar la GPO:  
   Computer Configuration  
   Policies  
   Windows Settings  
   Security Settings  
   Account Policies  
   Password Policy  
5. Modificar:  
   Minimum password length → 18  
   Maximum password age → 28  
   Password must meet complexity requirements → Disabled  
   Relax minimum password length limits → Enabled  
   (Implica permitir contraseñas mayores a 14 caracteres, como indica el PDF).  
6. Cerrar el editor.

---

2.3. Tercera GPO Proactiva (elegida por ti, basada en el PDF)  
Según la actividad, debes proponer una mejora realista para una empresa logística.  
Me baso en los ejemplos del PDF, especialmente la gestión de seguridad y organización del entorno.  
Propuesta técnica:  
GPO de “Bloqueo automático de pantalla” para aumentar seguridad en zonas de almacén.  
Justificación profesional:  
En una empresa logística, los empleados del almacén suelen moverse físicamente por el centro, dejando equipos desatendidos. Un bloqueo automático evita accesos no autorizados.  
Cómo implementarla:

1. Crear una GPO en la OU\_Magatzem\_17:  
   Nombre:  
   GPO\_Magatzem\_BlocPantalla\_17  
2. Editar:  
   User Configuration  
   Policies  
   Administrative Templates  
   Control Panel  
   Personalization  
3. Cambiar:  
   Screen saver timeout → 300 (5 minutos)  
   Force specific screen saver → Enabled  
   Password protect the screen saver → Enabled

Aunque el PDF no detalla exactamente esta GPO, sigue su línea de permisos, seguridad y políticas para departamentos.  
---

2.4. Aplicación y comprobación desde Windows 11  
En el cliente Windows 11 solo necesitas:

1. Conectarlo al dominio.  
2. Ejecutar “gpupdate /force”.  
3. Ver qué GPO se aplican mediante:  
   gpresult /R  
   Esto está explicado en el PDF (página 5).

FASE 3 – DESPLIEGUE AUTOMATIZADO DE SOFTWARE (GPO)  
Objetivo de la fase  
Configurar dos tipos de despliegue de software mediante Group Policy:

1. Departamento de Gestió (OU\_Gestio\_17): instalar **7zip** de manera **asignada** (instalación automática).  
2. Departamento de Gerència (OU\_Gerencia\_17): ofrecer **Firefox** mediante despliegue **publicado** (el usuario elige instalarlo desde Panel de Control).

Esto debe hacerse estrictamente siguiendo las técnicas del PDF: – Uso de un repositorio compartido llamado soft.  
– Permisos adecuados.  
– Elección correcta entre published o assigned.  
– Instalación basada en MSI.  
---

3.1. Crear el repositorio de software (soft)  
El PDF indica que primero debe existir una carpeta compartida en el servidor donde se almacenarán todos los .msi (páginas 24–28).  
Pasos:

1. En el servidor, crear en cualquier disco (por ejemplo E:) la carpeta:  
   soft\_17  
2. Click derecho → Properties → Sharing → Advanced Sharing  
   Marcar “Share this folder”.  
3. Share name:  
   soft  
4. Permissions:  
   Administrators → Full Control  
   IT (si existe) → Full Control  
   workers (o equivalente) → Read

Según el PDF, los trabajadores deben poder leer los MSI, pero solo IT y administradores pueden modificarlos.

1. Copiar dentro de esta carpeta los MSI necesarios:  
   – 7zip.msi  
   – firefox.msi

Nota importante  
Siempre se debe usar **ruta de red**:  
\\DC\\soft\\archivo.msi  
nunca unidad local.  
---

3.2. Crear GPO para 7zip (asignada) en OU\_Gestio\_17  
Lo que dice el PDF:  
– Instalación asignada \= instalación automática al iniciar sesión (páginas 22–23, 29–33).  
– Se recomienda aplicarlo a usuarios, no a equipos, para cargar al hacer logon.  
Pasos:

1. Abrir: Server Manager → Tools → Group Policy Management.  
2. Ir a:  
   OU\_TransLogic\_17 → USERS\_17 → OU\_Gestio\_17  
3. Click derecho → Create a GPO → Link it here.  
4. Nombre sugerido:  
   GPO\_7zip\_Gestio\_17  
5. Editar la GPO:  
   User Configuration  
   Policies  
   Software Settings  
   Software Installation  
   Click derecho → New → Package  
6. Seleccionar el paquete:  
   \\DC\\soft\\7zip.msi  
7. Cuando pregunte el tipo de instalación, seleccionar:  
   Advanced  
8. En “Deployment type”:  
   Assigned  
9. Marcar:  
   Install this application at logon.

Resultado esperado en Windows 11  
Al iniciar sesión un usuario del grupo gestio, 7zip se instalará automáticamente (PDF, página 31).  
---

3.3. Crear GPO para Firefox (publicada) en OU\_Gerencia\_17  
Lo que dice el PDF:  
– Publicada \= aparece en "Programas y características / Obtener programas" pero no se instala sola (páginas 33–36).  
– Solo se puede aplicar a usuarios.  
Pasos:

1. En Group Policy Management ir a:  
   OU\_TransLogic\_17 → USERS\_17 → OU\_Gerencia\_17  
2. Crear una GPO:  
   GPO\_Firefox\_Gerencia\_17  
3. Editar:  
   User Configuration  
   Policies  
   Software Settings  
   Software Installation  
   New → Package  
4. Seleccionar el MSI de Firefox:  
   \\DC\\soft\\firefox.msi  
5. Seleccionar:  
   Published  
6. No marcar instalación automática.

Resultado en Windows 11  
Los usuarios de gerencia verán Firefox disponible en:  
Panel de control → Programas → Obtener programas (PDF, página 39).  
No se instalará solo.  
---

3.4. Pregunta de consultoría: ¿Cómo crear un .msi desde un .exe?  
El PDF explica que las instalaciones automatizadas requieren archivos MSI.  
Si la aplicación solo proporciona .exe, existen herramientas externas que permiten:  
– Crear un MSI envolviendo el EXE (reempacar).  
– Instalar el EXE mientras se genera un MSI que registra todos los cambios.  
– Capturar instalación para transformarla en un MSI administrable.  
Ejemplos típicos (MÁS SENCILLO PARA ESTUDIANTE):  
– Usar un generador MSI (Advanced Installer, WiX Toolset, etc.).  
– Usar herramientas de reempaquetado (no incluidas en Windows Server por defecto).  
El concepto clave:  
Para que GPO pueda instalar software automáticamente, este debe estar en formato MSI.  
Si solo se dispone de EXE, se necesita un repackager que genere un MSI equivalente.  
---

3.5. Comprobación desde Windows 11 (como exige el enunciado)

1. Unir la máquina Windows 11 al dominio.  
2. Reiniciar.  
3. Iniciar sesión con un usuario de gestio para que instale 7zip.  
4. Ejecutar en PowerShell:  
   gpresult /R  
   Deberías ver:  
   GPO\_7zip\_Gestio\_17  
   GPO\_Firefox\_Gerencia\_17  
   Default Domain Policy  
   etc.  
5. Comprobar en inicio o menú aplicaciones que 7zip aparece instalado.  
6. Para gerencia, entrar en Panel de Control → Obtener programas y verificar que Firefox aparece disponible (no instalado).

# **\===================================================================== VERIFICAR GPO APLICADAS EN WINDOWS 11**

# **(Fase de comprobación – igual que aparece en el PDF, pág. 5\)**

En Windows 11 hay **dos métodos principales** para comprobar las directivas de grupo que se han aplicado a un usuario y a su equipo:  
---

1. MÉTODO 1 (RECOMENDADO): gpresult /R  
   Este método es el mismo que aparece en el PDF.

Sirve para mostrar un listado detallado de todas las GPO aplicadas tanto al EQUIPO como al USUARIO.  
Pasos:

1. En el Windows 11, iniciar sesión con el usuario que quieres comprobar.  
2. Pulsar:  
   Tecla Windows  
   Escribir:  
   cmd  
   Clic derecho → "Ejecutar como administrador".  
3. En la consola, escribir:  
   gpresult /R  
4. Pulsar Enter.

Interpretación del resultado: – En la sección CONFIGURACIÓN DE EQUIPO verás las GPO aplicadas al PC.  
– En la sección CONFIGURACIÓN DE USUARIO verás las GPO aplicadas al usuario actual.  
Esto es exactamente lo que muestra el PDF (pág. 5), con un ejemplo real.  
---

1. MÉTODO 2: gpresult /H informe.html  
   No está en tu PDF, pero ES SEGURO usarlo porque no añade nada nuevo, solo genera un informe visual más cómodo.

Pasos:

1. Abrir CMD como administrador.  
2. Ejecutar:  
   gpresult /H C:\\gpo.html  
3. Abrir el archivo generado con el navegador.

Ventaja:  
Informe mucho más claro y estructurado.  
Si no quieres usarlo, quédate solo con el MÉTODO 1, ya que es el único explícitamente mencionado en el PDF.  
---

1. CÓMO SABER SI UNA GPO SE HA APLICADO CORRECTAMENTE

Una GPO se considera aplicada correctamente si aparece en la lista de gpresult dentro de:  
– Objetos de directiva de grupo aplicados  
– Se muestra sin errores ni advertencias  
– Está dentro del apartado correcto: • CONFIGURACIÓN DE EQUIPO, si la GPO es de equipos.  
• CONFIGURACIÓN DE USUARIO, si la GPO afecta a usuarios.  
Si una GPO **NO aparece**, puede deberse a: – El usuario o equipo no está dentro de la OU correcta.  
– Falta permiso de lectura en "Security Filtering".  
– No se ha hecho gpupdate /force.  
– No se ha reiniciado (para políticas de equipo).  
– El MSI no está accesible por red (solo para software).  
Estos comportamientos están explicados indirectamente en el PDF.  
---

1. CUANDO DEBES USAR gpupdate /force  
   Según tu PDF (pág. 39), gpupdate sirve para refrescar las directivas.

En Windows 11:

1. Abrir PowerShell o CMD como administrador.  
2. Ejecutar:  
   gpupdate /force

Se recomienda hacerlo después de crear o modificar una GPO.  
---

1. EJEMPLOS REALES DE LO QUE DEBES VER

Para un usuario de Gestió (instalación asignada):  
Deberías ver en la sección de usuario:  
GPO\_7zip\_Gestio\_17  
Para un usuario de Gerència:  
Deberías ver en la sección de usuario:  
GPO\_Firefox\_Gerencia\_17  
Para todo el dominio:  
Siempre aparecerá:  
Default Domain Policy  
Según lo explicado en el PDF sobre jerarquía de GPO.

FASE 4 – PERFILS MÒBILS (ROAMING PROFILES)  
Basado únicamente en tu PDF (páginas 50–53), explicado paso a paso para un estudiante que empieza desde cero y adaptado a tu estructura con número de lista 17\.

# **\==================================================================== OBJETIVO DE LA FASE**

Configurar perfiles móviles para los usuarios del departamento **Gestió** (OU\_Gestio\_17), de forma que:  
– Su perfil se almacene en una carpeta compartida del servidor.  
– Cuando inicien sesión en cualquier Windows 11 unido al dominio, su perfil se copie automáticamente.  
– Al cerrar sesión, los cambios se sincronicen con el servidor.  
Esto permite movilidad entre equipos, tal como describe el PDF.

# **\==================================================================== 4.1. CREAR LA CARPETA PERFILS EN EL SERVIDOR**

Según el PDF (pág. 52), los perfiles móviles deben guardarse en una carpeta compartida en el servidor.

1. En el servidor, ve a tu disco de datos (por ejemplo “E:”).  
2. Crea una carpeta llamada:

perfils\_17

1. Click derecho → Properties → Sharing → Advanced Sharing.  
2. Marcar “Share this folder”.  
3. Share name:

perfils

1. Ir a Permissions:  
   – Administrators → Full Control  
   – SYSTEM → Full Control  
   – Domain Users → Change \+ Read  
   (o equivalente a “workers” tal como indica tu PDF).  
2. Aplicar y cerrar.

Esto refleja el mismo procedimiento usado en el PDF para carpetas de perfils y personals.

# **\==================================================================== 4.2. CONFIGURAR PERFIL MÓVIL EN LA PLANTILLA DE USUARIO**

El PDF recomienda asignar el perfil móvil desde la plantilla del grupo correspondiente (pág. 52).

1. Abrir:  
   Server Manager → Tools → Active Directory Users and Computers.  
2. Ir a:  
   OU\_TransLogic\_17 → USERS\_17 → OU\_Gestio\_17  
3. Seleccionar tu plantilla de usuario para Gestió.  
   Si no tienes ninguna, puedes crear una llamada:

Template\_Gestio\_17

1. Click derecho → Properties → pestaña Profile.  
2. En “Profile path”, escribir:

\\DC\\perfils%username%  
El PDF usa el formato “\\dc\\perfils\\\_Plantilla”, pero en tu caso es más correcto usar %username% para producir carpetas únicas por usuario, igual que en los ejemplos de perfiles reales (pág. 53).

1. Guardar los cambios.

Con esto, cualquier usuario creado a partir de esa plantilla recibirá perfil móvil automáticamente.

# **\==================================================================== 4.3. CREAR USUARIO DE PRUEBA (Gestió)**

1. En OU\_Gestio\_17, click derecho → New → User.  
2. Nombre del usuario:  
   Nombre: Usuari  
   Apellido: Prova  
   User logon name:

uprova17

1. Antes de finalizar, asegúrate de que se copie la configuración de la plantilla (si existe), o después revisa la pestaña Profile para confirmar:

Profile path \= \\DC\\perfils\\uprova17

# **\==================================================================== 4.4. PROBAR EN WINDOWS 11**

Según el PDF (pág. 53):

1. Inicia tu máquina Windows 11 unida al dominio.  
2. Inicia sesión con el usuario:

uprova17  
Qué debe ocurrir:  
– Windows creará un perfil local (C:\\Users\\uprova17).  
– Al mismo tiempo, en el servidor aparecerá una carpeta:  
E:\\perfils\_17\\uprova17.V6  
(V6 corresponde a la versión del perfil en Windows 10/11, tal como se ve en el PDF).

# **\==================================================================== 4.5. VERIFICAR CREACIÓN DEL PERFIL EN EL SERVIDOR**

Ir en el servidor a:  
E:\\perfils\_17\\  
Debe existir una carpeta con nombre:  
uprova17.V6  
Esto confirma que el perfil móvil está funcionando.

# **\==================================================================== 4.6. RESUMEN DE LA FASE**

Según lo que pide el enunciado:  
– Carpeta perfils creada y compartida.  
– Ruta asignada en la plantilla de usuarios.  
– Usuario de prueba creado.  
– Inicio de sesión en Windows 11 validado.  
– Carpeta uprova17.V6 creada automáticamente en el servidor.  
Si todo esto funciona, la fase está completada.

# **\==================================================================== OBJETIVO DE LA FASE**

Para evitar pérdida de datos si un equipo falla, vamos a **redirigir la carpeta local “Documents”** (del perfil del usuario) a una **ubicación de red segura**: su **carpeta personal (home folder)** en el servidor. Esto se hace con **GPO de “Folder Redirection”**, tal y como muestra el PDF. [\[material p...a chat GPT | PDF\]](https://mataroepiaeducat-my.sharepoint.com/personal/alu_santiago_moreno_mataro_epiaedu_cat/Documents/Fitxers%20del%20Microsoft%C2%A0Copilot%20Chat/material%20para%20chat%20GPT.pdf)  
Nota de contexto: La redirección evita mover archivos con cada inicio/cierre de sesión (como sí sucede en perfiles móviles). En Windows 11 esto permite que “Documentos” apunte directamente a tu recurso de red personal. [\[material p...a chat GPT | PDF\]](https://mataroepiaeducat-my.sharepoint.com/personal/alu_santiago_moreno_mataro_epiaedu_cat/Documents/Fitxers%20del%20Microsoft%C2%A0Copilot%20Chat/material%20para%20chat%20GPT.pdf)

# **\==================================================================== 5.1. PREPARACIÓN: VERIFICA QUE EXISTE LA CARPETA PERSONAL**

El PDF asume que **cada usuario posee una carpeta personal de red** (por ejemplo `\\DC\personal\%username%`). Si **ya la creaste en fases anteriores**, continúa. Si no, crea la compartición **personal** con **Read/Change** para usuarios y **Full Control** para Admin/SYSTEM (igual que hicimos en “soft” y “perfils”). El objetivo es que, al redirigir “Documents”, se cree automáticamente **\\DC\\personal\\Usuario\\Documents**. [\[material p...a chat GPT | PDF\]](https://mataroepiaeducat-my.sharepoint.com/personal/alu_santiago_moreno_mataro_epiaedu_cat/Documents/Fitxers%20del%20Microsoft%C2%A0Copilot%20Chat/material%20para%20chat%20GPT.pdf)  
**Nomenclatura con tu número de lista:**  
Puedes ubicar el recurso en tu disco de datos como `E:\personal_17`, compartido como `personal`. [\[material p...a chat GPT | PDF\]](https://mataroepiaeducat-my.sharepoint.com/personal/alu_santiago_moreno_mataro_epiaedu_cat/Documents/Fitxers%20del%20Microsoft%C2%A0Copilot%20Chat/material%20para%20chat%20GPT.pdf)

# **\==================================================================== 5.2. CREAR LA GPO DE REDIRECCIÓN (APLICABLE A TODO EL DOMINIO)**

El PDF muestra que las opciones de redirección se configuran en **User Configuration → Policies → Windows Settings → Folder Redirection** y que podemos aplicar **a todo el dominio** (Default Domain Policy) o a **una OU** concreta (si quieres limitarlo). En el enunciado se pide **para todo el dominio**, por lo que vamos a usar **Default Domain Policy**.   
**Pasos en el servidor (Windows Server):**

1. Abrir **Group Policy Management**:  
   Server Manager → Tools → Group Policy Management.   
2. Ir a: `Domains → translogic.local → Default Domain Policy` → clic derecho **Edit**.   
3. En el editor GPO, navegar a:  
   **User Configuration → Policies → Windows Settings → Folder Redirection**. (Ver captura y estructura en el PDF)   
4. Doble clic en **Documents** → pestaña **Target**.   
5. Seleccionar:  
   **Setting**: *Basic – Redirect everyone's folder to the same location*.  
   **Target folder location**: *Create a folder for each user under the root path*.  
   **Root Path**: `\\DC\personal` (adaptado a tu entorno).  
   La GPO creará automáticamente **\\DC\\personal\\NombreUsuario\\Documents** para cada usuario que inicie sesión. (Tal como indica el cuadro “For user Clair…”, mostrado en el PDF)   
6. **Aplicar** y **OK**.

Por qué “Basic” y “Create a folder for each user…”: Es lo que recomienda el PDF para simplificar, y además asegura que cada usuario recibe su propia ruta con subcarpeta “Documents” bajo su **home folder**. 

# **\==================================================================== 5.3. REFRESCAR GPO Y VERIFICAR EN WINDOWS 11**

**En el cliente Windows 11** (unido al dominio):

1. Inicia sesión con un usuario al que se aplica la Default Domain Policy.  
2. Abre **CMD/Powershell como administrador** y ejecuta:  
   `gpupdate /force` (para refrescar directivas justo después de configurar la GPO).   
3. Comprueba qué GPO se aplican con:  
   `gpresult /R` (deberías ver la **Default Domain Policy** entre las GPO aplicadas al usuario).   
4. Abre **Explorador de archivos** y navega a **Documentos** (carpeta del perfil de usuario). Según el PDF, ahora debe **apuntar** a la ruta de red personal y podrás verlo reflejado:  
   **Este equipo → Documentos** **(redirigido a \\DC\\personal\\Usuario\\Documents)**. (El PDF muestra la validación visual de cómo “Documentos” pasa a estar en el Z: de red)   
5. **Prueba funcional** (que pide el enunciado):  
   – Crea un archivo nuevo en **Documentos** desde el cliente (p.ej. `Nuevo documento de texto.txt`).  
   – Verifica en el servidor que el archivo aparece en: `E:\personal_17\Usuario\Documents`. (Equivalente a lo mostrado en la doble captura del PDF, donde el mismo fichero se ve desde el cliente y desde la carpeta de red) 

# **\==================================================================== 5.4. ¿QUÉ ESPERAR SI ALGO NO FUNCIONA?**

Si **no** ves el cambio:  
– Revisa que el usuario realmente esté en el dominio y que **Default Domain Policy** se aplique (gpresult).  
– Verifica que la **ruta de red** `\\DC\personal` es accesible desde el equipo, con permisos **Read/Change** para el usuario.  
– Si hace falta, **reinicia la sesión** (algunas redirecciones de carpetas se consolidan tras un logoff/logon). 

# **\==================================================================== 5.5. RESUMEN DE LA FASE (para tu informe)**

– **GPO creada** en **Default Domain Policy** → Folder Redirection → **Documents**.  
– **Configuración**: *Basic*, *Create a folder for each user*, **Root Path** `\\DC\personal`.  
– **Prueba**: Archivo de prueba creado desde **Documentos** en Windows 11, **visible en el servidor** en la ruta personal del usuario. (Capturas recomendadas en ambos puntos)

# **\==================================================================== FASE 6 – DELEGACIÓN DE FUNCIONES (HELPDESK)**

Objetivo:  
Crear un usuario “adminOU” dentro de **OU\_Admins\_17**, y delegarle permisos limitados sobre la OU principal **OU\_TransLogic\_17** para que pueda:

1. Reiniciar contraseñas de los trabajadores.  
2. Modificar la pertenencia a grupos.  
3. No debe poder crear usuarios nuevos.  
4. No debe tener privilegios globales de administrador.

La delegación funciona igual que las configuraciones de *Security Filtering* del PDF, donde se asignan permisos específicos a un usuario o grupo sin dar control total.

# **\==================================================================== 6.1. Crear el usuario adminOU**

En tu servidor:

1. Abrir: Server Manager → Tools → Active Directory Users and Computers.  
2. Ir a:  
   OU\_TransLogic\_17 → USERS\_17 → OU\_Admins\_17  
3. Click derecho → New → User.  
4. Datos:  
   Nombre: adminOU  
   Apellido: 17  
   User logon name: adminOU17  
5. Asignar una contraseña y marcar que no caduque (opcional).

Este usuario será el “técnico de soporte limitado”.

# **\==================================================================== 6.2. Delegar control sobre OU\_TransLogic\_17**

La delegación se hace igual que en tu PDF donde se aplican permisos específicos a grupos en “Security Filtering” (por ejemplo, añadir HR). La diferencia: en vez de filtrar GPO, delegamos control sobre una OU.  
Pasos:

1. En Active Directory Users and Computers, click derecho sobre:  
   OU\_TransLogic\_17  
2. Seleccionar:  
   Delegate Control…  
3. En el asistente, pulsar “Next”.  
4. Click en “Add…” → escribir:  
   adminOU17  
   → OK → Next.  
5. Seleccionar:  
   “Reset user passwords and force password change at next logon”.  
6. Marcar también:  
   “Modify membership of a group”.

Estas son exactamente las dos tareas solicitadas.

1. Finalizar.

Esto otorga permisos SOLO en esa OU, no en otras partes del dominio.

# **\==================================================================== 6.3. Verificar que adminOU17 tiene los permisos correctos**

Debemos comprobar **dos cosas:**  
A) Que SÍ puede:  
– Resetear una contraseña  
– Cambiar pertenencia a grupos  
B) Que NO puede:  
– Crear usuarios nuevos  
---

A) Cómo verificar que PUEDE resetear contraseñas

1. Iniciar sesión en Windows 11 O usar “Run as different user” para abrir ADUC como adminOU17.  
2. Abrir:  
   Active Directory Users and Computers  
3. Navegar a:  
   OU\_TransLogic\_17 → USERS\_17 → OU\_Gestio\_17 (o cualquier OU dentro de TransLogic).  
4. Click derecho en un usuario de prueba → “Reset Password”.  
   Si el botón está activo, la delegación funciona.

---

B) Cómo verificar que PUEDE modificar pertenencia a grupos

1. Desde ADUC con adminOU17:  
   Seleccionar cualquier usuario dentro de la OU.  
2. Ir a Propiedades → “Member Of”.  
3. Intentar agregar o quitar un grupo (gestio, magatzem, etc.).  
   Si funciona, la delegación es correcta.

---

C) Cómo verificar que NO puede crear usuarios

1. En ADUC con adminOU17:  
   Click derecho en cualquier OU dentro de OU\_TransLogic\_17.  
2. Debería aparecer “New → User” en gris o con error al intentar usarlo.  
3. Si intenta crear un usuario, debe obtener un mensaje de acceso denegado.

Esto demuestra que la delegación es correcta y limitada.

# **\==================================================================== 6.4. Resultado esperado (lo que debes mostrar en tu informe)**

El enunciado te pide demostrar mediante capturas:

1. Que adminOU17 puede resetear contraseñas.  
2. Que adminOU17 puede modificar membresías.  
3. Que adminOU17 NO puede crear usuarios.

Además, en tu informe técnico debes explicar:  
– Que la delegación se aplica solo sobre OU\_TransLogic\_17.  
– Que esto no otorga privilegios de dominio.  
– Que se ha hecho mediante el asistente “Delegate Control…” (misma lógica que *Security Filtering* del PDF).
