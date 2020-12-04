
## Исследование надёжности заёмщиков

Заказчик — кредитный отдел банка. Нужно разобраться, влияет ли семейное положение и количество детей клиента на факт погашения кредита в срок. Входные данные от банка — статистика о платёжеспособности клиентов.

Результаты исследования будут учтены при построении модели **кредитного скоринга** — специальной системы, которая оценивает способность потенциального заёмщика вернуть кредит банку.

### Инструкция по выполнению

#### Шаг 1. Изучим общую информацию о данных

#### Шаг 2. Предобработка данных
- определим и заполним пропущенные значения:
- описать, какие пропущенные значения вы обнаружили;
- привести возможные причины появления пропусков в данных;
- объяснить, по какому принципу заполнены пропуски;
- заменить вещественный тип данных на целочисленный:
- пояснить, как выбирали метод для изменения типа данных;
- удалить дубликаты:
- пояснить, как выбирали метод для поиска и удаления дубликатов в данных;
- привести возможные причины появления дубликатов;
- выделить леммы в значениях столбца с целями получения кредита:
- описать, как вы проводили лемматизацию целей кредита;
- категоризировать данные:
- перечислить, какие «словари»  выделили для этого набора данных,  почему.
- В данных могут встречаться артефакты — значения, которые не отражают действительность. Например, отрицательное количество дней трудового стажа. Для реальных данных — это нормально. Нужно описать возможные причины появления таких данных и обработать их.

#### Шаг 3. Вопросы   

- Есть ли зависимость между наличием детей и возвратом кредита в срок?
- Есть ли зависимость между семейным положением и возвратом кредита в срок?
- Есть ли зависимость между уровнем дохода и возвратом кредита в срок?
- Как разные цели кредита влияют на его возврат в срок?
- Ответы сопроводить интерпретацией — пояснить, о чём именно говорит полученный результат.

#### Шаг 4. Написать общий вывод

#### Описание данных

- children — количество детей в семье
- days_employed — общий трудовой стаж в днях
- dob_years — возраст клиента в годах
- education — уровень образования клиента
- education_id — идентификатор уровня образования
- family_status — семейное положение
- family_status_id — идентификатор семейного положения
- gender — пол клиента
- income_type — тип занятости
- debt — имел ли задолженность по возврату кредитов
- total_income — ежемесячный доход
- purpose — цель получения кредита

### Шаг 1. Откройте файл с данными и изучите общую информацию. 


```python
import pandas as pd
from IPython.display import display
from pymystem3 import Mystem
from collections import Counter
import os
#1_р_data.csv
#data = pd.read_csv('/datasets/data.csv')

#print(data.head()) #смотрим голову
#print('_____________________')
#print(data.tail(5)) # смотрим хвостъ
#print(data.info()) # смотрим инфу о элементах
#print(data.columns) # смотрим заголовки столбцов на пробелы
#print(data.isnull().sum()) # смотрим количество пропушенных значений и где они находятся
#print(data.isna().sum()) # тоже смотрим количество пропушенных значений и где они находятся
# посмотреть всю базу целиком
```


```python
os.getcwd()
```




    '/home/user-0-13339652/work'




```python
data = pd.read_csv('/datasets/data.csv')
```

### Вывод

Обнаружены пропуски в столбцах days_employed и total_income, их количества равны.  Возможно люди никогда не работали и на получали доход. Но это вывод предварительный далее оценим эту мысль точнее 
В столбцах days_employed и total_income содержаться данные вещественного типа (float), а должны быть целочисленные тк врядли зарплата указывается до ста тысячных долей рубля и отработанные дни тоже

### Шаг 2. Предобработка данных

### Обработка пропусков

#### Отсортируем значения чтобы посмотреть позиции содержащие пропуски сортируем столбец по убыванию


```python
data.sort_values(by = 'days_employed', ascending = False).tail(15) 

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education</th>
      <th>education_id</th>
      <th>family_status</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>21369</td>
      <td>2</td>
      <td>NaN</td>
      <td>42</td>
      <td>среднее</td>
      <td>1</td>
      <td>в разводе</td>
      <td>3</td>
      <td>M</td>
      <td>компаньон</td>
      <td>0</td>
      <td>NaN</td>
      <td>покупка жилой недвижимости</td>
    </tr>
    <tr>
      <td>21390</td>
      <td>20</td>
      <td>NaN</td>
      <td>53</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>компаньон</td>
      <td>0</td>
      <td>NaN</td>
      <td>покупка жилой недвижимости</td>
    </tr>
    <tr>
      <td>21391</td>
      <td>0</td>
      <td>NaN</td>
      <td>52</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>компаньон</td>
      <td>0</td>
      <td>NaN</td>
      <td>покупка жилья для семьи</td>
    </tr>
    <tr>
      <td>21407</td>
      <td>1</td>
      <td>NaN</td>
      <td>36</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>компаньон</td>
      <td>0</td>
      <td>NaN</td>
      <td>строительство жилой недвижимости</td>
    </tr>
    <tr>
      <td>21414</td>
      <td>0</td>
      <td>NaN</td>
      <td>65</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>NaN</td>
      <td>покупка своего жилья</td>
    </tr>
    <tr>
      <td>21415</td>
      <td>0</td>
      <td>NaN</td>
      <td>54</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>NaN</td>
      <td>операции с жильем</td>
    </tr>
    <tr>
      <td>21423</td>
      <td>0</td>
      <td>NaN</td>
      <td>63</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>NaN</td>
      <td>сделка с автомобилем</td>
    </tr>
    <tr>
      <td>21426</td>
      <td>0</td>
      <td>NaN</td>
      <td>49</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>1</td>
      <td>NaN</td>
      <td>недвижимость</td>
    </tr>
    <tr>
      <td>21432</td>
      <td>1</td>
      <td>NaN</td>
      <td>38</td>
      <td>неоконченное высшее</td>
      <td>2</td>
      <td>Не женат / не замужем</td>
      <td>4</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>NaN</td>
      <td>операции с жильем</td>
    </tr>
    <tr>
      <td>21463</td>
      <td>1</td>
      <td>NaN</td>
      <td>35</td>
      <td>высшее</td>
      <td>0</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>NaN</td>
      <td>на проведение свадьбы</td>
    </tr>
    <tr>
      <td>21489</td>
      <td>2</td>
      <td>NaN</td>
      <td>47</td>
      <td>Среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>компаньон</td>
      <td>0</td>
      <td>NaN</td>
      <td>сделка с автомобилем</td>
    </tr>
    <tr>
      <td>21495</td>
      <td>1</td>
      <td>NaN</td>
      <td>50</td>
      <td>среднее</td>
      <td>1</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>NaN</td>
      <td>свадьба</td>
    </tr>
    <tr>
      <td>21497</td>
      <td>0</td>
      <td>NaN</td>
      <td>48</td>
      <td>ВЫСШЕЕ</td>
      <td>0</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>компаньон</td>
      <td>0</td>
      <td>NaN</td>
      <td>строительство недвижимости</td>
    </tr>
    <tr>
      <td>21502</td>
      <td>1</td>
      <td>NaN</td>
      <td>42</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>NaN</td>
      <td>строительство жилой недвижимости</td>
    </tr>
    <tr>
      <td>21510</td>
      <td>2</td>
      <td>NaN</td>
      <td>28</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>NaN</td>
      <td>приобретение автомобиля</td>
    </tr>
  </tbody>
</table>
</div>



**Обнаружены пропуски в столбцах days_employed и total_income, их количества равны. Возможно люди никогда не работали и на получали доход.Люди имеюшие пропуски в столбцах days_employed- общий трудовой стаж в днях и total_income-ежемесячный доход старше 18 лет,поэтому врядли они нигде не работали и не получали зарплату скорее всего мы имеем дело с ошибкой ввода данных.
Заполним пропуски медианным значением**


#### Также обнаружены отрицательные значения в столбце  days_employed- общий трудовой стаж, скорее всего ошибка внесения , преобразуем их в положительные тк они могут повлиять на медиану.


```python
data['days_employed'] = data['days_employed'].apply(abs) # убираем отрицательные значения
data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education</th>
      <th>education_id</th>
      <th>family_status</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1</td>
      <td>8437.673028</td>
      <td>42</td>
      <td>высшее</td>
      <td>0</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>253875.639453</td>
      <td>покупка жилья</td>
    </tr>
    <tr>
      <td>1</td>
      <td>1</td>
      <td>4024.803754</td>
      <td>36</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>112080.014102</td>
      <td>приобретение автомобиля</td>
    </tr>
    <tr>
      <td>2</td>
      <td>0</td>
      <td>5623.422610</td>
      <td>33</td>
      <td>Среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>145885.952297</td>
      <td>покупка жилья</td>
    </tr>
    <tr>
      <td>3</td>
      <td>3</td>
      <td>4124.747207</td>
      <td>32</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>267628.550329</td>
      <td>дополнительное образование</td>
    </tr>
    <tr>
      <td>4</td>
      <td>0</td>
      <td>340266.072047</td>
      <td>53</td>
      <td>среднее</td>
      <td>1</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>158616.077870</td>
      <td>сыграть свадьбу</td>
    </tr>
    <tr>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>21520</td>
      <td>1</td>
      <td>4529.316663</td>
      <td>43</td>
      <td>среднее</td>
      <td>1</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>F</td>
      <td>компаньон</td>
      <td>0</td>
      <td>224791.862382</td>
      <td>операции с жильем</td>
    </tr>
    <tr>
      <td>21521</td>
      <td>0</td>
      <td>343937.404131</td>
      <td>67</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>155999.806512</td>
      <td>сделка с автомобилем</td>
    </tr>
    <tr>
      <td>21522</td>
      <td>1</td>
      <td>2113.346888</td>
      <td>38</td>
      <td>среднее</td>
      <td>1</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>1</td>
      <td>89672.561153</td>
      <td>недвижимость</td>
    </tr>
    <tr>
      <td>21523</td>
      <td>3</td>
      <td>3112.481705</td>
      <td>38</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>1</td>
      <td>244093.050500</td>
      <td>на покупку своего автомобиля</td>
    </tr>
    <tr>
      <td>21524</td>
      <td>2</td>
      <td>1984.507589</td>
      <td>40</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>82047.418899</td>
      <td>на покупку автомобиля</td>
    </tr>
  </tbody>
</table>
<p>21525 rows × 12 columns</p>
</div>



**Были обнаружены отрицательные значения в количестве детей и так как я считаю что это ошибка внесения данных оператором так же приводим их к положительному значению**


```python
data['children'] = data['children'].apply(abs)
```

**Проверяем максимальное количество детей**


```python
print(data['children'].max())
```

    20


**Вычисляем количество значений где количество детей 20**


```python
# count_child = data.pivot_table(index = ['children'], values = 'debt', aggfunc = ['count'])
# print(count_child)
print (len(data[data['children'] == 20]))
```

    76


**Я думаю это ошибка вноса данных вместо 2 внесено 20 , меням 20 на 2**


```python
data.loc[data['children'] == 20, 'children'] = 2

# проверяем

print (len(data[data['children'] == 20]))
```

    0


**Вычисляем медиану столбца days_employed- общий трудовой стаж и total_income**


```python
print(data['days_employed'].median())
```

    2194.220566878695



```python
print(data['total_income'].median())
```

    145017.93753253992


#### Проверим уровень максимального трудового стажа? сортируем столбец по убыванию


