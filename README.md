# Control de Caja · Grupo Alhándiga Grancampo

Dashboard de tesorería del grupo. Un único fichero HTML autocontenido, sin build ni
dependencias que instalar: todo el CSS y el JS van en línea y la librería de gráficos
se carga por CDN.

**Publicado en:** https://alejandrovicente97.github.io/grancampo-control-caja/

---

## Las tres divisiones

El grupo se organiza en tres marcas, y el dashboard las respeta: el selector de ámbito
permite ver el grupo consolidado, una división completa o una sociedad suelta, y el color
de acento de toda la interfaz cambia según la división activa.

| División | Marca | Sociedades | Web |
|---|---|---|---|
| **Grancampo** | verde oliva | Grancampo Desarrollos, Dehesa El Corcho XXI, Dehesa Castrillo XXI, Dehesa Checa, La Cañada de Hernandinos, Explotaciones Solalba | https://grancampo.es/ |
| **Ibéricos Alhándiga** | granate | Alhándiga Ibéricos, Universo Alhándiga, Grancampo Market Restauración | https://ibericosalhandiga.com/ |
| **Promociones Montellano** | azul marino | Promociones Urbanas Montellano | https://promocionesmontellano.com/ |

Los logotipos van embebidos en el propio HTML, así que el fichero sigue siendo autocontenido.
Los gráficos multiserie mantienen una paleta validada para daltonismo y contraste; los de una
sola serie usan el color de la división activa.

## Qué contiene

| Pestaña | Ámbito |
|---|---|
| **Cierre del mes** | Portada ejecutiva: tesorería, deuda, flujo del mes y bullets automáticos |
| **Bancos y cuentas** | Posición por sociedad y entidad, evolución mensual y curva diaria real |
| **Cobros y pagos** | Flujo por categoría con drill-down, sólo sociedades con libro diario |
| **Gastos de estructura** | Bloque propio, con el pago trazado hasta la cuenta de gasto |
| **Movimientos** | Detalle movimiento a movimiento, con buscador, filtros y exportación a CSV |
| **Calidad de datos** | Conciliaciones, incidencias abiertas y estado de carga por sociedad |

Selector de **ámbito** (grupo consolidado o cualquiera de las 10 sociedades) y de **mes**
en la cabecera. Responsive y con tema claro/oscuro.

---

## Publicar

1. Subir el contenido de esta carpeta a la raíz del repositorio.
2. `Settings › Pages` → *Source*: `Deploy from a branch` → rama `main`, carpeta `/ (root)`.
3. En un par de minutos queda publicado.

El fichero `.nojekyll` evita que GitHub procese el sitio con Jekyll. No borrarlo.

---

## Actualizar los datos

Todos los datos viven en un único bloque `const DATA = {...}` dentro de `index.html`,
separado de la lógica. **La lógica no se toca nunca para ampliar.**

### Añadir un mes

1. Ampliar `DATA.periodo.meses` y actualizar `DATA.periodo.hasta`.
2. Añadir una fila a `DATA.saldosMes` por cada cuenta: `{soc, m, cta, s, dup}`.
   Si una cuenta no tiene saldo informado → `s: null`. **Nunca cero.**
3. Añadir las filas de `DATA.deuda` del mes.
4. Si hay libro diario: `DATA.movs`, `DATA.traspasos` y `DATA.saldosDia`.
5. Si aparece una cuenta bancaria nueva, darla de alta antes en `DATA.cuentas`.

### Añadir una sociedad

Las diez sociedades del perímetro ya están dadas de alta. Para volcarle datos a una:

1. Alta en `DATA.sociedades` con su `div` (`GC`, `AL` o `MO`) y sus cuentas en `DATA.cuentas`, usando el nombre **normalizado** del banco.
2. Sus saldos en `DATA.saldosMes` y su deuda en `DATA.deuda`.
3. Si tiene libro diario, además: `DATA.aperturas`, `DATA.movs`, `DATA.traspasos`,
   `DATA.saldosDia` y, opcionalmente, `DATA.estructura`, `DATA.naturProv`,
   `DATA.cartera` y `DATA.conciliacion`.

En cuanto existan filas en `DATA.movs` para esa sociedad, aparece con ✓ en el selector
y se activan solas las pestañas de Cobros y pagos, Gastos de estructura y Movimientos.
Mientras tanto la sociedad se ve con normalidad en Cierre del mes y Bancos y cuentas,
y el resto muestra un aviso en lugar de una cifra falsa.

> El **esquema completo de cada tabla** está documentado dentro del propio dashboard,
> en la pestaña *Calidad de datos* → «Cómo ampliar este dashboard».

---

## Nota sobre los datos publicados

En esta copia los **IBANs van enmascarados** (sólo los cuatro últimos dígitos).
La versión íntegra se mantiene en la carpeta interna del grupo, fuera del repositorio.

## Criterios de cálculo

- **Fecha de corte**: último día natural del mes del nombre del fichero de origen.
  Las fechas internas de los Excel de Posición Tesorera están obsoletas.
- **Totales**: se recalculan cuenta a cuenta. No se usan las filas de TOTALES del Excel,
  que tienen fórmulas rotas en cuatro puntos.
- **IBANs duplicados**: se conserva una sola vez cada IBAN por sociedad y mes, con el
  importe mayor. La línea eliminada queda marcada en el detalle de cuentas.
- **Cuentas sin saldo**: se tratan como *no informado* y se excluyen del total.
- **Traspasos entre cuentas propias**: excluidos de cobros y pagos, se muestran aparte.
- **Signo**: cobros positivos, pagos negativos.
- **Nada se cuadra a la fuerza.** Todo descuadre se reporta en *Calidad de datos*.

---

## Estado de carga

| Sociedad | Posición | Libro diario | Cartera |
|---|:--:|:--:|:--:|
| Grancampo Desarrollos | ✓ | ✓ | ✓ |
| Alhándiga Ibéricos | ✓ | — | — |
| Universo Alhándiga | ✓ | — | — |
| Grancampo Market Restauración | — | — | — |
| Promociones Urbanas Montellano | parcial | — | — |
| Dehesa El Corcho XXI | ✓ | — | — |
| Dehesa Castrillo XXI | ✓ | — | — |
| Dehesa Checa | ✓ | — | — |
| La Cañada de Hernandinos | ✓ | — | — |
| Explotaciones Solalba | ✓ | — | — |

Periodo cargado: **enero – julio 2026**.

---

*Fuentes: Libro Diario Grancampo · Posición Tesorera mensual · Balance de sumas y saldos
julio 2026 (Grancampo y Promociones) · Cartera de cobros y pagos a 05/08/2026.*
