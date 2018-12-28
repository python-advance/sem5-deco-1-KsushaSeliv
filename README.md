# sem5-deco-1

<h1>Инвариантное задание</h1>

2.1 Разработать прототип программы «Калькулятор», позволяющую выполнять базовые арифметические действия и функцию обертку, сохраняющую название выполняемой операции, аргументы и результат в файл
2.2 Дополнение программы «Калькулятор» декоратором, сохраняющий действия, которые выполняются в файл-журнал.
2.3 Рефакторинг (модификация) программы с декоратором модулем functools

```python
import urllib.request #для URL
from xml.etree import ElementTree as ET #для XML файлов

def nerv(func):
  import functools #сборник функций высокого уровня: взаимодействующих с другими функциями или возвращающие другие функции
  import datetime #модуль datetime предоставляет классы для обработки времени и даты разными способами
  @functools.wraps(func) #оборачиваем функцию func
  def wrapper(calc):
      t = datetime.datetime.now()
      result = func(calc)
      t = datetime.datetime.now() - t

      with open('Record.txt', 'a') as f:
      
            f.write("Переводим " + str(calc) + " " + str(in_type) + " в " + str(convert_to) + "\n")
            f.write("Результат: " + str(result) + " " + str(convert_to) +"\n")
            f.write("Время выполнения декорируемой функции " +  str(t))
      return result
  return wrapper  

print ("Конвертер")
calc = float(input("Сумма для перевода: "))
in_type = input("Из какой валюты выполняется конвертирование введённой суммы (rubl, dollar, euro, iena):")
convert_to = input("В какую валюту перевести введённую сумму(rubl, dollar, euro, iena): ")

id_dollar = "R01235" #USD
id_euro = "R01239" #EUR
id_iena = "R01820" #GBP

valuta = ET.parse(urllib.request.urlopen("http://www.cbr.ru/scripts/XML_daily.asp")) #данные с сайта

@nerv #применяем наш декоратор
def convert(calc):
  for line in valuta.findall('Valute'):
    id_v = line.get('ID')
    if id_v == id_dollar:
        rub1 = line.find('Value').text
        print (rub1)
    elif id_v == id_euro:
        rub2 = line.find('Value').text
        print (rub2)
    elif id_v == id_iena:
        rub3 = line.find('Value').text
        print (rub3)
       
  rub1 = float(rub1.replace(',', '.'))
  rub2 = float(rub2.replace(',', '.'))
  rub3 = float(rub3.replace(',', '.'))

  if in_type == "rubl":
    if convert_to == "rubl":
      result = float (calc)
    elif convert_to == "dollar":
      result = float (calc) / rub1
    elif convert_to == "euro":
      result = float (calc) / rub2
    elif convert_to == "iena":
      result = float (calc) / rub3
   
  elif in_type == "dollar":  
    if convert_to == "rubl":
      result = float (calc) * rub1
    elif convert_to == "dollar":
      result = float (calc)
    elif convert_to == "euro":
      result = (float (calc) * rub1) / rub2
    elif convert_to == "iena":
      result = (float (calc) * rub1) / rub3

  elif in_type == "euro":  
    if convert_to == "rubl":
      result = float (calc) * rub2
    elif convert_to == "dollar":
      result = (float (calc) * rub2) / rub1
    elif convert_to == "euro":
      result = float (calc)
    elif convert_to == "iena":
      result = (float (calc) * rub2) / rub3

  elif in_type == "iena":  
    if convert_to == "rubl":
      result = float (calc) * rub3
    elif convert_to == "dollar":
      result = (float (calc) * rub3) / rub1
    elif convert_to == "euro":
      result = (float (calc) * rub3) / rub2
    elif convert_to == "iena":
      result = float (calc)     
  return result

result = convert(calc)

print (calc, in_type, " = ", result, convert_to)
```

![alt](https://github.com/python-advance/sem5-deco-1-KsushaSeliv/blob/master/21.JPG)

<h1>Вариантное задание</h1>
2.3 Разработка функции-декоратора, вычисляющей время выполнения декорируемой функции.

Есть у нас две функции, и вдруг захотелось нам вычислить, какая из них вычислится быстрее.

```python
from datetime import datetime

def one():
  start = datetime.now()
  l=[]
  for i in range(10**4):
    if i % 2 == 0:
      l.append(i)
  print(datetime.now()-start) 
  return l

def two():
  start = datetime.now()
  l = [x for x in range (10**4) if x % 2 ==0]
  print(datetime.now()-start) 
  return l

l1 = one()
l2 = two()

print(one)
print(two)
```
Вот только прописывать несколько раз start = datetime.now() и print(datetime.now()-start)  не очень хочется, поэтому сделаем ка мы декоратор.

```python
from datetime import datetime

def timeit(func):
  def wrapper():
    start = datetime.now()
    result= func()
    print(datetime.now()-start) 
    return result
  return wrapper

@timeit
def one():
  #start = datetime.now()
  l=[]
  for i in range(10**4):
    if i % 2 == 0:
      l.append(i)
  #print(datetime.now()-start) 
  return l

@timeit
def two():
  #start = datetime.now()
  l = [x for x in range (10**4) if x % 2 ==0]
  #print(datetime.now()-start) 
  return l

l1 = one()
l2 = two()

print(one)
print(two)
```

![alt](https://github.com/python-advance/sem5-deco-1-KsushaSeliv/blob/master/20.JPG)

