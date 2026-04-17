# Plan: Análisis Completo de IED en México (2006–2023)

## Estado actual del notebook

| Módulo | Estado |
|--------|--------|
| Marco macroeconómico (PIB emergentes) | ✅ Completo |
| IED como % del PIB + regresión lineal | ✅ Completo |
| Modelos probabilísticos (Bernoulli, Binomial) | ✅ Completo |
| IED por Entidad Federativa | ⚠️ Parcial |
| IED por Sector | ⚠️ Solo carga de datos |
| IED por País de Origen | ✅ Completo |
| IED por País × Sector (Top 5 inversores) | ✅ Completo |
| Análisis combinado País × Entidad | ❌ Faltante |
| Conclusiones | ❌ Faltante |

---

## Datasets disponibles

| Archivo | Contenido | Dimensión |
|---------|-----------|-----------|
| `WBORG_PIB.csv` | PIB mundial por país 1960–2022 (World Bank) | ~267 países × 67 años |
| `PIB_INEGI.csv` | PIB + Ahorro bruto México 1993–2022 (INEGI) | ~30 filas × 5 cols |
| `IED-PIB.csv` | IED y porcentaje del PIB 1999–2022 | ~24 filas × 4 cols |
| `IED-Pais1990-2023.csv` | IED por país de origen 1980–2023 | ~50 países × 44 años |
| `IED-Entidad.csv` | IED por entidad federativa 2006–2023 | 32 entidades × 18 años |
| `IED-SECTOR.csv` | IED por sector 2006–2023 | ~20 sectores × 18 años |
| `IED-SubsectorFinal.csv` | IED por sector y subsector 2006–2023 | ~80 subsectores |
| `IED-RAMA.csv` | IED por sector, subsector y rama 2006–2023 | ~300+ ramas |
| `IED-Tipo-Pais.csv` | IED por país y tipo (nuevas/reinversión/intercompañías) | ~50 países × 3 tipos |
| `IED-Pais-Entidad.csv` | IED por país de origen × entidad federativa | ~1600 combinaciones |
| `IED-Empresas-Entidad.csv` | Nº empresas por entidad 2006–2023 | 32 entidades × 18 años |
| `IED-EmpresasPais.csv` | Total histórico de empresas por país | ~50 países |
| `IED-Empresas-Origen.csv` | Nº empresas por país de origen 2006–2023 | ~50 países × 18 años |
| `IED-Empresas-Sector.csv` | Nº empresas por sector 2006–2023 | ~20 sectores × 18 años |
| `IED-EUA-Sector.csv` | IED de EUA por sector 2006–2023 | EUA × ~300 ramas |
| `IED-Alemania.csv` | IED de Alemania por sector 2006–2023 | Alemania × ~300 ramas |
| `IED-Canada-Sector.csv` | IED de Canadá por sector 2006–2023 | Canadá × ~300 ramas |
| `IED-Espana-Sector.csv` | IED de España por sector 2006–2023 | España × ~300 ramas |
| `IED-Japon-Sector.csv` | IED de Japón por sector 2006–2023 | Japón × ~300 ramas |

> **Nota de limpieza transversal:** Los valores `"C"` indican datos confidenciales (Secretaría de Economía). Deben reemplazarse con `NaN`. Las columnas numéricas usan coma como separador de miles y deben convertirse con `pd.to_numeric` o `str.replace`.

---

## Módulos a desarrollar

---

### Módulo 4 — IED por Entidad Federativa (completar)

**Datasets:** `IED-Entidad.csv`, `IED-Empresas-Entidad.csv`

**Lo que ya existe:**
- Histograma de distribución del promedio
- Bar chart top 10 mayor / menor IED
- Línea temporal top 5 mayor y menor

**Lo que falta:**

#### 4.1 Limpieza y preparación
```python
# IED-Entidad.csv ya no tiene "Total general" en las columnas originales
# Verificar y crear columna 'Total general' si no existe
IED_Entidad['Total general'] = IED_Entidad.iloc[:, :-1].sum(axis=1)
```

#### 4.2 Tendencia nacional agregada (2006–2023)
- Línea temporal del total nacional por año (suma de todas las entidades)
- Identificar años pico y caídas (2009 crisis, 2020 COVID)

#### 4.3 Crecimiento relativo por entidad
- Calcular tasa de crecimiento CAGR entre 2006 y 2023 por entidad
- Bar chart: entidades con mayor crecimiento porcentual

