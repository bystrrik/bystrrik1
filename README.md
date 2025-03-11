# bystrrik1
# Рынок заведений общественного питания Москвы

Инвесторы из фонда «Shut Up and Take My Money» решили попробовать открыть заведение общественного питания в Москве. Еще неизвестно, что это будет за место: кафе, ресторан, пиццерия, паб или бар, — и какими будут расположение, меню и цены.

Необходимо подготовить исследование рынка Москвы, найти интересные особенности и презентовать полученные результаты, которые в будущем помогут в выборе подходящего инвесторам места.

Нам доступен датасет с заведениями общественного питания Москвы, составленный на основе данных сервисов Яндекс Карты и Яндекс Бизнес на лето 2022 года. Информация, размещённая в сервисе Яндекс Бизнес, могла быть добавлена пользователями или найдена в общедоступных источниках. Она носит исключительно справочный характер.

Описание данных. Файл moscow_places.csv содержит следующую информацию:
- name — название заведения;
- address — адрес заведения;
- category — категория заведения, например «кафе», «пиццерия» или «кофейня»;
- hours — информация о днях и часах работы;
- lat — широта географической точки, в которой находится заведение;
- lng — долгота географической точки, в которой находится заведение;
- rating — рейтинг заведения по оценкам пользователей в Яндекс Картах (высшая оценка — 5.0);
- price — категория цен в заведении, например «средние», «ниже среднего», «выше среднего» и так далее;
- avg_bill — строка, которая хранит среднюю стоимость заказа в виде диапазона, например:
  - «Средний счёт: 1000–1500 ₽»;
  - «Цена чашки капучино: 130–220 ₽»;
  - «Цена бокала пива: 400–600 ₽».;
  - и так далее;
- middle_avg_bill — число с оценкой среднего чека, которое указано только для значений из столбца avg_bill, начинающихся с подстроки «Средний счёт»:
  - Если в строке указан ценовой диапазон из двух значений, в столбец войдёт медиана этих двух значений;
  - Если в строке указано одно число — цена без диапазона, то в столбец войдёт это число;
  - Если значения нет или оно не начинается с подстроки «Средний счёт», то в столбец ничего не войдёт;
- middle_coffee_cup — число с оценкой одной чашки капучино, которое указано только для значений из столбца avg_bill, начинающихся с подстроки «Цена одной чашки капучино»:
  - Если в строке указан ценовой диапазон из двух значений, в столбец войдёт медиана этих двух значений;
  - Если в строке указано одно число — цена без диапазона, то в столбец войдёт это число;
  - Если значения нет или оно не начинается с подстроки «Цена одной чашки капучино», то в столбец ничего не войдёт;
- chain — число, выраженное 0 или 1, которое показывает, является ли заведение сетевым (для маленьких сетей могут встречаться ошибки):
  - 0 — заведение не является сетевым;
  - 1 — заведение является сетевым;
- district — административный район, в котором находится заведение, например Центральный административный округ;
- seats — количество посадочных мест.

import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
import plotly.express as px
import json

from plotly import graph_objects as go 
from folium import Map, Choropleth
from folium import Map, Marker
from folium.plugins import MarkerCluster

try:
    data = pd.read_csv('/datasets/moscow_places.csv')
except:
    data = pd.read_csv('https://code.s3.yandex.net/datasets/moscow_places.csv')
    
data.head(10)

display('Количество заведений:', data['name'].nunique())

display(data.info())
display('Кол-во пропусков:')
display(data.isna().sum())
display('Кол-во дубликатов:')
display(data.duplicated().sum())

Мы имеем 5614 уникальных заведений, всего имеем информацию - о 8405. Все столбыцы имеют нужный тип данных, кроме seats. Имеется много пропущенных значений в столбцах: hours; price; avg_bill; middle_avg_bill; middle_coffee_cup; seats, что не позволяет нам их отбросить, поскольку это может исказить результаты, проведем их обработку. Дубликаты не были выялены, однко проверим это еще раз после предобработки данных.
