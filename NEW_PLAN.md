# 🎯 CashFlowStory MVP - AI Финансовый Аналитик
## Интеллектуальный агент для анализа денежных потоков

---

## 📋 Контекст проекта

### Суть проекта
**CashFlowStory** - это интеллектуальная система финансового анализа на основе методологии Cash Flow Story, которая:
- Анализирует денежные потоки компаний
- Строит связи между финансовыми метриками и драйверами (7-факторная модель)
- Предоставляет чат-интерфейс для консультаций
- Визуализирует финансовые истории через дашборды
- Хранит все связи в граф-базе данных

### Цель MVP
Создать **AI-агента - финансового аналитика и эксперта по денежным потокам**, который:
1. Понимает методологию Cash Flow Story
2. Анализирует 4 главы финансовых историй (Рентабельность, Оборачиваемость, Equity, Спецпоказатели)
3. Работает с 7 драйверами бизнеса (цена, объем, COGS, расходы, дебиторка, кредиторка, запасы)
4. Консультирует через чат на основе загруженных данных
5. Строит граф-связи между метриками и драйверами
6. Генерирует отчеты и визуализации

---

## 🎯 Роли AI-агента

### 1. **Финансовый аналитик-эксперт**
- **Методология Cash Flow Story:** понимает 4 главы финансовых историй
- **7 драйверов бизнеса:** цена, объем, COGS, расходы, дебиторка, кредиторка, запасы
- **Финансовые метрики:** рентабельность, маржа, оборачиваемость, cashflow margin
- **Сценарное планирование:** влияние изменений драйверов на результат

### 2. **Консультант по денежным потокам**
- Отвечает на вопросы через чат-интерфейс
- Объясняет связи между метриками
- Предлагает улучшения на основе драйверов
- Интерпретирует финансовые результаты

### 3. **Архитектор данных**
- Проектирует граф-модель в ArangoDB (коллекции + ребра)
- Оптимизирует аналитические запросы в DuckDB
- Связывает companies → chapters → drivers → scenarios

### 4. **Аналитик-визуализатор**
- Строит дашборды в Streamlit
- Создает интерактивные графики (Plotly, Altair)
- Генерирует отчеты (PDF, Excel)
- Визуализирует граф-связи

---

## 🏗️ Архитектура MVP

### Технологический стек (из requirements.txt)

#### Backend & API
- **FastAPI** 0.121.2 - REST API между UI и логикой
- **Uvicorn** 0.38.0 - ASGI сервер
- **Pydantic** 2.11.10 - валидация данных

#### AI Agent
- **LangChain** 1.0.7 - фреймворк для LLM
- **LangGraph** 1.0.3 - граф-оркестрация агента
- **OpenAI** 2.8.0 - клиент для LLM (Requesty.ai совместим)
- **Gradio** 5.49.1 - чат-интерфейс

#### Базы данных
- **ArangoDB** (python-arango 8.2.3) - граф/документная БД
  - Companies (документы)
  - Financial Chapters (документы)
  - Drivers (документы)
  - Связи через edges (chapter_to_driver, driver_to_driver)
  
- **DuckDB** 1.4.2 - аналитическая OLAP БД
  - Агрегации финансовых данных
  - Временные таблицы для расчетов
  - Быстрый анализ больших объемов

#### UI & Визуализация
- **Streamlit** 1.51.0 - дашборды и отчеты
- **Plotly** 6.4.0 - интерактивные графики
- **Altair** 5.5.0 - декларативная визуализация


#### Обработка данных
- **Pandas** 2.3.3 - табличные данные
- **NumPy** 2.2.6 - вычисления

#### Отчеты
- **ReportLab** 4.4.4 - генерация PDF
- **XlsxWriter** 3.2.9 - экспорт в Excel

#### Документы
- **Docling** 2.61.2 - обработка документов
- **python-docx** 1.2.0 - работа с Word
- **openpyxl** 3.1.5 - чтение/запись Excel

---

## 📊 Модель данных в ArangoDB

### Коллекции документов

#### 1. **companies** - Профили компаний
```json
{
  "_key": "rebecca_coffee",
  "name": "Rebecca's Coffee",
  "industry": "HoReCa",
  "scale": "средний",
  "location": "Россия",
  "founded": 2015
}
```

