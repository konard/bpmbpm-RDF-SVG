# Онтология VAD (Value Added Chain Diagram) для RDF-блоков

## 1. Введение

Данный документ описывает онтологию для диаграмм VAD (Value Added Chain diagram — диаграмма цепочки добавленной стоимости) в формате ARIS, используемую для создания RDF-блоков в SVG-файлах. Онтология основана на методологии ARIS (Architecture of Integrated Information Systems) и стандартах Linked Data.

**Пространство имён онтологии:**  
`http://example.org/vad#` (префикс: `vad:`)

---

## 2. Используемые внешние онтологии

```turtle
@prefix rdf:   <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs:  <http://www.w3.org/2000/01/rdf-schema#> .
@prefix owl:   <http://www.w3.org/2002/07/owl#> .
@prefix xsd:   <http://www.w3.org/2001/XMLSchema#> .
@prefix dc:    <http://purl.org/dc/elements/1.1/> .
@prefix dcterms: <http://purl.org/dc/terms/> .
@prefix skos:  <http://www.w3.org/2004/02/skos/core#> .
@prefix vad:   <http://example.org/vad#> .
```

---

## 3. Классы онтологии

### 3.1 Диаграмма (Diagram)

**URI:** `vad:Diagram`  
**Описание:** Диаграмма VAD в целом. Верхний уровень иерархии.

```turtle
vad:Diagram a owl:Class ;
    rdfs:label "Диаграмма VAD"@ru ;
    rdfs:label "VAD Diagram"@en ;
    rdfs:comment "Диаграмма цепочки добавленной стоимости (VAD) в методологии ARIS"@ru ;
    skos:definition "Value Added Chain Diagram — графическое представление цепочки добавленной стоимости бизнес-процессов"@ru .
```

**Свойства диаграммы:**

| Свойство | Предикат | Тип | Описание |
|---|---|---|---|
| Название | `dc:title` | xsd:string | Наименование диаграммы |
| Автор | `dc:creator` | xsd:string | Создатель диаграммы |
| Дата создания | `dc:date` | xsd:date | Дата создания |
| Описание | `dc:description` | xsd:string | Описание содержания |
| Версия | `vad:version` | xsd:string | Версия документа |
| Организация | `vad:organization` | xsd:string | Организация-владелец |
| Область применения | `vad:scope` | xsd:string | Область применения диаграммы |
| Статус | `vad:status` | xsd:string | Статус (черновик/утверждён/архив) |
| Язык | `dc:language` | xsd:string | Язык диаграммы |

---

### 3.2 Процесс (Process)

**URI:** `vad:Process`  
**Описание:** Элемент VAD, представляющий функцию или процесс цепочки добавленной стоимости. Соответствует типу объекта «Функция» (Function) в ARIS.

```turtle
vad:Process a owl:Class ;
    rdfs:label "Процесс"@ru ;
    rdfs:label "Process"@en ;
    rdfs:comment "Функция или процесс в цепочке добавленной стоимости"@ru ;
    skos:related <https://www.ariscommunity.com/aris-method> .
```

**Свойства процесса:**

| Свойство | Предикат | Тип | Описание |
|---|---|---|---|
| Название | `dc:title` | xsd:string | Наименование процесса |
| Описание | `dc:description` | xsd:string | Подробное описание |
| Исполнитель | `vad:performer` | vad:Role | Роль/исполнитель процесса |
| Входной документ | `vad:input` | vad:Document | Входной документ или ресурс |
| Выходной документ | `vad:output` | vad:Document | Выходной документ или ресурс |
| Используемая система | `vad:usedSystem` | vad:ITSystem | Используемая ИТ-система |
| Длительность | `vad:duration` | xsd:integer | Длительность в минутах |
| Единица длительности | `vad:durationUnit` | xsd:string | Единица измерения (мин/ч/д) |
| Приоритет | `vad:priority` | xsd:string | Приоритет выполнения |
| Стоимость | `vad:cost` | xsd:decimal | Стоимость выполнения |
| Валюта стоимости | `vad:costCurrency` | xsd:string | Валюта (RUB, USD, EUR) |
| Тип процесса | `vad:processType` | xsd:string | Тип (основной/вспомогательный/управление) |
| Подпроцесс | `vad:subProcess` | vad:Process | Декомпозиция на подпроцессы |
| Родительский процесс | `vad:parentProcess` | vad:Process | Ссылка на родительский процесс |
| Уровень зрелости | `vad:maturityLevel` | xsd:integer | Уровень зрелости (1-5) по CMMI |
| Риск | `vad:risk` | xsd:string | Описание рисков |
| Нормативный акт | `vad:regulation` | xsd:string | Ссылка на регламент |
| Автоматизирован | `vad:isAutomated` | xsd:boolean | Признак автоматизации |

