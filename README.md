# Sh4d0ws Toolbox

**Optimizador de Windows 11 portable, de pago, pensado para usuarios que no son técnicos.**
Diseño, ingeniería y producto de principio a fin por una sola persona.

> **Este repositorio es un escaparate.** El código fuente es privado porque es software comercial
> (ver [LICENCIA.txt](LICENCIA.txt)). Aquí se explica qué es, cómo está construido y qué decisiones
> de ingeniería hay detrás. Si estás evaluando mi trabajo, puedo enseñar el código en una revisión en directo.

![Inicio](docs/01-inicio.png)

## Qué es

Una aplicación de escritorio que mejora el rendimiento, la privacidad y la limpieza de Windows 11 sin
que el usuario tenga que saber qué es una clave del registro. Cada cambio se explica en lenguaje llano
**antes** de aplicarse, y **todo se puede deshacer**.

| | |
|---|---|
| **Un solo fichero** | Un `.exe` de unos 3 MB, sin instalador ni carpetas que acompañar. Lleva dentro los módulos, la configuración, la interfaz y las librerías |
| **Nada se aplica solo** | Los objetivos y el asistente solo *marcan* ajustes. Aplicar es un paso aparte, con una pantalla de revisión |
| **Todo es reversible** | Copia de seguridad antes de cada cambio; deshacer un ajuste, todos, o volver a un punto de restauración |
| **Modo prueba real** | Recorre todo lo marcado sin tocar absolutamente nada del sistema |
| **Licencia offline** | Firma RSA verificada sin conexión y ligada al equipo. Revertir cambios nunca depende de la licencia |

## Capturas

| Optimizar por objetivos | Asistente de inicio |
|---|---|
| ![Optimizar](docs/02-optimizar.png) | ![Asistente](docs/03-asistente.png) |

| Herramientas | Salud de los discos |
|---|---|
| ![Herramientas](docs/04-herramientas.png) | ![Discos](docs/05-discos.png) |

| Desinstalar programas | Deshacer, ajuste a ajuste |
|---|---|
| ![Programas](docs/06-programas.png) | ![Deshacer](docs/07-deshacer.png) |

Tema claro y oscuro, con contraste comprobado automáticamente (texto ≥ 4,5:1, controles ≥ 3:1):

![Tema claro](docs/08-tema-claro.png)

## Qué hace

- **Optimizar:** 82 ajustes de privacidad, rendimiento, interfaz, servicios y limpieza de Windows, agrupados por objetivos (juegos, privacidad, equipo más ágil, Windows sin molestias), cada uno con su nivel de riesgo y su explicación.
- **Asistente de inicio:** cuatro preguntas (uso, portátil o sobremesa, tipo de disco, molestias) y una propuesta adaptada al equipo.
- **Limpiar:** analiza cachés y temporales y limpia solo lo que el usuario marca.
- **Programas:** instalar y actualizar con WinGet, desinstalar y decidir qué arranca con Windows.
- **Herramientas:** estado del equipo, salud de los discos, qué programas consumen más, informes de energía, servicios, controladores, red y DNS, Windows Update, reparación del sistema.
- **Deshacer:** cambios hechos, puntos de restauración y registro de actividad.

## Cómo está hecho

```
Interfaz web (HTML/CSS/JS sin frameworks)  ──►  host de escritorio C# (.NET Framework 4.8 + WebView2)
                                                       │  puente JSON {acción, datos}
                                                       ▼
                                  un único runspace de PowerShell 5.1 con cola de trabajo
                                                       ▼
                                   módulos PowerShell  +  catálogo de ajustes declarativo
```

- **Host en C#:** ventana sin bordes con WebView2, navegación cerrada a un único origen, política de licencia y de modo prueba aplicada en un punto único, y un contrato de errores uniforme.
- **Motor en PowerShell:** un runspace en un hilo dedicado que serializa todas las operaciones, con topes de tiempo por tipo de tarea y rechazo explícito cuando está ocupado.
- **Interfaz sin dependencias:** componentes propios, sin `innerHTML` con datos externos y con una política de seguridad de contenido estricta. Pensada para usarse con teclado y con lectores de pantalla.
- **Reversibilidad por diseño:** ningún ajuste toca el sistema directamente. Todos pasan por envoltorios que guardan la copia de seguridad antes de escribir; el registro de deshacer solo guarda *nombres* de manejadores, nunca código ejecutable.
- **Un solo fichero portable:** los recursos viajan incrustados y se vuelcan de forma idempotente junto al `.exe`, sin tocar nunca los datos del usuario.
- **Calidad:** 17 suites de pruebas que se ejecutan sobre una copia aislada del proyecto, con un guardián que bloquea cualquier llamada que modifique el sistema real y comprueba al final que el original no cambió. El empaquetado verifica el contenido incrustado byte a byte antes de entregar nada.

### Tamaño

Unas 26.000 líneas entre C#, PowerShell, JavaScript, CSS y configuración, más las pruebas y las herramientas de empaquetado y de emisión de licencias.

## Lo que más me interesó resolver

- Hacer que cambios de sistema delicados fueran **seguros por construcción** y no por confianza (copia previa, simulación real, listas de elementos protegidos, confirmaciones según riesgo).
- Traducir lo técnico a **lenguaje llano** sin perder precisión, y comprobar la accesibilidad y el contraste de forma automática.
- Entregar **un único ejecutable portable** y verificable, con una cadena de empaquetado que se detiene si algo no cuadra.
- Probar software que modifica el sistema **sin tocar el sistema**.

## Tecnologías

C# · .NET Framework 4.8 · WinForms · WebView2 · PowerShell 5.1 · JavaScript (vanilla) · HTML/CSS · criptografía RSA · WinGet · Windows (registro, servicios, planes de energía, DISM)

---

## English summary

**Sh4d0ws Toolbox** is a commercial, single-file portable Windows 11 optimizer aimed at non-technical users: every change is explained in plain language before it is applied, nothing is applied automatically, every change is reversible (backup before write, per-change undo), and there is a real dry-run mode. Offline RSA-signed licensing. Stack: C# (.NET Framework 4.8, WebView2 host), a single queued PowerShell 5.1 runspace, and a framework-free web UI. The source code is private; this repository is a showcase. I'm happy to walk through the code in a live review.
