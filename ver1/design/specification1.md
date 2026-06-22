# Спецификации стандартов размещения RDF-метаданных в SVG

## 1. SVG 1.1 — Элемент `<metadata>`

**Источник:** W3C Recommendation, 16 August 2011  
**URL:** https://www.w3.org/TR/SVG11/metadata.html

### 1.1 Определение элемента

```
Element:  <metadata>
Категория: Описательный элемент
Содержимое: Любые элементы из других XML пространств имён
Атрибуты:
  - id (унаследован от XML)
  - xml:lang
  - xml:space
  - class
  - style
  - externalResourcesRequired
  - onload
```

### 1.2 Назначение

Элемент `<metadata>` предназначен для хранения метаданных о SVG-документе или его частях. Метаданные не отображаются пользователю и не влияют на рендеринг. Рекомендуется размещать `<metadata>` в начале документа, сразу после тега `<svg>`.

### 1.3 Позиционирование

- `<metadata>` может быть дочерним элементом `<svg>`, `<g>`, `<symbol>`, `<defs>`, а также большинства SVG-элементов отображения.
- Для метаданных всего документа — помещается в корневой `<svg>`.
- Для метаданных конкретного элемента — помещается внутрь соответствующей группы `<g>`.

### 1.4 Пример по спецификации

```xml
<?xml version="1.0" encoding="UTF-8"?>
<svg xmlns="http://www.w3.org/2000/svg"
     xmlns:dc="http://purl.org/dc/elements/1.1/"
     xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
     width="200" height="100">
  <metadata>
    <rdf:RDF>
      <rdf:Description rdf:about="">
        <dc:title xml:lang="ru">Пример SVG с метаданными</dc:title>
        <dc:creator>Автор документа</dc:creator>
        <dc:description>SVG-файл с RDF-метаданными</dc:description>
        <dc:date>2024-01-01</dc:date>
        <dc:format>image/svg+xml</dc:format>
        <dc:language>ru</dc:language>
      </rdf:Description>
    </rdf:RDF>
  </metadata>
  <rect width="200" height="100" fill="blue"/>
</svg>
```

---

## 2. RDF/XML Syntax Specification

**Источник:** W3C Recommendation, 10 February 2004  
**URL:** https://www.w3.org/TR/rdf-syntax-grammar/

### 2.1 Базовая структура RDF/XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<rdf:RDF
  xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
  xmlns:rdfs="http://www.w3.org/2000/01/rdf-schema#"
  xmlns:owl="http://www.w3.org/2002/07/owl#"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema#"
  xmlns:dc="http://purl.org/dc/elements/1.1/"
  xmlns:vad="http://example.org/vad#">

  <!-- Описание субъекта -->
  <rdf:Description rdf:about="http://example.org/diagram1">
    <rdf:type rdf:resource="http://example.org/vad#Diagram"/>
    <dc:title>VAD: Управление заказами</dc:title>
    <dc:creator>Аналитик</dc:creator>
  </rdf:Description>

  <!-- Описание элемента диаграммы -->
  <vad:Process rdf:about="http://example.org/diagram1#process1">
    <dc:title>Приём заказа</dc:title>
    <vad:performer rdf:resource="http://example.org/diagram1#role1"/>
    <vad:input rdf:resource="http://example.org/diagram1#doc1"/>
    <vad:output rdf:resource="http://example.org/diagram1#doc2"/>
  </vad:Process>

</rdf:RDF>
```

### 2.2 Синтаксические конструкции RDF/XML

#### 2.2.1 Типизированные узлы

```xml
<vad:Process rdf:about="...">
  <!-- эквивалентно rdf:type vad:Process -->
</vad:Process>
```

#### 2.2.2 Литералы с типами данных

```xml
<vad:duration rdf:datatype="http://www.w3.org/2001/XMLSchema#integer">30</vad:duration>
<vad:startDate rdf:datatype="http://www.w3.org/2001/XMLSchema#date">2024-01-15</vad:startDate>
```

#### 2.2.3 Литералы с языковыми тегами

```xml
<dc:title xml:lang="ru">Приём заказа</dc:title>
<dc:title xml:lang="en">Order Reception</dc:title>
```

#### 2.2.4 Анонимные узлы (blank nodes)

```xml
<vad:Process rdf:about="#process1">
  <vad:hasAttribute>
    <rdf:Description>
      <vad:name>Сложность</vad:name>
      <vad:value>Высокая</vad:value>
    </rdf:Description>
  </vad:hasAttribute>
