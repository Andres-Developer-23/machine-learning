# Documentación del Código: Detección de Datos Duplicados

## ¿Qué hace este código?

Este código analiza un archivo enorme con **2 millones de registros** de personas. Su objetivo es encontrar datos que están repetidos. Imagina que tienes una lista de contactos y quieres saber si hay personas duplicadas; pues algo similar hace este código, pero con muchos más datos.

---

## Estructura del Archivo de Datos

El archivo contiene información sobre personas con las siguientes **columnas** (como las columnas de una hoja de cálculo):

| Columna | Descripción | Ejemplo |
|---------|-------------|---------|
| Name | Nombre completo | "Haley Norton" |
| Age | Edad | 58.0 |
| Gender | Género | "Male" |
| Email | Correo electrónico | "rileylynn@example.net" |
| Income | Ingreso económico | 43977.0 |
| Country | País | "Macao" |
| City | Ciudad | "Michaelside" |
| Job Title | Título del trabajo | "Sales Manager" |
| Job Domain | Área del trabajo | "Medicine" |

**Nota:** El archivo tiene 2,000,000 de filas (registros).

---

## Explicación del Código Paso a Paso

### Bloque 1: Cargar los datos desde Google Drive

```python
from google.colab import drive
drive.mount('/content/drive')
```

**¿Qué hace esto?**
- Esta parte conecta con Google Drive (como cuando conectas una USB a tu computadora)
- Permite que el código pueda leer el archivo que está guardado en la nube

```python
import pandas as pd
dataframe = pd.read_csv('/content/drive/MyDrive/ml/synthetic_dataset.csv')
dataframe
```

**¿Qué hace esto?**
- `import pandas as pd` - Carga una herramienta (librería) que ayuda a trabajar con datos en forma de tabla
- `pd.read_csv(...)` - Lee un archivo CSV (un archivo de texto con datos separados por comas)
- El archivo se guarda en una variable llamada `dataframe` (como una variable en matemáticas, pero guardando una tabla completa)

**En términos simples:** Es como abrir un archivo de Excel desde una carpeta.

---

### Bloque 2: Función para encontrar datos duplicados

```python
def encontrar_duplicados(dataframe):
```

**¿Qué es esto?**
- `def` significa "definir función" - Es como crear una receta de cocina
- `encontrar_duplicados` es el nombre de la función
- `dataframe` es el parámetro (los datos que le vamos a pasar para analizar)

**En términos simples:** Estamos creando una "fábrica" que recibe datos y los analiza para encontrar repeticiones.

---

#### Parte 2.1: Buscar filas duplicadas (registros completos iguales)

```python
filas_duplicadas = dataframe[dataframe.duplicated(keep=False)]
```

**¿Qué hace esto?**
- `dataframe.duplicated(keep=False)` - Revisa cada fila de la tabla y pregunta "¿Esta fila ya apareció antes?"
- Si la respuesta es sí, la marca como "duplicada"
- `keep=False` significa que marca TODAS las copias duplicadas, no solo la primera

**Analogía:** Es como si tuvieras una lista de compras yemarkaras cada artículo que ya habías mencionado antes.

```python
if len(filas_duplicadas) > 0:
    print(f"Se encontraron {len(filas_duplicadas)} filas duplicadas")
```

**¿Qué hace esto?**
- `len(filas_duplicadas)` - Cuenta cuántas filas duplicadas encontró
- Si encontró más de 0, imprime un mensaje diciendo cuántas encontró

---

#### Parte 2.2: Buscar valores duplicados en cada columna

```python
for columna in dataframe.columns:
```

**¿Qué hace esto?**
- Un "for" es como ir uno por uno por una lista
- Aquí va pasando por cada columna de la tabla (Name, Age, Gender, etc.)

```python
total_valores = len(dataframe[columna])
valores_unicos = dataframe[columna].nunique()
duplicados = total_valores - valores_unicos
```

**¿Qué hace esto?**
- `total_valores` = cantidad total de datos en esa columna
- `valores_unicos` = cuántos datos diferentes hay (sin repetir)
- `duplicados` = la resta de los dos anteriores (los que se repiten)

**Analogía:** Si tienes 10 estudiantes en una clase y solo 8 nombres diferentes, entonces 2 nombres están repetidos.

```python
if duplicados > 0:
    print(f"{columna}: {duplicados} valores repetidos")
```

**¿Qué hace esto?**
- Si encontró valores repetidos, los imprime en pantalla
- Muestra el nombre de la columna y cuántos valores se repiten

---

#### Parte 2.3: Verificar si hay columnas iguales

```python
for i in range(len(dataframe.columns)):
    for j in range(i + 1, len(dataframe.columns)):
```

**¿Qué hace esto?**
- Estos dos "for" anidados comparan cada columna con todas las demás
- El primer ciclo va por cada columna
- El segundo ciclo va por las columnas que vienen DESPUÉS de la primera

