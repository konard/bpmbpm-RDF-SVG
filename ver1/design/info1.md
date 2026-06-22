# Анализ стандартов размещения RDF-метаданных в SVG-файлах

## Введение

Данный документ содержит обзор существующих стандартов и подходов к размещению (хранению и обработке) RDF-метаданных внутри SVG-файлов с учётом поддержки типовыми браузерами.

---

## 1. SVG и XML-пространства имён

SVG является XML-форматом, что позволяет встраивать произвольные XML-элементы из других пространств имён. Это является ключевой возможностью для интеграции RDF.

- **Стандарт:** SVG 1.1 (W3C, 2011), SVG 2 (W3C, в разработке)
- **Ссылка:** https://www.w3.org/TR/SVG11/, https://www.w3.org/TR/SVG2/
- **Поддержка браузерами:** Все современные браузеры (Chrome, Firefox, Safari, Edge) поддерживают SVG 1.1; SVG 2 поддерживается частично.

---

## 2. RDF/XML в блоке `<metadata>`

### Описание

SVG 1.1 определяет элемент `<metadata>` специально для хранения метаданных. Содержимое элемента не отображается и может содержать произвольный XML, включая RDF/XML.

### Пример

```xml
<svg xmlns="http://www.w3.org/2000/svg"
     xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
     xmlns:dc="http://purl.org/dc/elements/1.1/">
  <metadata>
    <rdf:RDF>
      <rdf:Description rdf:about="">
        <dc:title>Диаграмма VAD</dc:title>
        <dc:creator>Автор</dc:creator>
        <dc:date>2024-01-01</dc:date>
      </rdf:Description>
    </rdf:RDF>
  </metadata>
  <!-- SVG-содержимое -->
</svg>
```

### Поддержка браузерами

- **Chrome/Edge:** Блок `<metadata>` полностью игнорируется при рендеринге, но доступен через DOM API (`document.querySelector('metadata')`).
- **Firefox:** Аналогично Chrome.
- **Safari:** Аналогично.
- **Вывод:** Браузеры хранят содержимое в DOM, но не интерпретируют RDF нативно. Чтение требует JS.

### Стандарты

- SVG 1.1, раздел 5.6: `<metadata>`
- RDF/XML Syntax Specification (W3C, 2004): https://www.w3.org/TR/rdf-syntax-grammar/
- Dublin Core Metadata Initiative (DCMI): https://www.dublincore.org/

---

## 3. RDFa (RDF in Attributes) в SVG

### Описание

RDFa — стандарт W3C для встраивания RDF-данных в HTML/XML атрибуты. SVG-элементы могут использовать атрибуты RDFa (`typeof`, `property`, `resource`, `prefix`).

### Пример

```xml
<svg xmlns="http://www.w3.org/2000/svg"
     prefix="vad: http://example.org/vad# dc: http://purl.org/dc/elements/1.1/">
  <g id="process1"
     typeof="vad:Process"
     property="dc:title"
     content="Приём заказа">
    <rect width="120" height="60"/>
    <text>Приём заказа</text>
  </g>
</svg>
```

### Поддержка браузерами

- Браузеры не обрабатывают RDFa в SVG нативно (в отличие от HTML).
- RDFa-парсеры (например, `rdflib.js`, `jsonld.js`) позволяют читать эти атрибуты через JS.
- **Вывод:** Требует JS-библиотеки для обработки.

### Стандарты

- RDFa Core 1.1 (W3C, 2013): https://www.w3.org/TR/rdfa-core/
- RDFa Lite 1.1 (W3C, 2012): https://www.w3.org/TR/rdfa-lite/
- HTML+RDFa 1.1 (W3C, 2015): https://www.w3.org/TR/html-rdfa/

---

## 4. `data-*` атрибуты HTML5 в SVG

### Описание

SVG-элементы в HTML-документе поддерживают `data-*` атрибуты HTML5. Это не RDF, но широко используется для хранения произвольных метаданных элементов.

### Пример

```xml
<g id="process1"
   data-rdf-type="vad:Process"
   data-vad-name="Приём заказа"
   data-vad-performer="Менеджер">
  <rect width="120" height="60"/>
</g>
```

### Поддержка браузерами

- Полная поддержка во всех современных браузерах.
- Доступны через `element.dataset`.
- **Вывод:** Простейший подход, но не является формальным RDF.

---

## 5. JSON-LD в блоке `<metadata>`

### Описание

JSON-LD (JSON for Linked Data) — стандарт W3C для представления Linked Data в формате JSON. Может быть встроен в SVG через `<metadata>` или через `<script type="application/ld+json">`.

### Пример

```xml
<svg xmlns="http://www.w3.org/2000/svg">
  <metadata>
    <script type="application/ld+json">
    {
      "@context": {
        "vad": "http://example.org/vad#",
        "dc": "http://purl.org/dc/elements/1.1/"
      },
      "@id": "#process1",
      "@type": "vad:Process",
      "dc:title": "Приём заказа",
      "vad:performer": "Менеджер"
    }
    </script>
  </metadata>
</svg>
```

### Поддержка браузерами