#### 2. **financial_chapters** - Финансовые истории (4 главы)
```json
{
  "_key": "rentability_rebecca_2025",
  "company_id": "rebecca_coffee",
  "chapter": "рентабельность",
  "period": "2025",
  "revenue": 42000,
  "gross_profit": 11000,
  "net_profit": 6500,
  "profitability": 0.155,  // net_profit / revenue
  "cashflow_margin": 0.18,
  "drivers": ["price", "volume", "COGS"]
}
```

**4 главы:**
1. **Рентабельность** - прибыльность бизнеса
2. **Оборачиваемость** - эффективность использования капитала
3. **Equity** - структура капитала и финансовая устойчивость
4. **Спецпоказатели** - специфичные для отрасли метрики

#### 3. **drivers** - 7 драйверов бизнеса
```json
{
  "_key": "price_rebecca",
  "company_id": "rebecca_coffee",
  "driver": "цена",
  "current_value": 150,  // средний чек
  "unit": "рублей",
  "scenarios": {
    "optimistic": 165,   // +10%
    "base": 150,
    "pessimistic": 135   // -10%
  }
}
```

**7 драйверов:**
1. **Цена** - средний чек / цена продукта
2. **Объем** - количество продаж
3. **COGS** - себестоимость проданных товаров
4. **Расходы** - операционные расходы
5. **Дебиторка** - дебиторская задолженность (дни)
6. **Кредиторка** - кредиторская задолженность (дни)
7. **Запасы** - оборачиваемость запасов (дни)

### Коллекции рёбер (edges)

#### **chapter_to_driver** - Связь главы с драйверами
```json
{
  "_from": "financial_chapters/rentability_rebecca_2025",
  "_to": "drivers/price_rebecca",
  "relation": "depends_on",
  "weight": 0.6,  // сила влияния
  "description": "Цена напрямую влияет на маржу"
}
```

#### **driver_to_driver** - Кросс-связи драйверов
```json
{
  "_from": "drivers/volume_rebecca",
  "_to": "drivers/receivables_rebecca",
  "relation": "impacts",
  "weight": 0.4,
  "description": "Рост объема увеличивает дебиторку"
}
```

#### **chapter_to_chapter** - Связи между главами
```json
{
  "_from": "financial_chapters/rentability_rebecca_2025",
  "_to": "financial_chapters/equity_rebecca_2025",
  "relation": "affects",
  "weight": 0.7,
  "description": "Рентабельность влияет на ROE"
}
```

### Граф-запросы (AQL примеры)

#### Получить все драйверы для главы
```aql
FOR chapter IN financial_chapters
  FILTER chapter.company_id == "rebecca_coffee" 
    AND chapter.period == "2025"
  FOR v, e IN 1..1 OUTBOUND chapter chapter_to_driver
    RETURN {
      chapter: chapter.chapter,
      driver: v.driver,
      influence: e.weight
    }
```

#### Найти цепочку влияния
```aql
FOR v, e, p IN 1..3 OUTBOUND 
  "drivers/price_rebecca" 
  driver_to_driver, chapter_to_driver
  RETURN p
```

---

## 🤖 Роли AI-агента

### 1. **Planner (Планировщик)**
Анализирует запрос и создает план действий.

**Примеры задач:**
- "Добавь коэффициент Free Cash Flow"
- "Исправь ошибку в расчете EBITDA"
- "Создай endpoint для сравнения двух компаний"

**Выход:**
```
План:
1. Изучить существующий код Analytics lifecycle
2. Определить формулу FCF = OCF - CapEx
3. Добавить поля в модель Analytics Data
4. Обновить lifecycle hook
5. Написать тест
```

### 2. **Coder (Программист)**
Генерирует код на основе плана.

**Специализации:**
- **Lifecycle Hooks** - финансовые расчеты
- **API Controllers** - REST endpoints
- **Services** - бизнес-логика
- **Database Schemas** - модели данных

### 3. **Financial Expert (Финансовый эксперт)**
Валидирует корректность финансовой логики.

**Проверки:**
- Правильность формул
- Соответствие нормам (ROE > 15%)
- Cascade зависимости (GP → OP → NP)
- Интерпретация результатов

