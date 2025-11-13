
# 📊 Cafeterías Quito – Sistema Automatizado de Monitoreo de Reseñas

Este proyecto implementa un flujo automatizado para extraer reseñas de cafeterías desde Google Maps usando Apify, almacenarlas en Supabase, visualizarlas en Looker Studio y enviar alertas automáticas por Slack cuando se detecten reseñas negativas. (MESSI)

---

## 📌 Arquitectura del Proyecto

```
Apify (Google Maps Scraper)
         ↓
Make.com
   - Get Dataset Items
   - Insertar datos en Supabase
   - Router
       ↳ Rama 1 → Slack (alertas stars < 2)
       ↳ Rama 2 → Enviar enlace del Dashboard
         (SOLO una vez por ejecución)
         ↓
Supabase (PostgreSQL)
         ↓
Looker Studio (Dashboard)
```

---

## 🧩 Componentes

### 1. Apify – Extracción de Reseñas

Cada ejecución del actor genera un dataset con:

* title (nombre de la cafetería)
* url
* stars
* name (usuario)
* reviewurl
* text (reseña)

---

### 2. Make.com – Automatización del Flujo

El escenario contiene:

#### ✔ Módulo 1: Apify → Get Dataset Items

Obtiene todos los ítems del dataset más reciente.

#### ✔ Módulo 2: Supabase → Create Row

Inserta cada reseña en la tabla `reviews_google`.

#### ✔ Módulo 3: Router

Divide la automatización en dos caminos:

1. **Rama 1 – Alertas Slack:**
   Envía mensaje si `stars < 2`.

2. **Rama 2 – Enlace del Dashboard:**
   Se envía una sola vez porque este módulo va **después del iterator**.

---

### 3. Supabase – Base de Datos

Tabla: `reviews_google`

```sql
id            int8 PK
title         text
url           text
stars         int4
name          text
reviewurl     text
text          text
review_negativa int4 (campo calculado)
```

**Campo Calculado:**

```sql
CASE
  WHEN stars < 2 THEN 1
  ELSE 0
END
```

---

## 📊 Dashboard – Looker Studio

Incluye:

* Promedio de estrellas por cafetería
* Reseñas negativas por cafetería
* Distribución de calificaciones
* Selector de periodos

**Enlace al dashboard:**
(Coloca tu enlace aquí)

```
https://lookerstudio.google.com/XXXX
```

---

## 🔔 Slack Alerts

Se envían mensajes automáticos como:

```
⚠ Nueva reseña negativa detectada
Cafetería: Coffee Factory
Estrellas: 1 ⭐
Comentario: "Terrible atención"
Enlace: https://maps.google.com/…
```

Además, se envía:

```
📊 Dashboard actualizado:
https://lookerstudio.google.com/XXXX
```

---

## 🖼 Escenario en Make
![Escenario en Make](Screenshot%202025-11-12%20194350.png)

## 📊 Dashboard de Looker Studio
![Promedio de estrellas](Screenshot%202025-11-12%20194636.png)
![Reseñas negativas](Screenshot%202025-11-12%20194657.png)

* Link del dashboard en Looker Studio: https://lookerstudio.google.com/s/gRaA_CZ9T5I

## 📊 Mensaje en Slack para ver el dashboard
![Mensaje de slack - dashboard](Screenshot%202025-11-12%20202833.png)

## ⭐ Mensaje en Slack de las reseñas de 1 estrella
![Mensaje de slack - dashboard](Screenshot%202025-11-12%20202903.png)

## 🛠 Requisitos

* Cuenta Apify
* Cuenta Supabase
* Make.com
* Slack con webhook
* Looker Studio

---
