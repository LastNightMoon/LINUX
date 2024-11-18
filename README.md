awk '
BEGIN {
    # Определение массивов для дней недели и месяцев
    days_of_week["вс"]="воскресенье";
    days_of_week["пн"]="понедельник";
    days_of_week["вт"]="вторник";
    days_of_week["ср"]="среда";
    days_of_week["чт"]="четверг";
    days_of_week["пт"]="пятница";
    days_of_week["сб"]="суббота";

    months["Январь"]="января"; months["Февраль"]="февраля"; months["Март"]="марта";
    months["Апрель"]="апреля"; months["Май"]="мая"; months["Июнь"]="июня";
    months["Июль"]="июля"; months["Август"]="августа"; months["Сентябрь"]="сентября";
    months["Октябрь"]="октября"; months["Ноябрь"]="ноября"; months["Декабрь"]="декабря";
}

NR == 1 {
    # Первая строка содержит месяц и год
    current_month = $1;
    current_month_name = months[current_month];
}

NR == 2 {
    # Вторая строка содержит сокращения дней недели
    for (i = 1; i <= NF; i++) {
        day_positions[i] = $i;
    }
}

NR > 2 {
    # Остальные строки содержат числа календаря
    for (i = 1; i <= NF; i++) {
        if ($i == strftime("%d")) {
            # Находим текущий день
            current_day = $i;
            current_weekday = days_of_week[day_positions[i]];
        }
    }
}

END {
    # Выводим результат
    print "сегодня " current_weekday ", " current_day " " current_month_name;
}
' calendar.txt
