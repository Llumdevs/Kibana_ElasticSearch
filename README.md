# 📚 Books Dataset — Elasticsearch + Kibana

Proyecto de importación, búsqueda y edición de un dataset de libros usando **Elasticsearch 7**, **Kibana** y una web frontend con **Bootstrap**.

Desarrollado como actividad práctica del módulo de Big Data Aplicado.

**Autora:** Luz Vicent  
**Año:** 2026

---

## 📦 Dataset

El dataset utilizado es el [Books Dataset de Kaggle](https://www.kaggle.com/datasets/saurabhbagchi/books-dataset/), que contiene información de más de 270.000 libros con campos como ISBN, título, autor, año de publicación, editorial y URLs de portada.

---

## 🛠️ Tecnologías

- **Elasticsearch 7.17.29** — Motor de búsqueda e indexación
- **Kibana 7.17.29** — Interfaz de administración y Dev Tools
- **Docker + Docker Compose** — Entorno de ejecución
- **HTML + CSS + Bootstrap 4** — Frontend web
- **JavaScript (Fetch API)** — Comunicación con la API REST de Elasticsearch

---

## 🚀 Puesta en marcha

### 1. Requisitos
- [Docker Desktop](https://www.docker.com/products/docker-desktop/) instalado y en ejecución

### 2. Levantar el entorno

```bash
docker-compose up -d
```

Esto levanta dos contenedores:
- **Elasticsearch** en `http://localhost:9200`
- **Kibana** en `http://localhost:5601`

### 3. Verificar que Elasticsearch está activo

```bash
curl http://localhost:9200
```

Debe devolver un JSON con la información del cluster.

### 4. Importar los datos

1. Descarga el CSV desde [Kaggle](https://www.kaggle.com/datasets/saurabhbagchi/books-dataset/)
2. Abre Kibana en `http://localhost:5601`
3. Ve a **Machine Learning → Import Data**
4. Sube el fichero `Books.csv`
5. Usa el **mapping** y el **pipeline** de este repositorio antes de importar

---

## 🗂️ Estructura del proyecto

```
📁 repositorio/
├── README.md
├── docker-compose.yaml          ← Entorno Docker con CORS configurado
├── elasticsearch.yml            ← Configuración de Elasticsearch
├── mapping.json                 ← Mapping personalizado del índice
├── pipeline.json                ← Ingest pipeline de importación
├── templates/
│   ├── buscar_titulo.json       ← Search Template por título
│   ├── buscar_autor.json        ← Search Template por autor
│   ├── buscar_anyo.json         ← Search Template por rango de año
│   ├── buscar_editorial.json    ← Search Template por editorial
│   └── buscar_general.json      ← Search Template búsqueda global
└── web/
    ├── busqueda.html            ← Web de búsqueda
    └── modificacion.html        ← Web de edición por ISBN
```

---

## 🗺️ Mapping

El índice `books` utiliza el siguiente mapping personalizado con tipos optimizados para búsqueda y agregación:

| Campo | Tipo | Descripción |
|---|---|---|
| `isbn` | `keyword` | Identificador exacto |
| `titulo` | `text` + `keyword` | Búsqueda full-text y filtros |
| `autor` | `text` + `keyword` | Búsqueda full-text y filtros |
| `anyoPublicacion` | `date` (yyyy) | Rango temporal |
| `editorial` | `text` + `keyword` | Búsqueda full-text y filtros |
| `image_URL_S/M/L` | `keyword` | URLs de portada |

---

## 🔍 Search Templates

Se han creado 5 Search Templates en Elasticsearch accesibles desde `_scripts/`:

| Template | Campo | Tipo de query |
|---|---|---|
| `buscar_general` | titulo, autor, editorial | `multi_match` |
| `buscar_titulo` | titulo | `match` con `operator: or` |
| `buscar_autor` | autor | `match_phrase` |
| `buscar_anyo` | anyoPublicacion | `range` con `gte` / `lte` |
| `buscar_editorial` | editorial | `match` con `minimum_should_match: 75%` |

Para cargarlos en Elasticsearch abre Kibana Dev Tools y ejecuta los ficheros de la carpeta `templates/`.

---

## 🌐 Web

### busqueda.html
- Dropdown con 5 opciones de búsqueda
- Campos de rango para búsqueda por año (desde / hasta)
- Resultados con portada, título, autor, editorial y año
- Búsqueda en tiempo real al escribir

### modificacion.html
- Búsqueda de libro por ISBN
- Formulario de edición con todos los campos
- Preview de portada en tiempo real
- Actualización mediante la API `_update` de Elasticsearch

---

## ⚙️ Configuración CORS

El `docker-compose.yaml` incluye las variables de entorno necesarias para habilitar CORS y permitir que el navegador haga peticiones directamente a Elasticsearch:

```yaml
- http.cors.enabled=true
- http.cors.allow-origin=*
- http.cors.allow-headers=*
- http.cors.allow-methods=OPTIONS,HEAD,GET,POST,PUT,DELETE
```

---

## 📄 Licencia

Proyecto académico — uso educativo.