```python
data.sort_values(by = 'days_employed', ascending = False).head(15) 
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education</th>
      <th>education_id</th>
      <th>family_status</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>6954</td>
      <td>0</td>
      <td>401755.400475</td>
      <td>56</td>
      <td>среднее</td>
      <td>1</td>
      <td>вдовец / вдова</td>
      <td>2</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>176278.441171</td>
      <td>ремонт жилью</td>
    </tr>
    <tr>
      <td>10006</td>
      <td>0</td>
      <td>401715.811749</td>
      <td>69</td>
      <td>высшее</td>
      <td>0</td>
      <td>Не женат / не замужем</td>
      <td>4</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>57390.256908</td>
      <td>получение образования</td>
    </tr>
    <tr>
      <td>7664</td>
      <td>1</td>
      <td>401675.093434</td>
      <td>61</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>126214.519212</td>
      <td>операции с жильем</td>
    </tr>
    <tr>
      <td>2156</td>
      <td>0</td>
      <td>401674.466633</td>
      <td>60</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>325395.724541</td>
      <td>автомобили</td>
    </tr>
    <tr>
      <td>7794</td>
      <td>0</td>
      <td>401663.850046</td>
      <td>61</td>
      <td>среднее</td>
      <td>1</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>48286.441362</td>
      <td>свадьба</td>
    </tr>
    <tr>
      <td>4697</td>
      <td>0</td>
      <td>401635.032697</td>
      <td>56</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>48242.322502</td>
      <td>покупка недвижимости</td>
    </tr>
    <tr>
      <td>13420</td>
      <td>0</td>
      <td>401619.633298</td>
      <td>63</td>
      <td>Среднее</td>
      <td>1</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>51449.788325</td>
      <td>сыграть свадьбу</td>
    </tr>
    <tr>
      <td>17823</td>
      <td>0</td>
      <td>401614.475622</td>
      <td>59</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>152769.694536</td>
      <td>покупка жилья для сдачи</td>
    </tr>
    <tr>
      <td>10991</td>
      <td>0</td>
      <td>401591.828457</td>
      <td>56</td>
      <td>среднее</td>
      <td>1</td>
      <td>в разводе</td>
      <td>3</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>39513.517543</td>
      <td>получение дополнительного образования</td>
    </tr>
    <tr>
      <td>8369</td>
      <td>0</td>
      <td>401590.452231</td>
      <td>58</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>175306.312902</td>
      <td>образование</td>
    </tr>
    <tr>
      <td>1184</td>
      <td>0</td>
      <td>401575.196728</td>
      <td>55</td>
      <td>среднее</td>
      <td>1</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>M</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>29749.813177</td>
      <td>на проведение свадьбы</td>
    </tr>
    <tr>
      <td>4949</td>
      <td>0</td>
      <td>401573.905288</td>
      <td>54</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>52927.428675</td>
      <td>покупка коммерческой недвижимости</td>
    </tr>
    <tr>
      <td>15192</td>
      <td>0</td>
      <td>401556.753550</td>
      <td>68</td>
      <td>Высшее</td>
      <td>0</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>231820.611741</td>
      <td>свадьба</td>
    </tr>
    <tr>
      <td>5716</td>
      <td>0</td>
      <td>401524.259153</td>
      <td>57</td>
      <td>среднее</td>
      <td>1</td>
      <td>вдовец / вдова</td>
      <td>2</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>143342.778458</td>
      <td>приобретение автомобиля</td>
    </tr>
    <tr>
      <td>10484</td>
      <td>0</td>
      <td>401517.276388</td>
      <td>68</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>304295.888626</td>
      <td>операции с жильем</td>
    </tr>
  </tbody>
</table>
</div>



**или просто берем максимальные и минимальные элементы в столбце**


```python
print(data['days_employed'].max())
print(data['days_employed'].min())
```

    401755.40047533
    24.14163324048118


**Существование стажа в 1 тысчу лет маловероятно, наличие таких цир связано скорее всего с технической ошибкой , возможно обьеденили 2 базы в одной из которых стаж считался в часах или минутах поэтому всплески на медианные значения
Средняя продолжительность жизни мужчин в 2019 году в России составила 68,5 лет. Для женщин показатель еще выше — 78,5 лет
возьмем максимальное значение для замены на медиану 70 лет стажа это 25550 дней**


```python
data.loc[data['days_employed'] > 25550, 'days_employed'] = data['days_employed'].median()
data.sort_values(by = 'days_employed', ascending = False).head(15)
display(data.head())
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education</th>
      <th>education_id</th>
      <th>family_status</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1</td>
      <td>8437.673028</td>
      <td>42</td>
      <td>высшее</td>
      <td>0</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>253875.639453</td>
      <td>покупка жилья</td>
    </tr>
    <tr>
      <td>1</td>
      <td>1</td>
      <td>4024.803754</td>
      <td>36</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>112080.014102</td>
      <td>приобретение автомобиля</td>
    </tr>
    <tr>
      <td>2</td>
      <td>0</td>
      <td>5623.422610</td>
      <td>33</td>
      <td>Среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>145885.952297</td>
      <td>покупка жилья</td>
    </tr>
    <tr>
      <td>3</td>
      <td>3</td>
      <td>4124.747207</td>
      <td>32</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>267628.550329</td>
      <td>дополнительное образование</td>
    </tr>
    <tr>
      <td>4</td>
      <td>0</td>
      <td>2194.220567</td>
      <td>53</td>
      <td>среднее</td>
      <td>1</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>158616.077870</td>
      <td>сыграть свадьбу</td>
    </tr>
  </tbody>
</table>
</div>


#### Также меняем все пропущенные значения в days_employed- общий трудовой стаж в днях и total_income-ежемесячный доход на медианы этих столбцов


```python
data['days_employed'] = data['days_employed'].fillna(data['days_employed'].median()) # заменяем пропущенные значения в столбце days_employed(отработанные дни)
# на 0
#data['total_income'] = data['total_income'].fillna(data['total_income'].median()) # заменяем пропущенные значения в столбце total_income — ежемесячный доход
# на 0
print(data.isnull().sum()) # проверяем смотрим количество пропушенных значений и где они находятся
# print(data.info()) # еще раз проверяеv
display('Пустые до', data['total_income'].isnull().sum())
```

    children               0
    days_employed          0
    dob_years              0
    education              0
    education_id           0
    family_status          0
    family_status_id       0
    gender                 0
    income_type            0
    debt                   0
    total_income        2174
    purpose                0
    dtype: int64



    'Пустые до'



    2174


<div class="alert alert-info">
<strong>Смотрим категории заемщиков и пропуски</strong> </div>


```python
display(data['income_type'].value_counts())
#data_null_income = data.pivot_table(index = ['income_type'], values = 'total_income', aggfunc = [value_counts, isna])
#data_null_income = data.groupby('income_type').agg({'total_income':'isna'})
#print(data_null_income)
```


    сотрудник          11119
    компаньон           5085
    пенсионер           3856
    госслужащий         1459
    безработный            2
    предприниматель        2
    в декрете              1
    студент                1
    Name: income_type, dtype: int64


<div class="alert alert-info">
    <strong>Смотрим пропуски по типу занятости, может быть они одинаковы для всех и это ошибка , но если нет , чтобы не обрабатывать лишние значения
    Выявленная сума пропусков совпадает с data['total_income'].isnull().sum()</strong> </div>



```python
#print(data.info())
#data['total_income'] = data['total_income'].astype(int)
#print(data.info())
print('пенсионер',data.loc[data['income_type'] == 'пенсионер', 'total_income'].isna().value_counts())
print('сотрудник',data.loc[data['income_type'] == 'сотрудник', 'total_income'].isna().value_counts())
print('компаньон',data.loc[data['income_type'] == 'компаньон', 'total_income'].isna().value_counts())
print('госслужащий',data.loc[data['income_type'] == 'госслужащий', 'total_income'].isna().value_counts())
print('предприниматель',data.loc[data['income_type'] == 'предприниматель', 'total_income'].isna().value_counts())
print('безработный',data.loc[data['income_type'] == 'безработный', 'total_income'].isna().value_counts())
print('в декрете',data.loc[data['income_type'] == 'в декрете', 'total_income'].isna().value_counts())
print('студент',data.loc[data['income_type'] == 'студент', 'total_income'].isna().value_counts())
```

    пенсионер False    3443
    True      413
    Name: total_income, dtype: int64
    сотрудник False    10014
    True      1105
    Name: total_income, dtype: int64
    компаньон False    4577
    True      508
    Name: total_income, dtype: int64
    госслужащий False    1312
    True      147
    Name: total_income, dtype: int64
    предприниматель True     1
    False    1
    Name: total_income, dtype: int64
    безработный False    2
    Name: total_income, dtype: int64
    в декрете False    1
    Name: total_income, dtype: int64
    студент False    1
    Name: total_income, dtype: int64



<div class="alert alert-info">
    <strong>Вычисляем медиану для каждой категории заемщиков. . Меняем пропуски в total_income == ....... на  медиану по категориям</strong> </div>


```python
#display('пенсионер',data.loc[data['income_type'] == 'пенсионер', 'total_income'].median())
#display('сотрудник',data.loc[data['income_type'] == 'сотрудник', 'total_income'].median())
display('Пустые до', data['total_income'].isnull().sum())

for _type in data['income_type'].unique():
    median = data.loc[data['income_type'] == _type, 'total_income'].median()
    print('Медианная зарплата {} {:.2f}'.format(_type, median))
    data.loc[(data['total_income'].isna()) & (data['income_type'] == _type), 'total_income'] = median

display('Пустые после', data['total_income'].isnull().sum())    
```


    'Пустые до'



    2174


    Медианная зарплата сотрудник 142594.40
    Медианная зарплата пенсионер 118514.49
    Медианная зарплата компаньон 172357.95
    Медианная зарплата госслужащий 150447.94
    Медианная зарплата безработный 131339.75
    Медианная зарплата предприниматель 499163.14
    Медианная зарплата студент 98201.63
    Медианная зарплата в декрете 53829.13



    'Пустые после'



    0


#### Еще раз проверяем


```python
#data.sort_values(by = 'days_employed', ascending = False).tail(15)
data.sort_values(by = 'total_income', ascending = False).tail(10) 
display(data.head())
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education</th>
      <th>education_id</th>
      <th>family_status</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1</td>
      <td>8437.673028</td>
      <td>42</td>
      <td>высшее</td>
      <td>0</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>253875.639453</td>
      <td>покупка жилья</td>
    </tr>
    <tr>
      <td>1</td>
      <td>1</td>
      <td>4024.803754</td>
      <td>36</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>112080.014102</td>
      <td>приобретение автомобиля</td>
    </tr>
    <tr>
      <td>2</td>
      <td>0</td>
      <td>5623.422610</td>
      <td>33</td>
      <td>Среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>145885.952297</td>
      <td>покупка жилья</td>
    </tr>
    <tr>
      <td>3</td>
      <td>3</td>
      <td>4124.747207</td>
      <td>32</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>267628.550329</td>
      <td>дополнительное образование</td>
    </tr>
    <tr>
      <td>4</td>
      <td>0</td>
      <td>2194.220567</td>
      <td>53</td>
      <td>среднее</td>
      <td>1</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>158616.077870</td>
      <td>сыграть свадьбу</td>
    </tr>
  </tbody>
</table>
</div>


### Вывод

**Заменяем пропущенные данные столбцов days_employed — общий трудовой стаж в днях и total_income — ежемесячный доход  на медиану столбца, так как это возможно реальные клиенты с реальными показателями, Существование стажа в 1 тысчу лет маловероятно, наличие таких цир связано скорее всего с технической ошибкой , возможно обьеденили 2 базы в одной из которых стаж считался в часах или минутах поэтому так же как и пропуски меняем всплески на медианные значения**


### Замена типа данных

#### Меняем тип данных  в столбцах data['days_employed'] и data['total_income'] с float на int


```python
data['days_employed'] = data['days_employed'].astype(int) # меняем в столбце data['days_employed'] 
#вещественый тип float64 на int64
data['total_income'] = data['total_income'].astype(int) # меняем в столбце data['days_employed']
print(data.info())
#data[data.duplicated(keep = False)].sort_values(by = data.columns.values.tolist())

