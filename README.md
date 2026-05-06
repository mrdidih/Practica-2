# Práctica II – C++

Estructuras de Datos y Algoritmos   
Universidad EAFIT   
Docente: Alexander Narváez Berrío   
Estudiante: Juan Diego Muñoz Buitrago   
Fecha: Mayo 2026

---

## 📋 Descripción del Proyecto

Este programa implementa y compara **dos algoritmos de ordenamiento no-comparativos** sobre datasets de hasta **1.000.000 de elementos enteros**, tal como lo solicita la práctica:

- **DialSort** – Ordenamiento por conteo de frecuencias (Counting Sort sobre universo U)
- **RadixSort (LSD)** – Ordenamiento dígito a dígito de menos a más significativo

El objetivo es comparar el comportamiento de cada algoritmo variando el tamaño del input (n) y el tamaño del universo (U), midiendo tiempo de ejecución, desviación estándar y throughput. Los resultados se exportan automáticamente a un archivo `benchmark_results.csv`.

---

## 🗂 Estructura del Repositorio

```
practicados/
├── CMakeLists.txt
├── main.cpp
├── DialSort.h
├── DialSort.cpp
├── RadixSort.h
├── RadixSort.cpp
├── Benchmark.h
├── Benchmark.cpp
├── DataGenerator.h
├── DataGenerator.cpp
├── Visualizer.h
├── Visualizer.cpp
└── README.md
```

---

## ⚙️ Instrucciones de Ejecución

### Requisitos
- CMake 3.20 o superior
- Compilador C++20 (g++ o clang++)
- CLion (recomendado) o cualquier terminal con cmake instalado

### Compilar y ejecutar en CLion
1. Abrir CLion → **File → Open** → seleccionar la carpeta `practicados/`
2. CLion detecta el `CMakeLists.txt` automáticamente → clic en **Load CMake Project**
3. Presionar **Run ▶**

### Compilar y ejecutar por terminal
```bash
mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
cmake --build .
./practicados
```

### Menú del programa
```
1. Visualizar DialSort
2. Visualizar Radix Sort
3. Ejecutar Benchmark Completo
4. Salir
```

- Las opciones **1 y 2** generan un arreglo de 30 elementos aleatorios (rango 0–200) y muestran el arreglo original y ordenado en consola.
- La opción **3** ejecuta el benchmark completo y guarda los resultados en `benchmark_results.csv`.

---

## 📊 Resultados

El benchmark prueba todas las combinaciones de **n** y **U** con distribución uniforme aleatoria, repitiendo cada prueba 3 veces para calcular media y desviación estándar.

### Configuraciones evaluadas

| N         | Universo (U) |
|-----------|--------------|
| 100.000   | 10.000       |
| 100.000   | 100.000      |
| 100.000   | 1.000.000    |
| 500.000   | 10.000       |
| 500.000   | 100.000      |
| 500.000   | 1.000.000    |
| 1.000.000 | 10.000       |
| 1.000.000 | 100.000      |
| 1.000.000 | 1.000.000    |

### Resultados representativos (distribución uniforme)

| N         | U         | DialSort (ms) | RadixSort (ms) | Ganador   |
|-----------|-----------|---------------|----------------|-----------|
| 100.000   | 10.000    | ~5            | ~3             | RadixSort |
| 100.000   | 1.000.000 | ~35           | ~3             | RadixSort |
| 500.000   | 100.000   | ~25           | ~14            | RadixSort |
| 1.000.000 | 10.000    | ~15           | ~28            | DialSort  |
| 1.000.000 | 1.000.000 | ~360          | ~32            | RadixSort |

> **Observación clave:** DialSort es competitivo cuando U es pequeño respecto a n (ej. U = 10.000 con n = 1.000.000). Cuando U >> n, aloca un vector de conteo enorme con valores mayormente en cero, lo que degrada su rendimiento.

Los resultados completos quedan exportados en **`benchmark_results.csv`** con columnas: `Algorithm, N, Universe, MeanTime_ms, StdDev, Throughput`.

---

## 🔍 Análisis de Complejidad (Big O)

| Algoritmo      | Mejor caso      | Caso promedio   | Peor caso       | Espacio  |
|----------------|-----------------|-----------------|-----------------|----------|
| DialSort       | O(n + U)        | O(n + U)        | O(n + U)        | O(n + U) |
| RadixSort(LSD) | O(d × (n + 10)) | O(d × (n + 10)) | O(d × (n + 10)) | O(n + 10)|

Donde:
- **n** = número de elementos
- **U** = tamaño del universo (valor máximo + 1)
- **d** = número de dígitos del valor máximo (d = floor(log10(U)) + 1)

---

### Preguntas

**• Which algorithm performed better?**  
RadixSort funcionó mejor en la mayoría de configuraciones. Su complejidad de espacio es O(n + 10) independientemente del universo, por lo que no se ve afectado cuando U crece. DialSort solo superó a RadixSort cuando U era muy pequeño (U = 10.000) con n grande, porque en ese caso el vector de conteo es pequeño y el recorrido es rápido.

**• Why does theoretical complexity sometimes differ from practical results?**  
Ambos algoritmos son lineales en teoría, pero en la práctica la gestión de memoria tiene mucho impacto. DialSort aloca un vector de tamaño U para contar frecuencias: cuando U = 1.000.000, recorre un millón de posiciones aunque la mayoría estén en cero, generando muchos fallos de caché. RadixSort trabaja siempre con vectores de tamaño fijo (10 cubetas por pasada), lo que hace un uso mucho más eficiente de la caché del CPU.

**• What advantages or disadvantages does each data structure present?**

- **DialSort (Counting Sort sobre universo U)**:  
  Ventaja → Implementación simple, una sola pasada sobre los datos, muy rápido cuando U ≈ n.  
  Desventaja → Memoria O(n + U). Si U es grande, el consumo de memoria y tiempo de inicialización lo hacen ineficiente.

- **RadixSort LSD (Counting Sort por dígito)**:  
  Ventaja → Espacio O(n + 10), eficiente en memoria sin importar el rango de valores. Rendimiento estable en todas las configuraciones.  
  Desventaja → Requiere d pasadas sobre los datos (una por dígito), y solo aplica a tipos con representación posicional (enteros).

---

### Conclusión

Para este problema de ordenar grandes volúmenes de enteros con universo variable, **RadixSort es la mejor opción general**. Es consistentemente más rápido, usa mucha menos memoria cuando U es grande, y su rendimiento no depende del valor máximo de los datos.

DialSort sería preferible únicamente cuando U es pequeño en relación a n — por ejemplo, ordenar un millón de calificaciones entre 0 y 100 — ya que en ese caso su implementación es más simple e igual de rápida.
