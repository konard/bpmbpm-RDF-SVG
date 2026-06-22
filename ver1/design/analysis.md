# Анализ подхода хранения RDF в SVG с отображением в браузере

## 1. Введение

Данный документ содержит глубокий анализ подхода хранения RDF-данных непосредственно в SVG-файлах с нативной обработкой в браузере. Рассматриваются возможности использования SPARQL, поддержка TriG, альтернативные подходы и их сравнение в контексте бизнес-диаграмм формата VAD (Value Added Chain diagram).

---

## 2. Анализ подхода: хранение RDF внутри SVG

### 2.1 Архитектурная концепция

Подход «RDF внутри SVG» основан на двух ключевых свойствах SVG:

1. **SVG — это XML**, поэтому SVG-файл может содержать XML-элементы других пространств имён (в том числе `rdf:RDF`, `rdf:Description`).
2. **Элемент `<metadata>`** определён в спецификации SVG 1.1 специально для хранения метаданных документа и его элементов.

### 2.2 Уровни хранения RDF

#### Уровень 1: Метаданные всего документа (диаграммы)

```xml
<svg id="diagram1" ...>
  <metadata>
    <rdf:RDF>
      <rdf:Description rdf:about="#diagram1">
        <rdf:type rdf:resource="vad:Diagram"/>
        <dc:title>VAD: Управление заказами</dc:title>
        <dc:date>2024-01-01</dc:date>
      </rdf:Description>
    </rdf:RDF>
  </metadata>
  ...
</svg>
```

#### Уровень 2: Метаданные конкретного элемента

```xml
<g id="process1">
  <metadata>
    <rdf:RDF>
      <rdf:Description rdf:about="#process1">
        <rdf:type rdf:resource="vad:Process"/>
        <dc:title>Приём заказа</dc:title>
        <vad:performer rdf:resource="#role1"/>
      </rdf:Description>
    </rdf:RDF>
  </metadata>
  <rect .../>
</g>
```

### 2.3 Преимущества подхода

1. **Единый файл** — SVG и RDF хранятся вместе, нет проблемы синхронизации.
2. **Стандартность** — использует официальный элемент SVG `<metadata>`.
3. **Версионирование** — один файл = одна версия всех данных.
4. **Портативность** — файл можно отправить по email, хранить в файловой системе.
5. **Открытость** — XML — открытый формат, читается любым текстовым редактором.
6. **Семантика** — RDF обеспечивает формальные, машинопонимаемые описания.
7. **Расширяемость** — можно добавлять новые онтологии без изменения SVG-структуры.

### 2.4 Недостатки и ограничения

