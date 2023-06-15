#python 

- Скелет
``` python
def main():
    print("Hello World!")

if __name__ == "__main__":
    main()
```
- Выделить голову из списка
``` python
head, *tail = [1, 2, 3, 4, 5]
print(head)  # 1
print(tail)  # [2, 3, 4, 5]
head, *middle, tail = [1, 2, 3, 4, 5]
print(tail)    # 5
```
- Сделать из нескольких словарей один (Распаковка)
``` python
dictionary1 = {"red":"красный", "blue":"синий"}
dictionary2 = {"green":"зеленый", "yellow":"желтый"}
 
# распаковываем словари
dictionary3 = {**dictionary1, **dictionary2}
print(dictionary3)  # {'red': 'красный', 'blue': 'синий', 'green': 'зеленый', 'yellow': 'желтый'}
```