```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 21525 entries, 0 to 21524
    Data columns (total 12 columns):
    children            21525 non-null int64
    days_employed       21525 non-null int64
    dob_years           21525 non-null int64
    education           21525 non-null object
    education_id        21525 non-null int64
    family_status       21525 non-null object
    family_status_id    21525 non-null int64
    gender              21525 non-null object
    income_type         21525 non-null object
    debt                21525 non-null int64
    total_income        21525 non-null int64
    purpose             21525 non-null object
    dtypes: int64(7), object(5)
    memory usage: 2.0+ MB
    None


#### Еще метод Он превращает значения столбца в числовые типы float64 (вещественное число) или int64 (целое число) в зависимости от исходного значения.
У метода to_numeric() есть параметр errors. 
От значений, принимаемых errors, зависят действия to_numeric при встрече с некорректным значением:
`errors=raise' — дефолтное поведение: при встрече с некорректным значением выдаётся ошибка, операция перевода в числа прерывается  
`errors=coerce` — некорректные значения принудительно заменяются на NaN  
`errors=ignore` — некорректные значения игнорируются, но остаются  
https://praktikum.yandex.ru/trainer/data-analyst/lesson/208f2480-b9b2-4da3-bb77-2269882bdda5/task/11d58dfe-e6c3-46a9-929a-bdd252a53420/


```python
#transactions['amount'] = pd.to_numeric(transactions['amount'], errors='coerce')
```

### Вывод

**В столбцах days_employed и total_income содержаться данные вещественного типа (float), а должны быть целочисленные тк врядли зарплата указывается до ста тысячных долей рубля и отработанные дни тоже
использован метод astype**

### Обработка дубликатов

#### выводим сумму дубликатов


```python
#print(data.duplicated())
# print('________________________________')
print(data.duplicated().sum())
display(data.head())
```

    54



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education</th>
      <th>education_id</th>
      <th>family_status</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1</td>
      <td>8437</td>
      <td>42</td>
      <td>высшее</td>
      <td>0</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>253875</td>
      <td>покупка жилья</td>
    </tr>
    <tr>
      <td>1</td>
      <td>1</td>
      <td>4024</td>
      <td>36</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>112080</td>
      <td>приобретение автомобиля</td>
    </tr>
    <tr>
      <td>2</td>
      <td>0</td>
      <td>5623</td>
      <td>33</td>
      <td>Среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>145885</td>
      <td>покупка жилья</td>
    </tr>
    <tr>
      <td>3</td>
      <td>3</td>
      <td>4124</td>
      <td>32</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>267628</td>
      <td>дополнительное образование</td>
    </tr>
    <tr>
      <td>4</td>
      <td>0</td>
      <td>2194</td>
      <td>53</td>
      <td>среднее</td>
      <td>1</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>158616</td>
      <td>сыграть свадьбу</td>
    </tr>
  </tbody>
</table>
</div>


#### Чтобы посмотреть все дубли можно сделать вот так. keep = False значит "оставь все дубликаты". 
**По умолчанию duplicated оставляет только первые совпадения.**


```python
#print(data[data.duplicted(keep = False)].sort_values(by = data.columns.values.tolist()))
```

<div class="alert alert-info" role="alert">
<strong>Ок проверим тип `object`</strong> </div>



```python
display(data['education'].value_counts())
print('__________________________________')
display(data['family_status'].value_counts())
print('__________________________________')
display(data['income_type'].value_counts())
print('__________________________________')
display(data['purpose'].value_counts())

```


    среднее                13750
    высшее                  4718
    СРЕДНЕЕ                  772
    Среднее                  711
    неоконченное высшее      668
    ВЫСШЕЕ                   274
    Высшее                   268
    начальное                250
    Неоконченное высшее       47
    НЕОКОНЧЕННОЕ ВЫСШЕЕ       29
    НАЧАЛЬНОЕ                 17
    Начальное                 15
    ученая степень             4
    Ученая степень             1
    УЧЕНАЯ СТЕПЕНЬ             1
    Name: education, dtype: int64


    __________________________________



    женат / замужем          12380
    гражданский брак          4177
    Не женат / не замужем     2813
    в разводе                 1195
    вдовец / вдова             960
    Name: family_status, dtype: int64


    __________________________________



    сотрудник          11119
    компаньон           5085
    пенсионер           3856
    госслужащий         1459
    безработный            2
    предприниматель        2
    в декрете              1
    студент                1
    Name: income_type, dtype: int64


    __________________________________



    свадьба                                   797
    на проведение свадьбы                     777
    сыграть свадьбу                           774
    операции с недвижимостью                  676
    покупка коммерческой недвижимости         664
    покупка жилья для сдачи                   653
    операции с жильем                         653
    операции с коммерческой недвижимостью     651
    жилье                                     647
    покупка жилья                             647
    покупка жилья для семьи                   641
    строительство собственной недвижимости    635
    недвижимость                              634
    операции со своей недвижимостью           630
    строительство жилой недвижимости          626
    покупка недвижимости                      624
    покупка своего жилья                      620
    строительство недвижимости                620
    ремонт жилью                              612
    покупка жилой недвижимости                607
    на покупку своего автомобиля              505
    заняться высшим образованием              496
    автомобиль                                495
    сделка с подержанным автомобилем          489
    свой автомобиль                           480
    на покупку подержанного автомобиля        479
    автомобили                                478
    на покупку автомобиля                     472
    приобретение автомобиля                   462
    дополнительное образование                462
    сделка с автомобилем                      455
    высшее образование                        453
    получение дополнительного образования     447
    образование                               447
    получение образования                     443
    профильное образование                    436
    получение высшего образования             426
    заняться образованием                     412
    Name: purpose, dtype: int64


<div class="alert alert-info">
<strong>Да действительно в образовании и семейном положении регистр гуляет, что повлияет на дальнейшую
обработку и анализ данных приводим всё к нижнему регистру </strong> </div>


```python
data['education'] = data['education'].str.lower()
data['family_status'] = data['family_status'].str.lower()
```

<div class="alert alert-info">
<strong>проверяем все ли изменилось</strong> </div>


```python
display(data['education'].value_counts())
print('__________________________________')
display(data['family_status'].value_counts())
```


    среднее                15233
    высшее                  5260
    неоконченное высшее      744
    начальное                282
    ученая степень             6
    Name: education, dtype: int64


    __________________________________



    женат / замужем          12380
    гражданский брак          4177
    не женат / не замужем     2813
    в разводе                 1195
    вдовец / вдова             960
    Name: family_status, dtype: int64


<div class="alert alert-info">
    <strong>да , все в порядке</strong> </div>

#### удаляем дубликаты


```python
data = data.drop_duplicates().reset_index(drop=True)
```

<div class="alert alert-info">
<strong>Да действительно в образовании и семейном положении регистр гуляет, что повлияет на дальнейшую
обработку и анализ данных приводим всё к нижнему регистру </strong> </div>

#### проверяем удаление


```python
print(data.duplicated().sum()) # проверяем удалились ли дубликаты
#:
print(data[data.duplicated(keep = False)].sort_values(by = data.columns.values.tolist()))
display(data.head())

```

    0
    Empty DataFrame
    Columns: [children, days_employed, dob_years, education, education_id, family_status, family_status_id, gender, income_type, debt, total_income, purpose]
    Index: []



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education</th>
      <th>education_id</th>
      <th>family_status</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1</td>
      <td>8437</td>
      <td>42</td>
      <td>высшее</td>
      <td>0</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>253875</td>
      <td>покупка жилья</td>
    </tr>
    <tr>
      <td>1</td>
      <td>1</td>
      <td>4024</td>
      <td>36</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>112080</td>
      <td>приобретение автомобиля</td>
    </tr>
    <tr>
      <td>2</td>
      <td>0</td>
      <td>5623</td>
      <td>33</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>145885</td>
      <td>покупка жилья</td>
    </tr>
    <tr>
      <td>3</td>
      <td>3</td>
      <td>4124</td>
      <td>32</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>267628</td>
      <td>дополнительное образование</td>
    </tr>
    <tr>
      <td>4</td>
      <td>0</td>
      <td>2194</td>
      <td>53</td>
      <td>среднее</td>
      <td>1</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>158616</td>
      <td>сыграть свадьбу</td>
    </tr>
  </tbody>
</table>
</div>


### Вывод

**Дубликаты найдены, но вопрос в том стоит ли их удалять мб это совпадения реальных клиентов, а может быть и нет. Но я думаю что при таком количестве различных данных для каждого клиента реальные совпадения врядли возможны, поэтому удалил дубликаты. Причина возникновения , возможно ошибка оператора который их вносил. Либо если обьеденялись несколько баз в одну были внесены одни и теже клиенты. Также обнаружены внесения данных в разных регистрах**

### Лемматизация

#### импортируем библиотеки


```python
m = Mystem()
lemmas = m.lemmatize(data['purpose'][3])
print(lemmas)
print(len(data['purpose']))
```

    ['дополнительный', ' ', 'образование', '\n']
    21454


#### берем только уникальные значения без дубликатов , уменьшаем размер списка


```python
uniq_purpose = data['purpose'].unique()
print(uniq_purpose)
#print(data['purpose'])

```

    ['покупка жилья' 'приобретение автомобиля' 'дополнительное образование'
     'сыграть свадьбу' 'операции с жильем' 'образование'
     'на проведение свадьбы' 'покупка жилья для семьи' 'покупка недвижимости'
     'покупка коммерческой недвижимости' 'покупка жилой недвижимости'
     'строительство собственной недвижимости' 'недвижимость'
     'строительство недвижимости' 'на покупку подержанного автомобиля'
     'на покупку своего автомобиля' 'операции с коммерческой недвижимостью'
     'строительство жилой недвижимости' 'жилье'
     'операции со своей недвижимостью' 'автомобили' 'заняться образованием'
     'сделка с подержанным автомобилем' 'получение образования' 'автомобиль'
     'свадьба' 'получение дополнительного образования' 'покупка своего жилья'
     'операции с недвижимостью' 'получение высшего образования'
     'свой автомобиль' 'сделка с автомобилем' 'профильное образование'
     'высшее образование' 'покупка жилья для сдачи' 'на покупку автомобиля'
     'ремонт жилью' 'заняться высшим образованием']


#### преобразуем массив в питоновский лист


```python
uniq_purpose = uniq_purpose.tolist()
print(uniq_purpose)
```

    ['покупка жилья', 'приобретение автомобиля', 'дополнительное образование', 'сыграть свадьбу', 'операции с жильем', 'образование', 'на проведение свадьбы', 'покупка жилья для семьи', 'покупка недвижимости', 'покупка коммерческой недвижимости', 'покупка жилой недвижимости', 'строительство собственной недвижимости', 'недвижимость', 'строительство недвижимости', 'на покупку подержанного автомобиля', 'на покупку своего автомобиля', 'операции с коммерческой недвижимостью', 'строительство жилой недвижимости', 'жилье', 'операции со своей недвижимостью', 'автомобили', 'заняться образованием', 'сделка с подержанным автомобилем', 'получение образования', 'автомобиль', 'свадьба', 'получение дополнительного образования', 'покупка своего жилья', 'операции с недвижимостью', 'получение высшего образования', 'свой автомобиль', 'сделка с автомобилем', 'профильное образование', 'высшее образование', 'покупка жилья для сдачи', 'на покупку автомобиля', 'ремонт жилью', 'заняться высшим образованием']


#### Функция для Лемматизации элементов массива uniq_purpose, используем extend вместо append и формируем переменную а не массив,
еще ---- m.lemmatize(' '.join(data['purpose'])) рекомендован еще такой вариант