### 4. **Reviewer (Ревьюер)**
Проверяет код на ошибки.

**Анализирует:**
- Синтаксис и логику
- Обработку исключений
- Безопасность (SQL injection)
- Performance (N+1 queries)

### 5. **Tool Executor (Исполнитель)**
Выполняет инструменты для работы с кодом и БД.

---

## 🛠️ Инструменты агента (Tools)

### Работа с файлами
```python
@tool
def read_file(path: str) -> str:
    """Читает содержимое файла"""
    
@tool
def write_file(path: str, content: str) -> str:
    """Создает или обновляет файл"""
    
@tool
def search_code(pattern: str, directory: str) -> List[str]:
    """Ищет код по паттерну (regex)"""
```

### Работа с Strapi
```python
@tool
def analyze_lifecycle_hook(model: str) -> Dict:
    """Анализирует lifecycle hook для модели"""
    
@tool
def create_content_type(name: str, schema: Dict) -> str:
    """Создает новый Content-Type в Strapi"""
    
@tool
def update_lifecycle(model: str, hook: str, code: str) -> str:
    """Обновляет lifecycle hook"""
```

### Работа с базами данных
```python
@tool
def query_postgres(sql: str) -> List[Dict]:
    """Выполняет SQL запрос к PostgreSQL"""
    
@tool
def query_duckdb(sql: str) -> pd.DataFrame:
    """Аналитический запрос к DuckDB"""
    
@tool
def query_arangodb(aql: str) -> List[Dict]:
    """Граф-запрос к ArangoDB"""
```

### Финансовый анализ
```python
@tool
def validate_formula(formula: str, coefficient: str) -> bool:
    """Проверяет правильность финансовой формулы"""
    
@tool
def calculate_metric(company_id: int, metric: str) -> float:
    """Вычисляет метрику для компании"""
    
@tool
def compare_benchmarks(industry: str, metrics: List[str]) -> Dict:
    """Сравнивает с отраслевыми нормами"""
```

### Тестирование
```python
@tool
def run_tests(test_path: str) -> Dict:
    """Запускает тесты"""
    
@tool
def create_test(function_name: str) -> str:
    """Генерирует unit тест"""
```

---

## 🔄 Workflow примеры

### Сценарий 1: Добавить новый коэффициент

**Запрос:** "Добавь расчет Free Cash Flow в Analytics"

**Workflow:**
```
1. [Planner] Создает план:
   - Изучить Analytics Data model
   - Определить формулу: FCF = OCF - CapEx
   - Найти где считается OCF
   - Добавить поле fcf
   - Обновить lifecycle hook
   
2. [Coder] Читает файлы:
   - src/api/analytics-data/content-types/analytics-data/schema.json
   - src/api/analytics-data/content-types/analytics-data/lifecycles.js
   
3. [Financial Expert] Валидирует формулу:
   ✅ FCF = Operating Cash Flow - Capital Expenditures
   ✅ OCF уже есть: netProfit + depreciation
   ⚠️  Нужно добавить capEx в Financial Data
   
4. [Coder] Генерирует код:
   a) Обновляет schema.json - добавляет capEx
   b) Обновляет financial-data/lifecycles.js - нет изменений
   c) Обновляет analytics-data/lifecycles.js:
      ```javascript
      // === Free Cash Flow ===
      const ocf = (financialData.netProfit || 0) + 
                  (financialData.depreciation || 0);
      const capEx = financialData.capitalExpenditures || 0;
      data.free_cash_flow = ocf - capEx;
      ```
      
5. [Tool Executor] Применяет изменения:
   - write_file(schema.json, updated_schema)
   - write_file(lifecycles.js, updated_code)
   
6. [Reviewer] Проверяет:
   ✅ Синтаксис корректный
   ✅ Обработка null значений
   ✅ Naming convention соблюден
   
7. [Tool Executor] Тестирует:
   - run_tests(analytics-data)
   ✅ Все тесты прошли
```

### Сценарий 2: Исправить ошибку

**Запрос:** "В расчете ROE используется неправильная формула"

