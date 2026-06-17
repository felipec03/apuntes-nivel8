> Conjunto de técnicas para simplificar datos con muchas variables, representa la información en un espacio de menor dimensión.

Se hace para reducir complejidad en espacios de alta dimensión. Hace todo más simple.

## Análisis de Componentes principales
- Técnica de reducción de dimensionalidad
	- $p$  variables se transforman en menos componentes 
- Aprendizaje no supervisado
	- Usa variables **explicativas** $(X_1, ..., X_p)$
	- Busca estructura latente y **relaciones** internas.
- Condensa información.

## Álgebra lineal
**nigga values y nigga vectores**

Matriz de Covarianza / correlación:

$$
\sum = \frac{1}{n-1}(X-\hat{X})^T(X-\hat{X}) = V \land V^T
$$

Para encontrar sus direcciones principales hay que resolver:
$$
\sum \vec{v} = \lambda \vec{v}
$$