1. **Нет нативной обработки RDF в браузерах** — браузеры не выполняют SPARQL запросы напрямую; нужен JS-парсер.
2. **Размер файла** — RDF-блоки увеличивают размер SVG, особенно при большом количестве элементов.
3. **Сложность редактирования** — ручное редактирование XML-RDF затруднено; нужен специализированный редактор.
4. **Отсутствие валидации** — браузеры не проверяют корректность RDF; ошибки не сигнализируются.
5. **Проблемы с URI** — относительные URI (#process1) зависят от base URI документа.
6. **Нет интеграции с тройным хранилищем** — для сложных запросов нужен внешний triplestore.

---

## 3. SPARQL и SVG+RDF в браузере

### 3.1 Возможности

SPARQL можно использовать с данными из SVG, но с ограничениями:

#### 3.1.1 Локальный SPARQL через JS

После загрузки и парсинга RDF из SVG в память (через `rdflib.js` или `N3.js`), можно выполнять SPARQL-запросы в браузере:

```javascript
// Загрузка RDF из SVG
const metadataEl = svgDoc.querySelector('metadata');
const rdfXml = metadataEl.innerHTML;

// Парсинг RDF/XML через rdflib.js
const store = $rdf.graph();
$rdf.parse(rdfXml, store, baseURI, 'application/rdf+xml');

// SPARQL-запрос
const query = `
  PREFIX vad: <http://example.org/vad#>
  PREFIX dc: <http://purl.org/dc/elements/1.1/>
  SELECT ?id ?title WHERE {
    ?id a vad:Process ;
        dc:title ?title .
  }
`;
const results = $rdf.SPARQLToQuery(query, false, store);
```

#### 3.1.2 Ограничения браузерного SPARQL

- Поддерживается подмножество SPARQL 1.0 (не 1.1).
- Сложные запросы (OPTIONAL, UNION, FILTER) могут работать медленно на больших данных.
- Агрегатные функции (COUNT, SUM, AVG) ограничены.
- Именованные графы (FROM NAMED) доступны только при полной поддержке SPARQL 1.1 Dataset.

### 3.2 Серверный SPARQL endpoint

Для полноценного SPARQL рекомендуется:

1. **Apache Jena Fuseki** — бесплатный SPARQL-сервер
2. **Blazegraph** — высокопроизводительный triplestore
3. **GraphDB** — коммерческий triplestore с бесплатной версией
4. **Amazon Neptune** — облачный RDF-сервис

Схема использования:

```
SVG + RDF → (парсинг) → SPARQL endpoint → JS fetch → результаты в браузер
```

### 3.3 Пример SPARQL-запроса для VAD-диаграммы

```sparql
PREFIX vad: <http://example.org/vad#>
PREFIX dc: <http://purl.org/dc/elements/1.1/>

# Все процессы с их исполнителями и входными документами
SELECT ?process ?processTitle ?role ?roleTitle ?doc ?docTitle
WHERE {
  ?process a vad:Process ;
           dc:title ?processTitle ;
           vad:performer ?role ;
           vad:input ?doc .
  ?role dc:title ?roleTitle .
  ?doc dc:title ?docTitle .
}
ORDER BY ?processTitle
```

---

## 4. Поддержка TriG в SVG

### 4.1 Возможность хранения TriG

TriG **можно** хранить в SVG-файле. Для этого TriG-содержимое помещается в CDATA-секцию внутри `<metadata>` с указанием типа содержимого:

```xml
<svg xmlns="http://www.w3.org/2000/svg">
  <metadata id="rdf-data" data-format="trig">
    <![CDATA[
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix vad: <http://example.org/vad#> .
@prefix dc: <http://purl.org/dc/elements/1.1/> .

# Граф всей диаграммы
<http://example.org/graph/diagram1> {
  <http://example.org/diagram1> a vad:Diagram ;
      dc:title "VAD: Управление заказами"@ru ;
      dc:creator "Аналитик" .
}

# Граф элемента process1
<http://example.org/graph/process1> {
  <http://example.org/diagram1#process1> a vad:Process ;
      dc:title "Приём заказа"@ru ;
      vad:performer <http://example.org/diagram1#role1> ;
      vad:input <http://example.org/diagram1#doc_request> .
}
    ]]>
  </metadata>
</svg>
```

### 4.2 Парсинг TriG в браузере

```javascript
// Чтение TriG из SVG
const metaEl = svgDoc.querySelector('[data-format="trig"]');
const trigContent = metaEl.textContent;

// Парсинг через N3.js (поддерживает TriG)
const parser = new N3.Parser({ format: 'TriG' });
const quads = [];
parser.parse(trigContent, (error, quad, prefixes) => {
  if (quad) quads.push(quad);
});

// Фильтрация по именованному графу
const processQuads = quads.filter(q =>
  q.graph.value === 'http://example.org/graph/process1'
);
```

### 4.3 Преимущества TriG для VAD-диаграмм

1. **Изолированные контексты** — каждый элемент диаграммы имеет свой граф.
2. **Провенанс** — можно указать источник и дату каждого блока метаданных.
3. **Версионирование данных** — разные версии данных в разных графах.
4. **SPARQL Dataset** — запросы с `FROM NAMED` для работы с конкретными графами.

---

## 5. Альтернативные подходы

### 5.1 Подход A: Отдельный RDF-файл (sidecar)

Хранение RDF в отдельном файле `.ttl` или `.jsonld` рядом с SVG.

**Пример структуры:**
```
diagram1.svg        ← графика
diagram1.ttl        ← RDF-данные
diagram1.jsonld     ← JSON-LD метаданные
```

**Плюсы:**
- Чистое разделение данных и представления.
- Можно загружать в triplestore независимо.
- SVG-файл остаётся компактным.

**Минусы:**
- Проблема синхронизации (две копии данных).
- Необходимо поддерживать соответствие URI.
- Нельзя передать одним файлом.

### 5.2 Подход B: HTML-страница с inline SVG

SVG встроен в HTML, а RDF-данные хранятся в `<script type="application/ld+json">` в `<head>` HTML.

**Плюсы:**
- Полная поддержка JSON-LD в браузерах.
- Лучшая интеграция с поисковыми системами.
- Легко читать и редактировать JSON.

**Минусы:**
- SVG не является автономным файлом.
- Нет поддержки XML-пространств имён RDF.
- Потеря части семантики RDF/XML.

### 5.3 Подход C: Linked Data Platform (LDP)

RDF-данные хранятся в LDP-контейнере на сервере, SVG ссылается на них.

**Плюсы:**
- Полноценный REST API для RDF-данных.
- Поддержка SPARQL endpoint.
- Версионирование через HTTP.

**Минусы:**
- Требует сервер.
- Сложная инфраструктура.
- Нет работы в offline.

### 5.4 Подход D: GraphQL + RDF schema

Использование GraphQL API поверх RDF-триплстора.

**Плюсы:**
- Удобный API для фронтенда.
- Типизированные запросы.

**Минусы:**
- Теряется семантика RDF.
- Дополнительный слой абстракции.

### 5.5 Подход E: Property Graph (Neo4j, ArangoDB)

Использование граф-базы данных вместо RDF.

**Плюсы:**
- Высокая производительность запросов.
- Удобный Cypher/AQL синтаксис.

**Минусы:**
- Нет стандарта (каждая БД своя схема).
- Нет совместимости с Linked Data.
- Невозможно встроить в SVG.

### 5.6 Сравнительная таблица подходов

| Подход | Автономность | Стандартность | SPARQL | Сложность | Рекомендуется для |
|---|---|---|---|---|---|
| RDF в `<metadata>` SVG | Высокая | W3C | Через JS | Средняя | Проект VAD |
| TriG в `<metadata>` | Высокая | W3C | Через JS+N3 | Средняя | Расширенный проект |
| Отдельный RDF-файл | Низкая | W3C | Полная | Низкая | Серверные решения |
| HTML + JSON-LD | Средняя | W3C | Нет | Низкая | Публикация в вебе |
| LDP | Нет | W3C | Полная | Высокая | Enterprise |
| Property Graph | Нет | Нет | Нет | Высокая | Performance-критичные |

---

## 6. Рекомендованная архитектура для проекта

### 6.1 Структура SVG-файла

```
SVG-файл
├── <metadata id="doc-rdf">          ← RDF/XML всей диаграммы
│   └── <rdf:RDF>
│       └── <rdf:Description rdf:about="#diagram">
│           ├── dc:title
│           ├── dc:creator
│           └── vad:diagram-properties...
├── <g id="process1">                ← SVG-элемент (процесс)
│   ├── <metadata>                   ← RDF/XML элемента
│   │   └── <rdf:RDF>
│   │       └── <rdf:Description rdf:about="#process1">
│   │           ├── vad:performer
│   │           ├── vad:input
│   │           └── vad:output
│   ├── <rect>                       ← Визуальное представление
│   └── <text>                       ← Метка
└── <metadata id="trig-data">        ← TriG (опционально)
    └── <![CDATA[ ...TriG... ]]>
```

### 6.2 JS-архитектура браузерного приложения

```
index.html
├── app.js                           ← Главный модуль
├── rdf-parser.js                    ← Парсинг RDF из SVG DOM
├── svg-viewer.js                    ← Отображение SVG
├── properties-panel.js              ← Панель свойств
├── context-menu.js                  ← Контекстное меню
└── rdf-editor.js                    ← Редактор RDF-свойств
```

### 6.3 Поток данных

```
1. Пользователь открывает SVG-файл
   ↓
2. SVG загружается в DOM-дерево
   ↓
3. JS парсит <metadata> → извлекает RDF/XML
   ↓
4. RDF/XML парсится в JS-объект (триплеты)
   ↓
5. Пользователь кликает элемент → правая кнопка мыши
   ↓
6. Контекстное меню: [SVG-свойства | RDF-свойства | Редактировать RDF]
   ↓
7a. SVG-свойства → читаем атрибуты SVG-элемента из DOM
7b. RDF-свойства → ищем триплеты по URI элемента
7c. Редактировать RDF → форма редактирования → обновление DOM
```

---

## 7. Вывод

Подход «RDF в `<metadata>` SVG» является **оптимальным для данного проекта** по следующим причинам:

1. Соответствует стандартам W3C (SVG 1.1, RDF/XML, Dublin Core).
2. Обеспечивает хранение всех данных в одном файле.
3. Данные доступны через стандартный DOM API браузера.
4. Не требует серверной инфраструктуры.
5. Совместим с будущим переходом к серверному SPARQL endpoint.
6. Поддержка TriG обеспечивает именованные графы для разграничения контекстов.

**Дополнительно рекомендуется:**
- Хранить краткие RDF-свойства в `data-*` атрибутах для быстрого доступа без парсинга XML.
- Использовать JSON-LD для метаданных, требующих частого обновления.
- Предусмотреть экспорт RDF в Turtle/TriG для загрузки во внешний triplestore.
