<!-- Latest release: Release 2.0.0 (Ver. 2.0.0) -->
# NOVA Launcher

Windows launcher for [DCS World](https://www.digitalcombatsimulator.com/). One window starts your companion apps
(VAICOM, SimHaptic, DCRealistic, ...), DCS itself and a PowerShell script that sets CPU affinity and priority for
DCS and any other process. It also toggles Tacview, keeps launch profiles and writes a log shared with your other
tools, and a red button closes everything it opened.

## Download and install
Click **Releases** on the right, or get the files in this repository:
- **`NOVA_Launcher_Setup.exe`**: the installer (recommended). It installs for your user, with no administrator
  rights, and asks if you want a desktop shortcut. The uninstaller asks before deleting your settings. On the
  **Releases** page it is named with the release number: `NOVA_Launcher_Setup_X.Y.Z.exe`.
- **`NOVA_Launcher.exe`**: the same program without installing (portable). Put it wherever you like.

Windows may show a SmartScreen warning the first time, because the program is not code-signed: choose
"More info" > "Run anyway". The first start on a new PC opens Settings so you can choose your DCS executable, your
Saved Games profile and your companion apps; nothing is launched until you do.

## How it works
**The main window**
- **Profile bar**: pick a launch profile, **Save** the current state under its name, create a **+ New** one or
  **- Del**ete it. A profile remembers which companion apps are on, Tacview on/off, the affinity script on/off and
  its retry settings.
- **DCS OPTIONS**: *Launch DCS Client*, *CPU Affinity and Priority* and *Tacview Recording* switches. The **▶** button next
  to the first two runs just that part.
- **COMPANION APPS**: your apps, in your order, each with a switch and a **▶** button to start only that one. An app
  whose exe cannot be found is greyed out with "not found".
- **Buttons / keys**: **LAUNCH** (`Enter`), **No Tacview** (`Space`: launches with Tacview off this time; greyed out when Tacview is not installed or its
  recording is already off), **DCS Only**
  (`Num 0`: DCS + affinity, no companion apps), **CANCEL** (`Esc`). `A` runs only the affinity script, `Supr` is the red
  close button above No Tacview / DCS Only (see below).

**What LAUNCH does, in order**
1. Switches Tacview on or off in your DCS profile (`options.lua`), if the Tacview module is installed there (if it
   is not, **Tacview Recording** and **No Tacview** are greyed out and nothing is touched).
2. Starts each enabled companion app (those with a delay start that many seconds later, in their own thread).
3. Starts DCS with your Saved Games profile (`-w <profile folder>`, none if the folder is just `DCS`) at *Above Normal*
   priority.
4. Runs the affinity script elevated (Windows asks for permission): companion apps and the extra processes are set at
   once; the DCS **client** is retried every *Delay between retries* seconds, up to *Max retries* times, until DCS
   is in memory. A DCS **server** instance is recognised and never touched.
5. Depending on the option *When everything is started*, it stays as it is (default), stays in the taskbar, goes to the system tray or closes itself.

Everything is written to the log (newest line on top, entries older than 7 days are purged at launch).

## Settings
Nothing in Settings is stored until you press **SAVE** (the priority buttons store immediately). The options are grouped in tabs: Paths & Affinity, Start, After launch, CPU Boost Auto, Free VRAM, Updates and General. **CPU Boost Auto** (off by default) gives DCS its priority and all the CPUs while it runs and keeps the VR runtime and the background apps you list off its best cores; everything is put back when DCS ends. **Free VRAM** (off by default) asks the programs of your list (overlays, browsers...) to close when DCS starts and can start them again afterwards.

The launcher speaks **English and Spanish**: *General > Language* (Automatic = the language you chose in the installer, else Windows', else English). The change applies at the next start; the log is always in English.

| Section | Option | What it does |
|---|---|---|
| PATHS | DCS Executable | The `DCS.exe` that is launched. |
| | DCS Saved Games Profile | Your DCS Saved Games folder (Tacview is switched in its `Config\options.lua`; its name is passed to DCS). Next to the executable you set the DCS client's thread mask and priority. |
| | Log File | Where the log is written. It can be shared with other tools: the newest entry is always first. |
| AFFINITY | Delay between retries (seconds) | How long the script waits before each attempt to find the DCS client. |
| | Max retries | How many attempts before giving up. |
| OPTIONS | Remember window position | Reopens the window where you left it. |
| | Launch automatically at start | Off by default. Launches at once when the launcher starts (no countdown). While on, the launcher never closes by itself and updates are checked when the session ends. Hold Shift while it opens to skip it once. Skipped if something of the previous session is open or DCS is already running. |
| | Automatic launch: which one | LAUNCH or LAUNCH without Tacview. |
| | When everything is started | *Leave the window as it is* (default), *Close the launcher*, *Keep it in the taskbar* or *Send it to the system tray*; leaving it open lets you close the apps later. |
| | When DCS closes: close the Companion Apps | Off by default. When the DCS the launcher started ends, closes the apps it started (same as the red button). Needs the launcher to stay open. |
| | When DCS closes: bring the launcher to the front | Off by default. When DCS ends, brings the launcher to the front (from the taskbar or tray), whether or not the apps are closed automatically. |
| | When all the apps are closed: close the launcher | Off by default. When every app the launcher started is closed, the launcher closes itself. |
| | Check for updates automatically | Looks for a new release in the background when the launcher starts (or, with automatic launch, when the session ends). |
| | Update automatically | Off by default. When a new release is found, updates at once without asking. Releases only, not betas. |
| | Receive beta versions | Switches from stable releases to every development build (asks first; they are less tested). |
| | Muted warnings / Show all again | Lists the warnings you muted with "Don't ask again" and brings them back (applied on SAVE). |
| | Settings folder / Open folder | Opens `%LOCALAPPDATA%\NOVA_Launcher`, where `config.json` and `profiles\` live. |

**⚙ Companion Apps** (button at the bottom of Settings): add, remove and reorder (▲ ▼) your apps. For each one:
label, executable (**…** to browse), **⏱ start delay** in seconds, **priority** (Idle, Below Normal, Normal, Above
Normal, High, RealTime), **⚡ apply affinity** on/off and the **thread selection**. The process name used by the
affinity script is taken from the exe file name.

**⚡ Other Processes**: the same affinity and priority for any other process by name (OBS, a voice program, a
browser...). Type the process name or pick its `.exe` with the **...** button: only the name is kept (a path or `.exe` is removed). The launcher records how each of these processes looked before it changed them and puts them back when
you close the apps. Each row also has a **Close with session** switch (off by default): a marked process (for example `SR-ClientRadio`, the SRS client) is closed by name together with the apps the launcher started, whoever started it.

**⚙ DCS Optimizations** (button in Settings; switch in the main window, above *Launch DCS Client*): a module from [DCS Control](https://github.com/NIGos/dcs-control) (by NIGos and contributors, MIT licence) that DCS loads by itself from `Saved Games\DCS\Scripts` and that removes CPU work inside DCS without changing the image. Its window has one switch per optimization (multi-threaded G-buffer and shadows, streaming timer cache, culling partition, model allocator...), the terrain prefetch mode and its status. An indicator at the left of the switch is green when it works in DCS and red when it is on but not working. Its window also lets you set your own in-flight key (it switches the optimizations off and on while you fly, only temporarily) and your own sounds. Turning it on downloads and installs the module (DCS must be closed); turning it off only switches the optimizations off and leaves the module installed; **Back to stock DCS** in its window removes it. Off by default. Single player: servers with integrity checks have not been tested.

**⚡ Affinity Profiles** (button in the Settings header): named thread masks (for example "CCD0 only" or "P-cores")
that appear as one-click buttons in every thread selector.

**Thread selector**: a grid of your CPU's threads grouped by CCD / core type (detected automatically for many Intel
and AMD CPUs, including X3D). *Quick select* has **All**, **None**, one button per CCD / group and your affinity
profiles.

## Closing everything
The red button above **No Tacview / DCS Only** (key `Supr` (Delete)) only appears when there is something to close:
- **CLOSE APPS** closes only the programs this launcher started (also those an earlier run left open, even after a
  restart or an update): a polite close first, a kill after a few seconds, children included. A dedicated `-server`
  DCS is never touched. It also puts the **Other Processes** processes back as they were.
- If something needs **administrator rights**, it closes what it can and asks once before Windows shows the UAC
- If DCS ends but the companion apps stay open, **DCS Only** launches DCS again at once; **LAUNCH** and **No Tacview** stay dimmed and ask first, because the apps could be started twice.
- When everything the launcher started is closed (by you, by the red button or by the options), the session is over: the launcher is ready again and comes to the front. With automatic launch, this is when it checks for updates.
  prompt. Programs that were **already open before launching** are only closed if you agree.
- If the launcher has opened nothing but programs defined in Settings are running, the button turns darker and says
  **CLOSE ALL APPS**: it lists them, asks (default No) and closes them. Use it when something got stuck. The
  Other Processes marked **Close with session** (for example `VoiceAttack`) count as defined programs too.
- In *taskbar* mode the window is minimized after launching; in *tray* mode an icon appears next to the clock: **double
  click** shows or hides the window, **right click** gives *Close apps*, *Show / Hide window*, *Check for updates* and
  *Quit launcher (keeps the apps open)*.

**Automatic reactions** (Settings > OPTIONS, all off by default, only when the launcher stays open after launching):
launch, then the launcher waits in the taskbar or tray; **when DCS closes** it can *close the apps* or *bring itself to
the front*; **when every app is closed** it can *close itself*. Otherwise it stays ready for the next launch.

Warnings about administrator rights, programs that were already open, closing the launcher with apps open and beta
versions have a **Don't ask again** box. The update question and "close all defined apps" are always asked.

## Updates
The launcher updates itself: **Update** in the header checks now; at start it checks quietly if the switch is on. A
message at the bottom (green for a release, blue for a beta) offers **[Update]** or **[Later]**; updating asks first (default No), checks the SHA-256 of
the download, tests the new program, replaces itself and restarts, keeping your settings.
- **Stable** (default): only releases, published on this branch (`main`).
- **Beta**: every build, from the `builds` branch (switch in Settings > UPDATES).

Once a launcher runs a version that was released, its header says **Release X.Y.Z** instead of **Ver. N.NN**.

## Current release
- **Release 2.0.0** (Ver. 2.0.0), published on 2026-11-06
- SHA-256 of NOVA_Launcher.exe: `2e9ad7fea9ff945f9dc9a10e462facba69b404cec591897c65e558013f04b0ba`

What changed: [CHANGES.md](CHANGES.md). All releases: [RELEASES.md](RELEASES.md).

This repository only holds the compiled files; the source code is private. The **Nova314 shield** (top left of the
launcher) opens https://www.nova314.com/.

---

# NOVA Launcher (Español)

Lanzador para Windows de [DCS World](https://www.digitalcombatsimulator.com/). Una sola ventana arranca tus aplicaciones complementarias (VAICOM, SimHaptic, DCRealistic, ...), el propio DCS y un script de PowerShell que fija la afinidad y la prioridad de CPU de DCS y de cualquier otro proceso. También activa o desactiva Tacview, guarda perfiles de lanzamiento y escribe un log compartido con tus otras herramientas, y un botón rojo cierra todo lo que abrió.

## Descarga e instalación
Pulsa **Releases** a la derecha, o consigue los ficheros en este repositorio:
- **`NOVA_Launcher_Setup.exe`**: el instalador (recomendado). Se instala para tu usuario, sin derechos de administrador, y te pregunta si quieres un acceso directo en el escritorio. El desinstalador pregunta antes de borrar tu configuración. En la página de **Releases** lleva el número de la release en el nombre: `NOVA_Launcher_Setup_X.Y.Z.exe`.
- **`NOVA_Launcher.exe`**: el mismo programa sin instalar (portable). Ponlo donde quieras.

Windows puede mostrar un aviso de SmartScreen la primera vez, porque el programa no está firmado: elige "Más información" > "Ejecutar de todas formas". El primer arranque en un PC nuevo abre Ajustes para que elijas tu ejecutable de DCS, tu perfil de Juegos guardados y tus aplicaciones complementarias; no se lanza nada hasta que lo hagas.

## Cómo funciona
**La ventana principal**
- **Barra de perfiles:** elige un perfil de lanzamiento, **Save** guarda el estado actual con su nombre, **+ New** crea uno nuevo y **- Del** lo borra. Un perfil recuerda qué aplicaciones complementarias están activas, Tacview sí/no, el script de afinidad sí/no y sus reintentos.
- **DCS OPTIONS:** interruptores *Launch DCS Client*, *CPU Affinity and Priority* y *Tacview Recording*. El botón **▶** junto a los dos primeros ejecuta solo esa parte.
- **COMPANION APPS:** tus aplicaciones, en el orden que quieras, cada una con un interruptor y un botón **▶** para iniciar solo esa. Si no se encuentra el exe de una app, aparece en gris con "not found".
- **Botones y teclas:** **LAUNCH** (`Enter`), **No Tacview** (`Espacio`: lanza con Tacview apagado esta vez; queda en gris cuando Tacview no está instalado o su grabación ya está apagada), **DCS Only** (`Num 0`: DCS + afinidad, sin aplicaciones complementarias) y **CANCEL** (`Esc`). `A` ejecuta solo el script de afinidad, y `Supr` es el botón rojo de cierre, encima de No Tacview / DCS Only (ver más abajo).

**Qué hace LAUNCH, en orden**
1. Activa o desactiva Tacview en tu perfil de DCS (`options.lua`), si el módulo de Tacview está instalado allí (si no lo está, **Tacview Recording** y **No Tacview** quedan en gris y no se toca nada).
2. Inicia cada aplicación complementaria activada (las que tienen retraso arrancan esos segundos más tarde, en su propio hilo).
3. Inicia DCS con tu perfil de Juegos guardados (`-w <carpeta del perfil>`, ninguno si la carpeta es simplemente `DCS`) con prioridad *Above Normal*.
4. Ejecuta el script de afinidad elevado (Windows pide permiso): las aplicaciones complementarias y los procesos extra se ajustan de inmediato; el **cliente** de DCS se reintenta cada *Delay between retries* segundos, hasta *Max retries* veces, hasta que DCS esté en memoria. Una instancia de **servidor** DCS se reconoce y nunca se toca.
5. Según la opción *When everything is started*, se queda como está (por defecto), se queda en la barra de tareas, pasa a la bandeja del sistema o se cierra.

Todo se escribe en el log (la línea más nueva arriba; las entradas de más de 7 días se purgan al lanzar).

## Ajustes
Nada en Ajustes se guarda hasta que pulsas **SAVE** (los botones de prioridad se guardan al instante). Las opciones están agrupadas en pestañas: Paths & Affinity, Start, After launch, CPU Boost Auto, Free VRAM, Updates y General. **CPU Boost Auto** (apagado por defecto) da a DCS su prioridad y todas las CPU mientras funciona y mantiene el runtime de VR y las apps en segundo plano que listes fuera de sus mejores núcleos; todo se deja como estaba cuando DCS termina. **Liberar VRAM** (apagado por defecto) pide a los programas de tu lista (superposiciones, navegadores...) que se cierren cuando DCS arranca y puede volver a abrirlos después.

El lanzador habla **inglés y español**: *General > Idioma* (Automático = el idioma que elegiste en el instalador, si no el de Windows, si no inglés). El cambio se aplica al siguiente inicio; el log siempre está en inglés.

| Sección | Opción | Qué hace |
|---|---|---|
| PATHS | DCS Executable | El `DCS.exe` que se lanza. |
| | DCS Saved Games Profile | Tu carpeta de Juegos guardados de DCS (Tacview se activa en su `Config\options.lua`; su nombre se pasa a DCS). Junto al ejecutable defines la máscara de hilos y la prioridad del cliente de DCS. |
| | Log File | Dónde se escribe el log. Puede compartirse con otras herramientas: la entrada más nueva va siempre primero. |
| AFFINITY | Delay between retries (seconds) | Cuánto espera el script antes de cada intento de encontrar el cliente de DCS. |
| | Max retries | Cuántos intentos antes de rendirse. |
| OPTIONS | Remember window position | Reabre la ventana donde la dejaste. |
| | Launch automatically at start | Desactivado por defecto. Lanza al instante al arrancar (sin cuenta atrás). Mientras está activo, el lanzador nunca se cierra solo y las actualizaciones se comprueban al terminar la sesión. Mantén Shift al abrirlo para saltarlo esa vez. No lanza si algo de la sesión anterior sigue abierto o DCS ya está en marcha. |
| | Automatic launch: which one | LAUNCH o LAUNCH sin Tacview. |
| | When everything is started | *Leave the window as it is* (por defecto), *Close the launcher*, *Keep it in the taskbar* o *Send it to the system tray*; dejarlo abierto permite cerrar las apps más tarde. |
| | When DCS closes: close the Companion Apps | Desactivada por defecto. Cuando termina el DCS que lanzó el lanzador, cierra las apps que él inició (igual que el botón rojo). Necesita que el lanzador siga abierto. |
| | When DCS closes: bring the launcher to the front | Desactivada por defecto. Al terminar DCS, trae el lanzador al frente (desde la barra de tareas o la bandeja), se cierren o no las apps automáticamente. |
| | When all the apps are closed: close the launcher | Desactivada por defecto. Cuando se cierran todas las apps que inició el lanzador, este se cierra solo. |
| | Check for updates automatically | Busca una release nueva en segundo plano al arrancar el lanzador (o, con el lanzamiento automático, al terminar la sesión). |
| | Update automatically | Desactivado por defecto. Si hay una release nueva, actualiza al instante sin preguntar. Solo releases, no betas. |
| | Receive beta versions | Pasa de las releases estables a cada compilación de desarrollo (pregunta antes; están menos probadas). |
| | Muted warnings / Show all again | Lista los avisos que silenciaste con "No volver a preguntar" y los devuelve (se aplica al pulsar SAVE). |
| | Settings folder / Open folder | Abre `%LOCALAPPDATA%\NOVA_Launcher`, donde están `config.json` y `profiles\`. |

**⚙ Companion Apps** (botón al final de Ajustes): añade, quita y reordena (▲ ▼) tus apps. Para cada una: etiqueta, ejecutable (**…** para buscarlo), **⏱ retraso de arranque** en segundos, **prioridad** (Idle, Below Normal, Normal, Above Normal, High, RealTime), **⚡ aplicar afinidad** sí/no y la **selección de hilos**. El nombre de proceso que usa el script de afinidad se toma del nombre del fichero exe.

**⚡ Other Processes:** la misma afinidad y prioridad para cualquier otro proceso por nombre (OBS, un programa de voz, un navegador...). Escribe el nombre del proceso o elige su `.exe` con el botón **...**: solo se guarda el nombre (se quitan la ruta y el `.exe`). El lanzador anota cómo estaba cada uno antes de cambiarlo y lo deja como estaba al cerrar las apps. Cada fila tiene además un interruptor **Close with session** (desactivado por defecto): un proceso marcado (por ejemplo `SR-ClientRadio`, el cliente de SRS) se cierra por nombre junto con las apps que inició el lanzador, lo haya iniciado quien lo haya iniciado.

**⚙ DCS Optimizations** (botón en Ajustes; interruptor en la ventana principal, encima de *Launch DCS Client*): un módulo de [DCS Control](https://github.com/NIGos/dcs-control) (de NIGos y colaboradores, licencia MIT) que DCS carga solo desde `Saved Games\DCS\Scripts` y que quita trabajo de CPU dentro de DCS sin cambiar la imagen. Su ventana tiene un interruptor por cada optimización (G-buffer y sombras multihilo, caché del temporizador, reparto del culling, asignador de modelos...), el modo del arreglo de prefetch del terreno y su estado. Un indicador a la izquierda del interruptor está verde cuando funciona en DCS y rojo cuando está activado pero no funciona. Su ventana permite además elegir tu propia tecla de vuelo (apaga y enciende las optimizaciones mientras vuelas, solo de forma temporal) y tus propios sonidos. Al activarlo se descarga e instala el módulo (con DCS cerrado); al desactivarlo solo se apagan las optimizaciones y el módulo queda instalado; **Back to stock DCS** en su ventana lo quita. Desactivado por defecto. Un jugador: no se han probado servidores con comprobación de integridad.

**⚡ Affinity Profiles** (botón en la cabecera de Ajustes): máscaras de hilos con nombre (por ejemplo "solo CCD0" o "P-cores") que aparecen como botones de un clic en cada selector de hilos.

**Selector de hilos:** una cuadrícula con los hilos de tu CPU agrupados por CCD / tipo de núcleo (detectado automáticamente en muchas CPU Intel y AMD, incluidas las X3D). *Quick select* tiene **All**, **None**, un botón por cada CCD / grupo y tus perfiles de afinidad.

## Cerrar todo
El botón rojo encima de **No Tacview / DCS Only** (tecla `Supr`) solo aparece cuando hay algo que cerrar:
- **CLOSE APPS** cierra solo los programas que inició este lanzador (también los que dejó abiertos una ejecución anterior, incluso tras un reinicio o una actualización): primero un cierre educado, y tras unos segundos un cierre forzado, hijos incluidos. Un DCS dedicado con `-server` nunca se toca. También deja los procesos de **Other Processes** como estaban.
- Si algo necesita **derechos de administrador**, cierra lo que puede y pregunta una vez antes de que Windows muestre el aviso UAC. Los programas que ya estaban **abiertos antes de lanzar** solo se cierran si tú lo aceptas.
- Si DCS termina pero las Apps Auxiliares siguen abiertas, **DCS Only** lanza DCS de nuevo al instante; **LAUNCH** y **No Tacview** siguen atenuados y preguntan antes, porque las apps podrían iniciarse dos veces.
- Cuando todo lo que inició el lanzador está cerrado (por ti, por el botón rojo o por las opciones), la sesión ha terminado: el lanzador queda listo para lanzar de nuevo y pasa a primer plano. Con el lanzamiento automático, es entonces cuando comprueba las actualizaciones.
- Si el lanzador no ha abierto nada pero hay programas definidos en Ajustes en marcha, el botón se oscurece y pone **CLOSE ALL APPS**: los lista, pregunta (por defecto No) y los cierra. Úsalo cuando algo se haya quedado colgado. Los Other Processes marcados con **Close with session** (por ejemplo `VoiceAttack`) cuentan también como programas definidos.
- En modo *taskbar* la ventana se minimiza tras lanzar; en modo *tray* aparece un icono junto al reloj: **doble clic** muestra u oculta la ventana, **clic derecho** ofrece *Close apps*, *Show / Hide window*, *Check for updates* y *Quit launcher (keeps the apps open)*.

**Reacciones automáticas** (Ajustes > OPTIONS, todas desactivadas por defecto, solo cuando el lanzador sigue abierto tras lanzar): lanzas, el lanzador espera en la barra de tareas o la bandeja; **cuando DCS se cierra** puede *cerrar las apps* o *traerse al frente*; **cuando todas las apps están cerradas** puede *cerrarse solo*. Si no, queda listo para el siguiente lanzamiento.

Los avisos sobre derechos de administrador, programas que ya estaban abiertos, cerrar el lanzador con apps abiertas y versiones beta tienen una casilla **No volver a preguntar**. La pregunta de actualizar y "cerrar todas las apps definidas" siempre se hacen.

## Actualizaciones
El lanzador se actualiza solo: **Update** en la cabecera comprueba ahora; al arrancar comprueba en silencio si el interruptor está activado. Un mensaje en la parte inferior (verde para una release, azul para una beta) ofrece **[Update]** o **[Later]**; actualizar pregunta antes (por defecto No), comprueba el SHA-256 de la descarga, prueba el programa nuevo, se sustituye a sí mismo y se reinicia, conservando tus ajustes.
- **Estable** (por defecto): solo releases, publicadas en esta rama (`main`).
- **Beta:** cada compilación, desde la rama `builds` (interruptor en Ajustes > UPDATES).

Cuando un lanzador ejecuta una versión que fue release, su cabecera dice **Release X.Y.Z** en lugar de **Ver. N.NN**.

## Release actual
- **Release 2.0.0** (Ver. 2.0.0), publicada el 2026-11-06
- SHA-256 de NOVA_Launcher.exe: `2e9ad7fea9ff945f9dc9a10e462facba69b404cec591897c65e558013f04b0ba`

Qué ha cambiado: [CHANGES.md](CHANGES.md). Todas las releases: [RELEASES.md](RELEASES.md).

Este repositorio solo contiene los ficheros compilados; el código fuente es privado. El **escudo de Nova314** (arriba a la izquierda del lanzador) abre https://www.nova314.com/.
