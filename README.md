# 🎮 Gamer Analytics Lab: Modelo Relacional y Dashboard de Videojuegos

Este proyecto nació como un laboratorio personal para resolver un problema cotidiano: organizar mi catálogo de videojuegos, trackear mis sesiones activas nocturnas (post-20:00 hs) y analizar mis métricas de consumo de cara al resumen de fin de año para mi comunidad. 

El objetivo principal fue aplicar conceptos teóricos de **Bases de Datos Relacionales**, **Normalización** e **Integridad Referencial** en un entorno analítico real, utilizando un pipeline de datos automatizado.

---

## 🛠️ Arquitectura del Proyecto (Fase 1)

El sistema está diseñado separando la capa de almacenamiento de datos (Backend) de la capa de visualización interactiva (Frontend), lo que permite actualizaciones dinámicas desde cualquier dispositivo.

```text
[ Google Sheets ] ──( Conexión Web / API )──> [ Power Query ] ──> [ Power BI (Modelo en Estrella) ]
```

*   **Origen de Datos:** Google Sheets (actuando como Base de Datos transaccional).
*   **Motor de ETL:** Power Query (limpieza de datos, tipado estricto y eliminación de registros huérfanos).
*   **Motor Analítico:** Power BI Desktop + Expresiones DAX.
*   **Control de Versiones:** Git + GitHub.

---

## 📊 El Modelo de Datos (Teoría en la Práctica)

Para evitar la redundancia y garantizar la consistencia, apliqué reglas de normalización diseñando un **Modelo en Estrella (Star Schema)** compuesto por tablas de dimensiones y tablas de hechos conectadas mediante relaciones de **1 a Muchos (1:N)** con dirección de filtro único:

*   **`Dim_Catalogo_Juegos` (Tabla de Dimensión Maestro):** Contiene los datos descriptivos de cada título (ID único, Título, Consola, Género, Formato [Físico/Digital]).
*   **`Fact_Estado_Actual` (Tabla de Hechos):** Registra las partidas activas, prioridades ordinales (Alta, Media, Baja) y notas de progreso para retomar el juego sin perder el hilo.
*   **`Fact_Historial_Terminados` (Tabla de Hechos):** Almacena el log histórico de juegos completados, incluyendo marcas temporales, horas totales invertidas y puntajes en escala discreta entera (0 a 100).

---

## 🧠 Lógica de Ingeniería y Funciones DAX

Durante el desarrollo se implementaron soluciones a desafíos clásicos de lógica de datos:

1.  **Consistencia de Datos:** Implementación de validación de datos (listas desplegables) en el origen para evitar discrepancias de texto (*strings* inconsistentes).
2.  **Manejo de Tipos Ordinales:** Creación de una columna condicional de orden numérico (`Prioridad_Orden`) para forzar al motor visual a romper el orden alfabético por defecto de las prioridades.
3.  **Cálculos Analíticos (DAX):** Desarrollo de medidas personalizadas para el cálculo de KPIs en tiempo real:
    *   `Juegos_Terminados = COUNT(Historial_Terminados[Id_Juego])`
    *   `Total_Horas_Jugadas = SUM(Historial_Terminados[Horas_Totales])`
    *   `Puntaje_Promedio = AVERAGE(Historial_Terminados[Puntaje])`

---

## 🚀 Próximos Pasos: Fase 2 (Plan de Verano)

El proyecto está diseñado para escalar de un entorno monousuario a una plataforma comunitaria distribuida durante el receso académico. El roadmap técnico incluye:

*   **Migración de Base de Datos:** Reemplazar Google Sheets por un motor relacional robusto en la nube (**MySQL / PostgreSQL**).
*   **Desarrollo del Backend:** Crear una API REST en **Python (FastAPI o Flask)** para procesar las transacciones y asegurar la integridad referencial a nivel de motor.
*   **Aplicación Móvil:** Desarrollar una interfaz nativa para celulares utilizando **React Native**, consumiendo la API pública de **IGDB (Twitch)** como base de datos maestra de videojuegos para automatizar el tipado de títulos.
