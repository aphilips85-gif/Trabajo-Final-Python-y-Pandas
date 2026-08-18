# 📊 Análisis de Ventas - Tienda de Tecnología

## Descripción del Proyecto

Este proyecto tiene como objetivo aplicar técnicas de limpieza, transformación, análisis y visualización de datos utilizando Python y la librería Pandas sobre un conjunto de datos de ventas de una tienda de tecnología.

---

# 1. Importación de Librerías y Carga de Datos

```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv('ventas_tienda_tecnologia2.csv')
```

---

# 2. Exploración Inicial de los Datos

```python
df.head()
df.info()
df.describe()
df.columns
```

Objetivo:

- Conocer la estructura del dataset.
- Identificar tipos de datos.
- Detectar valores nulos y posibles inconsistencias.

---

# 3. Limpieza de Datos

## 3.1 Identificación de Valores Nulos

```python
df.isnull().sum()
```

```python
df.isnull().sum().sum()
```

---

## 3.2 Corrección de Valores Nulos en Precio Unitario

### Monitor 24 pulgadas

```python
df.loc[
    (df['producto'] == 'Monitor 24 pulgadas') &
    (df['precio_unitario'].isna()),
    'precio_unitario'
] = 168400
```

### Monitor 27 pulgadas curvo

```python
df.loc[
    (df['producto'] == 'Monitor 27 pulgadas curvo') &
    (df['precio_unitario'].isna()),
    'precio_unitario'
] = 293200
```

### Funda protectora celular

```python
df.loc[
    (df['producto'] == 'Funda protectora celular') &
    (df['precio_unitario'].isna()),
    'precio_unitario'
] = 7900
```

### Teclado mecánico

```python
df.loc[
    (df['producto'] == 'Teclado mecánico') &
    (df['precio_unitario'].isna()),
    'precio_unitario'
] = 15000
```

### Celular gama media

```python
df.loc[
    (df['producto'] == 'Celular gama media') &
    (df['precio_unitario'].isna()),
    'precio_unitario'
] = 200000
```

### Laptop 14 pulgadas

```python
df.loc[
    (df['producto'] == 'Laptop 14 pulgadas') &
    (df['precio_unitario'].isna()),
    'precio_unitario'
] = 450000
```

### Audífonos inalámbricos

```python
df.loc[
    (df['producto'] == 'Audífonos inalámbricos') &
    (df['precio_unitario'].isna()),
    'precio_unitario'
] = 15000
```

### Verificación

```python
df['precio_unitario'].isna().sum()
```

---

## 3.3 Corrección de Valores Nulos en Región

### Fabiola Araya → Cartago

```python
df.loc[
    (df['vendedor'].str.strip().str.upper() == 'FABIOLA ARAYA') &
    (df['region'].isna()),
    'region'
] = 'Cartago'
```

### Marisol Vargas → Limón

```python
df.loc[
    (df['vendedor'].str.strip().str.upper() == 'MARISOL VARGAS') &
    (df['region'].isna()),
    'region'
] = 'Limón'
```

### Ana Rojas → San José

```python
df.loc[
    (df['vendedor'].str.strip().str.upper() == 'ANA ROJAS') &
    (df['region'].isna()),
    'region'
] = 'San José'
```

### Kevin Solano → Heredia

```python
df.loc[
    (df['vendedor'].str.strip().str.upper() == 'KEVIN SOLANO') &
    (df['region'].isna()),
    'region'
] = 'Heredia'
```

### Verificación

```python
df['region'].isna().sum()
```

---

# 4. Eliminación de Duplicados

## Verificar Duplicados

```python
df['id_venta'].duplicated().sum()
```

## Eliminar Duplicados

```python
df = df.drop_duplicates(
    subset=['id_venta'],
    keep='first'
)
```

## Verificación

```python
df['id_venta'].duplicated().sum()
```

---

# 5. Selección de Datos con loc e iloc

## loc()

```python
df.loc[0:4, ['producto', 'cantidad', 'precio_unitario']]
```

## iloc()

```python
df.iloc[0:5, 0:3]
```

---

# 6. Aplicación de Filtros

## Ventas mayores a ₡500 000

```python
df[df['precio_unitario'] > 500000]
```

## Ventas en San José mayores a ₡400 000

