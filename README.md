# GeoJSON: Guía para consumir datos geoespaciales abiertos 🌍

> **Autor:** Luis Eduardo Ferrer Cruz ([@lefcgis](https://github.com/lefcgis)) · El Laboratorio de Lucho  
> **Archivo de ejemplo:** `limit_dep.geojson` — Límites departamentales del Perú

---

## ¿Qué es GeoJSON?

GeoJSON es un formato abierto basado en JSON (JavaScript Object Notation) diseñado para representar elementos geográficos junto con sus atributos no espaciales. Fue estandarizado por la IETF en el [RFC 7946](https://datatracker.ietf.org/doc/html/rfc7946) (2016).

En palabras simples: es un archivo de texto plano que cualquier editor puede abrir, donde cada "feature" describe una geometría (punto, línea, polígono…) con sus propiedades asociadas.

### Estructura básica

```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Polygon",
        "coordinates": [[[-77.0, -12.0], [-77.0, -11.5], [-76.5, -11.5], [-76.5, -12.0], [-77.0, -12.0]]]
      },
      "properties": {
        "departamento": "Lima",
        "capital": "Lima",
        "poblacion": 10000000
      }
    }
  ]
}
```

### Tipos de geometría soportados

| Tipo | Descripción | Ejemplo de uso |
|------|-------------|----------------|
| `Point` | Ubicación puntual | Estaciones de monitoreo, pozos |
| `MultiPoint` | Conjunto de puntos | Red de sensores |
| `LineString` | Línea simple | Ríos, carreteras, ductos |
| `MultiLineString` | Conjunto de líneas | Red vial completa |
| `Polygon` | Polígono cerrado | Departamentos, concesiones mineras, AMP |
| `MultiPolygon` | Conjunto de polígonos | Archipiélagos, territorios discontinuos |
| `GeometryCollection` | Mezcla de geometrías | Composiciones complejas |

---

## ¿Por qué es importante GeoJSON?

### 1. Interoperabilidad total

GeoJSON es el formato de intercambio geoespacial más universal que existe en la web. Cualquier API, cualquier lenguaje de programación y prácticamente cualquier software SIG lo entiende sin necesidad de drivers especiales. A diferencia de un Shapefile (que necesita mínimo 3 archivos: `.shp`, `.shx`, `.dbf`) o un File Geodatabase (formato propietario de Esri), GeoJSON es **un solo archivo de texto**.

### 2. Legible por humanos

Puedes abrirlo con el Bloc de Notas y entender qué contiene. Eso facilita la depuración, la auditoría de datos y la enseñanza.

### 3. Estándar abierto y libre

No depende de ninguna empresa privada. No necesitas licencia para leerlo, escribirlo ni compartirlo. Eso es crítico en contextos de gobierno abierto y datos públicos.

### 4. Nativo de la web

Los navegadores procesan JSON de forma nativa. Librerías como Leaflet, OpenLayers, Mapbox GL JS y Deck.gl consumen GeoJSON directamente, sin conversión previa.

### 5. Compatible con flujos de datos modernos

APIs REST, webhooks, pipelines ETL con Python, bases de datos como PostgreSQL/PostGIS, plataformas cloud (Google Earth Engine, AWS, Azure) — todos hablan GeoJSON.

### 6. Datos abiertos del Estado Peruano

Portales como SERNANP, ANA, INGEMMET, MINCUL y SENACE publican servicios WFS/REST que pueden exportar datos en GeoJSON, facilitando su integración directa en proyectos SIG.

---

## ¿Cómo consumir un GeoJSON?

### Método 1: Directo desde GitHub

GitHub renderiza automáticamente archivos `.geojson` como mapas interactivos. Si subes `limit_dep.geojson` a este repositorio, cualquier persona puede visualizarlo directamente en el navegador sin instalar nada.

**Truco:** puedes apuntar a la URL raw del archivo desde cualquier aplicación:

```
https://raw.githubusercontent.com/lefcgis/GeoJSON_QGIS/main/limit_dep.geojson
```

### Método 2: En QGIS (escritorio)

1. **Menú** → Capa → Añadir capa → Añadir capa vectorial
2. Selecciona el archivo `.geojson`
3. Click en **Añadir**

O simplemente **arrastra el archivo** desde tu explorador de archivos a la ventana de QGIS.

**Desde URL (sin descargar):**
1. Menú → Capa → Añadir capa → Añadir capa vectorial
2. En "Fuente", selecciona **Protocolo: HTTP(S)**
3. Pega la URL raw de GitHub
4. Click en Añadir

### Método 3: Con Python

```python
import geopandas as gpd

# Desde archivo local
gdf = gpd.read_file("limit_dep.geojson")

# Desde URL
url = "https://raw.githubusercontent.com/lefcgis/GeoJSON_QGIS/main/limit_dep.geojson"
gdf = gpd.read_file(url)

print(gdf.head())
print(f"CRS: {gdf.crs}")
print(f"Total features: {len(gdf)}")
```

### Método 4: Con JavaScript (web)

```javascript
// Leaflet
fetch('limit_dep.geojson')
  .then(response => response.json())
  .then(data => {
    L.geoJSON(data).addTo(map);
  });
```

### Método 5: Desde la línea de comandos con `ogr2ogr`

```bash
# Ver información del archivo
ogrinfo -al -so limit_dep.geojson

# Convertir a Shapefile
ogr2ogr -f "ESRI Shapefile" salida.shp limit_dep.geojson

# Convertir a GeoPackage
ogr2ogr -f "GPKG" salida.gpkg limit_dep.geojson

# Cargar a PostGIS
ogr2ogr -f "PostgreSQL" PG:"host=localhost dbname=w_sgi user=postgres" limit_dep.geojson -nln limites_departamentales
```

---

## Softwares y herramientas compatibles

### SIG de escritorio

| Software | Licencia | Soporte GeoJSON |
|----------|----------|-----------------|
| **QGIS** | Libre (GPL) | Lectura/escritura nativa |
| **ArcGIS Pro** | Propietario (Esri) | Lectura nativa, conversión a Feature Class |
| **gvSIG** | Libre (GPL) | Lectura/escritura |
| **GRASS GIS** | Libre (GPL) | Vía `v.in.ogr` |

### Librerías de programación

| Librería | Lenguaje | Uso |
|----------|----------|-----|
| **GeoPandas** | Python | Análisis vectorial completo |
| **Fiona / GDAL** | Python | Lectura/escritura de bajo nivel |
| **Shapely** | Python | Operaciones geométricas |
| **Leaflet** | JavaScript | Mapas web interactivos |
| **OpenLayers** | JavaScript | Mapas web avanzados |
| **Mapbox GL JS** | JavaScript | Mapas web con WebGL |
| **Deck.gl** | JavaScript | Visualización masiva de datos |
| **Turf.js** | JavaScript | Análisis espacial en el navegador |
| **sf** | R | Análisis espacial en R |
| **D3.js** | JavaScript | Visualización de datos cartográficos |

### Bases de datos

| Base de datos | Función |
|---------------|---------|
| **PostgreSQL + PostGIS** | `ST_AsGeoJSON()` / `ST_GeomFromGeoJSON()` |
| **MongoDB** | Soporte nativo de GeoJSON para índices `2dsphere` |
| **Elasticsearch** | Tipo `geo_shape` acepta GeoJSON directamente |
| **DuckDB + Spatial** | Lectura/escritura con extensión espacial |

### Servicios en la nube

| Plataforma | Capacidad |
|------------|-----------|
| **Google Earth Engine** | Exporta/importa como GeoJSON |
| **Mapbox** | Tilesets desde GeoJSON |
| **GitHub** | Renderización automática de `.geojson` |
| **geojson.io** | Editor visual en línea |
| **Felt** | Carga directa de GeoJSON |
| **Kepler.gl** | Visualización geoespacial avanzada |

---

## Consideraciones de seguridad

### Validación del archivo

Un GeoJSON es texto plano, pero eso no significa que sea inofensivo. Antes de consumir un archivo GeoJSON de una fuente no confiable:

- **Valida la estructura JSON** antes de procesarlo. Un JSON malformado puede causar errores inesperados o ser vector de inyección si se concatena a queries sin sanitizar.
- **Verifica el tamaño del archivo.** Archivos GeoJSON excesivamente grandes (cientos de MB) pueden agotar la memoria del navegador o del servidor. Para datasets grandes, considera GeoPackage, FlatGeobuf o formatos columnar como GeoParquet.
- **Revisa las coordenadas.** GeoJSON usa obligatoriamente WGS 84 (EPSG:4326) según el RFC 7946. Si recibes coordenadas en otro CRS (por ejemplo UTM), podrían estar proyectadas incorrectamente.

### Inyección y datos maliciosos

- Si insertas propiedades de un GeoJSON directamente en HTML (por ejemplo, popups de Leaflet), **sanitiza siempre** para prevenir XSS (Cross-Site Scripting):

```javascript
// MAL — vulnerable a XSS
layer.bindPopup(feature.properties.nombre);

// BIEN — escapar el contenido
function escapeHtml(text) {
  const div = document.createElement('div');
  div.textContent = text;
  return div.innerHTML;
}
layer.bindPopup(escapeHtml(feature.properties.nombre));
```

- Si cargas GeoJSON a PostGIS vía `ogr2ogr` o scripts Python, valida los nombres de campos para evitar inyección SQL.

### Privacidad y datos sensibles

- Antes de publicar un GeoJSON, verifica que no contenga coordenadas de ubicaciones privadas, datos personales en las propiedades, o información que no deba ser pública.
- En proyectos de EIA (Evaluación de Impacto Ambiental), ten cuidado con coordenadas de comunidades nativas, sitios arqueológicos sensibles, o información de terceros que podría tener restricciones de confidencialidad.

### Buenas prácticas

- Usa HTTPS siempre que consumas GeoJSON desde URLs remotas.
- Implementa límites de tamaño si tu aplicación acepta uploads de GeoJSON.
- En APIs, establece rate limiting para endpoints que sirvan GeoJSON pesados.

---

## Limitaciones de GeoJSON

Ningún formato es perfecto. Conviene saber cuándo GeoJSON **no** es la mejor opción:

| Limitación | Alternativa recomendada |
|------------|------------------------|
| Archivos muy grandes (>50 MB) | GeoPackage, FlatGeobuf, GeoParquet |
| Necesidad de múltiples capas en un solo archivo | GeoPackage |
| Topología explícita (fronteras compartidas) | TopoJSON |
| Renderización web de millones de features | Vector Tiles (MVT) |
| Datos raster | GeoTIFF, COG (Cloud Optimized GeoTIFF) |
| CRS distintos de WGS 84 | GeoPackage (soporta cualquier CRS) |

---

## Siguientes pasos

- [ ] **Explorar el archivo de ejemplo**: Abre `limit_dep.geojson` en QGIS o en [geojson.io](https://geojson.io) y explora las propiedades de cada departamento.
- [ ] **Consumir desde Python**: Usa el snippet de GeoPandas de esta guía para cargar el archivo y hacer tu primer análisis (área, centroide, filtros).
- [ ] **Conectar con PostGIS**: Carga el GeoJSON a tu base de datos PostgreSQL y practica consultas espaciales con `ST_Contains`, `ST_Intersects`, etc.
- [ ] **Crear un mapa web**: Usa Leaflet o Mapbox GL JS para publicar tu primer mapa interactivo consumiendo este GeoJSON directamente desde GitHub.
- [ ] **Explorar TopoJSON**: Convierte el archivo a TopoJSON con la herramienta `geo2topo` y compara el peso del archivo (especialmente útil para polígonos con fronteras compartidas como departamentos).
- [ ] **Conectar con APIs del Estado Peruano**: Consume servicios REST de SERNANP, ANA o INGEMMET en formato GeoJSON y combínalos con tus datos locales.

---

## Recursos adicionales

- [RFC 7946 — The GeoJSON Format](https://datatracker.ietf.org/doc/html/rfc7946) — Especificación oficial
- [geojson.io](https://geojson.io) — Editor visual en línea
- [geojson.org](https://geojson.org) — Sitio oficial del formato
- [GeoJSON Lint](https://geojsonlint.com) — Validador en línea
- [More than you ever wanted to know about GeoJSON](https://macwright.com/2015/03/23/geojson-second-bite.html) — Artículo técnico de referencia (Tom MacWright)
- [Right GeoJSON Tool](https://observablehq.com/@tmcw/right-geojson) — Herramientas de corrección

---

## Licencia

Este repositorio es material educativo de libre distribución.  
Los datos geoespaciales de ejemplo provienen de fuentes oficiales del gobierno peruano y son de dominio público.

---

*Hecho con ☕ y software libre desde Lima, Perú*  
*El Laboratorio de Lucho · [github.com/lefcgis](https://github.com/lefcgis)*
