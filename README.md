# Mi Salud

App personal de seguimiento: composición corporal, laboratorio, nutrición,
suplementos y fotos de progreso.

**Este repositorio es público y no contiene ningún dato de salud.** Es solo la
interfaz. Los datos viven en Google Sheets, en una cuenta privada, y se sirven a
través de un Apps Script protegido con una clave.

---

## Cómo funciona

```
index.html (aquí, público)  ──fetch/POST──►  Apps Script (privado)
                                                    │
                                      Google Sheets + Drive (privados)
```

La página no sabe nada: solo sabe pedir. Al abrirla por primera vez pide una URL
y una clave en ⚙ Ajustes, las guarda en el navegador, y a partir de ahí carga
todo desde ahí.

## Qué hay en este repositorio

| Archivo | Qué es |
|---|---|
| `index.html` | La app entera: interfaz, lógica y estilos en un solo archivo. |
| `registro.html` | Atajo directo al registro de comida, con su propio icono. |
| `manifest.webmanifest` | Para instalarla como app en el móvil. |
| `manifest-registro.webmanifest` | Manifiesto del atajo de registro. |
| `icon-app.png` · `icon-registro.png` | Iconos. iOS ignora los del manifiesto y usa `apple-touch-icon`, y necesita PNG, no SVG. |

## Qué NO va aquí

`Codigo.gs` **nunca se sube.** Ahí viven las explicaciones clínicas, los avisos
de interacción y los textos con valores de laboratorio. Va pegado directamente en
el editor de Apps Script, que es privado.

Y ningún dato de salud en `index.html`: ni valores, ni nombres de medicamentos,
ni la clave. Tampoco en comentarios ni en el texto de ayuda de un botón — es
justo donde se cuelan.

Antes de subir cambios, un barrido rápido:

```bash
grep -in "tu-medicamento\|tu-clave\|nombre-de-tu-hoja" index.html
```

## Instalar en el móvil

Abrir el sitio en Safari o Chrome → Compartir → **Añadir a pantalla de inicio**.

Dos cosas aprendidas por las malas: iOS ignora los iconos del manifiesto y usa
`apple-touch-icon`, que tiene que ser PNG. Y una página que redirige nada más
cargar **no se puede añadir a la pantalla de inicio**, porque Safari ya navegó
antes de que toques Compartir — por eso `registro.html` solo redirige si detecta
que ya está en modo app.

## Secciones

| Sección | Qué hace |
|---|---|
| **Composición Corporal** | Peso, grasa, músculo y agua a lo largo del tiempo. Detecta los tramos donde el peso engañó pero la composición mejoró. |
| **Laboratorio** | Marcadores por fecha, con rango de referencia y objetivo propio. Cruza cada marcador con la adherencia real a los suplementos que lo influyen. |
| **Nutrición** | Planes, tabla de alimentos con macros y registro diario de ingesta. |
| **Suplementos** | Protocolo por bloques del día, adherencia, racha, avisos de interacción y costo. |
| **Fotos** | Comparativa por fecha y ángulo. Se guardan en Drive, nunca en el repositorio. |

## Notas técnicas

**Guardado por ID, nunca por posición.** El cliente manda `tomados=vitd3,zinc` y
el Apps Script busca esas columnas por nombre. Por eso reordenar o mover
suplementos nunca desalinea el historial. Los IDs no se cambian una vez que hay
datos registrados.

**Las hojas terminan en un bloque `NOTAS`** que documenta sus columnas. Los
lectores lo usan como marca de fin de datos: no borrarlo, y no escribir debajo.

**CORS:** las peticiones GET funcionan cross-origin; las POST solo con
`Content-Type: text/plain`. Con `application/json` el navegador dispara un
preflight OPTIONS que Apps Script no sabe responder.

**Al cambiar el Apps Script:** Implementar → *Administrar* implementaciones → el
lápiz → **Nueva versión**. Nunca "Nueva implementación": eso genera otra URL y la
app se queda sin datos.

---

⚕️ Organiza información de salud, no la interpreta. Lo que la IA extrae o estima
se revisa antes de guardarse. Los objetivos son referencia para conversar con un
médico, no umbrales diagnósticos.
