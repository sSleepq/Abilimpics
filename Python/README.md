# Работа с БД через ЯП Python

### Подготовка:

### Установить Python

Для этого переходим на <a href="https://www.python.org/">официальный сайт</a> и качаем от туда установочный файл  
<img src="src\img\offpy.png">  
<img src="src\img\download.png">

### Создание проекта

Откройте редактор кода, желательно PyCharm

<img src="src\img\pycharm_main.png">

> Создайте в проекте два файла config.py и test.py

Установите пакет для работы с БД

<img src="src\img\pymysql.png">

> Зайдите в терминал и напишите команду

```sh
pip install pymysql
```

### Подключение к БД

Перейдите в файл config.py и напишите следующее

```python
host = "localhost"
user = "root"
password = "1234"
db_name = "test"
```

> - host - имя сервера к которому мы подключаемся  
> - user - пользователь, от которого заходим в БД  
> - password - пароль от пользователя  
> - db_name - база с которой хотим работать

Перейдите в файл test.py  
Для начала подключим библиотеку для работы с БД, а так же наш файл config.py с установленными параметрами

```python
import pymysql
from config import host, user, password, db_name
```

После напишим следующую конструкцию:

```python
try:
    conn = pymysql.connect(
        host=host,
        port=3306,
        user=user,
        password=password,
        database=db_name,
        cursorclass=pymysql.cursors.DictCursor
    )
    print("successfully connected...")
    print("#" * 20)
except Exception as ex:
    print("Error")
    print(ex)
```

В данной части кода проиходит связь нашего файла python с mysql  
переменная `conn` обеспечивает связь с БД по параметрам, котрые мы передаем функции `connect` и библиотеки `pymysql`

Если вы сделали все правильно и параметры подошли, будет выведенно сообщение об успешном подключении:

<img src="src\img\conn_succ.png">

> Иначе программа выдаст ошибку:

<img src="src\img\conn_error.png">

### Вывод информации из таблицы

Добавим следующую запись в блок try:

```python
try:
    with conn.cursor() as cursor:
        create_table_query = "SELECT * FROM `users`"
        cursor.execute(create_table_query)
        rows = cursor.fetchall()
        for row in rows:
            print(row)
        print("#" * 20)
finally:
    conn.close()
```

В данном случае после завершения действий, сессия с нашей базой данной будет закрыта

`create_table_query` - хранит в себе запрос написанный на MYSQL ( В данном случае вывод всех данных таблицы `users` )  
`cursor.execute(create_table_query)` - программа передает запрос в нашу БД  
`rows = cursor.fetchall()` - функция `fetchall()` возращает данные из таблицы в виде списка словарей данных
`for * in *` - цикл for each переберает список и выводит его данные ( В данном случае выводит все строки нашей таблицы, хранящиеся в виде словарей )  

Пример вывода:  

<img src="src\img\select_table.png">

> При необходимости вы можете пробежаться по словарям и вытянуть от туда конкретные данные

### Создание таблицы и другие похожие запросы

Для примера создадим таблицу `usr`  

```python
    with conn.cursor() as cursor:
        create_table_query = "CREATE TABLE `usr`(id int AUTO_INCREMENT," \
                         " name varchar(32), " \
                         " password varchar(32), " \
                         " email varchar(32), PRIMARY KEY (id));"
        cursor.execute(create_table_query)
        print("Table created successfully")
```

> Так же как и в прошлом примере пишим обычный запрос и записываем его в переменную. А дальше при помощи функции `execute()` отправляем запрос в нашу БД. Зайдите в MySQL Workbench и посмотрите результат

Точно так же вы можете и отправлять другие запросы, на добавление, изменения данных в таблице