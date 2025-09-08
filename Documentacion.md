# 📑 Reporte Técnico del Proyecto: Sistema de Recomendación Basado en Contenido

## 🎯 Objetivo

Desarrollar un sistema de recomendación de películas utilizando un enfoque de **Content-Based Filtering (CBF)**.  
El sistema aprovecha embeddings de texto para representar las películas en un espacio vectorial y genera recomendaciones personalizadas en base al historial de ratings de los usuarios.

---

## 📂 Dataset

### 🔹 Origen

El dataset proviene de la combinación de dos fuentes oficiales:

- **[The Movie Database (TMDb)](https://www.themoviedb.org/):**  
  Información de películas como títulos, sinopsis, géneros, reparto, equipo técnico, productoras y palabras clave.

- **[MovieLens (GroupLens Research)](https://grouplens.org/datasets/movielens/):**  
  Ratings de usuarios para las películas, en una escala de 1 a 5.

Este dataset fue ensamblado como parte de un proyecto de *Springboard Data Science Career Track* y está disponible públicamente en Kaggle.

---

### 🔹 Contenido original

El dataset completo incluye:

- **`movies_metadata.csv`** → Información general de 45,000 películas (presupuesto, ingresos, fecha de estreno, idiomas, productoras, países, votos en TMDB, etc.).  
- **`keywords.csv`** → Palabras clave de la trama, en formato JSON.  
- **`credits.csv`** → Información de reparto y equipo técnico, en formato JSON.  
- **`links.csv`** → IDs de películas en TMDB e IMDB.  
- **`links_small.csv`** → Subconjunto reducido con 9,000 películas.  
- **`ratings_small.csv`** → Subconjunto de 100,000 ratings de 700 usuarios sobre 9,000 películas.  
- **Dataset completo** → 26 millones de ratings de 270,000 usuarios para todas las 45,000 películas.

---

### 🔹 Subconjunto utilizado en este proyecto

Para este sistema de recomendación, se trabajó con los siguientes archivos:

1. **`movies_metadata.csv`**  
   - Se usaron las columnas principales: `title`, `overview` y `genres`.  
   - Estas características fueron combinadas y preprocesadas para generar los **embeddings de contenido**.

2. **`ratings_small.csv`**  
   - Contiene 100,000 calificaciones de 700 usuarios sobre 9,000 películas.  
   - Este archivo se usó para construir los perfiles de usuario y evaluar el sistema.

👉 Este enfoque balancea entre tener un dataset suficientemente grande para evaluación y mantener la computación manejable.

---

### 🔹 Propiedades relevantes del dataset

- **Usuarios totales:** ~700  
- **Películas evaluadas:** ~9,000  
- **Número de ratings:** 100,000  
- **Escala de calificación:** 1 a 5  
- **Fecha límite de películas:** hasta julio de 2017  

---


## 🔄 Flujo del Proyecto

### 1. Procesamiento de datos (`Procesamiento de datos.ipynb`)

**Objetivo**: preparar y limpiar la información de películas y ratings.

- **Metadata de películas**:
  - Columnas utilizadas: `title`, `overview`, `genres`.
- **ratings de películas**:
  - Columnas utilizadas: `user_id`, `movies_id`, `rating`.

- **Limpieza de texto**:
  - Análisis de los dtypes
  - Conversión a numeros.
  - Eliminación de valores nulos y duplicados.

👉 Este preprocesamiento asegura que la representación textual sea consistente antes de generar embeddings.

---

### 2. Construcción del modelo (`Modelo.ipynb`)

La clase principal del modelo es:

```python
class ContentBasedFiltering:
    def __init__(self, metadata_df, reviews_df): ...
    def preprocess_content(self): ...
    def build_model(self): ...
    def get_content_similarities(self, movie_id, top_k=10): ...
    def build_user_profile(self, user_id, min_rating=3.0): ...
    def recommend_movies(self, user_id, n_recommendations=10, exclude_rated=True): ...
    def get_feature_importance(self, movie_id, top_k=10): ...
```

#### Flujo de funciones principales
  1. preprocess_content()
      - Convierte valores en texto (maneja listas, arrays y valores nulos).
      - Construye la columna combined_features.
      - Limpia el texto final.

  2. build_model()
      - Convierte combined_features en embeddings usando OpenAIEmbeddings (text-embedding-3-small).
      - Construye la matriz de embeddings (content_matrix).

      - Calcula la matriz de similitud coseno entre todas las películas (movie_similarity_matrix).

      - Crea diccionarios de acceso rápido: movie_to_idx y idx_to_movie.

  3. get_content_similarities(movie_id, top_k)

      - Recupera las películas más similares a una película dada, según embeddings.

  4. build_user_profile(user_id, min_rating)

      - Toma las películas que el usuario ha calificado con puntajes altos.
      - Construye un vector promedio ponderado por las calificaciones.
      - Este vector representa los intereses del usuario en el espacio de embeddings.

  5. recommend_movies(user_id, n_recommendations, exclude_rated)
      - Calcula similitud entre el perfil del usuario y todas las películas.
      - Genera un ranking ordenado de películas.
      - Excluye aquellas que el usuario ya calificó.
      - Devuelve las Top-N recomendaciones personalizadas.

  6. get_feature_importance(movie_id, top_k)
      - Evalúa qué palabras del texto (título, overview, géneros) son más representativas de la posición de la película en el espacio de embeddings.

## 📌Metodología
1. **Preprocesamiento de contenido**
   - Conversión de todas las características textuales a strings.
   - Combinación de título, descripción y géneros en un único campo `combined_features`.
   - Limpieza de texto: minúsculas, eliminación de caracteres especiales y normalización de espacios.

2. **Generación de embeddings**
   - Se utilizaron embeddings de OpenAI (`text-embedding-3-small`) para representar las películas en un espacio vectorial.

3. **Cálculo de similitudes**
   - Se construyó una **matriz de similitud coseno** entre todas las películas.
   - Esto permite encontrar películas similares a cada película base.

4. **Perfil del usuario**
   - Se construye a partir de películas altamente valoradas por el usuario (rating ≥ 3.0).
   - Se calcula un vector ponderado de contenido basado en las calificaciones.

5. **Recomendación**
   - Se calculan las similitudes entre el perfil del usuario y todas las películas.
   - Se generan las top-K recomendaciones excluyendo, opcionalmente, películas ya vistas.

6. **Evaluación**
   - Se evaluó usando **Precision@10, Recall@10 y NDCG@10**.
   - La evaluación consideró usuarios con suficiente historial de ratings y se realizó un split train/test por usuario.

## 📊 Resultados del Modelo

### Perfil de un usuario de ejemplo
- **USER ID:** 2
- **Total ratings:** 17
- **Average rating:** 3.24
- **Preferred genres:** Drama (3), Romance (3), Action (2)

**Películas altamente valoradas (4+ estrellas):**
1. Night on Earth – ['Comedy', 'Drama'] – Rating: 5.0
2. A Nightmare on Elm Street – ['Horror'] – Rating: 4.0
3. Hero – ['Drama', 'Adventure', 'Action', 'History'] – Rating: 4.0
4. The 39 Steps – ['Action', 'Thriller', 'Mystery'] – Rating: 4.0
5. Talk to Her – ['Drama', 'Romance'] – Rating: 4.0

**Recomendaciones content-based para USER 2:**
1. The Story of a Cheat – ['Comedy', 'Drama'] – Similarity: 0.670 – Community rating: 3.74
2. The Hunter – ['Drama', 'Thriller'] – Similarity: 0.663 – Community rating: 3.96
3. Dead End – ['Horror', 'Thriller'] – Similarity: 0.661 – Community rating: 3.25
4. Dreamcatcher – ['Drama', 'Horror', 'Science Fiction', 'Thriller'] – Similarity: 0.658 – Community rating: 3.24
5. L – ['Drama', 'Thriller', 'Foreign'] – Similarity: 0.650 – Community rating: 3.40
6. The Prisoner of Zenda – ['Action', 'Adventure', 'Comedy'] – Similarity: 0.650 – Community rating: 3.95
7. Irreversible – ['Drama', 'Thriller', 'Crime', 'Mystery'] – Similarity: 0.644 – Community rating: 3.15
8. Dr. Terror's House of Horrors – ['Horror'] – Similarity: 0.642 – Community rating: 3.47
9. That Man from Rio – ['Action', 'Adventure', 'Comedy'] – Similarity: 0.642 – Community rating: 3.81
10. Versus – ['Action', 'Horror'] – Similarity: 0.641 – Community rating: 2.98

---

### Evaluación general del modelo (muestra de 26 usuarios)
- **Precision@10:** 0.038  
- **Recall@10:** 0.189  
- **NDCG@10:** 0.308  
- **Usuarios evaluados:** 26

**Interpretación:**
- El modelo logra identificar parcialmente películas relevantes para los usuarios con historial suficiente.
- La cobertura de películas relevantes es limitada (recall bajo), pero el ranking de las recomendaciones es consistente y justificable.
- Se observa que algunas recomendaciones coinciden con los géneros favoritos del usuario y son explicables a través de palabras clave del contenido.


## 📈 Conclusiones

1. **Perfil del usuario y preferencias**
   - El sistema identifica los géneros y tipos de películas preferidas por cada usuario, alineando las recomendaciones con sus intereses.

2. **Calidad de las recomendaciones**
   - Las recomendaciones muestran alta similitud con las películas previamente valoradas y son explicables mediante palabras clave del contenido.

3. **Evaluación del modelo**
   - Las métricas obtenidas sobre la muestra de usuarios evaluados fueron:
     - Precision@10 = 0.038
     - Recall@10 = 0.189
     - NDCG@10 = 0.308
   - Esto indica que el modelo **logra identificar parcialmente películas relevantes**, aunque todavía hay espacio para mejorar la cobertura y ranking.
   - El ranking generado es consistente y justificable según las similitudes de contenido.

4. **Limitaciones**
   - Depende exclusivamente del contenido textual; no recomienda películas muy distintas al historial del usuario.
   - Usuarios con pocos ratings tienen perfiles incompletos, reduciendo la efectividad.
   - La explicación basada en embeddings de palabras puede no capturar completamente la semántica.

5. **Potencial de mejora**
   - Integrar un enfoque híbrido combinando contenido y filtrado colaborativo.
   - Incluir más características (actores, directores, año) para enriquecer el perfil del usuario.