---

### 3.3 Роль (Role)

**URI:** `vad:Role`  
**Описание:** Роль или должность, выполняющая функцию в процессе. Соответствует типу «Позиция» (Position) или «Роль» (Role) в ARIS.

```turtle
vad:Role a owl:Class ;
    rdfs:label "Роль"@ru ;
    rdfs:label "Role"@en ;
    rdfs:comment "Роль или организационная единица, выполняющая процесс"@ru .
```

**Свойства роли:**

| Свойство | Предикат | Тип | Описание |
|---|---|---|---|
| Название | `dc:title` | xsd:string | Наименование роли/должности |
| Описание | `dc:description` | xsd:string | Описание обязанностей |
| Подразделение | `vad:department` | xsd:string | Организационное подразделение |
| Тип | `vad:roleType` | xsd:string | Тип (роль/должность/подразделение) |
| Уровень ответственности | `vad:responsibilityLevel` | xsd:string | (R/A/C/I по RACI) |
| Компетенции | `vad:competency` | xsd:string | Требуемые компетенции |
| Численность | `vad:headcount` | xsd:integer | Количество сотрудников |

---

### 3.4 Документ (Document)

**URI:** `vad:Document`  
**Описание:** Документ или информационный объект, используемый как вход или выход процесса. Соответствует типу «Кластер данных» (Data Cluster) или «Документ» (Document) в ARIS.

```turtle
vad:Document a owl:Class ;
    rdfs:label "Документ"@ru ;
    rdfs:label "Document"@en ;
    rdfs:comment "Информационный объект, входной или выходной для процесса"@ru .
```

**Свойства документа:**

| Свойство | Предикат | Тип | Описание |
|---|---|---|---|
| Название | `dc:title` | xsd:string | Наименование документа |
| Тип документа | `vad:documentType` | xsd:string | Тип (регламент/форма/отчёт) |
| Формат | `dc:format` | xsd:string | Формат файла (PDF, DOCX, XML) |
| Ответственный | `vad:responsible` | vad:Role | Ответственный за документ |
| Срок хранения | `vad:retentionPeriod` | xsd:string | Срок хранения |
| Реквизиты | `vad:requisites` | xsd:string | Реквизиты документа |
| Ссылка | `vad:link` | xsd:anyURI | Ссылка на шаблон/образец |

---

### 3.5 ИТ-система (ITSystem)

**URI:** `vad:ITSystem`  
**Описание:** Информационная система, используемая в процессе. Соответствует типу «Прикладная система» (Application System) в ARIS.

```turtle
vad:ITSystem a owl:Class ;
    rdfs:label "ИТ-система"@ru ;
    rdfs:label "IT System"@en ;
    rdfs:comment "Программная система, используемая в процессе"@ru .
```

**Свойства ИТ-системы:**

| Свойство | Предикат | Тип | Описание |
|---|---|---|---|
| Название | `dc:title` | xsd:string | Наименование системы |
| Производитель | `vad:vendor` | xsd:string | Производитель ПО |
| Версия | `vad:version` | xsd:string | Версия системы |
| Тип | `vad:systemType` | xsd:string | Тип (ERP/CRM/ECM/BI) |
| Статус | `vad:systemStatus` | xsd:string | Статус (эксплуатация/план/архив) |

---

### 3.6 Событие (Event)

**URI:** `vad:Event`  
**Описание:** Событие, инициирующее или завершающее процесс. Соответствует типу «Событие» (Event) в ARIS EPC.