#### 4.4 Correlación IED vs número de empresas por entidad
```python
# Cruzar IED_Entidad['Total general'] con IED_Empresas_Entidad['Total general']
# Scatter plot + coeficiente de Pearson
```

#### 4.5 Análisis de la Ciudad de México como outlier
- Comparar CDMX vs resto con y sin el outlier
- Participación porcentual de CDMX en el total nacional por año

---

### Módulo 5 — IED por Sector Económico

**Datasets:** `IED-SECTOR.csv`, `IED-SubsectorFinal.csv`, `IED-RAMA.csv`, `IED-Empresas-Sector.csv`

#### 5.1 Limpieza
```python
IED_Sector = pd.read_csv('IED/IED-SECTOR.csv')
year_cols = [c for c in IED_Sector.columns if c.startswith('Total 20')]
for col in year_cols:
    IED_Sector[col] = pd.to_numeric(IED_Sector[col], errors='coerce')
```

#### 5.2 Top 5 sectores por IED total histórica
- Bar chart horizontal con `Total general`
- Etiquetar porcentaje que representa cada sector

#### 5.3 Evolución temporal de los top 5 sectores (2006–2023)
- Línea temporal múltiple
- Identificar sectores con tendencia creciente vs decreciente

#### 5.4 Composición sectorial por año (stacked bar)
- Stacked bar chart mostrando la participación relativa de sectores por año
- Resalta cambios estructurales (e.g. crecimiento manufacturas vs minería)

#### 5.5 Drill-down: Top 3 subsectores del sector líder
- Usar `IED-SubsectorFinal.csv`
- Pie chart de composición interna del sector con mayor IED

#### 5.6 Correlación empresas vs flujo por sector
- Cruzar `IED-SECTOR.csv` y `IED-Empresas-Sector.csv`
- ¿Más empresas implica más inversión?

---

### Módulo 6 — IED por País de Origen

**Datasets:** `IED-Pais1990-2023.csv`, `IED-EmpresasPais.csv`, `IED-Empresas-Origen.csv`, `IED-Tipo-Pais.csv`

#### 6.1 Limpieza
```python
IED_Historico = pd.read_csv('IED/IED-Pais1990-2023.csv')
year_cols = [c for c in IED_Historico.columns if c.isdigit()]
for col in year_cols:
    IED_Historico[col] = (IED_Historico[col]
                          .astype(str).str.replace(',', '', regex=False)
                          .pipe(pd.to_numeric, errors='coerce'))
```

#### 6.2 Top 10 países por IED total histórica (1980–2023)
- Bar chart horizontal con valores totales
- Calcular participación porcentual

#### 6.3 Tendencia histórica top 5 países (1999–2023)
- Línea temporal: EUA, Alemania, Canadá, España, Japón
- Añadir anotaciones en eventos clave (crisis 2009, TMEC 2020, COVID 2020)

#### 6.4 Tipos de inversión por país (Tipo-Pais)
```python
# Columnas: Nuevas inversiones | Reinversión de utilidades | Cuentas entre compañías
# Stacked bar por país mostrando composición del flujo
```
- ¿Qué países reinvierten más vs hacen inversiones nuevas?
- Indicador de arraigo empresarial (alta reinversión = empresas consolidadas)

#### 6.5 Concentración geográfica de origen
- Calcular el índice HHI (Herfindahl-Hirschman) por año para medir concentración
- ¿Depende México demasiado de EUA?

#### 6.6 Número de empresas por país (total y tendencia)
- Usar `IED-EmpresasPais.csv` y `IED-Empresas-Origen.csv`
- Países con más empresas pero menor inversión por empresa (promedio)

---

### Módulo 7 — Análisis por País × Sector (Top 5 Inversores)

**Datasets:** `IED-EUA-Sector.csv`, `IED-Alemania.csv`, `IED-Canada-Sector.csv`, `IED-Espana-Sector.csv`, `IED-Japon-Sector.csv`

> **Nota de limpieza:** `IED-Alemania.csv` y `IED-Espana-Sector.csv` tienen columna `Unnamed: 1` con el sector — unir las dos primeras columnas antes de procesar.

#### 7.1 Función de limpieza unificada para archivos por país
```python
def limpiar_ied_pais(path, pais_col='País de Origen', sector_col='Sector, subsector y rama'):
    df = pd.read_csv(path)
    # Manejar formato con Unnamed: 1
    # Reemplazar 'C' con NaN
    # Convertir columnas de años a numérico
    return df
```