</vad:Process>
```

---

## 3. RDFa Core 1.1

**Источник:** W3C Recommendation, 07 June 2012  
**URL:** https://www.w3.org/TR/rdfa-core/

### 3.1 Ключевые атрибуты RDFa

| Атрибут | Назначение | Пример |
|---|---|---|
| `vocab` | Объявление словаря по умолчанию | `vocab="http://schema.org/"` |
| `prefix` | Объявление префиксов | `prefix="vad: http://example.org/vad#"` |
| `typeof` | Указание типа ресурса | `typeof="vad:Process"` |
| `property` | Указание предиката | `property="dc:title"` |
| `resource` | URI ресурса | `resource="http://example.org/p1"` |
| `about` | Субъект триплета | `about="#process1"` |
| `content` | Литеральное значение | `content="Приём заказа"` |
| `datatype` | Тип данных литерала | `datatype="xsd:integer"` |
| `rel` | Предикат для URI-объектов | `rel="vad:performer"` |
| `href` | URI объекта | `href="#role1"` |
| `inlist` | Включение в rdf:List | `inlist=""` |

### 3.2 Пример RDFa в SVG

```xml
<svg xmlns="http://www.w3.org/2000/svg"
     prefix="vad: http://example.org/vad#
             dc: http://purl.org/dc/elements/1.1/
             xsd: http://www.w3.org/2001/XMLSchema#"
     about="http://example.org/diagram1"
     typeof="vad:Diagram">

  <metadata>
    <span property="dc:title" content="VAD: Управление заказами"/>
    <span property="dc:creator" content="Аналитик"/>
  </metadata>

  <g id="process1"
     about="#process1"
     typeof="vad:Process">
    <desc property="dc:title">Приём заказа</desc>
    <rect width="120" height="60" fill="#4A90D9"/>
    <text x="10" y="35">Приём заказа</text>
  </g>

</svg>
```

### 3.3 Инициальные контексты RDFa 1.1

RDFa 1.1 определяет набор встроенных префиксов (Initial Context):

```
rdf:   http://www.w3.org/1999/02/22-rdf-syntax-ns#
rdfs:  http://www.w3.org/2000/01/rdf-schema#
owl:   http://www.w3.org/2002/07/owl#
xsd:   http://www.w3.org/2001/XMLSchema#
dc:    http://purl.org/dc/terms/
dcterms: http://purl.org/dc/terms/
foaf:  http://xmlns.com/foaf/0.1/
skos:  http://www.w3.org/2004/02/skos/core#
schema: http://schema.org/
```

---

## 4. JSON-LD 1.1

**Источник:** W3C Recommendation, 16 July 2020  
**URL:** https://www.w3.org/TR/json-ld11/

### 4.1 Ключевые ключевые слова JSON-LD

| Ключевое слово | Назначение |
|---|---|
| `@context` | Определение контекста (префиксы, типы) |
| `@id` | URI ресурса (субъект) |
| `@type` | Тип ресурса (rdf:type) |
| `@value` | Литеральное значение |
| `@language` | Языковой тег |
| `@datatype` | Тип данных |
| `@graph` | Именованный граф |
| `@list` | RDF-список |
| `@set` | Множество значений |
| `@base` | Базовый URI |
| `@vocab` | Словарь по умолчанию |

### 4.2 Полный пример JSON-LD для SVG-диаграммы

```json
{
  "@context": {
    "@base": "http://example.org/",
    "vad": "http://example.org/vad#",
    "dc": "http://purl.org/dc/elements/1.1/",
    "xsd": "http://www.w3.org/2001/XMLSchema#",
    "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
    "performer": {"@id": "vad:performer", "@type": "@id"},
    "input": {"@id": "vad:input", "@type": "@id"},
    "output": {"@id": "vad:output", "@type": "@id"},
    "duration": {"@id": "vad:duration", "@type": "xsd:integer"},
    "priority": {"@id": "vad:priority", "@type": "xsd:string"}
  },
  "@graph": [
    {
      "@id": "diagram1",
      "@type": "vad:Diagram",
      "dc:title": "VAD: Управление заказами",
      "dc:creator": "Аналитик",
      "dc:date": "2024-01-01",
      "dc:description": "Диаграмма цепочки добавленной стоимости"
    },
    {
      "@id": "diagram1#process1",
      "@type": "vad:Process",
      "dc:title": "Приём заказа",
      "performer": "diagram1#role1",
      "input": ["diagram1#doc_request"],
      "output": ["diagram1#doc_order"],
      "duration": 30,
      "priority": "Высокий"
    },
    {
      "@id": "diagram1#role1",
      "@type": "vad:Role",
      "dc:title": "Менеджер по продажам",
      "vad:department": "Отдел продаж"
    }
  ]
}
```

### 4.3 Встраивание JSON-LD в SVG

```xml
<svg xmlns="http://www.w3.org/2000/svg">
  <metadata>
    <script type="application/ld+json">
    { ...JSON-LD контент... }
    </script>
  </metadata>