- `<script type="application/ld+json">` нативно поддерживается браузерами в HTML (используется для SEO/Schema.org).
- В SVG браузеры не выполняют скрипт автоматически, но содержимое доступно через DOM.
- **Вывод:** Гибкий подход, легко разбирается через JS (`JSON.parse`).

### Стандарты

- JSON-LD 1.1 (W3C, 2020): https://www.w3.org/TR/json-ld11/
- JSON-LD 1.1 Processing Algorithms: https://www.w3.org/TR/json-ld11-api/

---

## 6. Turtle / N-Triples в CDATA-блоке

### Описание

RDF в форматах Turtle или N-Triples может быть встроен в SVG через CDATA-секции внутри `<metadata>` или специальных `<desc>` элементов.

### Пример

```xml
<svg xmlns="http://www.w3.org/2000/svg">
  <metadata>
    <![CDATA[
@prefix vad: <http://example.org/vad#> .
@prefix dc: <http://purl.org/dc/elements/1.1/> .

<#process1> a vad:Process ;
    dc:title "Приём заказа" ;
    vad:performer "Менеджер" .
    ]]>
  </metadata>
</svg>
```

### Поддержка браузерами

- Содержимое доступно через `element.textContent`, но не интерпретируется нативно.
- Требует JS-парсера Turtle (например, `N3.js`).
- **Вывод:** Читаемый формат, но требует дополнительной библиотеки.

---

## 7. TriG в `<metadata>`

### Описание

TriG — расширение Turtle для именованных графов (named graphs). Позволяет хранить несколько RDF-графов в одном файле.

### Пример

```xml
<svg xmlns="http://www.w3.org/2000/svg">
  <metadata id="rdf-trig">
    <![CDATA[
@prefix vad: <http://example.org/vad#> .
@prefix dc: <http://purl.org/dc/elements/1.1/> .

# Граф документа
<http://example.org/diagram1> {
  <http://example.org/diagram1> a vad:Diagram ;
      dc:title "VAD: Управление заказами" .
}

# Граф элемента process1
<http://example.org/diagram1#process1> {
  <http://example.org/diagram1#process1> a vad:Process ;
      dc:title "Приём заказа" ;
      vad:performer "Менеджер по продажам" .
}
    ]]>
  </metadata>
</svg>
```

### Поддержка браузерами

- Нет нативной поддержки; требует JS-парсера (например, `N3.js` поддерживает TriG).
- **Вывод:** Мощный подход для хранения нескольких контекстов (графов) в одном SVG.

### Стандарты

- TriG (W3C, 2014): https://www.w3.org/TR/trig/

---

## 8. Семантические аннотации через `xlink:href` и `rdf:about`

### Описание

Исторически SVG активно использовал `xlink:href` для связей между элементами. Совместно с `rdf:about` это позволяло создавать явные связи между SVG-элементами и RDF-описаниями.

### Поддержка браузерами

- `xlink:href` устарел в SVG 2 (заменён на `href`).
- `rdf:about` требует RDF-парсера.

---

## 9. Сравнительная таблица подходов

| Подход | Стандарт | Браузерная поддержка | RDF-совместимость | Сложность JS-обработки |
|---|---|---|---|---|
| RDF/XML в `<metadata>` | W3C SVG 1.1 + RDF/XML | DOM-доступ | Полная | Высокая (DOMParser) |
| RDFa атрибуты | W3C RDFa 1.1 | Нет нативной | Полная | Средняя (rdflib.js) |
| `data-*` атрибуты | HTML5 | Полная | Нет | Низкая |
| JSON-LD в `<metadata>` | W3C JSON-LD 1.1 | DOM-доступ | Полная | Низкая (JSON.parse) |
| Turtle/N-Triples CDATA | W3C RDF | DOM-доступ | Полная | Средняя (N3.js) |
| TriG CDATA | W3C TriG | DOM-доступ | Полная (named graphs) | Средняя (N3.js) |

---

## 10. Рекомендуемый подход для проекта

Для данного проекта рекомендуется **комбинированный подход**:

1. **Документный уровень:** RDF/XML в корневом `<metadata>` SVG-файла — содержит метаданные всей диаграммы.
2. **Элементный уровень:** JSON-LD в атрибуте `data-rdf` каждого SVG-элемента — содержит RDF-свойства конкретного элемента.
3. **Расширенный вариант:** TriG в `<metadata>` — для хранения именованных графов документа и каждого элемента.

Такой подход обеспечивает:
- Максимальную совместимость с браузерами
- Минимальную зависимость от внешних библиотек
- Полноценное хранение RDF-данных

---

## Источники

- W3C SVG 1.1: https://www.w3.org/TR/SVG11/
- W3C RDF/XML: https://www.w3.org/TR/rdf-syntax-grammar/
- W3C RDFa Core 1.1: https://www.w3.org/TR/rdfa-core/
- W3C JSON-LD 1.1: https://www.w3.org/TR/json-ld11/
- W3C Turtle: https://www.w3.org/TR/turtle/
- W3C TriG: https://www.w3.org/TR/trig/
- Dublin Core: https://www.dublincore.org/specifications/dublin-core/
- ARIS Method Reference: https://www.ariscommunity.com/aris-method
