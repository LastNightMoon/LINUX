import os
import subprocess
import mysql.connector
from mysql.connector import Error
from datetime import datetime

# ---------------------------
# Параметры восстановления
# ---------------------------
# Имя базы данных, которую необходимо восстановить
target_db = "cis_erkin"

# Имя резервной копии (без расширения)
backup_name = "CIS_backup"

# Директория, где находится резервная копия
backup_dir = r"E:\BACKUP\CIS_TEST_BKP"  # используйте "r" для сырых строк (Windows путь)

# Формирование полного пути к файлу резервной копии (предполагается, что это SQL-дамп)
full_backup_file = os.path.join(backup_dir, backup_name + ".sql")

# ---------------------------
# Параметры подключения к MySQL
# ---------------------------
mysql_host = "localhost"            # адрес MySQL-сервера
mysql_user = "your_mysql_user"      # имя пользователя MySQL
mysql_password = "your_mysql_password"  # пароль MySQL
mysql_port = 3306

# ---------------------------
# Получение информации о резервной копии
# ---------------------------
try:
    # Используем время модификации файла как дату резервной копии
    backup_timestamp = os.path.getmtime(full_backup_file)
    backup_date = datetime.fromtimestamp(backup_timestamp)
    print(f"Информация о резервной копии: {backup_name} от {backup_date.strftime('%d.%m.%Y %H:%M:%S')}")
except Exception as e:
    print("Ошибка при получении информации о файле резервной копии:", e)
    backup_date = datetime.now()  # Если не удалось, берем текущее время

# ---------------------------
# Шаг 1. Подключение к MySQL, удаление и создание базы данных
# ---------------------------
try:
    connection = mysql.connector.connect(
        host=mysql_host,
        user=mysql_user,
        password=mysql_password,
        port=mysql_port
    )
    if connection.is_connected():
        cursor = connection.cursor()
        # Удаляем базу, если она существует
        cursor.execute(f"DROP DATABASE IF EXISTS `{target_db}`;")
        # Создаем новую базу
        cursor.execute(f"CREATE DATABASE `{target_db}`;")
        connection.commit()
        print(f"База данных [{target_db}] удалена (если существовала) и создана заново.")
except Error as err:
    print(f"Ошибка при работе с MySQL: {err}")
    exit(1)
finally:
    if connection.is_connected():
        cursor.close()
        connection.close()

# ---------------------------
# Шаг 2. Восстановление базы данных из резервной копии
# ---------------------------
# Для восстановления используем вызов клиентской утилиты mysql.
# В данном примере используется shell-подстановка "<", поэтому команда передается через shell.
restore_command = f'mysql -h {mysql_host} -u {mysql_user} -p"{mysql_password}" {target_db} < "{full_backup_file}"'

try:
    print("Запуск восстановления базы данных...")
    subprocess.run(restore_command, shell=True, check=True)
    print("Восстановление базы данных завершено успешно.")
except subprocess.CalledProcessError as e:
    print("Ошибка при восстановлении базы данных:", e)
    exit(1)
