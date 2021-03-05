# Ответы на вопросы

### 1) Не поняла, как сохранить программу с созданием базы данных в формате sql. Вот перечень возможных форматов. Не вижу нужный. 
Скрипт можно сохранить в простом тесктовом файле, например _sql.txt_ или _sqript.sql_  
  
Структура программы:
- bot.py

```python
from random import randrange
import sqlite3

conn = sqlite3.connect("baza_content.db")
cur = conn.cursor()
sql_script = None
with open('sql.txt',mode='r') as file:
  sql_script = file.read()

try:
  cur.executescript(sql_script)
  conn.commit
except:
  print('Tables already created')

print('\nПривет! Я робот , который составит для тебя персональный контент-план. Пришли мне три списка, описывающих твою деятельность, а остальное я беру на себя. Поехали!\n\n')

# остальная часть кода
```

- sql.txt

```sql
CREATE TABLE IF NOT EXISTS allusers(
        id INT PRIMARY KEY,
        name TEXT,
        sphere TEXT);
CREATE TABLE IF NOT EXISTS allneeds(
        needid INT PRIMARY KEY,
        need TEXT,
        userid INT);
CREATE TABLE IF NOT EXISTS allpossibles(
        possibid INT PRIMARY KEY,
        possible TEXT,
        userid INT);
-- Остальные таьлицы
```
> cur.executescript(sql_script) - запускает скрипт целиком
  
### 2) Не поняла, какую структуру таблиц лучше сделать. Создать разные таблицы для каждого из списков или объединить их все в одну таблицу?
Создать одну таблицу со списками и вторую с записями. Т.е. у Вас будет всего две таблицы для списков (сколько бы списков у Вас ни было).  
Агоритм действий:
- Создать таблицу "lists" 
```sql
CREATE TABLE IF NOT EXISTS lists
...
```
- Добавить поля в таблицу "lists", например list1, list2, list3
```sql
INSERT INTO lists VALUES
...
```
- Создать таблицу записей, например records
```sql
CREATE TABLE IF NOT EXISTS records
...
```
- Добавить записи  
Каждая запись принадлежит к определенному списку. Принадлежность записи к списку можно реализовать через связи таблиц. О них можно почитать [здесь](https://metanit.com/sql/tutorial/1.3.php) или [здесь](http://jtest.ru/bazyi-dannyix/sql-dlya-nachinayushhix-chast-3.html).  Подумайте, какая связь лучше подойдёт для Вашей задачи?
  
  
### 3) Как прикрутить счётчик id?
Можно указать id как ```INTEGER PRIMARY KEY``` или ```INTEGER PRIMARY KEY AUTOINCREMENT```
```sql
CREATE TABLE user
           (id INTEGER PRIMARY KEY,
            first_name varchar(20) NOT NULL,
            last_name varchar(20) NOT NULL)
```
id будет автоматически добавляться 


