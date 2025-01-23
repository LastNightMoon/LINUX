from sqlalchemy import create_engine, text

# Подключение к базе данных
engine = create_engine("postgresql://user:password@localhost/dbname")  # Или другой драйвер

# Шаг 1. Преобразование строк в даты
with engine.connect() as conn:
    # Если данные находятся в формате 'DD/MM/YYYY'
    conn.execute(
        text("""
        UPDATE your_table
        SET old_text_column = TO_DATE(old_text_column, 'DD/MM/YYYY')
        WHERE old_text_column IS NOT NULL
        """)
    )

# Шаг 2. Изменение типа столбца
with engine.connect() as conn:
    conn.execute(
        text("""
        ALTER TABLE your_table 
        ALTER COLUMN old_text_column TYPE DATE 
        USING old_text_column::DATE
        """)
    )