```python
uniq_purpose_lemm = []
for i in uniq_purpose:
    uniq_purpose_lemm.extend(m.lemmatize(i))
    print(uniq_purpose_lemm)    
```

    ['покупка', ' ', 'жилье', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n', 'операция', ' ', 'со', ' ', 'свой', ' ', 'недвижимость', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n', 'операция', ' ', 'со', ' ', 'свой', ' ', 'недвижимость', '\n', 'автомобиль', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n', 'операция', ' ', 'со', ' ', 'свой', ' ', 'недвижимость', '\n', 'автомобиль', '\n', 'заниматься', ' ', 'образование', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n', 'операция', ' ', 'со', ' ', 'свой', ' ', 'недвижимость', '\n', 'автомобиль', '\n', 'заниматься', ' ', 'образование', '\n', 'сделка', ' ', 'с', ' ', 'подержанный', ' ', 'автомобиль', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n', 'операция', ' ', 'со', ' ', 'свой', ' ', 'недвижимость', '\n', 'автомобиль', '\n', 'заниматься', ' ', 'образование', '\n', 'сделка', ' ', 'с', ' ', 'подержанный', ' ', 'автомобиль', '\n', 'получение', ' ', 'образование', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n', 'операция', ' ', 'со', ' ', 'свой', ' ', 'недвижимость', '\n', 'автомобиль', '\n', 'заниматься', ' ', 'образование', '\n', 'сделка', ' ', 'с', ' ', 'подержанный', ' ', 'автомобиль', '\n', 'получение', ' ', 'образование', '\n', 'автомобиль', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n', 'операция', ' ', 'со', ' ', 'свой', ' ', 'недвижимость', '\n', 'автомобиль', '\n', 'заниматься', ' ', 'образование', '\n', 'сделка', ' ', 'с', ' ', 'подержанный', ' ', 'автомобиль', '\n', 'получение', ' ', 'образование', '\n', 'автомобиль', '\n', 'свадьба', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n', 'операция', ' ', 'со', ' ', 'свой', ' ', 'недвижимость', '\n', 'автомобиль', '\n', 'заниматься', ' ', 'образование', '\n', 'сделка', ' ', 'с', ' ', 'подержанный', ' ', 'автомобиль', '\n', 'получение', ' ', 'образование', '\n', 'автомобиль', '\n', 'свадьба', '\n', 'получение', ' ', 'дополнительный', ' ', 'образование', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n', 'операция', ' ', 'со', ' ', 'свой', ' ', 'недвижимость', '\n', 'автомобиль', '\n', 'заниматься', ' ', 'образование', '\n', 'сделка', ' ', 'с', ' ', 'подержанный', ' ', 'автомобиль', '\n', 'получение', ' ', 'образование', '\n', 'автомобиль', '\n', 'свадьба', '\n', 'получение', ' ', 'дополнительный', ' ', 'образование', '\n', 'покупка', ' ', 'свой', ' ', 'жилье', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n', 'операция', ' ', 'со', ' ', 'свой', ' ', 'недвижимость', '\n', 'автомобиль', '\n', 'заниматься', ' ', 'образование', '\n', 'сделка', ' ', 'с', ' ', 'подержанный', ' ', 'автомобиль', '\n', 'получение', ' ', 'образование', '\n', 'автомобиль', '\n', 'свадьба', '\n', 'получение', ' ', 'дополнительный', ' ', 'образование', '\n', 'покупка', ' ', 'свой', ' ', 'жилье', '\n', 'операция', ' ', 'с', ' ', 'недвижимость', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n', 'операция', ' ', 'со', ' ', 'свой', ' ', 'недвижимость', '\n', 'автомобиль', '\n', 'заниматься', ' ', 'образование', '\n', 'сделка', ' ', 'с', ' ', 'подержанный', ' ', 'автомобиль', '\n', 'получение', ' ', 'образование', '\n', 'автомобиль', '\n', 'свадьба', '\n', 'получение', ' ', 'дополнительный', ' ', 'образование', '\n', 'покупка', ' ', 'свой', ' ', 'жилье', '\n', 'операция', ' ', 'с', ' ', 'недвижимость', '\n', 'получение', ' ', 'высокий', ' ', 'образование', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n', 'операция', ' ', 'со', ' ', 'свой', ' ', 'недвижимость', '\n', 'автомобиль', '\n', 'заниматься', ' ', 'образование', '\n', 'сделка', ' ', 'с', ' ', 'подержанный', ' ', 'автомобиль', '\n', 'получение', ' ', 'образование', '\n', 'автомобиль', '\n', 'свадьба', '\n', 'получение', ' ', 'дополнительный', ' ', 'образование', '\n', 'покупка', ' ', 'свой', ' ', 'жилье', '\n', 'операция', ' ', 'с', ' ', 'недвижимость', '\n', 'получение', ' ', 'высокий', ' ', 'образование', '\n', 'свой', ' ', 'автомобиль', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n', 'операция', ' ', 'со', ' ', 'свой', ' ', 'недвижимость', '\n', 'автомобиль', '\n', 'заниматься', ' ', 'образование', '\n', 'сделка', ' ', 'с', ' ', 'подержанный', ' ', 'автомобиль', '\n', 'получение', ' ', 'образование', '\n', 'автомобиль', '\n', 'свадьба', '\n', 'получение', ' ', 'дополнительный', ' ', 'образование', '\n', 'покупка', ' ', 'свой', ' ', 'жилье', '\n', 'операция', ' ', 'с', ' ', 'недвижимость', '\n', 'получение', ' ', 'высокий', ' ', 'образование', '\n', 'свой', ' ', 'автомобиль', '\n', 'сделка', ' ', 'с', ' ', 'автомобиль', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n', 'операция', ' ', 'со', ' ', 'свой', ' ', 'недвижимость', '\n', 'автомобиль', '\n', 'заниматься', ' ', 'образование', '\n', 'сделка', ' ', 'с', ' ', 'подержанный', ' ', 'автомобиль', '\n', 'получение', ' ', 'образование', '\n', 'автомобиль', '\n', 'свадьба', '\n', 'получение', ' ', 'дополнительный', ' ', 'образование', '\n', 'покупка', ' ', 'свой', ' ', 'жилье', '\n', 'операция', ' ', 'с', ' ', 'недвижимость', '\n', 'получение', ' ', 'высокий', ' ', 'образование', '\n', 'свой', ' ', 'автомобиль', '\n', 'сделка', ' ', 'с', ' ', 'автомобиль', '\n', 'профильный', ' ', 'образование', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n', 'операция', ' ', 'со', ' ', 'свой', ' ', 'недвижимость', '\n', 'автомобиль', '\n', 'заниматься', ' ', 'образование', '\n', 'сделка', ' ', 'с', ' ', 'подержанный', ' ', 'автомобиль', '\n', 'получение', ' ', 'образование', '\n', 'автомобиль', '\n', 'свадьба', '\n', 'получение', ' ', 'дополнительный', ' ', 'образование', '\n', 'покупка', ' ', 'свой', ' ', 'жилье', '\n', 'операция', ' ', 'с', ' ', 'недвижимость', '\n', 'получение', ' ', 'высокий', ' ', 'образование', '\n', 'свой', ' ', 'автомобиль', '\n', 'сделка', ' ', 'с', ' ', 'автомобиль', '\n', 'профильный', ' ', 'образование', '\n', 'высокий', ' ', 'образование', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n', 'операция', ' ', 'со', ' ', 'свой', ' ', 'недвижимость', '\n', 'автомобиль', '\n', 'заниматься', ' ', 'образование', '\n', 'сделка', ' ', 'с', ' ', 'подержанный', ' ', 'автомобиль', '\n', 'получение', ' ', 'образование', '\n', 'автомобиль', '\n', 'свадьба', '\n', 'получение', ' ', 'дополнительный', ' ', 'образование', '\n', 'покупка', ' ', 'свой', ' ', 'жилье', '\n', 'операция', ' ', 'с', ' ', 'недвижимость', '\n', 'получение', ' ', 'высокий', ' ', 'образование', '\n', 'свой', ' ', 'автомобиль', '\n', 'сделка', ' ', 'с', ' ', 'автомобиль', '\n', 'профильный', ' ', 'образование', '\n', 'высокий', ' ', 'образование', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'сдача', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n', 'операция', ' ', 'со', ' ', 'свой', ' ', 'недвижимость', '\n', 'автомобиль', '\n', 'заниматься', ' ', 'образование', '\n', 'сделка', ' ', 'с', ' ', 'подержанный', ' ', 'автомобиль', '\n', 'получение', ' ', 'образование', '\n', 'автомобиль', '\n', 'свадьба', '\n', 'получение', ' ', 'дополнительный', ' ', 'образование', '\n', 'покупка', ' ', 'свой', ' ', 'жилье', '\n', 'операция', ' ', 'с', ' ', 'недвижимость', '\n', 'получение', ' ', 'высокий', ' ', 'образование', '\n', 'свой', ' ', 'автомобиль', '\n', 'сделка', ' ', 'с', ' ', 'автомобиль', '\n', 'профильный', ' ', 'образование', '\n', 'высокий', ' ', 'образование', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'сдача', '\n', 'на', ' ', 'покупка', ' ', 'автомобиль', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n', 'операция', ' ', 'со', ' ', 'свой', ' ', 'недвижимость', '\n', 'автомобиль', '\n', 'заниматься', ' ', 'образование', '\n', 'сделка', ' ', 'с', ' ', 'подержанный', ' ', 'автомобиль', '\n', 'получение', ' ', 'образование', '\n', 'автомобиль', '\n', 'свадьба', '\n', 'получение', ' ', 'дополнительный', ' ', 'образование', '\n', 'покупка', ' ', 'свой', ' ', 'жилье', '\n', 'операция', ' ', 'с', ' ', 'недвижимость', '\n', 'получение', ' ', 'высокий', ' ', 'образование', '\n', 'свой', ' ', 'автомобиль', '\n', 'сделка', ' ', 'с', ' ', 'автомобиль', '\n', 'профильный', ' ', 'образование', '\n', 'высокий', ' ', 'образование', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'сдача', '\n', 'на', ' ', 'покупка', ' ', 'автомобиль', '\n', 'ремонт', ' ', 'жилье', '\n']
    ['покупка', ' ', 'жилье', '\n', 'приобретение', ' ', 'автомобиль', '\n', 'дополнительный', ' ', 'образование', '\n', 'сыграть', ' ', 'свадьба', '\n', 'операция', ' ', 'с', ' ', 'жилье', '\n', 'образование', '\n', 'на', ' ', 'проведение', ' ', 'свадьба', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'семья', '\n', 'покупка', ' ', 'недвижимость', '\n', 'покупка', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'покупка', ' ', 'жилой', ' ', 'недвижимость', '\n', 'строительство', ' ', 'собственный', ' ', 'недвижимость', '\n', 'недвижимость', '\n', 'строительство', ' ', 'недвижимость', '\n', 'на', ' ', 'покупка', ' ', 'подержать', ' ', 'автомобиль', '\n', 'на', ' ', 'покупка', ' ', 'свой', ' ', 'автомобиль', '\n', 'операция', ' ', 'с', ' ', 'коммерческий', ' ', 'недвижимость', '\n', 'строительство', ' ', 'жилой', ' ', 'недвижимость', '\n', 'жилье', '\n', 'операция', ' ', 'со', ' ', 'свой', ' ', 'недвижимость', '\n', 'автомобиль', '\n', 'заниматься', ' ', 'образование', '\n', 'сделка', ' ', 'с', ' ', 'подержанный', ' ', 'автомобиль', '\n', 'получение', ' ', 'образование', '\n', 'автомобиль', '\n', 'свадьба', '\n', 'получение', ' ', 'дополнительный', ' ', 'образование', '\n', 'покупка', ' ', 'свой', ' ', 'жилье', '\n', 'операция', ' ', 'с', ' ', 'недвижимость', '\n', 'получение', ' ', 'высокий', ' ', 'образование', '\n', 'свой', ' ', 'автомобиль', '\n', 'сделка', ' ', 'с', ' ', 'автомобиль', '\n', 'профильный', ' ', 'образование', '\n', 'высокий', ' ', 'образование', '\n', 'покупка', ' ', 'жилье', ' ', 'для', ' ', 'сдача', '\n', 'на', ' ', 'покупка', ' ', 'автомобиль', '\n', 'ремонт', ' ', 'жилье', '\n', 'заниматься', ' ', 'высокий', ' ', 'образование', '\n']