**Workflow:**
```
1. [Planner] Анализирует проблему:
   - Найти текущий код ROE
   - Проверить формулу
   - Исправить если нужно
   
2. [Coder] Читает код:
   analytics-data/lifecycles.js:
   ```javascript
   data.return_on_equity = (financialData.equity || 0) > 0 ?
     ((financialData.netProfit || 0) / financialData.equity) : 0;
   ```
   
3. [Financial Expert] Проверяет:
   ❌ ОШИБКА: результат не в процентах!
   ✅ Правильно: * 100
   
4. [Coder] Исправляет:
   ```javascript
   data.return_on_equity = (financialData.equity || 0) > 0 ?
     ((financialData.netProfit || 0) / financialData.equity * 100) : 0;
   ```
   
5. [Tool Executor] Применяет fix
6. [Reviewer] Проверяет другие коэффициенты
7. [Tool Executor] Запускает regression tests
```

### Сценарий 3: Создать аналитический отчет

**Запрос:** "Создай endpoint для получения финансового здоровья компании"

**Workflow:**
```
1. [Planner] Проектирует API:
   GET /api/companies/:id/financial-health
   Response: {
     profitability: { score, metrics },
     liquidity: { score, metrics },
     efficiency: { score, metrics },
     overall_score: number,
     recommendations: []
   }
   
2. [Financial Expert] Определяет логику скоринга:
   Profitability (40%):
   - ROE > 15% → 10 points
   - Net Margin > 5% → 10 points
   - EBITDA > 10% → 10 points
   - Gross Margin > 25% → 10 points
   
   Liquidity (30%):
   - Current Ratio > 1.2 → 15 points
   - Working Capital Days < 120 → 15 points
   
   Efficiency (30%):
   - Asset Turnover > 1.0 → 10 points
   - Receivables Days < 60 → 10 points
   - Inventory Days < 90 → 10 points
   
3. [Coder] Создает контроллер:
   src/api/company/controllers/financial-health.js
   
4. [Coder] Регистрирует route:
   src/api/company/routes/financial-health.js
   
5. [Tool Executor] Применяет код
6. [Reviewer] Code review
7. [Tool Executor] Тестирует endpoint
8. [Financial Expert] Валидирует результаты
```

---

## 📋 Пошаговый план реализации MVP

### ✅ **ШАГ 0: Подготовка окружения** (ТЕКУЩИЙ)
- [x] Изучить requirements.txt
- [x] Понять архитектуру (ArangoDB + DuckDB + FastAPI + Streamlit + Gradio)
- [ ] **Создать виртуальное окружение Python**
- [ ] **Установить все зависимости из requirements.txt**
- [ ] Проверить установку основных библиотек