#### 7.2 Top 5 sectores por país
- Para cada uno de los 5 países: bar chart de sus 5 sectores principales
- Subplots 1×5 o comparativo lado a lado

#### 7.3 Comparativo del sector manufacturero entre países
- ¿Cuánto invierten EUA, Alemania, Japón en manufactura en México?
- Relevante para el contexto de nearshoring

#### 7.4 Sectores exclusivos por país
- ¿Hay sectores donde un solo país domina la inversión?
- Heatmap: país × top 10 sectores con intensidad de color = monto

---

### Módulo 8 — Análisis Geográfico: País × Entidad Federativa

**Dataset:** `IED-Pais-Entidad.csv`

#### 8.1 Limpieza
```python
IED_Pais_Entidad = pd.read_csv('IED/IED-Pais-Entidad.csv')
year_cols = [c for c in IED_Pais_Entidad.columns if c.startswith('Total 20')]
for col in year_cols:
    IED_Pais_Entidad[col] = pd.to_numeric(IED_Pais_Entidad[col], errors='coerce')
```

#### 8.2 Heatmap país × entidad
```python
pivot = IED_Pais_Entidad.groupby(['País de Origen', 'Entidad federativa'])['Total general'].sum().unstack()
# seaborn heatmap con top 10 países y 10 entidades
```

#### 8.3 ¿Qué estados diversifican más sus fuentes de inversión?
- Contar el número de países inversores por entidad
- Bar chart de diversificación de origen

#### 8.4 Estados dominados por un solo país
- Para cada entidad: identificar el país con mayor participación
- ¿CDMX depende de EUA? ¿Nuevo León de Alemania/Japón?

---

### Módulo 9 — Síntesis y Conclusiones

#### 9.1 Dashboard resumen (subplots)
- Figura 4×2 con los 8 hallazgos principales en miniatura

#### 9.2 Tabla de oportunidades por entidad y sector
```python
# Combinar:
# - Crecimiento relativo de IED por entidad (Módulo 4.3)
# - Sectores con mayor crecimiento (Módulo 5.3)
# - Países con tendencia creciente (Módulo 6.3)
# Generar DataFrame de "entidades × sectores con potencial"
```

#### 9.3 Índice de dependencia por entidad
- Ratio: IED promedio / PIB estimado estatal
- Identificar las entidades más dependientes de capital extranjero

#### 9.4 Texto de conclusiones
- Impacto del T-MEC/USMCA (post-2020)
- Efecto COVID-19 en los flujos de 2020
- Nearshoring: ¿se refleja en los datos 2021–2023?
- Recomendaciones por perfil (inversionista, entidad federativa, secretaría)

---

## Orden de implementación sugerido

```
Módulo 4  →  Módulo 5  →  Módulo 6  →  Módulo 7  →  Módulo 8  →  Módulo 9
(Entidad)   (Sector)    (País)       (País×Sector)  (País×Entidad)  (Síntesis)
```

Cada módulo sigue este patrón de celda:
1. **Carga y limpieza** del dataset
2. **Estadísticas descriptivas** (`.describe()`, `.info()`)
3. **Visualización(es)** con título, etiquetas y `plt.tight_layout()`
4. **Celda markdown** con interpretación de los resultados

---

## Funciones de utilidad a agregar (celda #3 del notebook)

```python
def limpiar_numericos(df, excluir=None):
    """Convierte columnas con valores 'C' y comas a float."""
    excluir = excluir or []
    for col in df.columns:
        if col not in excluir:
            df[col] = (df[col].astype(str)
                       .str.replace(',', '', regex=False)
                       .replace('C', np.nan)
                       .pipe(pd.to_numeric, errors='coerce'))
    return df

def graficar_linea_multiple(df, titulo, xlabel, ylabel, columnas=None):
    """Línea temporal con múltiples series desde un DataFrame transpuesto."""
    columnas = columnas or df.index.tolist()
    plt.figure(facecolor='white', figsize=(15, 5))
    for col in columnas:
        plt.plot(df.columns, df.loc[col], label=col)
    plt.title(titulo)
    plt.xlabel(xlabel)
    plt.ylabel(ylabel)
    plt.legend()
    plt.xticks(rotation=45)
    plt.tight_layout()
    plt.show()
```
