# web-scraping-data-cleaning
A data engineering repository dedicated to cleaning, parsing, and transforming raw web-scraped data from major female music tours into an analysis-ready format.

# Data Cleaning and Preparation: Tour Revenue Dataset
# Limpeza e Preparação de Dados: Dataset de Receitas de Turnês

## 🇬🇧 English Version

### 📌 Project Overview

This project focuses on cleaning and preparing a dataset containing tour-related information, including tour titles, years, gross revenue, average gross revenue, and chart rankings.

The goal is to transform raw, inconsistent data into a structured dataset suitable for further analysis, demonstrating practical skills in data wrangling and preprocessing using Python and Pandas.

### 🎯 Objectives

- Inspect the dataset and identify data quality issues.
- Standardize column names for easier access and manipulation.
- Convert financial values stored as text into numeric data types.
- Remove irrelevant columns and citation markers.
- Clean chart ranking columns and handle missing values.
- Extract start and end years from inconsistent year formats.
- Standardize tour titles and prepare the dataset for analysis.

### 🛠️ Tools and Technologies

- **Python** — data manipulation and preprocessing.
- **Pandas** — data inspection, cleaning, transformation, and missing-value handling.
- **Regular Expressions (Regex)** — extracting years and removing unwanted text patterns.
- **Jupyter Notebook** — interactive data cleaning and documentation.

### 🧹 Data Cleaning Process

#### 1. Data Inspection

Loaded the CSV file using Pandas and inspected the first rows, individual columns, data types, and dataset structure.

```python
tour_df = pd.read_csv('tour.csv')

display(tour_df.head())
tour_df.info()
```

**Purpose:** Understand the dataset's structure and identify potential data quality issues before applying transformations.

#### 2. Standardizing Column Names

Converted column names to lowercase, replaced spaces and non-breaking spaces with underscores, and removed leading or trailing whitespace.

```python
tour_df.columns = tour_df.columns.str.lower()
tour_df.columns = tour_df.columns.str.replace(' ', '_')
tour_df.columns = tour_df.columns.str.replace('\xa0', '_')
tour_df.columns = tour_df.columns.str.strip()
```

**Purpose:** Improve consistency and make column names easier to reference in Python.

#### 3. Cleaning Financial Columns

Removed dollar signs and thousands separators from the following columns:

- `actual_gross`
- `adjusted_gross_(in_2022_dollars)`
- `average_gross`

Converted the cleaned values into numeric data types using `pd.to_numeric()`.

```python
columns = [
    'actual_gross',
    'adjusted_gross_(in_2022_dollars)',
    'average_gross'
]

for col in columns:
    tour_df[col] = tour_df[col].astype(str).str.replace(
        '$', '', regex=False
    )
    tour_df[col] = tour_df[col].str.replace(
        ',', '', regex=False
    )
    tour_df[col] = pd.to_numeric(
        tour_df[col], errors='coerce'
    )
```

**Purpose:** Make financial data usable for calculations, statistical summaries, and comparisons.

Values that could not be converted were set to missing values (`NaN`) rather than being interpreted as valid numbers.

#### 4. Removing Irrelevant Columns

Removed the `ref.` column because it contained source citation markers rather than information required for the planned analysis.

```python
tour_df = tour_df.drop(columns=['ref.'])
```

**Purpose:** Keep the dataset focused on relevant analytical variables.

#### 5. Cleaning Chart Ranking Columns

Converted `peak` and `all_time_peak` to string types, removed citation references enclosed in square brackets, and converted the resulting values to nullable integers.

```python
tour_df['peak'] = tour_df['peak'].astype('string')
tour_df['all_time_peak'] = tour_df['all_time_peak'].astype('string')

tour_df['peak'] = tour_df['peak'].str.replace(
    r'\[.*?\]', '', regex=True
).str.strip()

tour_df['all_time_peak'] = tour_df['all_time_peak'].str.replace(
    r'\[.*?\]', '', regex=True
).str.strip()

tour_df['peak'] = pd.to_numeric(
    tour_df['peak'], errors='coerce'
).astype('Int64')

tour_df['all_time_peak'] = pd.to_numeric(
    tour_df['all_time_peak'], errors='coerce'
).astype('Int64')
```

**Purpose:** Ensure ranking values are stored in a consistent numeric format while allowing missing values.

#### 6. Extracting Start and End Years

Some records in `year(s)` contained a single year, while others included a start and end year. Regular expressions were used to extract both values into separate columns.

```python
years = tour_df['year(s)'].str.extract(
    r'(\d{4})(?:\D+(\d{4}))?'
)

tour_df['start_year'] = years[0]
tour_df['end_year'] = years[1].fillna(years[0])
```

**Purpose:** Structure inconsistent year information into separate fields, making it easier to analyze tour durations and compare tours by year.

When only one year was available, it was assigned to both `start_year` and `end_year`, representing a single-year tour for analytical purposes.

#### 7. Cleaning Tour Titles

