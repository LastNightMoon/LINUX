from sqlalchemy import create_engine, MetaData, Table
from sqlalchemy.orm import sessionmaker

# Подключения к базам данных
engine1 = create_engine("postgresql://user1:password1@host1/db1")  # Первая БД
engine2 = create_engine("postgresql://user2:password2@host2/db2")  # Вторая БД

Session1 = sessionmaker(bind=engine1)
Session2 = sessionmaker(bind=engine2)
session1 = Session1()
session2 = Session2()

# Функция для получения списка таблиц и их полей
def get_table_structure(engine):
    metadata = MetaData()
    metadata.reflect(bind=engine)
    tables = {}
    for table_name, table in metadata.tables.items():
        columns = {col.name: str(col.type) for col in table.columns}
        tables[table_name] = columns
    return tables

# Получаем структуру таблиц из обеих баз
tables1 = get_table_structure(engine1)
tables2 = get_table_structure(engine2)

# Проверяем соответствие таблиц
if tables1.keys() != tables2.keys():
    raise ValueError("Структуры таблиц в базах данных различаются!")

# Перенос данных
for table_name, columns in tables2.items():
    print(f"Обработка таблицы: {table_name}")
    
    # Извлечение данных из второй базы
    table2 = Table(table_name, MetaData(), autoload_with=engine2)
    data = session2.execute(table2.select()).fetchall()
    
    # Удаление данных из первой базы
    table1 = Table(table_name, MetaData(), autoload_with=engine1)
    session1.execute(table1.delete())
    session1.commit()
    
    # Вставка данных в первую базу
    if data:
        session1.execute(table1.insert(), [dict(row) for row in data])
        session1.commit()

print("Данные успешно перенесены!")
