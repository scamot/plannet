# Plannet

Web app personal de ocio (restaurantes, cine, teatro, conciertos, expos, planes), pensada para el iPhone y publicada con GitHub Pages.

- `index.html`: la app. Tiene tres pestañas: **Barcelona**, **Destino/Escapadas** y **Descubrir**.
- `datos/planes.json`: la lista de planes. Es la fuente de verdad.
- `datos/destino.json`: los viajes. La pestaña de destino aparece 14 días antes de `desde` y desaparece después de `hasta`.
- `datos/sugerencias.json`: las propuestas de Claude, que se renuevan periódicamente.

La app no escribe nada: todos los cambios se hacen editando estos JSON (vía Claude) y haciendo commit.

## Formato de un plan

```json
{
  "id": "slug-unico",
  "titulo": "…",
  "tipo": "restaurante | cine | teatro | concierto | espectaculo | museo | plan",
  "lugar": "Barcelona | Cerdanya | Empordà | Menorca | …",
  "zona": "Barrio o pueblo",
  "direccion": "…",
  "enlace": "https://…",
  "descripcion": "…",
  "cocina": "solo restaurantes",
  "momentos": ["Desayuno", "Brunch", "Café", "Comida", "Cena", "Vermut", "Tardeo", "Afterwork", "Vinos", "Copas", "Delivery", "Cowork"],
  "fecha_inicio": "AAAA-MM-DD",
  "fecha_fin": "AAAA-MM-DD",
  "sesiones": ["AAAA-MM-DD"],
  "horario": "Texto libre para lo recurrente (p. ej. «Domingos 11:00»)",
  "precio": "Texto libre",
  "notas": "…",
  "estado": "pendiente | hecho | descartado",
  "valoracion": "opcional, cuando está hecho",
  "añadido": "AAAA-MM-DD"
}
```

Solo `id`, `titulo`, `tipo`, `lugar` y `estado` son obligatorios. Las fechas se ponen solo en lo temporal: cuando `fecha_fin` (o la última de las `sesiones`) ya ha pasado, la app lo tacha y lo mueve a «Ya pasado» sin tocar los datos.

Para quitar un plan, se borra del JSON. Si es algo que no se quiere volver a ver sugerido, se marca como `descartado`.

## Viajes (`destino.json`)

```json
{ "viajes": [ { "lugar": "Menorca", "desde": "2027-07-10", "hasta": "2027-07-17", "notas": "Con el peque" } ] }
```

`lugar` debe coincidir con el `lugar` de los planes y las sugerencias de ese sitio. Los viajes pasados se pueden dejar: no estorban.

## Sugerencias (`sugerencias.json`)

```json
{
  "actualizado": "AAAA-MM-DD",
  "sugerencias": [
    { "id": "…", "titulo": "…", "tipo": "…", "lugar": "Barcelona", "zona": "…",
      "fecha_inicio": "…", "fecha_fin": "…", "precio": "…", "enlace": "…",
      "motivo": "Por qué encaja (una frase)" }
  ]
}
```

Criterios para generarlas:

- Unas 10–15 para Barcelona y, si hay un viaje activo o próximo, unas 8–12 para el destino, centradas en sus fechas.
- Lo que tenga fecha debe caer en las próximas 4–6 semanas (o en las fechas del viaje).
- Mezcla de tipos: estrenos de cine, teatro, conciertos, expos, restaurantes nuevos y planes.
- Hay que fijarse en lo que ya hay en `planes.json` (gustos, zonas, lo marcado como hecho o descartado) y no repetir nada de lo que ya está.
- Cada ejecución sustituye las sugerencias anteriores; lo que ya pasó se elimina.
- Cuando una sugerencia se «pasa a la lista», se copia a `planes.json` con `añadido` y se quita de `sugerencias.json`.