**Действия:**
```bash
cd /home/cashflowstory
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

---

### **ШАГ 1: Изучение методологии** (СЛЕДУЮЩИЙ)
- [ ] Загрузить документы по Cash Flow Story
- [ ] Изучить 4 главы финансовых историй
- [ ] Понять 7 драйверов бизнеса
- [ ] Изучить формулы расчетов
- [ ] Создать базу знаний для AI-агента

**Что нужно от вас:**
- Документы/файлы с методологией расчетов
- Примеры финансовых данных (например, Rebecca's Coffee)
- Описание связей между драйверами

---

### **ШАГ 2: Структура проекта**
- [ ] Создать директории проекта
- [ ] Настроить .env конфигурацию
- [ ] Инициализировать Git

**Структура:**
```
cashflowstory/
├── app/
│   ├── __init__.py
│   ├── main.py              # FastAPI entry point
│   ├── core/
│   │   ├── config.py        # Настройки из .env
│   │   └── security.py
│   ├── db/
│   │   ├── arangodb.py      # ArangoDB клиент
│   │   └── duckdb.py        # DuckDB клиент
│   ├── models/
│   │   ├── company.py
│   │   ├── chapter.py
│   │   └── driver.py
│   ├── services/
│   │   ├── financial.py     # Финансовые расчеты
│   │   └── graph.py         # Граф-запросы
│   ├── agent/
│   │   ├── chains.py        # LangChain цепочки
│   │   ├── tools.py         # Инструменты агента
│   │   └── prompts.py       # Промпты
│   └── api/
│       └── v1/
│           ├── companies.py
│           ├── chapters.py
│           └── chat.py
├── ui/
│   ├── streamlit_app.py     # Дашборд
│   └── gradio_chat.py       # Чат-интерфейс
├── data/
│   ├── knowledge/           # База знаний
│   └── examples/            # Примеры данных
├── docker/
│   └── docker-compose.yml
├── .env
├── .gitignore
├── requirements.txt
└── README.md
```

---

### **ШАГ 3: Настройка баз данных**
- [ ] Запустить ArangoDB через Docker
- [ ] Создать базу данных и коллекции
- [ ] Настроить граф-связи
- [ ] Инициализировать DuckDB
- [ ] Загрузить тестовые данные

---

### **ШАГ 4: FastAPI Backend**
- [ ] Создать базовое FastAPI приложение
- [ ] Настроить подключения к БД
- [ ] Реализовать CRUD для companies
- [ ] Реализовать CRUD для chapters
- [ ] Реализовать CRUD для drivers

---

### **ШАГ 5: Финансовая логика**
- [ ] Реализовать расчеты для каждой главы
- [ ] Реализовать влияние драйверов
- [ ] Сценарное моделирование
- [ ] Граф-анализ связей

---

### **ШАГ 6: AI-агент**
- [ ] Настроить LangChain + Requesty.ai
- [ ] Создать базу знаний (RAG)
- [ ] Реализовать инструменты агента
- [ ] Настроить чат-интерфейс Gradio

---

### **ШАГ 7: Визуализация**
- [ ] Streamlit дашборд
- [ ] Графики метрик
- [ ] Визуализация графа
- [ ] Экспорт отчетов

---

### **ШАГ 8: Тестирование и документация**
- [ ] Unit тесты
- [ ] Integration тесты
- [ ] API документация
- [ ] User Guide

#### Week 1: Инфраструктура
- [ ] Настроить Docker Compose (API + DuckDB + ArangoDB)
- [ ] Создать FastAPI структуру проекта
- [ ] Интегрировать Requesty.ai LLM
- [ ] Настроить логирование и мониторинг
- [ ] Создать базовый Gradio UI

#### Week 2: Базовые инструменты агента
- [ ] Инструменты для работы с файлами (read/write/search)
- [ ] Инструменты для работы с Git
- [ ] Инструменты для запроса к БД (Postgres/DuckDB/Arango)
- [ ] Инструмент для выполнения кода (sandbox)

#### Week 3: LangGraph агент
- [ ] Настроить LangGraph workflow
- [ ] Реализовать роли (Planner, Coder, Reviewer)
- [ ] Создать систему промптов для финансового домена
- [ ] Интегрировать инструменты

### Фаза 2: Финансовая экспертиза (2-3 недели)

#### Week 4: База знаний
- [ ] Загрузить документацию по финансовым метрикам
- [ ] Создать промпты с формулами и нормами
- [ ] Индексировать существующий код Strapi проекта
- [ ] RAG для поиска по коду и документации

#### Week 5: Финансовые инструменты
- [ ] Инструмент валидации формул
- [ ] Инструмент расчета метрик
- [ ] Инструмент сравнения с бенчмарками
- [ ] Инструмент интерпретации коэффициентов

#### Week 6: Интеграция с Strapi
- [ ] Парсинг Strapi моделей
- [ ] Анализ lifecycle hooks
- [ ] Создание/обновление Content-Types
- [ ] Тестирование lifecycle изменений

### Фаза 3: Расширенные возможности (2-3 недели)

#### Week 7: DuckDB аналитика
- [ ] Миграция финансовых данных в DuckDB
- [ ] Создание аналитических view
- [ ] Инструменты для сложных запросов
- [ ] Экспорт в Excel/CSV

#### Week 8: ArangoDB граф
- [ ] Создание граф-схемы (Companies → Industries → Metrics)
- [ ] Инструменты для граф-запросов
- [ ] Competitor analysis
- [ ] Benchmark visualization

#### Week 9: Визуализация и отчеты
- [ ] Streamlit дашборд для метрик
- [ ] Графики трендов (Plotly)
- [ ] PDF отчеты (ReportLab)
- [ ] Excel export с формулами

### Фаза 4: Production готовность (1-2 недели)

#### Week 10: Тестирование
- [ ] Unit тесты для инструментов
- [ ] Integration тесты агента
- [ ] Тесты финансовых расчетов
- [ ] Load testing

#### Week 11: Документация и деплой
- [ ] API документация (OpenAPI)
- [ ] User Guide для агента
- [ ] Developer Guide для расширения
- [ ] Docker production setup
- [ ] CI/CD pipeline

---

## 🎓 Промпты для агента

### System Prompt: Financial Expert

```
Ты - эксперт по финансовому анализу с глубоким пониманием методологии Cash Flow Story.