```turtle
vad:Event a owl:Class ;
    rdfs:label "Событие"@ru ;
    rdfs:label "Event"@en .
```

**Свойства события:**

| Свойство | Предикат | Тип | Описание |
|---|---|---|---|
| Название | `dc:title` | xsd:string | Наименование события |
| Тип события | `vad:eventType` | xsd:string | (начало/промежуточное/завершение) |
| Триггер | `vad:trigger` | xsd:string | Условие возникновения |

---

### 3.7 Связь (Connection)

**URI:** `vad:Connection`  
**Описание:** Связь между элементами VAD-диаграммы.

```turtle
vad:Connection a owl:Class ;
    rdfs:label "Связь"@ru ;
    rdfs:label "Connection"@en .
```

**Свойства связи:**

| Свойство | Предикат | Тип | Описание |
|---|---|---|---|
| Тип связи | `vad:connectionType` | xsd:string | (предшествование/параллель/выбор) |
| Источник | `vad:source` | owl:Thing | URI исходного элемента |
| Цель | `vad:target` | owl:Thing | URI целевого элемента |

---

## 4. Свойства (предикаты) онтологии

### 4.1 Объектные свойства (Object Properties)

```turtle
# Исполнитель процесса
vad:performer a owl:ObjectProperty ;
    rdfs:label "исполнитель"@ru ;
    rdfs:domain vad:Process ;
    rdfs:range vad:Role ;
    rdfs:comment "Роль, ответственная за выполнение процесса"@ru .

# Входной объект
vad:input a owl:ObjectProperty ;
    rdfs:label "вход"@ru ;
    rdfs:domain vad:Process ;
    rdfs:range vad:Document ;
    rdfs:comment "Документ или ресурс, необходимый для выполнения процесса"@ru .

# Выходной объект
vad:output a owl:ObjectProperty ;
    rdfs:label "выход"@ru ;
    rdfs:domain vad:Process ;
    rdfs:range vad:Document ;
    rdfs:comment "Документ или результат, производимый процессом"@ru .

# Используемая ИТ-система
vad:usedSystem a owl:ObjectProperty ;
    rdfs:label "использует систему"@ru ;
    rdfs:domain vad:Process ;
    rdfs:range vad:ITSystem .

# Подпроцесс
vad:subProcess a owl:ObjectProperty ;
    rdfs:label "подпроцесс"@ru ;
    rdfs:domain vad:Process ;
    rdfs:range vad:Process ;
    owl:inverseOf vad:parentProcess .

# Родительский процесс
vad:parentProcess a owl:ObjectProperty ;
    rdfs:label "родительский процесс"@ru ;
    rdfs:domain vad:Process ;
    rdfs:range vad:Process .

# Ответственный за документ
vad:responsible a owl:ObjectProperty ;
    rdfs:label "ответственный"@ru ;
    rdfs:domain vad:Document ;
    rdfs:range vad:Role .

# Источник связи
vad:source a owl:ObjectProperty ;
    rdfs:label "источник"@ru ;
    rdfs:domain vad:Connection ;
    rdfs:range owl:Thing .

# Цель связи
vad:target a owl:ObjectProperty ;
    rdfs:label "цель"@ru ;
    rdfs:domain vad:Connection ;
    rdfs:range owl:Thing .
```

### 4.2 Свойства данных (Data Properties)