```python
df[
    (df['region'] == 'San José') &
    (df['precio_unitario'] > 400000)
]
```

---

# 7. Creación de Nueva Columna

## Monto de Venta

```python
df['monto_venta'] = df['cantidad'] * df['precio_unitario']
```

Verificación:

```python
df[['producto', 'cantidad', 'precio_unitario', 'monto_venta']].head()
```

---

# 8. Uso de apply() y lambda()

## Clasificación de Ventas

```python
df['nivel_venta'] = df['monto_venta'].apply(
    lambda x: 'Alto' if x >= 500000 else 'Bajo'
)
```

Verificación:

```python
df[['monto_venta', 'nivel_venta']].head()
```

---

# 9. Uso de merge()

## Tabla de Comisiones

```python
comisiones = pd.DataFrame({
    'vendedor': [
        'Ana Rojas',
        'Fabiola Araya',
        'Kevin Solano',
        'Luis Chinchilla',
        'Marisol Vargas'
    ],
    'comision': [0.05, 0.04, 0.06, 0.05, 0.04]
})
```

## Combinar Tablas

```python
df_merge = pd.merge(
    df,
    comisiones,
    on='vendedor',
    how='left'
)
```

---

# 10. Uso de value_counts()

## Frecuencia por Categoría

```python
df['categoria'].value_counts()
```

---

# 11. Uso de groupby()

## Resumen por Categoría

```python
df.groupby('categoria')['monto_venta'].agg(
    ['sum', 'mean', 'count']
)
```

---

# 12. Uso de pivot_table()

## Ventas por Categoría y Método de Pago

```python
pd.pivot_table(
    df,
    values='monto_venta',
    index='categoria',
    columns='metodo_pago',
    aggfunc='sum',
    fill_value=0
)
```

---

# 13. Visualizaciones

## Gráfico 1: Top 10 Productos Más Vendidos

```python
top10_productos = (
    df.groupby('producto')['cantidad']
      .sum()
      .sort_values(ascending=False)
      .head(10)
)

plt.figure(figsize=(10,6))
top10_productos.plot(kind='bar', color='steelblue')

plt.title('Top 10 Productos Más Vendidos')
plt.xlabel('Producto')
plt.ylabel('Cantidad Vendida')

plt.tight_layout()
plt.show()
```

### Conclusión

Los productos ubicados en las primeras posiciones presentan la mayor demanda y representan oportunidades estratégicas para fortalecer el inventario y las promociones.

---

## Gráfico 2: Histograma de Montos de Venta

```python
plt.figure(figsize=(8,5))

plt.hist(
    df['monto_venta'],
    bins=20,
    color='orange',
    edgecolor='black'
)

plt.title('Distribución de los Montos de Venta')
plt.xlabel('Monto de Venta (₡)')
plt.ylabel('Frecuencia')

plt.show()
```

### Observación

La mayoría de las ventas se concentran en rangos de valor medio y bajo, mientras que las ventas de montos elevados se presentan con menor frecuencia.

---

## Gráfico 3: Top 5 Productos Más Vendidos (Gráfico Circular)

```python
top5 = (
    df.groupby('producto')['cantidad']
      .sum()
      .sort_values(ascending=False)
      .head(5)
)

plt.figure(figsize=(8,8))

plt.pie(
    top5,
    labels=top5.index,
    autopct='%1.1f%%',
    startangle=90
)

plt.title('Top 5 Productos Más Vendidos')

plt.show()
```

### Descripción

El gráfico muestra la participación porcentual de los productos con mayor cantidad de unidades vendidas, permitiendo identificar los artículos con mayor demanda.

---

# Conclusiones Generales

- Se identificaron y corrigieron valores nulos.
- Se eliminaron registros duplicados utilizando `id_venta`.
- Se creó la variable `monto_venta` para medir los ingresos generados por cada venta.
- Se aplicaron técnicas de filtrado, selección y transformación de datos.
- Se utilizaron funciones de análisis como `value_counts()`, `groupby()` y `pivot_table()`.
- Se combinaron tablas mediante `merge()`.
- Se desarrollaron visualizaciones para identificar productos más vendidos y patrones de comportamiento en las ventas.
- El análisis permitió obtener información relevante sobre categorías, vendedores, productos y desempeño comercial de la tienda.