#### подсчёт числа их упоминаний в тексте


```python
print(Counter(uniq_purpose_lemm))
```

    Counter({' ': 59, '\n': 38, 'покупка': 10, 'недвижимость': 10, 'автомобиль': 9, 'образование': 9, 'жилье': 7, 'с': 5, 'операция': 4, 'на': 4, 'свой': 4, 'свадьба': 3, 'строительство': 3, 'получение': 3, 'высокий': 3, 'дополнительный': 2, 'для': 2, 'коммерческий': 2, 'жилой': 2, 'заниматься': 2, 'сделка': 2, 'приобретение': 1, 'сыграть': 1, 'проведение': 1, 'семья': 1, 'собственный': 1, 'подержать': 1, 'со': 1, 'подержанный': 1, 'профильный': 1, 'сдача': 1, 'ремонт': 1})


**наиболее частые слова**
**образование недвижимость автомобиль жилье свадьба**

#### Функция для Лемматизации столбца data['purpose'] и меняем содержимое на леммы


```python
display(data.head()) # проверяем таблицу
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education</th>
      <th>education_id</th>
      <th>family_status</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1</td>
      <td>8437</td>
      <td>42</td>
      <td>высшее</td>
      <td>0</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>253875</td>
      <td>покупка жилья</td>
    </tr>
    <tr>
      <td>1</td>
      <td>1</td>
      <td>4024</td>
      <td>36</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>112080</td>
      <td>приобретение автомобиля</td>
    </tr>
    <tr>
      <td>2</td>
      <td>0</td>
      <td>5623</td>
      <td>33</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>145885</td>
      <td>покупка жилья</td>
    </tr>
    <tr>
      <td>3</td>
      <td>3</td>
      <td>4124</td>
      <td>32</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>267628</td>
      <td>дополнительное образование</td>
    </tr>
    <tr>
      <td>4</td>
      <td>0</td>
      <td>2194</td>
      <td>53</td>
      <td>среднее</td>
      <td>1</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>158616</td>
      <td>сыграть свадьбу</td>
    </tr>
  </tbody>
</table>
</div>



```python
def lemm_purpose(row):
    if 'недвижимость' in m.lemmatize(row):
        return 'недвижимость'
           
    if 'автомобиль' in m.lemmatize(row):
        return 'автомобиль'
            
    if 'жилье' in m.lemmatize(row):
        return 'жилье'
            
    if 'свадьба' in m.lemmatize(row):
        return 'свадьба'
    
    if 'образование' in m.lemmatize(row):
        return 'образование'
             
    else: return 'другое'         
      

```

<div class="alert alert-info">
    
# перестаёт исполнятся код<a id="cell2"></a>   

#### применяем к столбцу data['purpose']  и проверяем


```python
data['purpose'] = data['purpose'].apply(lemm_purpose)
display(data.head())
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education</th>
      <th>education_id</th>
      <th>family_status</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1</td>
      <td>8437</td>
      <td>42</td>
      <td>высшее</td>
      <td>0</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>253875</td>
      <td>жилье</td>
    </tr>
    <tr>
      <td>1</td>
      <td>1</td>
      <td>4024</td>
      <td>36</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>112080</td>
      <td>автомобиль</td>
    </tr>
    <tr>
      <td>2</td>
      <td>0</td>
      <td>5623</td>
      <td>33</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>145885</td>
      <td>жилье</td>
    </tr>
    <tr>
      <td>3</td>
      <td>3</td>
      <td>4124</td>
      <td>32</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>267628</td>
      <td>образование</td>
    </tr>
    <tr>
      <td>4</td>
      <td>0</td>
      <td>2194</td>
      <td>53</td>
      <td>среднее</td>
      <td>1</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>158616</td>
      <td>свадьба</td>
    </tr>
  </tbody>
</table>
</div>


### Вывод

**Данные столбца data['purpose'] лемматизированы, цели для удобства сортировки отражены одним словом**

### Категоризация данных

#### Проводим категоризацию данных согласно заданию
####  Делаем сводную таблицу: влияние  количество детей клиента на факт погашения кредита
**Bстроим строку с процентами кто имел задолжность количество детей клиента  факт погашения кредита**


```python
data_child = data.pivot_table(index = ['children'], values = 'debt', aggfunc = [sum, 'count'])
#data_child = data.groupby(['children',]).agg({'debt':'sum','children':'count'})
data_child['%_total_debt'] = (data_child ['sum','debt']/data_child ['count','debt'])
#data_child.columns = ['Кол-во невозвратов','Кол-во заемщиков','% невозврата']
data_child.style.format({'% невозврата':'{:.2%}'})
#print(data_child)
```




<style  type="text/css" >
</style><table id="T_40ee5782_3610_11eb_a05e_02420a39eb2e" ><thead>    <tr>        <th class="blank level0" ></th>        <th class="col_heading level0 col0" >sum</th>        <th class="col_heading level0 col1" >count</th>        <th class="col_heading level0 col2" >%_total_debt</th>    </tr>    <tr>        <th class="blank level1" ></th>        <th class="col_heading level1 col0" >debt</th>        <th class="col_heading level1 col1" >debt</th>        <th class="col_heading level1 col2" ></th>    </tr>    <tr>        <th class="index_name level0" >children</th>        <th class="blank" ></th>        <th class="blank" ></th>        <th class="blank" ></th>    </tr></thead><tbody>
                <tr>
                        <th id="T_40ee5782_3610_11eb_a05e_02420a39eb2elevel0_row0" class="row_heading level0 row0" >0</th>
                        <td id="T_40ee5782_3610_11eb_a05e_02420a39eb2erow0_col0" class="data row0 col0" >1063</td>
                        <td id="T_40ee5782_3610_11eb_a05e_02420a39eb2erow0_col1" class="data row0 col1" >14091</td>
                        <td id="T_40ee5782_3610_11eb_a05e_02420a39eb2erow0_col2" class="data row0 col2" >0.0754382</td>
            </tr>
            <tr>
                        <th id="T_40ee5782_3610_11eb_a05e_02420a39eb2elevel0_row1" class="row_heading level0 row1" >1</th>
                        <td id="T_40ee5782_3610_11eb_a05e_02420a39eb2erow1_col0" class="data row1 col0" >445</td>
                        <td id="T_40ee5782_3610_11eb_a05e_02420a39eb2erow1_col1" class="data row1 col1" >4855</td>
                        <td id="T_40ee5782_3610_11eb_a05e_02420a39eb2erow1_col2" class="data row1 col2" >0.0916581</td>
            </tr>
            <tr>
                        <th id="T_40ee5782_3610_11eb_a05e_02420a39eb2elevel0_row2" class="row_heading level0 row2" >2</th>
                        <td id="T_40ee5782_3610_11eb_a05e_02420a39eb2erow2_col0" class="data row2 col0" >202</td>
                        <td id="T_40ee5782_3610_11eb_a05e_02420a39eb2erow2_col1" class="data row2 col1" >2128</td>
                        <td id="T_40ee5782_3610_11eb_a05e_02420a39eb2erow2_col2" class="data row2 col2" >0.0949248</td>
            </tr>
            <tr>
                        <th id="T_40ee5782_3610_11eb_a05e_02420a39eb2elevel0_row3" class="row_heading level0 row3" >3</th>
                        <td id="T_40ee5782_3610_11eb_a05e_02420a39eb2erow3_col0" class="data row3 col0" >27</td>
                        <td id="T_40ee5782_3610_11eb_a05e_02420a39eb2erow3_col1" class="data row3 col1" >330</td>
                        <td id="T_40ee5782_3610_11eb_a05e_02420a39eb2erow3_col2" class="data row3 col2" >0.0818182</td>
            </tr>
            <tr>
                        <th id="T_40ee5782_3610_11eb_a05e_02420a39eb2elevel0_row4" class="row_heading level0 row4" >4</th>
                        <td id="T_40ee5782_3610_11eb_a05e_02420a39eb2erow4_col0" class="data row4 col0" >4</td>
                        <td id="T_40ee5782_3610_11eb_a05e_02420a39eb2erow4_col1" class="data row4 col1" >41</td>
                        <td id="T_40ee5782_3610_11eb_a05e_02420a39eb2erow4_col2" class="data row4 col2" >0.097561</td>
            </tr>
            <tr>
                        <th id="T_40ee5782_3610_11eb_a05e_02420a39eb2elevel0_row5" class="row_heading level0 row5" >5</th>
                        <td id="T_40ee5782_3610_11eb_a05e_02420a39eb2erow5_col0" class="data row5 col0" >0</td>
                        <td id="T_40ee5782_3610_11eb_a05e_02420a39eb2erow5_col1" class="data row5 col1" >9</td>
                        <td id="T_40ee5782_3610_11eb_a05e_02420a39eb2erow5_col2" class="data row5 col2" >0</td>
            </tr>
    </tbody></table>



####  Делаем сводную таблицу *семейное положение * - факт погашения кредита Bстроим строку с процентами кто имел задолжность *семейное положение * - факт погашения кредита 


```python
data_family = data.groupby('family_status').agg({'debt':'sum', 'family_status':'count'})
data_family['%_total_debt'] = (data_family['debt']/data_family['family_status'])
data_family.columns = ['Кол-во невозвратов','Кол-во заемщиков','% невозврата']
data_family.style.format({'% невозврата':'{:.2%}'})
#print(data_family)
```




