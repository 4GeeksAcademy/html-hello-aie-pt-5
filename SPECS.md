SPECS — Panel de Administración AgentHub
1. Descripción del producto
AgentHub es una plataforma SaaS donde las empresas alquilan agentes de IA preconfigurados,
que pueden equiparse con distintas skills (navegar por la web, leer documentos, gestionar
calendarios) y desplegarse para tareas de negocio específicas.
Este documento especifica el panel de administración interno de AgentHub. Lo usa el equipo
administrador de la plataforma para supervisar ingresos, usuarios, agentes, skills,
contratos de alquiler y errores de ejecución desde un solo lugar.
2. Stack y restricciones
HTML5 semántico: uso de `header`, `nav`, `main`, `section`, `article`, `table` y similares.
Tailwind CSS vía CDN (`https://cdn.tailwindcss.com`) para todos los estilos, configurado con
`tailwind.config = { darkMode: 'class' }`.
JavaScript vanilla para toda la interactividad: sin frameworks (React, Vue, etc.),
sin jQuery y sin herramientas de build.
Sin backend ni APIs: todos los datos están hardcodeados en el HTML.
Sin archivos CSS propios ni atributos `style` en línea.
Un único archivo `index.html`: las seis secciones viven en la misma página y JavaScript
muestra solo la sección activa, lo que permite mantener el modo oscuro al navegar.
Íconos como SVG en línea (estilo outline).
Diseño usable en escritorio (≥ 1024 px) y tablet (≥ 768 px).
Moneda: dólares estadounidenses (USD). Fecha de referencia del prototipo: octubre de 2026.
3. Datos de ejemplo
Todos los nombres se repiten entre secciones. Los números del Dashboard y del catálogo de
skills se calculan a partir de estas tablas.
3.1 Usuarios
Nombre	Email	Empresa	Plan	Estado	Registro
Laura Méndez	laura@novaretail.com	Nova Retail	Enterprise	Activo	2025-11-03
Diego Ramírez	diego@finexa.io	Finexa	Pro	Activo	2026-02-14
Sofía Castro	sofia@lumenlegal.com	Lumen Legal	Pro	Activo	2026-03-22
Ana Torres	ana@bloomhealth.com	Bloom Health	Starter	Activo	2025-12-10
Martín Rojas	martin@kairolabs.dev	Kairo Labs	Starter	Suspendido	2026-01-08
Valentina Ruiz	valentina@ortizstudio.com	Ortiz Studio	Starter	Pendiente	2026-09-28
3.2 Skills
Skill	Descripción	Precio / mes	Agentes que la usan
Navegación web	Busca y lee páginas web en tiempo real	$400	3 (SalesBot, SupportAI, DataScout)
Lectura de documentos	Extrae y resume texto de PDF, Word y hojas de cálculo	$300	3 (DocReader, SupportAI, DataScout)
Gestión de calendario	Crea, mueve y consulta eventos de calendario	$250	2 (DocReader, Scheduler Pro)
Envío de emails	Redacta y envía correos desde la cuenta del cliente	$200	2 (SalesBot, SupportAI)
3.3 Agentes
Agente	Propietario	Estado	Skills	Prompt de sistema
SalesBot	Laura Méndez (Nova Retail)	Activo	Navegación web, Envío de emails	Eres SalesBot, asistente comercial de Nova Retail. Investiga prospectos en la web y redacta emails de seguimiento breves y cordiales.
DocReader	Sofía Castro (Lumen Legal)	Activo	Lectura de documentos, Gestión de calendario	Eres DocReader, asistente legal de Lumen Legal. Lee contratos, resume las cláusulas clave y agenda los vencimientos en el calendario.
SupportAI	Diego Ramírez (Finexa)	Fallando	Navegación web, Lectura de documentos, Envío de emails	Eres SupportAI, agente de soporte de Finexa. Responde consultas de clientes por email usando la documentación oficial y la web.
Scheduler Pro	Ana Torres (Bloom Health)	Inactivo	Gestión de calendario	Eres Scheduler Pro, asistente de agenda de Bloom Health. Coordina citas y evita conflictos de horario.
DataScout	Laura Méndez (Nova Retail)	Fallando	Navegación web, Lectura de documentos	Eres DataScout, analista de mercado de Nova Retail. Recopila precios de la competencia en la web y extrae datos de informes PDF.
Resumen: 2 activos, 2 fallando, 1 inactivo.
3.4 Contratos
El precio mensual es la suma de las skills contratadas. El importe pagado es
(precio mensual × meses) − descuento.
ID	Cliente	Agente	Skills	Inicio	Fin	Meses	Subtotal	Descuento	Pagado	Estado
C-001	Nova Retail	SalesBot	Navegación web, Envío de emails	2026-07-01	2026-12-31	6	$3,600	—	$3,600	Activo
C-002	Lumen Legal	DocReader	Lectura de documentos, Gestión de calendario	2026-07-01	2026-12-31	6	$3,300	—	$3,300	Activo
C-003	Finexa	SupportAI	Navegación web, Lectura de documentos, Envío de emails	2026-08-01	2027-01-31	6	$5,400	Cupón LAUNCH10 (−10 %): −$540	$4,860	Activo
C-004	Bloom Health	Scheduler Pro	Gestión de calendario	2026-01-01	2026-06-30	6	$1,500	—	$1,500	Finalizado
C-005	Nova Retail	DataScout	Navegación web, Lectura de documentos	2026-09-01	2026-11-30	3	$2,100	Cupón WELCOME20 (−20 %): −$420	$1,680	Activo
3.5 Errores
ID	Timestamp	Agente	Tipo	Descripción	Estado inicial
E-01	2026-10-02 09:14	SupportAI	Autenticación	El token de la cuenta de email expiró al enviar una respuesta	Pendiente
E-02	2026-10-02 08:47	DataScout	Timeout	La página objetivo no respondió en 30 s	Pendiente
E-03	2026-10-01 22:05	SupportAI	Límite de tasa	Se superó el límite de solicitudes del proveedor de email	Pendiente
E-04	2026-10-01 17:30	DataScout	Parsing	No se pudo extraer texto de un PDF escaneado	Pendiente
E-05	2026-09-30 11:12	SalesBot	Timeout	La búsqueda web excedió el tiempo límite	Resuelto
E-06	2026-09-29 15:40	DocReader	Parsing	Formato de archivo `.pages` no soportado	Resuelto
Cada error tiene una traza ficticia de 4 a 6 líneas. Ejemplo para E-01:
```
AuthError: invalid_grant (token expired)
    at EmailSkill.send (skills/email.js:88:13)
    at AgentRunner.executeStep (runner/agent.js:212:21)
    at AgentRunner.run (runner/agent.js:145:9)
Agent: SupportAI | Contrato: C-003 | Intento: 3/3
```
3.6 Valores del Dashboard (octubre 2026)
Ingresos del mes: $2,520 (contratos activos: 600 + 550 + 810 + 560).
Pérdida por descuentos y cupones del mes: $230 (C-003: $90, C-005: $140).
Agentes activos: 2.
Agentes fallando: 2.
4. Especificaciones por sección
4.1 Dashboard
Cuatro tarjetas de métrica (componente 5.2) en una cuadrícula responsive: 1 columna en
pantallas pequeñas y 2×2 desde `sm`. Cada tarjeta muestra un ícono, una etiqueta y el
valor hardcodeado de la sección 3.6.
Cada tarjeta usa un color de acento distinto según la métrica, aplicado como borde
izquierdo (`border-l-4`) y fondo del ícono: ingresos en verde, descuentos en ámbar,
agentes activos en índigo y agentes fallando en rojo. Todas tienen `shadow-sm` y
esquinas redondeadas.
Debajo de las tarjetas, un `div` de ancho completo y `h-64`, con borde discontinuo
(`border-2 border-dashed`) y el texto centrado "Gráfico de actividad semanal", representa
el gráfico.
La tarjeta de agentes fallando incluye un enlace "Ver log de errores" que navega a la
sección Log de errores.
4.2 Gestión de usuarios
Una `table` con columnas Nombre, Email, Plan, Estado y Acciones, con los 6 usuarios de
la sección 3.1. La cabecera usa `thead` con texto en mayúsculas pequeñas y las filas
tienen separadores horizontales y un fondo suave al pasar el mouse.
El estado se muestra como badge (componente 5.4): Activo en verde, Suspendido en rojo y
Pendiente en ámbar. El plan se muestra como texto.
La última columna contiene el dropdown ⋮ (componente 5.3) con "Ver detalle" y "Eliminar".
"Ver detalle" abre el modal (componente 5.5) con todos los datos del usuario: nombre,
email, empresa, plan, estado, fecha de registro y agentes que posee (por ejemplo, Laura
Méndez: SalesBot, DataScout).
"Eliminar" muestra un `confirm()` nativo; si se acepta, la fila se elimina del DOM.
4.3 Gestión de agentes
Un listado de tarjetas (`article`), una por cada agente de la sección 3.3, en una
cuadrícula de 1 columna en tablet y 2 columnas desde `xl`. Cada tarjeta muestra el nombre
del agente como título, el propietario debajo y el badge de estado arriba a la derecha:
Activo en verde, Inactivo en gris y Fallando en rojo.
Cada tarjeta incluye una lista de skills colapsable (componente 5.6), cerrada por
defecto. El botón muestra "Skills (N)" con una flecha que gira al abrirse; las skills se
muestran como etiquetas pequeñas.
Arriba a la derecha de cada tarjeta, junto al badge, hay un dropdown ⋮ con "Configurar"
y "Eliminar".
"Configurar" abre el modal con el título "Configurar SalesBot" (según el agente), una
etiqueta "Prompt de sistema" y un `<textarea>` editable de 8 filas que contiene el prompt
de la sección 3.3. El modal tiene los botones "Cancelar" (cierra sin guardar) y "Guardar"
(guarda el texto en memoria y cierra; no hay persistencia tras recargar).
"Eliminar" muestra un `confirm()`; si se acepta, la tarjeta se elimina del DOM.
4.4 Skills
En la parte superior, un recuadro informativo con fondo índigo suave y un ícono de
información explica: "Una skill es una capacidad que se puede adjuntar a un agente, como
navegar por la web o leer documentos. Cada skill tiene un precio mensual y se contrata
junto con el agente."
Debajo, una cuadrícula de tarjetas (2 columnas en tablet y 4 desde `xl`) con las skills
de la sección 3.2. Cada tarjeta muestra el nombre, la descripción, el precio mensual y un
badge índigo con el texto "N agentes".
Cada tarjeta tiene un dropdown ⋮ con "Ver detalle" y "Eliminar".
"Ver detalle" abre el modal con el nombre, la descripción, el precio mensual y la lista
de agentes que tienen la skill habilitada.
"Eliminar" muestra un `confirm()`; si se acepta, la tarjeta se elimina del DOM.
4.5 Contrataciones de agentes
Una `table` con columnas ID, Cliente, Agente, Skills, Fechas, Importe pagado, Estado y
Acciones, con los 5 contratos de la sección 3.4.
La columna Skills muestra cada skill como etiqueta pequeña; Fechas muestra
"inicio → fin"; Importe pagado se alinea a la derecha con formato `$4,860`; Estado es un
badge: Activo en verde y Finalizado en gris.
Cada fila tiene un dropdown ⋮ con "Ver detalle".
"Ver detalle" abre el modal con el desglose del contrato: cliente, agente, fechas, una
tabla interna con columnas Skill, Precio/mes, Meses y Subtotal (una fila por skill), y
debajo el subtotal general, el descuento con el nombre del cupón (o "Sin descuento") y el
total pagado en negrita.
4.6 Log de errores
Una `table` con columnas Timestamp, Agente, Tipo, Descripción, Estado y Acciones, con los
6 errores de la sección 3.5, ordenados del más reciente al más antiguo.
El tipo se muestra como badge con código de color: Autenticación en rojo, Límite de tasa
en naranja, Timeout en ámbar y Parsing en violeta. El estado es un badge: Pendiente en
gris y Resuelto en verde.
Cada fila tiene un dropdown ⋮ con "Ver detalle" y "Marcar como resuelto".
"Ver detalle" abre el modal con el ID, el agente, el tipo, la descripción y la traza
completa dentro de un bloque `<pre>` con fuente monoespaciada y fondo gris oscuro.
"Marcar como resuelto" cambia el badge de estado de esa fila a Resuelto (verde) y
desactiva esa opción del menú. En los errores ya resueltos, la opción aparece desactivada
desde el inicio.
4.7 Layout e interacciones globales
Estructura: `aside` con la sidebar a la izquierda y, a la derecha, un `header` fijo arriba
y un `main` con las seis `section` (solo una visible a la vez).
El `header` muestra el nombre de la sección activa como `h1` y, a la derecha, el toggle
de modo oscuro (componente 5.7).
Al cargar, la sección visible es el Dashboard.
Todos los dropdowns se cierran al hacer clic fuera de ellos, y solo puede haber uno
abierto a la vez.
Todos los modales se cierran con el botón ✕, con clic en el backdrop y con la tecla Escape.
5. Inventario de componentes
5.1 Sidebar
`aside` con un `nav` que contiene el logo "AgentHub" y seis enlaces con ícono: Dashboard,
Usuarios, Agentes, Skills, Contrataciones y Log de errores. En tablet (`md`) solo muestra los
íconos (ancho `w-20`); desde `lg` muestra ícono y texto (ancho `w-64`). El enlace activo
tiene fondo índigo y texto blanco. Al hacer clic en un enlace, JavaScript oculta todas las
secciones (clase `hidden`), muestra la seleccionada, marca el enlace como activo y actualiza
el título del header.
5.2 Tarjeta de métrica
Contenedor redondeado con `shadow-sm`, borde izquierdo de color de acento, ícono dentro de un
círculo del mismo color, etiqueta en texto pequeño gris y valor en texto grande y negrita.
5.3 Dropdown de acciones
Botón con el carácter ⋮ (`aria-haspopup="true"`, `aria-expanded` actualizado por JS) y un menú
`absolute right-0` con sombra, oculto por defecto. Comportamiento:
Clic en ⋮: abre el menú; si ya estaba abierto, lo cierra.
Abrir un dropdown cierra cualquier otro que esté abierto.
Clic fuera del dropdown: lo cierra.
Elegir una opción ejecuta la acción y cierra el menú.
La opción "Eliminar" va en texto rojo.
Para que el menú de la última fila no quede recortado, los contenedores de tablas no usan
`overflow-hidden` y tienen espacio inferior (`pb-24`).
5.4 Badge
`span` con `rounded-full px-2.5 py-0.5 text-xs font-medium`, fondo suave y texto oscuro del
mismo color (por ejemplo, `bg-green-100 text-green-800`), con variantes `dark:` más oscuras
(por ejemplo, `dark:bg-green-900 dark:text-green-200`).
5.5 Modal
Un único modal reutilizable. Backdrop `fixed inset-0 bg-black/50` y un panel centrado
(`max-w-lg`, redondeado, con sombra) con título, botón ✕ arriba a la derecha y un área de
contenido que JavaScript rellena según la acción. Comportamiento:
Se cierra con el botón ✕, con clic en el backdrop (no en el panel) y con Escape.
Mientras está abierto, el `body` no hace scroll (clase `overflow-hidden`).
5.6 Lista de skills colapsable
Botón "Skills (N)" con una flecha (`aria-expanded`). El contenido usa
`overflow-hidden transition-all duration-300 ease-in-out` y alterna entre `max-h-0`
(cerrado, estado inicial) y `max-h-40` (abierto). La flecha alterna entre sin rotación y
`rotate-90`. Un segundo clic la vuelve a cerrar.
5.7 Toggle de modo oscuro
Botón en el header con ícono de luna en modo claro y de sol en modo oscuro, con
`aria-label` descriptivo. Al hacer clic, agrega o quita la clase `dark` en `<html>` y guarda
la preferencia en `localStorage` para aplicarla al recargar. Todos los componentes definen
colores para ambos modos con las utilidades `dark:` (por ejemplo, fondo general `bg-gray-50`
y `dark:bg-gray-900`).
6. Criterios de aceptación
`SPECS.md` existe en la raíz del repositorio y fue commiteado en un commit propio antes de
cualquier archivo HTML.
El proyecto es un único `index.html` que carga Tailwind vía CDN, sin archivos CSS propios
ni atributos `style` en línea.
Toda la interactividad está escrita en JavaScript vanilla.
La sidebar muestra enlaces a las seis secciones; al hacer clic en cada uno se muestra la
sección correcta y el enlace queda marcado como activo.
El HTML usa `aside`, `nav`, `header`, `main`, `section` y `table` donde corresponde.
El Dashboard muestra cuatro tarjetas con ícono, etiqueta y los valores de la sección 3.6,
y un área de gráfico con borde discontinuo debajo.
La tabla de usuarios tiene al menos 5 filas con nombre, email, plan y badge de estado.
El listado de agentes tiene al menos 4 agentes con nombre, propietario, badge de estado y
skills colapsadas.
El catálogo de skills tiene al menos 4 skills con nombre, descripción y número de agentes,
más el recuadro explicativo.
La tabla de contratos tiene al menos 4 contratos con cliente, agente, skills, fechas e
importe pagado.
El log tiene al menos 6 errores con timestamp, agente, badge de tipo con color y
descripción.
Dropdown: en cada fila o tarjeta, el botón ⋮ abre el menú, un segundo clic lo cierra, un
clic fuera lo cierra y nunca hay dos abiertos a la vez.
Modal: "Ver detalle" abre un modal con el contenido correcto en Usuarios, Skills,
Contrataciones y Log de errores.
Modal: "Configurar" abre un modal con el prompt del agente en un `<textarea>` editable.
Modal: todos los modales se cierran con el botón ✕, con clic en el backdrop y con Escape;
un clic dentro del panel no lo cierra.
Colapsable: las skills de cada agente están ocultas al cargar, se expanden con una
transición visible al hacer clic y se colapsan con un segundo clic.
"Marcar como resuelto" cambia el badge de estado de esa fila a Resuelto.
Modo oscuro: el toggle cambia todo el panel (fondo, sidebar, tarjetas, tablas, modales y
badges) entre claro y oscuro.
Modo oscuro: el modo elegido se mantiene al cambiar de sección y al recargar la página.
Los nombres de agentes coinciden entre Gestión de agentes, Contrataciones y Log de
errores, y los números del Dashboard y del catálogo de skills coinciden con los datos.
El panel se ve y funciona correctamente a 1280 px (escritorio) y a 768 px (tablet), sin
contenido cortado.