Used a regular expression to retain letters, numbers, and whitespace in the `tour_title` column.

```python
tour_df['tour_title'] = tour_df['tour_title'].str.extract(
    r'([a-zA-Z0-9\s]+)'
)
```

**Purpose:** Remove unwanted characters and standardize tour titles.

This approach assumes that the relevant title begins with an alphanumeric character. Titles containing punctuation or non-ASCII characters should be reviewed to ensure that meaningful information is not lost.

#### 8. Handling Missing Values

Inspected missing values in the chart ranking columns:

```python
tour_df['peak'].isna().sum()
tour_df['all_time_peak'].isna().sum()
```

The initial inspection identified:

- **9 missing values** in `peak`.
- **14 missing values** in `all_time_peak`.

These values were retained as missing rather than replaced with zero or another ranking.

**Reasoning:** Missing information does not necessarily mean that a tour failed to achieve a ranking. Without reliable evidence to determine the cause, preserving missing values avoids introducing unsupported assumptions.

### 📊 Outcome

The cleaning workflow prepares the dataset for further analysis by:

- Standardizing column names.
- Converting financial fields into numeric values.
- Cleaning and converting ranking fields.
- Separating tour start and end years.
- Removing irrelevant citation markers.
- Preserving missing ranking information without assuming its meaning.

The resulting dataset is better structured for exploratory data analysis (EDA), revenue comparisons, ranking analysis, and year-based investigations.

### 🚀 Potential Next Steps

- Validate the data types and missing values after cleaning.
- Check for duplicate records and inconsistent tour titles.
- Investigate invalid or implausible year values.
- Compare actual and inflation-adjusted gross revenue.
- Analyze revenue per tour and average gross revenue.
- Explore trends in tour rankings over time.

### 📁 Project Structure

```text
tour-data-cleaning/
├── tour.csv
├── data_cleaning.ipynb
└── README.md
```

*Note: Update the file names in this structure to match the actual files in the repository.*

---

## 🇧🇷 Versão em Português

### 📌 Visão Geral do Projeto

Este projeto tem como foco a limpeza e preparação de um conjunto de dados com informações sobre turnês, incluindo títulos, anos, receita bruta, receita bruta média e posições em rankings musicais.

O objetivo é transformar dados brutos e inconsistentes em uma base estruturada e adequada para análises posteriores, demonstrando habilidades práticas de manipulação e pré-processamento de dados com Python e Pandas.

### 🎯 Objetivos

- Inspecionar a estrutura dos dados e identificar problemas de qualidade.
- Padronizar os nomes das colunas.
- Converter valores financeiros armazenados como texto em dados numéricos.
- Remover colunas irrelevantes e marcadores de citações.
- Limpar as colunas de posições em rankings e tratar valores ausentes.
- Extrair os anos de início e término de turnês.
- Padronizar os títulos das turnês e preparar a base para análise.

### 🛠️ Ferramentas e Tecnologias

- **Python** — manipulação e pré-processamento de dados.
- **Pandas** — inspeção, limpeza, transformação e tratamento de valores ausentes.
- **Expressões regulares (Regex)** — extração de anos e remoção de padrões indesejados.
- **Jupyter Notebook** — execução e documentação do processo de limpeza.

### 🧹 Etapas de Limpeza de Dados

#### 1. Inspeção dos Dados

O arquivo CSV foi carregado com Pandas. Em seguida, foram inspecionadas as primeiras linhas, as colunas, os tipos de dados e a estrutura geral do conjunto de dados.

```python
tour_df = pd.read_csv('tour.csv')

display(tour_df.head())
tour_df.info()
```

**Objetivo:** compreender a estrutura da base e identificar possíveis problemas de qualidade antes de realizar as transformações.

#### 2. Padronização dos Nomes das Colunas

Os nomes das colunas foram convertidos para letras minúsculas, os espaços foram substituídos por underscores e os espaços extras no início ou no final foram removidos.

```python
tour_df.columns = tour_df.columns.str.lower()
tour_df.columns = tour_df.columns.str.replace(' ', '_')
tour_df.columns = tour_df.columns.str.replace('\xa0', '_')
tour_df.columns = tour_df.columns.str.strip()
```

**Objetivo:** melhorar a consistência dos nomes e facilitar o acesso às colunas durante a manipulação dos dados.

#### 3. Limpeza das Colunas Financeiras

Foram removidos os símbolos de dólar e os separadores de milhares das seguintes colunas:

- `actual_gross`
- `adjusted_gross_(in_2022_dollars)`
- `average_gross`

Depois, os valores foram convertidos para tipos numéricos com `pd.to_numeric()`.

```python
columns = [
    'actual_gross',
    'adjusted_gross_(in_2022_dollars)',
    'average_gross'
]

for col in columns:
    tour_df[col] = tour_df[col].astype(str).str.replace(
        '$', '', regex=False
    )
    tour_df[col] = tour_df[col].str.replace(
        ',', '', regex=False
    )
    tour_df[col] = pd.to_numeric(
        tour_df[col], errors='coerce'
    )
```