```turtle
# Версия
vad:version a owl:DatatypeProperty ;
    rdfs:label "версия"@ru ;
    rdfs:range xsd:string .

# Организация
vad:organization a owl:DatatypeProperty ;
    rdfs:label "организация"@ru ;
    rdfs:range xsd:string .

# Статус
vad:status a owl:DatatypeProperty ;
    rdfs:label "статус"@ru ;
    rdfs:range xsd:string .

# Подразделение
vad:department a owl:DatatypeProperty ;
    rdfs:label "подразделение"@ru ;
    rdfs:range xsd:string .

# Длительность процесса (в минутах)
vad:duration a owl:DatatypeProperty ;
    rdfs:label "длительность"@ru ;
    rdfs:domain vad:Process ;
    rdfs:range xsd:integer .

# Единица длительности
vad:durationUnit a owl:DatatypeProperty ;
    rdfs:label "единица длительности"@ru ;
    rdfs:range xsd:string .

# Стоимость
vad:cost a owl:DatatypeProperty ;
    rdfs:label "стоимость"@ru ;
    rdfs:domain vad:Process ;
    rdfs:range xsd:decimal .

# Валюта
vad:costCurrency a owl:DatatypeProperty ;
    rdfs:label "валюта"@ru ;
    rdfs:range xsd:string .

# Тип процесса
vad:processType a owl:DatatypeProperty ;
    rdfs:label "тип процесса"@ru ;
    rdfs:range xsd:string .

# Уровень зрелости
vad:maturityLevel a owl:DatatypeProperty ;
    rdfs:label "уровень зрелости"@ru ;
    rdfs:range xsd:integer .

# Признак автоматизации
vad:isAutomated a owl:DatatypeProperty ;
    rdfs:label "автоматизирован"@ru ;
    rdfs:range xsd:boolean .

# Тип роли
vad:roleType a owl:DatatypeProperty ;
    rdfs:label "тип роли"@ru ;
    rdfs:range xsd:string .

# Уровень ответственности по RACI
vad:responsibilityLevel a owl:DatatypeProperty ;
    rdfs:label "уровень ответственности (RACI)"@ru ;
    rdfs:range xsd:string .

# Тип документа
vad:documentType a owl:DatatypeProperty ;
    rdfs:label "тип документа"@ru ;
    rdfs:range xsd:string .

# Тип события
vad:eventType a owl:DatatypeProperty ;
    rdfs:label "тип события"@ru ;
    rdfs:range xsd:string .

# Тип связи
vad:connectionType a owl:DatatypeProperty ;
    rdfs:label "тип связи"@ru ;
    rdfs:range xsd:string .

# Ссылка на ресурс
vad:link a owl:DatatypeProperty ;
    rdfs:label "ссылка"@ru ;
    rdfs:range xsd:anyURI .
```

---

## 5. Полный пример RDF-блока в формате Turtle