</svg>
```

---

## 5. W3C Turtle (Terse RDF Triple Language)

**Источник:** W3C Recommendation, 25 February 2014  
**URL:** https://www.w3.org/TR/turtle/

### 5.1 Синтаксис Turtle

```turtle
@base <http://example.org/> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix owl: <http://www.w3.org/2002/07/owl#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix dc: <http://purl.org/dc/elements/1.1/> .
@prefix vad: <http://example.org/vad#> .

# Документ
<diagram1> a vad:Diagram ;
    dc:title "VAD: Управление заказами"@ru ;
    dc:creator "Аналитик" ;
    dc:date "2024-01-01"^^xsd:date .

# Процесс
<diagram1#process1> a vad:Process ;
    dc:title "Приём заказа"@ru ;
    vad:performer <diagram1#role1> ;
    vad:input <diagram1#doc_request> ;
    vad:output <diagram1#doc_order> ;
    vad:duration "30"^^xsd:integer ;
    vad:priority "Высокий" .

# Роль
<diagram1#role1> a vad:Role ;
    dc:title "Менеджер по продажам"@ru ;
    vad:department "Отдел продаж" .
```

### 5.2 Сокращения Turtle

- `a` — эквивалент `rdf:type`
- `;` — продолжение предикатов для того же субъекта
- `,` — перечисление объектов для того же субъекта-предиката
- `[...]` — анонимный узел (blank node)

---

## 6. TriG

**Источник:** W3C Recommendation, 25 February 2014  
**URL:** https://www.w3.org/TR/trig/

### 6.1 Структура TriG

TriG расширяет Turtle добавлением именованных графов:

```trig
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix vad: <http://example.org/vad#> .
@prefix dc: <http://purl.org/dc/elements/1.1/> .

# Дефолтный граф
{
  <http://example.org/diagram1> a vad:Diagram .
}

# Именованный граф документа
<http://example.org/graph/diagram1> {
  <http://example.org/diagram1> a vad:Diagram ;
      dc:title "VAD: Управление заказами"@ru ;
      dc:creator "Аналитик" .
}

# Именованный граф элемента process1
<http://example.org/graph/process1> {
  <http://example.org/diagram1#process1> a vad:Process ;
      dc:title "Приём заказа"@ru ;
      vad:performer <http://example.org/diagram1#role1> .
}

# Именованный граф элемента role1
<http://example.org/graph/role1> {
  <http://example.org/diagram1#role1> a vad:Role ;
      dc:title "Менеджер по продажам"@ru .
}
```

### 6.2 Преимущества TriG

1. Поддержка нескольких контекстов (именованных графов) в одном файле
2. Совместимость с SPARQL Dataset (FROM NAMED)
3. Позволяет чётко разделить метаданные документа и метаданные элементов

---

## 7. Dublin Core Metadata Initiative (DCMI)

**Источник:** DCMI, Dublin Core Metadata Element Set, Version 1.1  
**URL:** https://www.dublincore.org/specifications/dublin-core/dces/

### 7.1 Базовые элементы Dublin Core

| Элемент | URI | Описание |
|---|---|---|
| `dc:title` | `http://purl.org/dc/elements/1.1/title` | Название |
| `dc:creator` | `http://purl.org/dc/elements/1.1/creator` | Создатель |
| `dc:subject` | `http://purl.org/dc/elements/1.1/subject` | Тема |
| `dc:description` | `http://purl.org/dc/elements/1.1/description` | Описание |
| `dc:publisher` | `http://purl.org/dc/elements/1.1/publisher` | Издатель |
| `dc:contributor` | `http://purl.org/dc/elements/1.1/contributor` | Участник |
| `dc:date` | `http://purl.org/dc/elements/1.1/date` | Дата |
| `dc:type` | `http://purl.org/dc/elements/1.1/type` | Тип |
| `dc:format` | `http://purl.org/dc/elements/1.1/format` | Формат |
| `dc:identifier` | `http://purl.org/dc/elements/1.1/identifier` | Идентификатор |
| `dc:source` | `http://purl.org/dc/elements/1.1/source` | Источник |
| `dc:language` | `http://purl.org/dc/elements/1.1/language` | Язык |
| `dc:relation` | `http://purl.org/dc/elements/1.1/relation` | Связь |
| `dc:coverage` | `http://purl.org/dc/elements/1.1/coverage` | Охват |
| `dc:rights` | `http://purl.org/dc/elements/1.1/rights` | Права |

