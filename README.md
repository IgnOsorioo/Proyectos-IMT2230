# Proyecto 1 — LSA con SVD

**IMT2230 · Álgebra Lineal Avanzada y Modelamiento — Pontificia Universidad Católica de Chile**

**Integrantes:**
* Reinaldo Oliva Morales
* Ignacio Osorio Orellana

Análisis Semántico Latente (LSA) sobre un corpus de 294 artículos de Wikipedia en español, usando la descomposición en valores singulares (SVD) de la matriz término-documento TF-IDF.

## Estructura del repositorio
```text
├── README.md                    # Este archivo
├── corpus_wikipedia.csv         # Corpus cacheado (294 documentos, 5 categorías)
└── Entregables/
    ├── Informe.pdf              # Informe final
    ├── lsa_svd.ipynb            # Notebook fuente
    ├── lsa_svd_ejecutado.ipynb  # Notebook con outputs y figuras generadas
    ├── requirements.txt         # Dependencias de Python
    └── Figuras/
        ├── valores_singulares.png
        ├── documentos_2d.png
        ├── documentos_2d_c2c3.png
        ├── terminos_2d.png
        └── similitud_categorias.png
```

---

## Instalación

Requiere Python 3.10+ (desarrollado con 3.13).

```bash
pip install -r requirements.txt
```

Las dependencias son: `numpy`, `pandas`, `matplotlib`, `seaborn`, `scikit-learn`, `nltk`, `wikipedia-api`.
---

## Cómo reproducir los resultados

### 1. Ejecutar el notebook

Desde la raíz del repositorio:

```bash
jupyter notebook Entregables/lsa_svd.ipynb
```

Y ejecutar todas las celdas (Cell → Run All), o bien sin abrir Jupyter:

```bash
python3 -m nbconvert --to notebook --execute Entregables/lsa_svd.ipynb \
    --output lsa_svd_ejecutado.ipynb --ExecutePreprocessor.timeout=600
```

El notebook detecta automáticamente la raíz del proyecto: puede ejecutarse desde cualquier directorio. Si `corpus_wikipedia.csv` ya está presente (lo está en este repo), se carga desde cache; si no, se descargan los 294 artículos vía la API pública de Wikipedia (toma ~5 minutos la primera vez).

Las cinco figuras se guardan en `Entregables/Figuras/`.

### 2. Regenerar el informe PDF

```bash
python3 Entregables/generar_informe.py
```

Genera `Entregables/Informe.pdf` a partir del contenido fijo del proyecto (usa las figuras de `Entregables/Figuras/`).

---

## Datos

- **Fuente**: Wikipedia en español ([https://es.wikipedia.org](https://es.wikipedia.org)).
- **Acceso**: API pública vía `wikipedia-api`.
- **Selección**: 5 categorías temáticas × ~60 artículos por categoría.
- **Total efectivo**: 294 documentos (algunos títulos no se descargan).
- **Cache**: `corpus_wikipedia.csv` (incluido en el repo, ~780 KB).

| Categoría | N° documentos |
|-----------|---------------|
| deporte   | 60 |
| ciencia   | 60 |
| economia  | 60 |
| cultura   | 59 |
| historia  | 55 |
| **Total** | **294** |

---

## Resultados principales

- **Vocabulario tras preprocesamiento**: 3 000 términos.
- **Matriz textual**: $A \in \mathbb{R}^{3000 \times 294}$, densidad 3.11%.
- **Energía capturada con $k = 20$**: 21.2 %.
- **Componentes 2–5** codifican respectivamente: ciencia ↔ historia, deporte ↔ política, cultura ↔ economía, ciencia/astronomía ↔ cultura.
- **Vecindades coseno**: documentos del mismo tema con sim ≥ 0.83 (top vecino casi siempre > 0.95).
- **Par cruzado más alto** entre categorías: historia–economía = 0.210 (confirma la hipótesis de solapamiento por vocabulario histórico-político).

Discusión completa, figuras y conclusiones en [`Entregables/Informe.pdf`](Entregables/Informe.pdf).

---

## Cómo se construyó el corpus

El notebook contiene listas de títulos por categoría y descarga cada artículo con `wikipedia-api`. Si un título no existe exactamente con el nombre buscado o el resumen tiene menos de 150 caracteres, se omite. El resultado se cachea en `corpus_wikipedia.csv`.

Para forzar una descarga nueva, borrar `corpus_wikipedia.csv` antes de ejecutar.