<style  type="text/css" >
</style><table id="T_40f4c748_3610_11eb_a05e_02420a39eb2e" ><thead>    <tr>        <th class="blank level0" ></th>        <th class="col_heading level0 col0" >Кол-во невозвратов</th>        <th class="col_heading level0 col1" >Кол-во заемщиков</th>        <th class="col_heading level0 col2" >% невозврата</th>    </tr>    <tr>        <th class="index_name level0" >family_status</th>        <th class="blank" ></th>        <th class="blank" ></th>        <th class="blank" ></th>    </tr></thead><tbody>
                <tr>
                        <th id="T_40f4c748_3610_11eb_a05e_02420a39eb2elevel0_row0" class="row_heading level0 row0" >в разводе</th>
                        <td id="T_40f4c748_3610_11eb_a05e_02420a39eb2erow0_col0" class="data row0 col0" >85</td>
                        <td id="T_40f4c748_3610_11eb_a05e_02420a39eb2erow0_col1" class="data row0 col1" >1195</td>
                        <td id="T_40f4c748_3610_11eb_a05e_02420a39eb2erow0_col2" class="data row0 col2" >7.11%</td>
            </tr>
            <tr>
                        <th id="T_40f4c748_3610_11eb_a05e_02420a39eb2elevel0_row1" class="row_heading level0 row1" >вдовец / вдова</th>
                        <td id="T_40f4c748_3610_11eb_a05e_02420a39eb2erow1_col0" class="data row1 col0" >63</td>
                        <td id="T_40f4c748_3610_11eb_a05e_02420a39eb2erow1_col1" class="data row1 col1" >959</td>
                        <td id="T_40f4c748_3610_11eb_a05e_02420a39eb2erow1_col2" class="data row1 col2" >6.57%</td>
            </tr>
            <tr>
                        <th id="T_40f4c748_3610_11eb_a05e_02420a39eb2elevel0_row2" class="row_heading level0 row2" >гражданский брак</th>
                        <td id="T_40f4c748_3610_11eb_a05e_02420a39eb2erow2_col0" class="data row2 col0" >388</td>
                        <td id="T_40f4c748_3610_11eb_a05e_02420a39eb2erow2_col1" class="data row2 col1" >4151</td>
                        <td id="T_40f4c748_3610_11eb_a05e_02420a39eb2erow2_col2" class="data row2 col2" >9.35%</td>
            </tr>
            <tr>
                        <th id="T_40f4c748_3610_11eb_a05e_02420a39eb2elevel0_row3" class="row_heading level0 row3" >женат / замужем</th>
                        <td id="T_40f4c748_3610_11eb_a05e_02420a39eb2erow3_col0" class="data row3 col0" >931</td>
                        <td id="T_40f4c748_3610_11eb_a05e_02420a39eb2erow3_col1" class="data row3 col1" >12339</td>
                        <td id="T_40f4c748_3610_11eb_a05e_02420a39eb2erow3_col2" class="data row3 col2" >7.55%</td>
            </tr>
            <tr>
                        <th id="T_40f4c748_3610_11eb_a05e_02420a39eb2elevel0_row4" class="row_heading level0 row4" >не женат / не замужем</th>
                        <td id="T_40f4c748_3610_11eb_a05e_02420a39eb2erow4_col0" class="data row4 col0" >274</td>
                        <td id="T_40f4c748_3610_11eb_a05e_02420a39eb2erow4_col1" class="data row4 col1" >2810</td>
                        <td id="T_40f4c748_3610_11eb_a05e_02420a39eb2erow4_col2" class="data row4 col2" >9.75%</td>
            </tr>
    </tbody></table>



### Сделаем общую сводную таблицу c процентами


```python
data_child_family = data.groupby(['children', 'family_status']).agg({'debt':'sum', 'family_status':'count'})
data_child_family['%_total_debt'] = (data_child_family['debt']/data_child_family['family_status'])*100
data_child_family.columns = ['Кол. невозвр','Кол-во заемщиков','% невозврата']
#data_child_family.format({'Процент невозврата':'{:.2%}'})
display(data_child_family)
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Кол. невозвр</th>
      <th>Кол-во заемщиков</th>
      <th>% невозврата</th>
    </tr>
    <tr>
      <th>children</th>
      <th>family_status</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="5" valign="top">0</td>
      <td>в разводе</td>
      <td>55</td>
      <td>784</td>
      <td>7.015306</td>
    </tr>
    <tr>
      <td>вдовец / вдова</td>
      <td>53</td>
      <td>847</td>
      <td>6.257379</td>
    </tr>
    <tr>
      <td>гражданский брак</td>
      <td>229</td>
      <td>2730</td>
      <td>8.388278</td>
    </tr>
    <tr>
      <td>женат / замужем</td>
      <td>516</td>
      <td>7468</td>
      <td>6.909480</td>
    </tr>
    <tr>
      <td>не женат / не замужем</td>
      <td>210</td>
      <td>2262</td>
      <td>9.283820</td>
    </tr>
    <tr>
      <td rowspan="5" valign="top">1</td>
      <td>в разводе</td>
      <td>21</td>
      <td>316</td>
      <td>6.645570</td>
    </tr>
    <tr>
      <td>вдовец / вдова</td>
      <td>7</td>
      <td>81</td>
      <td>8.641975</td>
    </tr>
    <tr>
      <td>гражданский брак</td>
      <td>118</td>
      <td>1000</td>
      <td>11.800000</td>
    </tr>
    <tr>
      <td>женат / замужем</td>
      <td>247</td>
      <td>3004</td>
      <td>8.222370</td>
    </tr>
    <tr>
      <td>не женат / не замужем</td>
      <td>52</td>
      <td>454</td>
      <td>11.453744</td>
    </tr>
    <tr>
      <td rowspan="5" valign="top">2</td>
      <td>в разводе</td>
      <td>8</td>
      <td>83</td>
      <td>9.638554</td>
    </tr>
    <tr>
      <td>вдовец / вдова</td>
      <td>3</td>
      <td>24</td>
      <td>12.500000</td>
    </tr>
    <tr>
      <td>гражданский брак</td>
      <td>33</td>
      <td>355</td>
      <td>9.295775</td>
    </tr>
    <tr>
      <td>женат / замужем</td>
      <td>148</td>
      <td>1582</td>
      <td>9.355247</td>
    </tr>
    <tr>
      <td>не женат / не замужем</td>
      <td>10</td>
      <td>84</td>
      <td>11.904762</td>
    </tr>
    <tr>
      <td rowspan="5" valign="top">3</td>
      <td>в разводе</td>
      <td>1</td>
      <td>11</td>
      <td>9.090909</td>
    </tr>
    <tr>
      <td>вдовец / вдова</td>
      <td>0</td>
      <td>6</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>гражданский брак</td>
      <td>8</td>
      <td>56</td>
      <td>14.285714</td>
    </tr>
    <tr>
      <td>женат / замужем</td>
      <td>17</td>
      <td>249</td>
      <td>6.827309</td>
    </tr>
    <tr>
      <td>не женат / не замужем</td>
      <td>1</td>
      <td>8</td>
      <td>12.500000</td>
    </tr>
    <tr>
      <td rowspan="5" valign="top">4</td>
      <td>в разводе</td>
      <td>0</td>
      <td>1</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>вдовец / вдова</td>
      <td>0</td>
      <td>1</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>гражданский брак</td>
      <td>0</td>
      <td>8</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>женат / замужем</td>
      <td>3</td>
      <td>29</td>
      <td>10.344828</td>
    </tr>
    <tr>
      <td>не женат / не замужем</td>
      <td>1</td>
      <td>2</td>
      <td>50.000000</td>
    </tr>
    <tr>
      <td rowspan="2" valign="top">5</td>
      <td>гражданский брак</td>
      <td>0</td>
      <td>2</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <td>женат / замужем</td>
      <td>0</td>
      <td>7</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
</div>


### Есть ли зависимость между уровнем дохода и возвратом кредита в срок?
**установим уровни дохода средний высокий низкий**


```python
print(data['total_income'].max())
```

    2265604



```python
print(data['total_income'].min())
```

    20667



```python
print(data['total_income'].median())
```

    142594.0


#### Пробуем найти кретерии для определения уровня зарплат  
* **от 0 до 33% уровня  низкая зарплата**
* **между 33% и 66% средняя зарплата**
* **66% и выше высокая зарплата**


```python
data_int_profit = data['total_income'].sort_values(ascending = False)
data_int_profit.quantile([0.33, 0.66])
```




    0.33    118514.0
    0.66    172357.0
    Name: total_income, dtype: float64



**Делаем функцию разграничиваем уровень зарплат**


```python
def level_earn(row):
    if row < 122202:
        return 'Низкий уровень зарплаты'
    if 122202 <= row <= 169221:
        return 'Средний уровень зарплаты'
    if row > 169221:
        return 'Высокий уровень зарплаты'
      
```

**Меняем цифры в столбце**


```python
data['total_income'] = data['total_income'].apply(level_earn)

```

#### Сделаем общую сводную таблицу c процентами


```python
data_profit = data.groupby('total_income').agg({'debt':'sum', 'total_income':'count'})
data_profit['%_total_debt'] = (data_profit['debt']/data_profit['total_income'])
data_profit.columns = ['Кол-во невозвратов','Кол-во заемщиков','% невозврата']
data_profit.style.format({'% невозврата':'{:.2%}'})
#display(data_profit)
```




<style  type="text/css" >
</style><table id="T_411acae2_3610_11eb_a05e_02420a39eb2e" ><thead>    <tr>        <th class="blank level0" ></th>        <th class="col_heading level0 col0" >Кол-во невозвратов</th>        <th class="col_heading level0 col1" >Кол-во заемщиков</th>        <th class="col_heading level0 col2" >% невозврата</th>    </tr>    <tr>        <th class="index_name level0" >total_income</th>        <th class="blank" ></th>        <th class="blank" ></th>        <th class="blank" ></th>    </tr></thead><tbody>
                <tr>
                        <th id="T_411acae2_3610_11eb_a05e_02420a39eb2elevel0_row0" class="row_heading level0 row0" >Высокий уровень зарплаты</th>
                        <td id="T_411acae2_3610_11eb_a05e_02420a39eb2erow0_col0" class="data row0 col0" >586</td>
                        <td id="T_411acae2_3610_11eb_a05e_02420a39eb2erow0_col1" class="data row0 col1" >7802</td>
                        <td id="T_411acae2_3610_11eb_a05e_02420a39eb2erow0_col2" class="data row0 col2" >7.51%</td>
            </tr>
            <tr>
                        <th id="T_411acae2_3610_11eb_a05e_02420a39eb2elevel0_row1" class="row_heading level0 row1" >Низкий уровень зарплаты</th>
                        <td id="T_411acae2_3610_11eb_a05e_02420a39eb2erow1_col0" class="data row1 col0" >614</td>
                        <td id="T_411acae2_3610_11eb_a05e_02420a39eb2erow1_col1" class="data row1 col1" >7470</td>
                        <td id="T_411acae2_3610_11eb_a05e_02420a39eb2erow1_col2" class="data row1 col2" >8.22%</td>
            </tr>
            <tr>
                        <th id="T_411acae2_3610_11eb_a05e_02420a39eb2elevel0_row2" class="row_heading level0 row2" >Средний уровень зарплаты</th>
                        <td id="T_411acae2_3610_11eb_a05e_02420a39eb2erow2_col0" class="data row2 col0" >541</td>
                        <td id="T_411acae2_3610_11eb_a05e_02420a39eb2erow2_col1" class="data row2 col1" >6182</td>
                        <td id="T_411acae2_3610_11eb_a05e_02420a39eb2erow2_col2" class="data row2 col2" >8.75%</td>
            </tr>
    </tbody></table>



### Как разные цели кредита влияют на его возврат в срок?


```python
data_goal = data.pivot_table(index = ['purpose'], values = 'debt', aggfunc = [sum, 'count'])
#data_child = data.groupby(['children',]).agg({'debt':'sum','children':'count'})
data_goal['%_total_debt'] = (data_goal['sum','debt']/data_goal['count','debt'])
data_goal.columns = ['Кол-во невозвратов','Кол-во заемщиков','% невозврата']
data_goal.style.format({'% невозврата':'{:.2%}'})
#print(data_child)
```




<style  type="text/css" >
</style><table id="T_41231a3a_3610_11eb_a05e_02420a39eb2e" ><thead>    <tr>        <th class="blank level0" ></th>        <th class="col_heading level0 col0" >Кол-во невозвратов</th>        <th class="col_heading level0 col1" >Кол-во заемщиков</th>        <th class="col_heading level0 col2" >% невозврата</th>    </tr>    <tr>        <th class="index_name level0" >purpose</th>        <th class="blank" ></th>        <th class="blank" ></th>        <th class="blank" ></th>    </tr></thead><tbody>
                <tr>
                        <th id="T_41231a3a_3610_11eb_a05e_02420a39eb2elevel0_row0" class="row_heading level0 row0" >автомобиль</th>
                        <td id="T_41231a3a_3610_11eb_a05e_02420a39eb2erow0_col0" class="data row0 col0" >403</td>
                        <td id="T_41231a3a_3610_11eb_a05e_02420a39eb2erow0_col1" class="data row0 col1" >4306</td>
                        <td id="T_41231a3a_3610_11eb_a05e_02420a39eb2erow0_col2" class="data row0 col2" >9.36%</td>
            </tr>
            <tr>
                        <th id="T_41231a3a_3610_11eb_a05e_02420a39eb2elevel0_row1" class="row_heading level0 row1" >жилье</th>
                        <td id="T_41231a3a_3610_11eb_a05e_02420a39eb2erow1_col0" class="data row1 col0" >308</td>
                        <td id="T_41231a3a_3610_11eb_a05e_02420a39eb2erow1_col1" class="data row1 col1" >4460</td>
                        <td id="T_41231a3a_3610_11eb_a05e_02420a39eb2erow1_col2" class="data row1 col2" >6.91%</td>
            </tr>
            <tr>
                        <th id="T_41231a3a_3610_11eb_a05e_02420a39eb2elevel0_row2" class="row_heading level0 row2" >недвижимость</th>
                        <td id="T_41231a3a_3610_11eb_a05e_02420a39eb2erow2_col0" class="data row2 col0" >474</td>
                        <td id="T_41231a3a_3610_11eb_a05e_02420a39eb2erow2_col1" class="data row2 col1" >6351</td>
                        <td id="T_41231a3a_3610_11eb_a05e_02420a39eb2erow2_col2" class="data row2 col2" >7.46%</td>
            </tr>
            <tr>
                        <th id="T_41231a3a_3610_11eb_a05e_02420a39eb2elevel0_row3" class="row_heading level0 row3" >образование</th>
                        <td id="T_41231a3a_3610_11eb_a05e_02420a39eb2erow3_col0" class="data row3 col0" >370</td>
                        <td id="T_41231a3a_3610_11eb_a05e_02420a39eb2erow3_col1" class="data row3 col1" >4013</td>
                        <td id="T_41231a3a_3610_11eb_a05e_02420a39eb2erow3_col2" class="data row3 col2" >9.22%</td>
            </tr>
            <tr>
                        <th id="T_41231a3a_3610_11eb_a05e_02420a39eb2elevel0_row4" class="row_heading level0 row4" >свадьба</th>
                        <td id="T_41231a3a_3610_11eb_a05e_02420a39eb2erow4_col0" class="data row4 col0" >186</td>
                        <td id="T_41231a3a_3610_11eb_a05e_02420a39eb2erow4_col1" class="data row4 col1" >2324</td>
                        <td id="T_41231a3a_3610_11eb_a05e_02420a39eb2erow4_col2" class="data row4 col2" >8.00%</td>
            </tr>
    </tbody></table>



### Вывод

**Сводные таблицы сделаны данные представлены в удобном для чтения виде**

### Шаг 3. Ответьте на вопросы

- Есть ли зависимость между наличием детей и возвратом кредита в срок?


```python
data_child = data.pivot_table(index = ['children'], values = 'debt', aggfunc = [sum, 'count'])
data_child['%_total_debt'] = (data_child ['sum','debt']/data_child ['count','debt'])
data_child.columns = ['Кол-во невозвратов','Кол-во заемщиков','% невозврата']
data_child.style.format({'% невозврата':'{:.2%}'})