**Analogía:** Es como si compararas cada libro de tu biblioteca con todos los demás para ver si hay dos que tengan exactamente el mismo contenido.

```python
if list(dataframe[col1]) == list(dataframe[col2]):
    columnas_iguales.append(f"{col1} y {col2}")
```

**¿Qué hace esto?**
- Compara si la primera columna tiene exactamente los mismos valores que la segunda
- Si son iguales, las guarda en una lista

---

#### Parte 2.4: Llamar a la función

```python
resultado = encontrar_duplicados(dataframe)
```

**¿Qué hace esto?**
- Ejecuta la función que creamos con los datos del archivo
- Guarda el resultado en una variable llamada "resultado"

---

### Bloque 3: Análisis más detallado de duplicados

```python
def solo_duplicados(dataframe, limite=30):
```

**¿Qué es esto?**
- Otra función, pero esta vez más detallada
- `limite=30` significa que por defecto solo mostrará los primeros 30 resultados (para no saturar la pantalla)

---

#### Parte 3.1: Mostrar las filas duplicadas

```python
filas_duplicadas = dataframe[dataframe.duplicated(keep=False)]
print(filas_duplicadas.head(limite))
```

**¿Qué hace esto?**
- Igual que antes, busca las filas duplicadas
- `.head(limite)` muestra solo las primeras 30 (o las que indique el límite)
- Es como hojear un libro y ver las primeras páginas

---

#### Parte 3.2: Análisis detallado por columna

```python
for columna in dataframe.columns:
    valores_duplicados = dataframe[columna][dataframe[columna].duplicated(keep=False)]
    conteo = valores_duplicados.value_counts()
```

**¿Qué hace esto?**
- Para cada columna, extrae solo los valores que están duplicados
- `.value_counts()` cuenta cuántas veces se repite cada valor
- Por ejemplo: si "Male" aparece 1,000,000 de veces, lo dice

```python
for valor, cantidad in conteo.head(limite).items():
    print(f"   • {valor}: {cantidad} veces")
```

**¿Qué hace esto?**
- Itera por los valores que más se repiten
- Imprime cada valor junto con cuántas veces aparece

**Analogía:** Es como hacer un ranking de los nombres más populares en una ciudad.

---

## Resultados del Análisis

El código encontró los siguientes datos:

### Filas completamente duplicadas
**NO SE ENCONTRARON** - Esto significa que no hay dos registros que sean exactamente iguales en todas las columnas.

### Valores duplicados por columna

| Columna | Valores Repetidos |
|---------|-------------------|
| Name | 1,532,499 |
| Age | 1,999,947 |
| Gender | 1,999,996 |
| Email | 1,187,536 |
| Income | 1,869,996 |
| Country | 1,999,755 |
| City | 1,895,441 |
| Job Title | 1,991,443 |
| Job Domain | 1,991,138 |

**¿Qué significa esto?**
- Todos los datos tienen muchos valores repetidos
- Esto es normal en un dataset synthetic (artificial) generado al azar

### Ejemplos de valores más repetidos

**Nombres:**
- "Michael Smith": 1,018 veces
- "Michael Johnson": 733 veces
- "David Smith": 647 veces

**Edades:**
- 44.0 años: 41,015 veces
- 40.0 años: 41,940 veces

**Géneros:**
- Male: 1,000,415 veces
- Female: 996,749 veces

**Títulos de trabajo:**
- Marketing Specialist: 499,338 veces
- Software Engineer: 499,048 veces
- Data Scientist: 499,038 veces

---

## Glosario de Términos técnicos

| Término | Explicación Simple |
|---------|-------------------|
| **DataFrame** | Una tabla de datos (como una hoja de Excel) |
| **Fila** | Un registro completo (una persona con todos sus datos) |
| **Columna** | Un tipo de dato específico (todas las edades, todos los nombres) |
| **Duplicado** | Algo que se repite más de una vez |
| **Función** | Un conjunto de instrucciones que hace una tarea específica |
| **Variable** | Un espacio en memoria donde guardamos información |
| **Librería/Paquete** | Código ya escrito que podemos usar para facilitarnos el trabajo |
| **CSV** | Formato de archivo de texto donde los datos están separados por comas |

---

## Resumen para Principiantes

1. **El código lee un archivo** enorme con 2 millones de personas
2. **Busca duplicados** de tres maneras:
   - Filas completas que sean iguales
   - Valores que se repiten en cada columna
   - Columnas que tengan los mismos datos
3. **Muestra los resultados** de forma organizada
4. **NO encontró filas completamente duplicadas**, pero sí muchos valores repetidos dentro de cada columna

El código es útil para limpiar datos antes de usarlos en Machine Learning, ya que los datos duplicados pueden afectar los resultados de los modelos.