
# Читаем данные из системного файла
datetime=$(cat /proc/driver/rtc)

# Извлекаем дату и время
rtc_date=$(echo "$datetime" | grep "^rtc_date" | awk '{print $3}')
rtc_time=$(echo "$datetime" | grep "^rtc_time" | awk '{print $3}')

# Извлекаем компоненты даты
year=$(echo "$rtc_date" | cut -d'-' -f1)
month=$(echo "$rtc_date" | cut -d'-' -f2)
day=$(echo "$rtc_date" | cut -d'-' -f3)

# Определяем день недели (через `bash`)
days=("воскресенье" "понедельник" "вторник" "среда" "четверг" "пятница" "суббота")
day_of_week=$(date -d "$rtc_date" +%u) # Определение дня недели (1 = понедельник)
weekday=${days[$day_of_week]}

# Преобразуем номер месяца в название
months=("января" "февраля" "марта" "апреля" "мая" "июня" "июля" "августа" "сентября" "октября" "ноября" "декабря")
month_name=${months[$((10#$month - 1))]}

# Выводим результат
echo "сегодня $weekday, $day $month_name $year года"
