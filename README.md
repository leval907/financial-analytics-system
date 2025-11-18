# 🤖💰 CashFlowStory AI Agent
## Интеллектуальный финансовый аналитик и эксперт по денежным потокам

[![Python](https://img.shields.io/badge/Python-3.11+-blue.svg)](https://python.org)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.121.2-green.svg)](https://fastapi.tiangolo.com)
[![LangChain](https://img.shields.io/badge/LangChain-1.0.7-orange.svg)](https://langchain.com)
[![ArangoDB](https://img.shields.io/badge/ArangoDB-Graph-red.svg)](https://arangodb.com)
[![DuckDB](https://img.shields.io/badge/DuckDB-1.4.2-yellow.svg)](https://duckdb.org)

---

## 🎯 Описание проекта

**CashFlowStory AI Agent** - интеллектуальная система финансового анализа на основе методологии **Cash Flow Story**, которая помогает бизнесу понять и управлять денежными потоками через анализ 7 ключевых драйверов и 4 финансовых историй.

### Что умеет AI-агент:

🔍 **Анализирует 4 главы финансовых историй:**
1. **Рентабельность** - прибыльность бизнеса
2. **Оборачиваемость** - эффективность использования капитала
3. **Equity** - структура капитала и финансовая устойчивость
4. **Спецпоказатели** - отраслевые метрики

💡 **Работает с 7 драйверами бизнеса:**
1. Цена (средний чек / цена продукта)
2. Объем (количество продаж)
3. COGS (себестоимость)
4. Расходы (операционные)
5. Дебиторка (дни)
6. Кредиторка (дни)
7. Запасы (оборачиваемость в днях)

💬 **Консультирует через чат:**
- Объясняет связи между метриками
- Предлагает улучшения на основе драйверов
- Проводит сценарный анализ
- Отвечает на вопросы по финансам

📊 **Визуализирует данные:**
- Интерактивные дашборды в Streamlit
- Графы связей драйверов
- Тренды и прогнозы
- Экспорт отчетов (PDF, Excel)

---

## 🏗️ Архитектура

### Технологический стек

#### Backend & API
- **FastAPI** - REST API для взаимодействия с UI
- **Pydantic** - валидация данных
- **Uvicorn** - ASGI сервер

#### AI Agent
- **LangChain** - фреймворк для LLM приложений
- **LangGraph** - граф-оркестрация агента
- **OpenAI API** - LLM интерфейс (совместим с Requesty.ai)
- **Gradio** - чат-интерфейс с агентом

#### Базы данных
- **ArangoDB** - мультимодельная БД (документы + граф)
  - Коллекции: `companies`, `financial_chapters`, `drivers`
  - Рёбра: `chapter_to_driver`, `driver_to_driver`, `chapter_to_chapter`
  
- **DuckDB** - аналитическая in-process OLAP БД
  - Агрегации и вычисления
  - Временные таблицы для расчетов
  - Быстрый анализ больших данных

#### Визуализация
- **Streamlit** - дашборды и аналитика
- **Plotly** - интерактивные графики
- **Altair** - декларативная визуализация

#### Обработка данных
- **Pandas** - табличные данные
- **NumPy** - вычисления

---

## 📊 Модель данных (ArangoDB)

### Граф-структура

```
┌─────────────┐
│  Companies  │
└──────┬──────┘
       │
       ├──► ┌──────────────────┐
       │    │ Financial        │
       │    │ Chapters         │
       │    │ (4 истории)      │
       │    └────────┬─────────┘
       │             │
       │             │ chapter_to_driver
       │             ▼
       └──► ┌──────────────────┐
            │    Drivers       │
            │  (7 драйверов)   │
            └────────┬─────────┘
                     │
                     │ driver_to_driver
                     ▼
            ┌──────────────────┐
            │   Scenarios      │
            └──────────────────┘
```

### Примеры документов

**Company:**
```json
{
  "_key": "rebecca_coffee",
  "name": "Rebecca's Coffee",
  "industry": "HoReCa",
  "scale": "средний"
}
```

**Financial Chapter (Рентабельность):**
```json
{
  "_key": "rentability_rebecca_2025",
  "company_id": "rebecca_coffee",
  "chapter": "рентабельность",
  "period": "2025",
  "revenue": 42000,
  "net_profit": 6500,
  "profitability": 0.155,
  "cashflow_margin": 0.18
}
```

**Driver (Цена):**
```json
{
  "_key": "price_rebecca",
  "company_id": "rebecca_coffee",
  "driver": "цена",
  "current_value": 150,
  "scenarios": {
    "optimistic": 165,
    "base": 150,
    "pessimistic": 135
  }
}
```

**Edge (связь):**
```json
{
  "_from": "financial_chapters/rentability_rebecca_2025",
  "_to": "drivers/price_rebecca",
  "relation": "depends_on",
  "weight": 0.6
}
```

---

## 🚀 Быстрый старт

### Требования
- Python 3.11+
- Docker & Docker Compose (для ArangoDB)
- 4GB RAM минимум

### Установка

#### 1. Клонировать репозиторий
```bash
git clone https://github.com/leval907/financial-analytics-system.git
cd financial-analytics-system
```

#### 2. Создать виртуальное окружение
```bash
python3 -m venv .venv
source .venv/bin/activate  # Linux/Mac
# или
.venv\Scripts\activate  # Windows
```

#### 3. Установить зависимости
```bash
pip install --upgrade pip
pip install -r requirements.txt
```

#### 4. Настроить переменные окружения
```bash
cp .env.example .env
# Отредактировать .env - добавить API ключи
```

#### 5. Запустить ArangoDB
```bash
docker-compose up -d arangodb
```

#### 6. Инициализировать базу данных
```bash
python scripts/init_db.py
```

#### 7. Запустить FastAPI backend
```bash
uvicorn app.main:app --reload --port 8001
```

#### 8. Запустить Gradio чат (в другом терминале)
```bash
python ui/gradio_chat.py
```

#### 9. Запустить Streamlit дашборд (опционально)
```bash
streamlit run ui/streamlit_app.py
```

---

## 📁 Структура проекта

```
cashflowstory/
├── app/
│   ├── __init__.py
│   ├── main.py                    # FastAPI entry point
│   ├── core/
│   │   ├── config.py              # Конфигурация из .env
│   │   └── security.py            # Безопасность
│   ├── db/
│   │   ├── arangodb.py            # ArangoDB клиент
│   │   └── duckdb.py              # DuckDB клиент
│   ├── models/
│   │   ├── company.py             # Pydantic модели
│   │   ├── chapter.py
│   │   └── driver.py
│   ├── services/
│   │   ├── financial.py           # Финансовые расчеты
│   │   ├── graph.py               # Граф-запросы ArangoDB
│   │   └── analytics.py           # Аналитика DuckDB
│   ├── agent/
│   │   ├── chains.py              # LangChain цепочки
│   │   ├── tools.py               # Инструменты агента
│   │   ├── prompts.py             # Промпты для LLM
│   │   └── memory.py              # Память агента
│   └── api/
│       └── v1/
│           ├── companies.py       # CRUD компаний
│           ├── chapters.py        # CRUD глав
│           ├── drivers.py         # CRUD драйверов
│           └── chat.py            # Чат endpoint
├── ui/
│   ├── streamlit_app.py           # Дашборд
│   └── gradio_chat.py             # Чат-интерфейс
├── data/
│   ├── knowledge/                 # База знаний для RAG
│   │   └── cashflow_methodology.md
│   └── examples/                  # Примеры данных
│       └── rebecca_coffee.json
├── scripts/
│   ├── init_db.py                 # Инициализация БД
│   └── load_examples.py           # Загрузка примеров
├── tests/
│   ├── unit/
│   └── integration/
├── docker/
│   └── docker-compose.yml         # ArangoDB
├── .env.example
├── .gitignore
├── requirements.txt
├── README.md
└── NEW_PLAN.md                    # Детальный план MVP
```

---

## 💬 Примеры запросов к агенту

### Через чат (Gradio)

**Анализ:**
```
"Проанализируй финансовое состояние Rebecca's Coffee за 2025 год"
```

**Драйверы:**
```
"Какие драйверы больше всего влияют на рентабельность?"
```

**Сценарии:**
```
"Что будет если увеличить цену на 10% и объем упадет на 5%?"
```

**Рекомендации:**
```
"Предложи 3 способа улучшить cashflow margin"
```

### Через API

**Получить все главы компании:**
```bash
curl http://localhost:8001/api/v1/companies/rebecca_coffee/chapters
```

**Создать сценарий:**
```bash
curl -X POST http://localhost:8001/api/v1/scenarios \
  -H "Content-Type: application/json" \
  -d '{
    "company_id": "rebecca_coffee",
    "changes": {
      "price": 1.1,
      "volume": 0.95
    }
  }'
```

---

## 🎓 Методология Cash Flow Story

### 4 главы финансовых историй

#### 1. Рентабельность
- **Ключевые метрики:** Gross Margin, Operating Margin, Net Margin, EBITDA Margin
- **Драйверы:** Цена, Объем, COGS, Расходы
- **Вопрос:** Насколько эффективно бизнес генерирует прибыль?

#### 2. Оборачиваемость
- **Ключевые метрики:** Receivables Days, Inventory Days, Payables Days, Cash Cycle
- **Драйверы:** Дебиторка, Запасы, Кредиторка
- **Вопрос:** Как быстро бизнес конвертирует активы в деньги?

#### 3. Equity (Капитал)
- **Ключевые метрики:** ROE, Debt/Equity, Equity Ratio
- **Драйверы:** Все 7 драйверов через прибыль
- **Вопрос:** Насколько эффективно используется капитал?

#### 4. Спецпоказатели
- **Ключевые метрики:** Unit Economics, CAC, LTV, Churn
- **Драйверы:** Специфичны для отрасли
- **Вопрос:** Какие уникальные факторы влияют на бизнес?

### 7 драйверов (факторная модель)

```
Прибыль = (Цена × Объем) - (COGS × Объем) - Расходы

CashFlow = Прибыль + (Дебиторка + Запасы - Кредиторка)
```

**Power of One:** изменение каждого драйвера на 1% влияет на прибыль по-разному.

---

## 🔧 Конфигурация (.env)

```bash
# LLM
REQUESTY_API_KEY=your_key_here
REQUESTY_BASE_URL=https://router.requesty.ai/v1
LLM_MODEL=smart/task
LLM_TEMPERATURE=0
MAX_TOKENS=4000

# ArangoDB
ARANGO_URL=http://localhost:8529
ARANGO_DB=cashflowstory
ARANGO_USER=root
ARANGO_PASSWORD=your_password

# DuckDB
DUCKDB_PATH=data/cashflow.duckdb

# API
API_HOST=0.0.0.0
API_PORT=8001

# Agent
MAX_ITERATIONS=10
ENABLE_STREAMING=true
```

---

## 🧪 Тестирование

```bash
# Все тесты
pytest

# Unit тесты
pytest tests/unit/

# Integration тесты
pytest tests/integration/

# С покрытием
pytest --cov=app tests/
```

---

## 📈 Roadmap

### ✅ MVP v0.1 (текущая фаза)
- [x] Архитектура проекта
- [x] Технический стек
- [ ] Установка окружения
- [ ] База данных (ArangoDB + DuckDB)
- [ ] Базовый FastAPI
- [ ] Простой AI-агент

### 🔄 v0.2 - Финансовая логика
- [ ] 4 главы: расчеты
- [ ] 7 драйверов: модель
- [ ] Граф-связи в ArangoDB
- [ ] Сценарное моделирование

### 🔄 v0.3 - AI-агент
- [ ] LangChain интеграция
- [ ] RAG база знаний
- [ ] Инструменты агента
- [ ] Gradio чат

### 🔄 v0.4 - Визуализация
- [ ] Streamlit дашборд
- [ ] Графики метрик
- [ ] Граф-визуализация
- [ ] Экспорт отчетов

### 🔄 v1.0 - Production
- [ ] Тестирование
- [ ] Документация
- [ ] Docker полная сборка
- [ ] CI/CD

---

## 👥 Вклад в проект

Проект находится в активной разработке. Если хотите помочь:

1. Fork репозитория
2. Создайте feature ветку (`git checkout -b feature/amazing-feature`)
3. Commit изменения (`git commit -m 'Add amazing feature'`)
4. Push в ветку (`git push origin feature/amazing-feature`)
5. Откройте Pull Request

---

## 📄 Лицензия

MIT License - см. файл [LICENSE](LICENSE)

---

## 📞 Контакты

**Автор:** leval907  
**GitHub:** [@leval907](https://github.com/leval907)  
**Проект:** [financial-analytics-system](https://github.com/leval907/financial-analytics-system)

---

## 🙏 Благодарности

- **Cash Flow Story** методология
- **LangChain** команда за отличный фреймворк
- **ArangoDB** за мультимодельную БД
- **DuckDB** за быстрый аналитический движок

---

**Создано с ❤️ для финансовых аналитиков и предпринимателей**

*Последнее обновление: 18 ноября 2025*