---

## 8. Пространства имён XML (XML Namespaces)

**Источник:** W3C Recommendation, 8 December 2009  
**URL:** https://www.w3.org/TR/xml-names/

### 8.1 Объявление пространств имён в SVG

```xml
<svg
  xmlns="http://www.w3.org/2000/svg"
  xmlns:xlink="http://www.w3.org/1999/xlink"
  xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
  xmlns:rdfs="http://www.w3.org/2000/01/rdf-schema#"
  xmlns:owl="http://www.w3.org/2002/07/owl#"
  xmlns:dc="http://purl.org/dc/elements/1.1/"
  xmlns:dcterms="http://purl.org/dc/terms/"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema#"
  xmlns:vad="http://example.org/vad#"
  xmlns:foaf="http://xmlns.com/foaf/0.1/">
```

---

## 9. SPARQL 1.1

**Источник:** W3C Recommendation, 21 March 2013  
**URL:** https://www.w3.org/TR/sparql11-query/

### 9.1 Применение к данным из SVG

После парсинга RDF из SVG в JS-объект (через `rdflib.js` или `N3.js`) можно выполнять SPARQL-запросы:

```sparql
PREFIX vad: <http://example.org/vad#>
PREFIX dc: <http://purl.org/dc/elements/1.1/>

SELECT ?process ?title ?performer
WHERE {
  ?process a vad:Process ;
           dc:title ?title ;
           vad:performer ?performer .
}
ORDER BY ?title
```

### 9.2 SPARQL в браузере

- Библиотека `rdflib.js` поддерживает подмножество SPARQL в браузере.
- Библиотека `sparqljs` позволяет парсить SPARQL-запросы.
- Для полноценного SPARQL рекомендуется использовать SPARQL endpoint на сервере.

---

## 10. Схема итоговой структуры SVG-файла с RDF

```xml
<?xml version="1.0" encoding="UTF-8"?>
<svg xmlns="http://www.w3.org/2000/svg"
     xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
     xmlns:dc="http://purl.org/dc/elements/1.1/"
     xmlns:vad="http://example.org/vad#"
     width="800" height="600"
     id="diagram1">

  <!-- 1. Метаданные уровня документа в RDF/XML -->
  <metadata id="doc-metadata">
    <rdf:RDF>
      <rdf:Description rdf:about="#diagram1">
        <rdf:type rdf:resource="http://example.org/vad#Diagram"/>
        <dc:title>VAD: Управление заказами</dc:title>
        <dc:creator>Аналитик</dc:creator>
        <dc:date>2024-01-01</dc:date>
      </rdf:Description>
    </rdf:RDF>
  </metadata>

  <!-- 2. Элемент с inline RDF через data-атрибут -->
  <g id="process1"
     data-rdf-type="vad:Process"
     data-rdf-title="Приём заказа"
     data-rdf-performer="#role1">
    <rect x="50" y="50" width="120" height="60"/>
    <!-- 3. Метаданные уровня элемента -->
    <metadata>
      <rdf:RDF>
        <rdf:Description rdf:about="#process1">
          <rdf:type rdf:resource="http://example.org/vad#Process"/>
          <dc:title>Приём заказа</dc:title>
          <vad:performer rdf:resource="#role1"/>
        </rdf:Description>
      </rdf:RDF>
    </metadata>
  </g>

</svg>
```