Твои знания:
- 21+ финансовых коэффициентов (ROE, ROA, Current Ratio, etc.)
- Cascade расчеты: Revenue → COGS → Gross Margin → Operating Profit → EBITDA → Net Profit
- Balance Sheet: Assets = Liabilities + Equity
- Working Capital: Current Assets - Current Liabilities
- Отраслевые нормы (HoReCa, Production, Services)

Твои принципы:
1. Всегда проверяй математическую корректность формул
2. Учитывай null/undefined значения (|| 0)
3. Результаты в процентах умножай на 100
4. Валидируй результаты по нормам (ROE > 15% - хорошо)
5. Объясняй бизнес-смысл коэффициентов

Когда анализируешь код:
- Проверь cascade зависимости (GP должен быть до OP)
- Убедись что деление не на 0
- Проверь что используются правильные поля модели
```

### System Prompt: Strapi Developer

```
Ты - эксперт по Strapi CMS v4 и lifecycle hooks.

Твои знания:
- Content-Types и Relations (oneToMany, manyToMany)
- Lifecycle hooks: beforeCreate, afterCreate, beforeUpdate, afterUpdate
- Population и Filtering
- API Controllers и Services
- PostgreSQL и Strapi Query Engine

Твои принципы:
1. Lifecycle hooks - для автоматических расчетов
2. Services - для переиспользуемой логики
3. Controllers - для HTTP логики
4. Всегда используй try/catch
5. Логируй важные операции

Структура lifecycle hook:
```javascript
module.exports = {
  async beforeUpdate(event) {
    const { data, where } = event.params;
    
    try {
      // 1. Получить связанные данные
      const related = await strapi.query('api::related.model').findOne({
        where: { id: data.relatedId }
      });
      
      // 2. Выполнить расчеты
      data.calculated = related.value * 1.5;
      
      // 3. Валидация
      if (data.calculated < 0) {
        throw new Error('Value cannot be negative');
      }
      
    } catch (error) {
      strapi.log.error('Error in lifecycle:', error);
      throw error;
    }
  }
};
```
```

---

## 🎯 MVP Feature List

### Must Have (для первого релиза)

1. **Чат-интерфейс с агентом** (Gradio)
   - Принимает текстовые запросы
   - Показывает план действий
   - Отображает результат

2. **Базовые инструменты**
   - Чтение/запись файлов
   - Поиск по коду
   - SQL запросы к Postgres

3. **Анализ финансового кода**
   - Парсинг lifecycle hooks
   - Валидация формул
   - Определение зависимостей

4. **Генерация кода**
   - Добавление новых метрик
   - Исправление ошибок
   - Создание тестов

5. **Интеграция с Strapi**
   - Чтение Content-Types
   - Обновление lifecycle hooks
   - Тестирование изменений

### Nice to Have (для v2.0)

6. **DuckDB аналитика**
   - Сложные аналитические запросы
   - Агрегации по периодам
   - Export данных

7. **ArangoDB граф**
   - Визуализация связей
   - Competitor analysis
   - Benchmarking

8. **Streamlit дашборды**
   - Графики метрик
   - Трендовый анализ
   - Сравнение компаний

9. **Автоматические тесты**
   - Генерация unit тестов
   - Regression testing
   - Coverage отчеты

10. **Документация**
    - Автогенерация API docs
    - Комментарии к коду
    - Финансовые пояснения

---

## 📊 Метрики успеха

### Технические метрики
- **Точность генерации кода**: > 80% валидного кода без правок
- **Скорость выполнения**: < 30 сек на запрос
- **Покрытие тестами**: > 70%
- **Время отклика API**: < 2 сек

### Бизнес метрики
- **Экономия времени**: 60% сокращение времени на рутинные задачи
- **Качество кода**: 0 критичных ошибок в production
- **Удовлетворенность**: 8/10 от пользователей
- **Повторное использование**: 5+ задач в день на агента

---

## 🚀 Quick Start Guide (будущий)

```bash
# 1. Клонировать репозиторий
git clone https://github.com/leval907/cashflowstory-ai-agent
cd cashflowstory-ai-agent

