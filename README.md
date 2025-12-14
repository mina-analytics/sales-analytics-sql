# Анализ продаж и KPI менеджеров в SQL Server
## Описание
Проект для автоматизации расчета KPI менеджеров по продажам: кол-во уникальных клиентов, сумма приходов, средний чек в разрезе услуг, анализ роста чека (cross-sell/up-sell).

## Технологии
- **SQL Server 2022** — хранение данных
- **Python 3.13** —  подготовка данных и автоматизация 
- **pandas** — работа с Excel
- **pyodbc** — подключение к SQL Server
- **Git** — версионный контроль

## Установка
### 1. Установка SQL Server
- Скачай SQL Server (https://www.microsoft.com/sql-server/sql-server-downloads)
- При установке выбери:- При установке выбери:
  - **Database Engine Services**
  - **Windows Authentication Mode**
  - Добавь текущего пользователя в SQL Server administrators

### 2. Установка Python
Установка библиотек (Windows\system32\cmd.exe)
pip install pandas openpyxl pyodbc
pip install pandas openpyxl
### 3. Создание базы данных
Открой SSMS, подключись к серверу и выполни:
CREATE DATABASE AnalyticsDB;
GO
### 4. Создание таблицы
USE AnalyticsDB;
GO

CREATE TABLE SalesData (
[ID клиента] INT,
[Наименование клиента] NVARCHAR(255),
[Дата оплаты] DATE,
[Сумма оплаты] DECIMAL(18,2),
[Продавец, продавший размещение] NVARCHAR(100),
[Услуга лота] NVARCHAR(255),
[Месяц прихода] INT
);
GO
### 5. Загрузка данных
Поместите файл `Prikhody-kvartal.xlsx` в папку проекта и запустите:
python load_data.py

## Использование
### Проверка данных
python check_table.py
### Расчет KPI менеджеров
python calculate_kpi.py
### Расчет роста среднего чека (cross-sell)
python calculate_avg_check_growth.py
### Подключение Excel
1. В Excel: Данные → Из других источников → Из SQL Server
2. Укажите сервер: `DESKTOP-T1EH7UF`
3. Выберите базу: `AnalyticsDB`
4. Импортируйте таблицу `SalesData`


## Структура проекта
sales-analytics-sql/
├── README.md # Этот файл
├── Prikhody-kvartal.xlsx # Исходные данные
├── check_table.py # Проверка существования таблицы
├── load_data.py # Загрузка данных в SQL Server
├── calculate_kpi.py # Расчет KPI менеджеров
├── calculate_avg_check_growth.py # Расчет роста среднего чека
└── dashboard.pbix # Power BI дашборд

## Примеры SQL-запросов

### KPI менеджеров (базовые метрики)
SELECT
[Продавец, продавший размещение] AS manager,
COUNT(DISTINCT [ID клиента]) AS unique_clients,
SUM([Сумма оплаты]) AS revenue,
COUNT(*) AS deals_count,
AVG([Сумма оплаты]) AS avg_check
FROM SalesData
GROUP BY [Продавец, продавший размещение]
ORDER BY revenue DESC;

### Cross-sell анализ (рост среднего чека)
-- Клиенты с несколькими услугами
WITH client_stats AS (
SELECT
[Продавец, продавший размещение] AS manager,
[ID клиента],
COUNT(DISTINCT [Услуга лота]) AS service_count,
MIN([Дата оплаты]) AS first_date,
MAX([Дата оплаты]) AS last_date
FROM SalesData
GROUP BY [Продавец, продавший размещение], [ID клиента]
)
SELECT manager, COUNT(*) AS multi_service_clients
FROM client_stats
WHERE service_count > 1
GROUP BY manager;

## Автор
- GitHub:[@mina-analytics](https://github.com/mina-analytics/sales-analytics-sql.git)
- Email:mina7ru@mail.ru

## Лицензия
MIT License — можно использовать в любых целях.

Файл №1 (check_table.py) — Проверка: есть ли таблица в SQL Server?
Файл №2 (load_data.py) — Загрузчик: берет Excel и заливает данные в SQL Server
Файл №3 (calculate_kpi.py) — Аналитика: считает KPI менеджеров из SQL Server
Файл №4 (README.md) — Инструкция: описание проекта для других людей


Шаг 5. Загрузка файлов в Git
В терминале (в папке sales-analytics-sql):

bash
# Добавить все файлы
git add .

# Сделать коммит
git commit -m "Initial commit: добавлены скрипты анализа продаж"

# Загрузить на GitHub
git push -u origin main