**Objetivo:** permitir cálculos matemáticos, estatísticas descritivas e comparações financeiras.

Valores que não puderam ser convertidos foram classificados como ausentes (`NaN`), em vez de serem tratados como números válidos.

#### 4. Remoção de Colunas Irrelevantes

A coluna `ref.` foi removida porque continha marcadores de citações das fontes, e não informações necessárias para a análise planejada.

```python
tour_df = tour_df.drop(columns=['ref.'])
```

**Objetivo:** manter na base apenas as variáveis relevantes para o trabalho analítico.

#### 5. Limpeza das Colunas de Ranking

As colunas `peak` e `all_time_peak` foram convertidas para strings, tiveram as referências entre colchetes removidas e, em seguida, foram convertidas para números inteiros que permitem valores ausentes.

```python
tour_df['peak'] = tour_df['peak'].astype('string')
tour_df['all_time_peak'] = tour_df['all_time_peak'].astype('string')

tour_df['peak'] = tour_df['peak'].str.replace(
    r'\[.*?\]', '', regex=True
).str.strip()

tour_df['all_time_peak'] = tour_df['all_time_peak'].str.replace(
    r'\[.*?\]', '', regex=True
).str.strip()

tour_df['peak'] = pd.to_numeric(
    tour_df['peak'], errors='coerce'
).astype('Int64')

tour_df['all_time_peak'] = pd.to_numeric(
    tour_df['all_time_peak'], errors='coerce'
).astype('Int64')
```

**Objetivo:** garantir que as posições nos rankings estejam armazenadas em um formato numérico consistente, preservando a possibilidade de valores ausentes.

#### 6. Extração dos Anos de Início e Término

Alguns registros da coluna `year(s)` continham apenas um ano, enquanto outros apresentavam dois anos. Foram utilizadas expressões regulares para extrair essas informações e armazená-las em duas novas colunas.

```python
years = tour_df['year(s)'].str.extract(
    r'(\d{4})(?:\D+(\d{4}))?'
)

tour_df['start_year'] = years[0]
tour_df['end_year'] = years[1].fillna(years[0])
```

**Objetivo:** estruturar as informações temporais em campos separados, facilitando futuras análises da duração das turnês e comparações entre períodos.

Quando apenas um ano estava disponível, ele foi atribuído tanto a `start_year` quanto a `end_year`, representando uma turnê de um único ano para fins analíticos.

#### 7. Limpeza dos Títulos das Turnês

Foi utilizada uma expressão regular para manter letras, números e espaços na coluna `tour_title`.

```python
tour_df['tour_title'] = tour_df['tour_title'].str.extract(
    r'([a-zA-Z0-9\s]+)'
)
```

**Objetivo:** remover caracteres indesejados e padronizar os títulos.

Essa abordagem pressupõe que o título relevante começa com um caractere alfanumérico. Títulos com pontuação ou caracteres especiais devem ser revisados para evitar a perda de informações importantes.

#### 8. Tratamento de Valores Ausentes

Foi realizada uma inspeção dos valores ausentes nas colunas de ranking:

```python
tour_df['peak'].isna().sum()
tour_df['all_time_peak'].isna().sum()
```

A inspeção inicial identificou:

- **9 valores ausentes** na coluna `peak`.
- **14 valores ausentes** na coluna `all_time_peak`.

Esses valores foram mantidos como ausentes, sem substituição por zero ou por outra posição no ranking.

**Justificativa:** a ausência de informação não significa necessariamente que uma turnê não tenha alcançado determinada posição. Sem evidências confiáveis para determinar a causa, manter os valores ausentes evita a introdução de suposições não comprovadas.

### 📊 Resultados

O processo de limpeza prepara a base para análises futuras por meio de:

- Padronização dos nomes das colunas.
- Conversão de campos financeiros para valores numéricos.
- Limpeza e conversão das colunas de ranking.
- Separação dos anos de início e término das turnês.
- Remoção de marcadores de citações irrelevantes.
- Preservação de informações ausentes sem atribuir significados não confirmados.

A base resultante apresenta uma estrutura mais adequada para análise exploratória de dados (EDA), comparação de receitas, análise de rankings e investigação de tendências ao longo dos anos.

### 🚀 Próximos Passos

- Validar os tipos de dados e os valores ausentes após a limpeza.
- Verificar registros duplicados e títulos inconsistentes.
- Investigar anos inválidos ou fora do esperado.
- Comparar receitas brutas reais e ajustadas pela inflação.
- Analisar a receita por turnê e a receita bruta média.
- Explorar tendências nas posições dos rankings ao longo do tempo.

### 📁 Estrutura do Projeto

```text
tour-data-cleaning/
├── tour.csv
├── data_cleaning.ipynb
└── README.md
```

*Observação: ajuste os nomes dos arquivos dessa estrutura para corresponder aos arquivos reais do seu repositório.*