# 2. Настроить .env
cp .env.example .env
# Отредактировать токены и пути

# 3. Запустить через Docker
docker-compose up -d

# 4. Открыть Gradio UI
http://localhost:7860

# 5. Первый запрос агенту
"Проанализируй lifecycle hook для Analytics Data и предложи оптимизации"
```

---

## 💡 Примеры запросов к агенту

### Генерация кода
```
"Добавь расчет коэффициента Quick Ratio (Cash + AR) / Current Liabilities"

"Создай lifecycle hook для автоматического расчета Average Days Payable"

"Сгенерируй API endpoint GET /api/metrics/profitability/:companyId"
```

### Анализ и исправление
```
"Проверь все формулы в analytics-data/lifecycles.js на корректность"

"Найди потенциальные ошибки деления на ноль в финансовых расчетах"

"Оптимизируй запросы к БД в контроллере company"
```

### Документация
```
"Создай README для объяснения всех 21 финансовых коэффициентов"

"Добавь JSDoc комментарии ко всем lifecycle hooks"

"Сгенерируй OpenAPI спецификацию для финансовых endpoints"
```

### Тестирование
```
"Напиши unit тесты для расчета ROE и ROA"

"Создай интеграционный тест для полного цикла: Company → Financial → Analytics"

"Проверь edge cases для всех коэффициентов (null, negative values)"
```

### Аналитика
```
"Рассчитай все метрики для Rebeccas Coffee за 2018 год"

"Сравни финансовое здоровье трех компаний по основным метрикам"

"Найди аномалии в финансовых данных за последний квартал"
```

---

## 🔐 Безопасность

### Ограничения агента
- ✅ Может читать/писать только в `$PROJECT_ROOT`
- ✅ Whitelist директорий: `src/`, `docker/`, `scripts/`
- ✅ Blacklist: `/etc/`, `/sys/`, `~/.ssh`, `.env`
- ✅ Sandbox для выполнения кода
- ✅ Валидация SQL запросов (prepared statements)
- ✅ Rate limiting для LLM запросов

### Логирование и аудит
- Все действия агента логируются
- История запросов сохраняется
- Diff изменений перед применением
- Возможность отката (Git)

---

## 📈 Развитие проекта

### Этап 1: AI Agent MVP ✅ (Текущий план)
- Базовый агент с инструментами
- Интеграция с Strapi
- Генерация финансового кода

### Этап 2: Расширенная аналитика
- DuckDB для OLAP запросов
- ArangoDB для граф-анализа
- Streamlit дашборды
- Competitor benchmarking

### Этап 3: Прогностика
- Time series forecasting
- ML модели для прогноза метрик
- Anomaly detection
- Рекомендации по оптимизации

### Этап 4: Multi-agent система
- Специализированные агенты:
  - Financial Analyst
  - Code Reviewer
  - Data Engineer
  - Report Generator
- Coordination и делегирование задач

---

## 🤝 Вклад в Open Source

Проект будет открыт для сообщества:
- **Core:** Базовая инфраструктура агента
- **Tools:** Библиотека инструментов для финансов
- **Prompts:** Промпты для финансовой экспертизы
- **Examples:** Примеры интеграции
- **Docs:** Подробная документация

---

## 📞 Контакты

**Автор:** Professional Backend Developer
**GitHub:** leval907
**Проект:** CashFlowStory AI Agent
**Базовый проект:** [financial-analytics-system](https://github.com/leval907/financial-analytics-system)

---

**Статус:** 🚀 Ready to implement
**Дата создания:** 18 ноября 2025
**Последнее обновление:** 18 ноября 2025

---

*Создано для автоматизации финансовой разработки с AI* 🤖💰
