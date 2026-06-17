## Descubrir conocimiento

## Procesos de descubrimiento (KDD)
> Knowledge Discovery in Databases: Metodología que organiza las etapas necesarias para transformar datos en conocimiento útil, desde la selección y preparación de la información hasta la extracción y validación de patrones.


![[Pasted image 20260407142319.png]]

### Ejemplo ilustrado: Pacientes diabéticos
1. Selección de datos: Definición de fuentes de información. Sacar info de registro clínico.
2. Preprocesamiento de datos: Depuración y limpieza. Eliminar duplicados, sacar inconsistencias, filtrar por grupo de interés.
3. Transformación de datos: Conversión y construcción de nuevas variables para mejorar el análisis.
4. MInería de datos / Modelamiento de los datos: Aplicación de algoritmos para descubrir patrones en los datos transformados (paso 3).
5. Evaluación e interpretación de datos: Validación de resultados.

### Otras metodologías
**CRISP-DM** (The CRoss Industry Standard Process for Data Mining)
Es lo mismo que KDD pero con conocimieto de negocio y despliegue XD:

![[Pasted image 20260407142426.png]]

**SEMMA smas**:
Basada en KDD; más técnica directo a los datos.
![[Pasted image 20260407143004.png]]
*Freaky note:* En fase de exploración es importante verificar homogeneidad de los datos, osea que la varianza no sea muy alta para poder hacer predicciones.

**E = Mc^2 + AI**
![[Pasted image 20260407144746.png]]
## Evolución de Arquitectura de Datos
- Data Warehouse: Repo central de datos estructurados, ETL para limpiar e integrar info, optimizado para reportes.
- Data Lake: Almacena datos estructurados, semi estruct y no estruct. Data science y ML.
- Data Lakehouse: Mejor de los dos, capa unificada de metadatos: BI + ML.

