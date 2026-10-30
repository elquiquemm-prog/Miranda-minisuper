# Minisuper Miranda · inventario y ventas

Demo frontend adaptable para practicar inventario, ventas y reportes. Funciona directamente desde `index.html`; no necesita instalar dependencias.

## Estructura

- `index.html`: estructura de las pantallas.
- `styles.css`: identidad visual, componentes y diseño adaptable.
- `js/app.js`: navegación, inventario, caja, compras, reportes y persistencia local.
- `js/sheets.js`: transporte entre la página y Google Apps Script.
- `apps-script/Code.gs`: API de Apps Script y creación de las siete pestañas.
- `js/data.js`: catálogo y ventas de demostración.
- `assets/`: fotografías de categorías, ilustraciones propias de cada producto y distintivo de Miranda.
- `manifest.webmanifest` y `service-worker.js`: instalación y caché sin conexión al publicarse en HTTPS.

## Cómo probarla

Abre `index.html` en un navegador moderno. Los cambios se guardan en el almacenamiento local de ese navegador. Para probar el modo instalable y la caché sin conexión, publica la carpeta completa en un hosting HTTPS o sírvela desde un servidor local; los *service workers* no se habilitan desde `file://`.

### Atajos de caja

- `F2`: enfocar el buscador de productos.
- `F4`: enfocar el campo de efectivo.
- `F8`: completar la venta cuando el pago alcanza.
- `Ctrl+K` o `⌘K`: abrir el punto de venta y enfocar el buscador.
- Un lector de código de barras que envíe `Enter` agrega el producto si el código coincide.

## Google Sheets y compras

La app organiza la información en **un archivo de Google Sheets con siete pestañas**:

| Pestaña | Contenido |
|---|---|
| `Productos` | Catálogo, costo, precio y existencias actuales |
| `Ventas` | Encabezado de cada venta |
| `DetalleVentas` | Productos, cantidades y precios de cada venta |
| `Proveedores` | Contactos de proveedores |
| `PedidosCompra` | Proveedor, fecha, estado e importe del pedido |
| `DetalleCompras` | Artículos, cantidades y costo de cada pedido |
| `MovimientosInventario` | Entradas y salidas rastreables del inventario |

Los reportes se calculan desde las ventas y no requieren una pestaña aparte. Los pedidos pendientes no cambian el stock: al recibir un pedido, Minisuper Miranda suma las unidades, actualiza el costo de compra y registra los movimientos de inventario.

### Activar la conexión

1. Abre [la hoja de cálculo](https://docs.google.com/spreadsheets/d/1mmFLDwOAu_HRC1osq5pfSxV9KSwHgiBaDhbAD1GmUPc/edit).
2. En la hoja, abre **Extensiones → Apps Script**. Reemplaza el contenido de `Code.gs` con [`apps-script/Code.gs`](./apps-script/Code.gs) y guarda.
3. En el editor de Apps Script, selecciona `setupNativaDatabase` y pulsa **Ejecutar**. Autoriza el script. Se crearán las pestañas faltantes; si encuentra información que no reconoce o encabezados incompatibles, se detendrá sin reemplazarlos. Abre el registro de ejecución y copia el token privado que aparece al final.
4. Crea una implementación **Aplicación web**. Selecciona **Ejecutar como: yo** y **Quién tiene acceso: Cualquier persona**, si tu cuenta permite esa opción. Cada petición valida el token privado antes de leer o escribir; la URL por sí sola no concede acceso a los datos. No publiques el token ni lo compartas fuera de los navegadores autorizados.
5. Copia la URL desplegada que termina en `/exec`. En Minisuper Miranda, pulsa **Configurar conexión**, pega la URL y el token, y selecciona **Probar y conectar**.
6. Si la hoja está vacía, Minisuper Miranda ofrecerá importar el catálogo local. No copiará las ventas de demostración. Si la hoja ya tiene datos, éstos se cargarán en la página.

El token es una clave compartida: no lo publiques ni lo agregues al código del sitio. Guárdalo solo en los navegadores autorizados; puedes revocarlo cambiando `NATIVA_API_TOKEN` en **Configuración del proyecto → Propiedades del script** y volviendo a conectar los navegadores con el token nuevo. La URL y el token se guardan en el almacenamiento local de cada navegador. La configuración instala además un activador para detectar ediciones manuales en las pestañas de la base.

Los cambios se sincronizan con control de revisión para evitar que dos dispositivos sobrescriban silenciosamente la versión del otro. Si la hoja cambia en otro dispositivo, la app informa el conflicto; vuelve a conectarla para cargar la versión actual antes de seguir. Las operaciones también quedan guardadas localmente, pero **el almacenamiento del navegador no es un respaldo de base de datos**. Conserva una copia de la hoja antes de usarla como registro real.

## Alcance y seguridad

La app incluye autenticación con token compartido, pero no cuentas individuales, roles, permisos por empleado ni un sistema de auditoría resistente a administradores. Protege la hoja y limita quién puede usar la implementación. No uses un despliegue anónimo sin token. Para una operación comercial crítica o varios cajeros simultáneos se recomienda un backend con autenticación individual, copias de seguridad y validaciones transaccionales.

Cada uno de los doce productos de demostración tiene una ilustración vectorial propia en `assets/products/`; carga rápidamente y funciona sin conexión. Los productos nuevos usan la fotografía de su categoría hasta que se asigne una ilustración específica. Las fotografías de categoría proceden de [Unsplash](https://unsplash.com/); fueron descargadas y recortadas:

- `assets/produce.jpg`: `photo-1542838132-92c53300491e`
- `assets/bakery.jpg`: `photo-1509440159596-0249088772ff`
- `assets/dairy.jpg`: `photo-1550583724-b2692b85b150`
- `assets/pantry.jpg`: `photo-1547592180-85f173990554`
- `assets/drinks.jpg`: `photo-1517093157656-b9eccef91cb1`