```




<style  type="text/css" >
</style><table id="T_412b11cc_3610_11eb_a05e_02420a39eb2e" ><thead>    <tr>        <th class="blank level0" ></th>        <th class="col_heading level0 col0" >Кол-во невозвратов</th>        <th class="col_heading level0 col1" >Кол-во заемщиков</th>        <th class="col_heading level0 col2" >% невозврата</th>    </tr>    <tr>        <th class="index_name level0" >children</th>        <th class="blank" ></th>        <th class="blank" ></th>        <th class="blank" ></th>    </tr></thead><tbody>
                <tr>
                        <th id="T_412b11cc_3610_11eb_a05e_02420a39eb2elevel0_row0" class="row_heading level0 row0" >0</th>
                        <td id="T_412b11cc_3610_11eb_a05e_02420a39eb2erow0_col0" class="data row0 col0" >1063</td>
                        <td id="T_412b11cc_3610_11eb_a05e_02420a39eb2erow0_col1" class="data row0 col1" >14091</td>
                        <td id="T_412b11cc_3610_11eb_a05e_02420a39eb2erow0_col2" class="data row0 col2" >7.54%</td>
            </tr>
            <tr>
                        <th id="T_412b11cc_3610_11eb_a05e_02420a39eb2elevel0_row1" class="row_heading level0 row1" >1</th>
                        <td id="T_412b11cc_3610_11eb_a05e_02420a39eb2erow1_col0" class="data row1 col0" >445</td>
                        <td id="T_412b11cc_3610_11eb_a05e_02420a39eb2erow1_col1" class="data row1 col1" >4855</td>
                        <td id="T_412b11cc_3610_11eb_a05e_02420a39eb2erow1_col2" class="data row1 col2" >9.17%</td>
            </tr>
            <tr>
                        <th id="T_412b11cc_3610_11eb_a05e_02420a39eb2elevel0_row2" class="row_heading level0 row2" >2</th>
                        <td id="T_412b11cc_3610_11eb_a05e_02420a39eb2erow2_col0" class="data row2 col0" >202</td>
                        <td id="T_412b11cc_3610_11eb_a05e_02420a39eb2erow2_col1" class="data row2 col1" >2128</td>
                        <td id="T_412b11cc_3610_11eb_a05e_02420a39eb2erow2_col2" class="data row2 col2" >9.49%</td>
            </tr>
            <tr>
                        <th id="T_412b11cc_3610_11eb_a05e_02420a39eb2elevel0_row3" class="row_heading level0 row3" >3</th>
                        <td id="T_412b11cc_3610_11eb_a05e_02420a39eb2erow3_col0" class="data row3 col0" >27</td>
                        <td id="T_412b11cc_3610_11eb_a05e_02420a39eb2erow3_col1" class="data row3 col1" >330</td>
                        <td id="T_412b11cc_3610_11eb_a05e_02420a39eb2erow3_col2" class="data row3 col2" >8.18%</td>
            </tr>
            <tr>
                        <th id="T_412b11cc_3610_11eb_a05e_02420a39eb2elevel0_row4" class="row_heading level0 row4" >4</th>
                        <td id="T_412b11cc_3610_11eb_a05e_02420a39eb2erow4_col0" class="data row4 col0" >4</td>
                        <td id="T_412b11cc_3610_11eb_a05e_02420a39eb2erow4_col1" class="data row4 col1" >41</td>
                        <td id="T_412b11cc_3610_11eb_a05e_02420a39eb2erow4_col2" class="data row4 col2" >9.76%</td>
            </tr>
            <tr>
                        <th id="T_412b11cc_3610_11eb_a05e_02420a39eb2elevel0_row5" class="row_heading level0 row5" >5</th>
                        <td id="T_412b11cc_3610_11eb_a05e_02420a39eb2erow5_col0" class="data row5 col0" >0</td>
                        <td id="T_412b11cc_3610_11eb_a05e_02420a39eb2erow5_col1" class="data row5 col1" >9</td>
                        <td id="T_412b11cc_3610_11eb_a05e_02420a39eb2erow5_col2" class="data row5 col2" >0.00%</td>
            </tr>
    </tbody></table>



### Вывод

Существует небольшая зависимость между наличием детей и возвратом кредита в срок, люди с детьми допускают просрочки чаще чем люди без детей, но это разница исчезающе мала не исключено что это статистическая погрешность.

- Есть ли зависимость между семейным положением и возвратом кредита в срок?


```python
data_family = data.groupby('family_status').agg({'debt':'sum', 'family_status':'count'})
data_family['%_total_debt'] = (data_family['debt']/data_family['family_status'])
data_family.columns = ['Кол-во невозвратов','Кол-во заемщиков','% невозврата']
data_family.style.format({'% невозврата':'{:.2%}'})
```




<style  type="text/css" >
</style><table id="T_4130a22c_3610_11eb_a05e_02420a39eb2e" ><thead>    <tr>        <th class="blank level0" ></th>        <th class="col_heading level0 col0" >Кол-во невозвратов</th>        <th class="col_heading level0 col1" >Кол-во заемщиков</th>        <th class="col_heading level0 col2" >% невозврата</th>    </tr>    <tr>        <th class="index_name level0" >family_status</th>        <th class="blank" ></th>        <th class="blank" ></th>        <th class="blank" ></th>    </tr></thead><tbody>
                <tr>
                        <th id="T_4130a22c_3610_11eb_a05e_02420a39eb2elevel0_row0" class="row_heading level0 row0" >в разводе</th>
                        <td id="T_4130a22c_3610_11eb_a05e_02420a39eb2erow0_col0" class="data row0 col0" >85</td>
                        <td id="T_4130a22c_3610_11eb_a05e_02420a39eb2erow0_col1" class="data row0 col1" >1195</td>
                        <td id="T_4130a22c_3610_11eb_a05e_02420a39eb2erow0_col2" class="data row0 col2" >7.11%</td>
            </tr>
            <tr>
                        <th id="T_4130a22c_3610_11eb_a05e_02420a39eb2elevel0_row1" class="row_heading level0 row1" >вдовец / вдова</th>
                        <td id="T_4130a22c_3610_11eb_a05e_02420a39eb2erow1_col0" class="data row1 col0" >63</td>
                        <td id="T_4130a22c_3610_11eb_a05e_02420a39eb2erow1_col1" class="data row1 col1" >959</td>
                        <td id="T_4130a22c_3610_11eb_a05e_02420a39eb2erow1_col2" class="data row1 col2" >6.57%</td>
            </tr>
            <tr>
                        <th id="T_4130a22c_3610_11eb_a05e_02420a39eb2elevel0_row2" class="row_heading level0 row2" >гражданский брак</th>
                        <td id="T_4130a22c_3610_11eb_a05e_02420a39eb2erow2_col0" class="data row2 col0" >388</td>
                        <td id="T_4130a22c_3610_11eb_a05e_02420a39eb2erow2_col1" class="data row2 col1" >4151</td>
                        <td id="T_4130a22c_3610_11eb_a05e_02420a39eb2erow2_col2" class="data row2 col2" >9.35%</td>
            </tr>
            <tr>
                        <th id="T_4130a22c_3610_11eb_a05e_02420a39eb2elevel0_row3" class="row_heading level0 row3" >женат / замужем</th>
                        <td id="T_4130a22c_3610_11eb_a05e_02420a39eb2erow3_col0" class="data row3 col0" >931</td>
                        <td id="T_4130a22c_3610_11eb_a05e_02420a39eb2erow3_col1" class="data row3 col1" >12339</td>
                        <td id="T_4130a22c_3610_11eb_a05e_02420a39eb2erow3_col2" class="data row3 col2" >7.55%</td>
            </tr>
            <tr>
                        <th id="T_4130a22c_3610_11eb_a05e_02420a39eb2elevel0_row4" class="row_heading level0 row4" >не женат / не замужем</th>
                        <td id="T_4130a22c_3610_11eb_a05e_02420a39eb2erow4_col0" class="data row4 col0" >274</td>
                        <td id="T_4130a22c_3610_11eb_a05e_02420a39eb2erow4_col1" class="data row4 col1" >2810</td>
                        <td id="T_4130a22c_3610_11eb_a05e_02420a39eb2erow4_col2" class="data row4 col2" >9.75%</td>
            </tr>
    </tbody></table>



### Вывод

Семейное положение влияет на наличие задолжности по кредитам, не семейные люди чаще допускают просрочки.

- Есть ли зависимость между уровнем дохода и возвратом кредита в срок?


```python
data_profit = data.groupby('total_income').agg({'debt':'sum', 'total_income':'count'})
data_profit['%_total_debt'] = (data_profit['debt']/data_profit['total_income'])
data_profit.columns = ['Кол-во невозвратов','Кол-во заемщиков','% невозврата']
data_profit.style.format({'% невозврата':'{:.2%}'})
```




<style  type="text/css" >
</style><table id="T_413929ba_3610_11eb_a05e_02420a39eb2e" ><thead>    <tr>        <th class="blank level0" ></th>        <th class="col_heading level0 col0" >Кол-во невозвратов</th>        <th class="col_heading level0 col1" >Кол-во заемщиков</th>        <th class="col_heading level0 col2" >% невозврата</th>    </tr>    <tr>        <th class="index_name level0" >total_income</th>        <th class="blank" ></th>        <th class="blank" ></th>        <th class="blank" ></th>    </tr></thead><tbody>
                <tr>
                        <th id="T_413929ba_3610_11eb_a05e_02420a39eb2elevel0_row0" class="row_heading level0 row0" >Высокий уровень зарплаты</th>
                        <td id="T_413929ba_3610_11eb_a05e_02420a39eb2erow0_col0" class="data row0 col0" >586</td>
                        <td id="T_413929ba_3610_11eb_a05e_02420a39eb2erow0_col1" class="data row0 col1" >7802</td>
                        <td id="T_413929ba_3610_11eb_a05e_02420a39eb2erow0_col2" class="data row0 col2" >7.51%</td>
            </tr>
            <tr>
                        <th id="T_413929ba_3610_11eb_a05e_02420a39eb2elevel0_row1" class="row_heading level0 row1" >Низкий уровень зарплаты</th>
                        <td id="T_413929ba_3610_11eb_a05e_02420a39eb2erow1_col0" class="data row1 col0" >614</td>
                        <td id="T_413929ba_3610_11eb_a05e_02420a39eb2erow1_col1" class="data row1 col1" >7470</td>
                        <td id="T_413929ba_3610_11eb_a05e_02420a39eb2erow1_col2" class="data row1 col2" >8.22%</td>
            </tr>
            <tr>
                        <th id="T_413929ba_3610_11eb_a05e_02420a39eb2elevel0_row2" class="row_heading level0 row2" >Средний уровень зарплаты</th>
                        <td id="T_413929ba_3610_11eb_a05e_02420a39eb2erow2_col0" class="data row2 col0" >541</td>
                        <td id="T_413929ba_3610_11eb_a05e_02420a39eb2erow2_col1" class="data row2 col1" >6182</td>
                        <td id="T_413929ba_3610_11eb_a05e_02420a39eb2erow2_col2" class="data row2 col2" >8.75%</td>
            </tr>
    </tbody></table>



### Вывод

Зависимости между уровнем дохода и возвратом кредита в срок практически нет, но тем не менее возможно что люди с высоким уровнем заработной платы допускаю больше просрочем связанно с тем что их финансовое положение позволяет им меньше беспокоится о штрафах и пенях за просрочку, но вполне возможно разница в цифрах является скорее всего статистической погрешностью

- Как разные цели кредита влияют на его возврат в срок?


```python
data_goal = data.pivot_table(index = ['purpose'], values = 'debt', aggfunc = [sum, 'count'])
#data_child = data.groupby(['children',]).agg({'debt':'sum','children':'count'})
data_goal['%_total_debt'] = (data_goal['sum','debt']/data_goal['count','debt'])
data_goal.columns = ['Кол-во невозвратов','Кол-во заемщиков','% невозврата']
data_goal.style.format({'% невозврата':'{:.2%}'})
#print(data_child)
```




<style  type="text/css" >
</style><table id="T_41444caa_3610_11eb_a05e_02420a39eb2e" ><thead>    <tr>        <th class="blank level0" ></th>        <th class="col_heading level0 col0" >Кол-во невозвратов</th>        <th class="col_heading level0 col1" >Кол-во заемщиков</th>        <th class="col_heading level0 col2" >% невозврата</th>    </tr>    <tr>        <th class="index_name level0" >purpose</th>        <th class="blank" ></th>        <th class="blank" ></th>        <th class="blank" ></th>    </tr></thead><tbody>
                <tr>
                        <th id="T_41444caa_3610_11eb_a05e_02420a39eb2elevel0_row0" class="row_heading level0 row0" >автомобиль</th>
                        <td id="T_41444caa_3610_11eb_a05e_02420a39eb2erow0_col0" class="data row0 col0" >403</td>
                        <td id="T_41444caa_3610_11eb_a05e_02420a39eb2erow0_col1" class="data row0 col1" >4306</td>
                        <td id="T_41444caa_3610_11eb_a05e_02420a39eb2erow0_col2" class="data row0 col2" >9.36%</td>
            </tr>
            <tr>
                        <th id="T_41444caa_3610_11eb_a05e_02420a39eb2elevel0_row1" class="row_heading level0 row1" >жилье</th>
                        <td id="T_41444caa_3610_11eb_a05e_02420a39eb2erow1_col0" class="data row1 col0" >308</td>
                        <td id="T_41444caa_3610_11eb_a05e_02420a39eb2erow1_col1" class="data row1 col1" >4460</td>
                        <td id="T_41444caa_3610_11eb_a05e_02420a39eb2erow1_col2" class="data row1 col2" >6.91%</td>
            </tr>
            <tr>
                        <th id="T_41444caa_3610_11eb_a05e_02420a39eb2elevel0_row2" class="row_heading level0 row2" >недвижимость</th>
                        <td id="T_41444caa_3610_11eb_a05e_02420a39eb2erow2_col0" class="data row2 col0" >474</td>
                        <td id="T_41444caa_3610_11eb_a05e_02420a39eb2erow2_col1" class="data row2 col1" >6351</td>
                        <td id="T_41444caa_3610_11eb_a05e_02420a39eb2erow2_col2" class="data row2 col2" >7.46%</td>
            </tr>
            <tr>
                        <th id="T_41444caa_3610_11eb_a05e_02420a39eb2elevel0_row3" class="row_heading level0 row3" >образование</th>
                        <td id="T_41444caa_3610_11eb_a05e_02420a39eb2erow3_col0" class="data row3 col0" >370</td>
                        <td id="T_41444caa_3610_11eb_a05e_02420a39eb2erow3_col1" class="data row3 col1" >4013</td>
                        <td id="T_41444caa_3610_11eb_a05e_02420a39eb2erow3_col2" class="data row3 col2" >9.22%</td>
            </tr>
            <tr>
                        <th id="T_41444caa_3610_11eb_a05e_02420a39eb2elevel0_row4" class="row_heading level0 row4" >свадьба</th>
                        <td id="T_41444caa_3610_11eb_a05e_02420a39eb2erow4_col0" class="data row4 col0" >186</td>
                        <td id="T_41444caa_3610_11eb_a05e_02420a39eb2erow4_col1" class="data row4 col1" >2324</td>
                        <td id="T_41444caa_3610_11eb_a05e_02420a39eb2erow4_col2" class="data row4 col2" >8.00%</td>
            </tr>
    </tbody></table>



### Вывод

Цели кредита влияют на его возврат в срок, кредиты на покупку жилья недвижимости а также свадьбы менее рискованные чем кредиты на покупку машины. Потому что решения на получения таких кредитов принимаються наиболее осознано.

### Шаг 4. Общий вывод

1. Обнаружены пропуски в столбцах days_employed и total_income, их количества равны. Возможно люди никогда не работали и на получали доход.Люди имеюшие пропуски в столбцах days_employed- общий трудовой стаж в днях и total_income-ежемесячный доход старше 18 лет,поэтому врядли они нигде не работали и не получали зарплату скорее всего мы имеем дело с ошибкой ввода данных. Поэтому заполним пропуски медианным значением.  Также обнаружены отрицательные значения в столбце  days_employed- общий трудовой стаж и в количестве детей, скорее всего ошибка внесения, были преобразованы в положительные тк они могут повлиять на медиану.  

**Рекомендую: обратить внимание операторов и разработчиков онлайн форм, необходим контроль за внесением данного типа данных.** 

2. Были обнаруженны  существенные всплески в значениях стажа. Существование стажа в 1 тысчу лет маловероятно, наличие таких цифр связано скорее всего с технической ошибкой , возможно обьеденили 2 базы в одной из которых стаж считался в часах или минутах поэтому всплески на заменил на медианные значения Средняя продолжительность жизни мужчин в 2019 году в России составила 68,5 лет. Для женщин показатель еще выше — 78,5 лет. Поэтому возьмем максимальное значение для замены на медиану 70 лет стажа это 25550 дней

**Рекомендую: обратить внимание на формат данных при обьединении баз данных.** 

3. В столбцах days_employed и total_income содержатся десятичные дроби до ста тысячных долей, а должны быть целочисленные тк зарплата в рублях и стаж в днях 

**Рекомендую: Возможно это возникает при преобразовании одного формата в другой. Возможно в каких то базах данных Стаж указывается в часах и зарплата в копейках, при преобразовании хорошо бы их приводить к целочисленному значению** 

4. В базе найдены дбликаты данных, но вопрос в том стоит ли их удалять мб это совпадения реальных клиентов, а может быть и нет. Но я думаю что при таком количестве различных данных для каждого клиента реальные совпадения врядли возможны, поэтому удалил дубликаты. Причина возникновения , возможно ошибка оператора который их вносил. Либо если обьеденялись несколько баз в одну были внесены одни и теже клиенты. Также обнаружены внесения данных в разных регистрах, все приведено к нижнему регистру для удобства сортировки , причина ошибка операторов при внесении.

**Рекомендую: обратить внимание операторов и разработчиков онлайн форм, необходим контроль за внесением данного типа данных.** 

5. Цели кредита лучше упростить до одного слова "свадьба, машина , недвижимость" это упростить и ускорит обработку

**Рекомендую: в анкетах заполняемых вручную и онлайн формах проставить готовые варианты ответов .** 

### При выдаче кредита необходимо:
1. Обращать внимание на семейное положения заемщика люди не побывавшие в браке более склонны допускать просрочки платежей.
2. Обращать внимание на доход заемщика люди с высоким уровнем дохода более склонны допускать просрочки платежей.
3. Обращать внимание на наличие детей у заемщика люди с длетьми более склонны допускать просрочки платежей.
4. Иметь ввиду что кредиты на покупку автомобиля и образование более рискованные чем на покупку недвижимости и свадьбы.

### Чек-лист готовности проекта

Поставьте 'x' в выполненных пунктах. Далее нажмите Shift+Enter.

- [x]  открыт файл;
- [x]  файл изучен;
- [x]  определены пропущенные значения;
- [x]  заполнены пропущенные значения;
- [x]  есть пояснение, какие пропущенные значения обнаружены;
- [x]  описаны возможные причины появления пропусков в данных;
- [x]  объяснено, по какому принципу заполнены пропуски;
- [x]  заменен вещественный тип данных на целочисленный;
- [x]  есть пояснение, какой метод используется для изменения типа данных и почему;
- [x]  удалены дубликаты;
- [x]  есть пояснение, какой метод используется для поиска и удаления дубликатов;
- [x]  описаны возможные причины появления дубликатов в данных;
- [x]  выделены леммы в значениях столбца с целями получения кредита;
- [x]  описан процесс лемматизации;
- [x]  данные категоризированы;
- [x]  есть объяснение принципа категоризации данных;
- [x]  есть ответ на вопрос: "Есть ли зависимость между наличием детей и возвратом кредита в срок?";
- [x]  есть ответ на вопрос: "Есть ли зависимость между семейным положением и возвратом кредита в срок?";
- [x]  есть ответ на вопрос: "Есть ли зависимость между уровнем дохода и возвратом кредита в срок?";
- [x]  есть ответ на вопрос: "Как разные цели кредита влияют на его возврат в срок?";
- [x]  в каждом этапе есть выводы;
- [x]  есть общий вывод.