```turtle
@base <http://example.org/> .
@prefix rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix xsd:  <http://www.w3.org/2001/XMLSchema#> .
@prefix dc:   <http://purl.org/dc/elements/1.1/> .
@prefix vad:  <http://example.org/vad#> .

# --- Диаграмма ---
<diagram/order-management> a vad:Diagram ;
    dc:title "VAD: Управление заказами"@ru ;
    dc:creator "Иванов И.И." ;
    dc:date "2024-01-15"^^xsd:date ;
    dc:description "Диаграмма цепочки добавленной стоимости процесса управления заказами клиентов"@ru ;
    vad:version "1.2" ;
    vad:organization "ООО Пример" ;
    vad:status "Утверждён" ;
    dc:language "ru" .

# --- Роли ---
<role/sales-manager> a vad:Role ;
    dc:title "Менеджер по продажам"@ru ;
    vad:department "Отдел продаж" ;
    vad:roleType "Должность" ;
    vad:headcount "5"^^xsd:integer .

<role/warehouse-worker> a vad:Role ;
    dc:title "Кладовщик"@ru ;
    vad:department "Склад" ;
    vad:roleType "Должность" .

<role/accountant> a vad:Role ;
    dc:title "Бухгалтер"@ru ;
    vad:department "Бухгалтерия" ;
    vad:roleType "Должность" .

# --- Документы ---
<doc/customer-request> a vad:Document ;
    dc:title "Запрос клиента"@ru ;
    vad:documentType "Форма" ;
    dc:format "PDF" .

<doc/order> a vad:Document ;
    dc:title "Заказ"@ru ;
    vad:documentType "Документ" ;
    dc:format "1С:Документ" .

<doc/invoice> a vad:Document ;
    dc:title "Счёт на оплату"@ru ;
    vad:documentType "Документ" ;
    dc:format "PDF" .

<doc/waybill> a vad:Document ;
    dc:title "Товарная накладная"@ru ;
    vad:documentType "Первичный документ" ;
    dc:format "ТОРГ-12" .

# --- ИТ-системы ---
<system/crm> a vad:ITSystem ;
    dc:title "CRM-система"@ru ;
    vad:vendor "Битрикс24" ;
    vad:version "24.0" ;
    vad:systemType "CRM" ;
    vad:systemStatus "Эксплуатация" .

<system/erp> a vad:ITSystem ;
    dc:title "1С:Управление торговлей"@ru ;
    vad:vendor "1С" ;
    vad:version "11.5" ;
    vad:systemType "ERP" ;
    vad:systemStatus "Эксплуатация" .

# --- Процессы ---
<process/receive-order> a vad:Process ;
    dc:title "Приём заказа"@ru ;
    dc:description "Регистрация входящего запроса клиента и создание заказа в системе"@ru ;
    vad:performer <role/sales-manager> ;
    vad:input <doc/customer-request> ;
    vad:output <doc/order> ;
    vad:usedSystem <system/crm> ;
    vad:duration "30"^^xsd:integer ;
    vad:durationUnit "мин" ;
    vad:cost "500"^^xsd:decimal ;
    vad:costCurrency "RUB" ;
    vad:processType "Основной" ;
    vad:maturityLevel "3"^^xsd:integer ;
    vad:isAutomated "true"^^xsd:boolean ;
    vad:risk "Неполные данные от клиента" .

<process/issue-invoice> a vad:Process ;
    dc:title "Выставление счёта"@ru ;
    dc:description "Формирование и отправка счёта на оплату клиенту"@ru ;
    vad:performer <role/accountant> ;
    vad:input <doc/order> ;
    vad:output <doc/invoice> ;
    vad:usedSystem <system/erp> ;
    vad:duration "15"^^xsd:integer ;
    vad:durationUnit "мин" ;
    vad:processType "Основной" ;
    vad:isAutomated "true"^^xsd:boolean .

<process/ship-goods> a vad:Process ;
    dc:title "Отгрузка товара"@ru ;
    dc:description "Подготовка и отгрузка товара клиенту"@ru ;
    vad:performer <role/warehouse-worker> ;
    vad:input <doc/order> ;
    vad:output <doc/waybill> ;
    vad:usedSystem <system/erp> ;
    vad:duration "120"^^xsd:integer ;
    vad:durationUnit "мин" ;
    vad:processType "Основной" ;
    vad:risk "Ошибка при комплектации заказа" .
```

---

## 6. Соответствие с ARIS Method Reference

Предикаты онтологии соответствуют ключевым концепциям ARIS:

| ARIS-концепция | Онтология VAD | Описание |
|---|---|---|
| Function (Функция) | `vad:Process` | Основной элемент VAD |
| Position (Позиция) | `vad:Role` | Исполнитель функции |
| Document (Документ) | `vad:Document` | Входные/выходные данные |
| Application System | `vad:ITSystem` | Поддерживающие ИТ-системы |
| Event (Событие) | `vad:Event` | Триггеры и результаты |
| is performed by | `vad:performer` | Связь функции и исполнителя |
| has input | `vad:input` | Входные данные функции |
| has output | `vad:output` | Выходные данные функции |
| is supported by | `vad:usedSystem` | Поддерживающая система |
| consists of | `vad:subProcess` | Декомпозиция функции |
| has predecessor | (порядок в VAD) | Предшествующая функция |

---

## 7. Соответствие со стандартами

- **OWL 2** — для определения классов и свойств онтологии
- **Dublin Core** — для базовых метаданных (title, creator, date)
- **SKOS** — для словарных определений и связей
- **ARIS Method Reference** — методологическая основа для предикатов

---

## Источники

- ARIS Method Reference: https://www.ariscommunity.com/aris-method
- OWL 2 Web Ontology Language (W3C, 2012): https://www.w3.org/TR/owl2-overview/
- Dublin Core Metadata Initiative: https://www.dublincore.org/
- SKOS Simple Knowledge Organization System: https://www.w3.org/TR/skos-reference/
- CMMI Institute: https://cmmiinstitute.com/
