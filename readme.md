# 📽️ Sistema de Recomendación Basado en Contenido

Este repositorio contiene un proyecto de **sistemas de recomendación** enfocado en películas, utilizando un enfoque **Content-Based Filtering (CBF)**.  

El proyecto está dividido en dos notebooks principales:

- **Procesamiento de datos.ipynb** → limpieza, transformación y preparación de la metadata y ratings.  
- **Modelo.ipynb** → construcción del modelo de recomendación basado en contenido, generación de recomendaciones personalizadas y evaluación del desempeño.  

---

## 📌 Metodología

1. **Procesamiento de datos**
   - Limpieza de valores nulos y duplicados.
   - Selección de atributos relevantes de las películas: *título, descripción (overview), géneros*.
   - Creación de una columna `combined_features` que combina texto ponderando la importancia de cada atributo.
   - Preprocesamiento de texto: minúsculas, eliminación de caracteres especiales, normalización de espacios.

2. **Modelo de recomendación**
   - Se utiliza el modelo de embeddings de OpenAI (`text-embedding-3-small`) para representar el contenido textual de cada película en un espacio vectorial.
   - Se calcula la **similitud coseno** entre películas para identificar las más parecidas.
   - Se construyen perfiles de usuario a partir de las películas que calificaron con alta puntuación.
   - Se generan recomendaciones personalizadas excluyendo las películas ya vistas.

3. **Evaluación**
   - División de datos en **train/test por usuario** (se reservan 10 ítems positivos por usuario para test).
   - Cálculo de métricas de calidad:
     - Precision@K
     - Recall@K
     - MAP@K (Mean Average Precision)

---

## 📊 Métricas de evaluación

- **Precision@K**: mide la proporción de recomendaciones en el Top-K que son relevantes.  
- **Recall@K**: mide qué fracción de los ítems relevantes (del test) fueron recuperados en el Top-K.  
- **MAP@K**: evalúa la calidad del ranking considerando la posición de los ítems relevantes.  

---

## ⚙️ Requisitos

- Python 3.9+  
- Librerías principales:
  - `pandas`
  - `numpy`
  - `scikit-learn`
  - `openai`
  - `scipy`

Instalación rápida:

```bash
pip install -r requirements.txt
```

## 🚀 Uso

1. Clonar el repositorio:

```bash
git clone https://github.com/usuario/repositorio.git
```
2. Abrir y ejecutar los notebooks en orden:

    Procesamiento de datos.ipynb

    Modelo.ipynb

3. Explorar resultados de:

    Recomendaciones personalizadas para un usuario.

    Métricas de evaluación del sistema.


## 📄 Archivos principales
1. Procesamiento de datos.ipynb → Limpieza y preparación de datos.
2. Modelo.ipynb → Construcción, prueba y evaluacion del modelo de recomendación.
3. README.md → Descripción general del proyecto.
4. report.md → Documento explicativo detallado del proyecto y sus resultados.
5. ratings.csv → Dataset original que contiene los ratings de cada pelicula por usuario.
6. reviews_final.csv → Dataset de ratings procesado para el uso del Modelo
7. movies_metadata.csv → Dataset que contiene el detalle de cada pelicula.
8. metadata_final → Dataset de metadatos procesado para el uso del Modelo


## 👤 Autor

Proyecto desarrollado por Armando Alberto Lluen Gallardo