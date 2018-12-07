## Generics and Collections

### <a name="start"></a> Вопросы раздела

- [x] [1. Working with Generics](#intro)

- [x] [2. Using Lists, Sets, Maps and Queues](#collections)

- [x] [3. Comparator vs. Comparable](#compare)

- [x] [4. Searching and sorting](#sort)


[К оглавлению](https://gitlab.com/ITokarev/java-core/blob/master/README.md)


---

### <a name="intro"></a> 1. Working with Generics

---

Невозможно создать массив параметра типа:
```java
T[] t = new T[10]; // Ошибка !
```
Невозможно создать массив Generic-классов:

```java
List<?>[] la = new List<?>[10]; // Ошибка !!
```

Уничтожение информации о типе

```java
List l = new ArrayList<String>();
```


Дженерики необходимы для более строгой типизации, они отрабатывают на этапе компиляции, в байт коде всё по старому.  Вайлд кард раскрывается в Object. 

Конвенция по именованию дженериков: 

 * E - Element (used extensively by the Java Collections Framework)
 * K - Key
 * N - Number
 * T - Type
 * V - Value
 * S,U,V etc. - 2nd, 3rd, 4th types

Сырым типом называется дженерик без явного указания типа <T>. 
При ограничении дженерика <T extends Integer> можно использовать методы класса, которым мы ограничились. 
Можно использовать множественное ограничение <T extends B1 & B2 & B3>. Если какой-то тип является классом, то он должен быть указан первым. B2 - class то, B2 & B1 & B3 (может указываться только 1 класс).

Конструкторы также могут быть типизированы new MyClass<Integer>("").

“?” -  unbounded wildcard, означает неизвестный тип. Его нельзя использовать в качестве аргумента метода, при создании класса или супертипа. Его часто используют когда нужно произвести операцию через методы Object или с переменной class. 

[К оглавлению](#start)

---

### <a name="collections"></a> 2. Using Lists, Sets, Maps and Queues

---

В библиотеке коллекций Java существует два базовых интерфейса, реализации которых и представляют совокупность всех классов коллекций:

1. Collection - коллекция содержит набор объектов (элементов). Здесь определены основные методы для манипуляции с данными, такие как вставка (add, addAll), удаление (remove, removeAll, clear), поиск (contains)
2. Map -  описывает коллекцию, состоящую из пар "ключ — значение". У каждого ключа только одно значение, что соответствует математическому понятию однозначной функции или отображения (тар). Такую коллекцию часто называют еще словарем (dictionary) или ассоциативным массивом (associative array). Никак НЕ относится к интерфейсу Collection и является самостоятельным.

Интерфейс Collection расширяют интерфейсы List, Set и Queue. 
   1. List - Представляет собой неупорядоченную коллекцию, в которой допустимы дублирующие значения. Иногда их называют последовательностями (sequence ). Элементы такой коллекции пронумерованы, начиная от нуля, к ним можно обратиться по индексу. 
   2. Set - описывает неупорядоченную коллекцию, не содержащую повторяющихся элементов. Это соответствует математическому понятию множества (set).
   3. Queue - очередь. Это коллекция, предназначенная для хранения элементов в порядке, нужном для их обработки. В дополнение к базовым операциям интерфейса Collection, очередь предоставляет дополнительные операции вставки, получения и контроля.
    
Интерфейс Map соотносит уникальные ключи со значениями. Ключ — это объект, который вы используете для последующего извлечения данных. Задавая ключ и значение, вы можете помещать значения в объект карты. После того как это значение сохранено, вы можете получить его по ключу. Интерфейс Map — это обобщенный интерфейс, объявленный так, как показано ниже.
 
 interface Мар<К, V>
 
 Здесь К указывает тип ключей, а V — тип хранимых значений.   
 
 [К оглавлению](#start)
 
---

### <a name="compare"></a> 3. Comparator vs. Comparable

---

Для того, чтобы объекты созданного класса можно было сравнить и сортировать, они должны реализовывать интерфейс Comparable<E>. При применении интерфейса он типизируется текущим классом.
Интерфейс Comparable содержит один единственный метод int compareTo(E item), который сравнивает текущий объект с объектом, переданным в качестве параметра. Если этот метод возвращает отрицательное число, то текущий объект будет располагаться перед тем, который передается через параметр. Если метод вернет положительное число, то, наоборот, после второго объекта. Если метод возвратит ноль, значит, оба объекта равны.

Однако перед нами может возникнуть проблема, что если разработчик не реализовал в своем классе, который мы хотим использовать, интерфейс Comparable, либо реализовал, но нас не устраивает его функциональность, и мы хотим ее переопределить? На этот случай есть еще более гибкий способ, предполагающий применение интерфейса Comparator<E>.

Интерфейс Comparator содержит ряд методов, ключевым из которых является метод compare():
```java
public interface Comparator<E> {
 
    int compare(T a, T b);
    
}
```
Метод compare также возвращает числовое значение - если оно отрицательное, то объект a предшествует объекту b, иначе - наоборот. А если метод возвращает ноль, то объекты равны. Для применения интерфейса нам вначале надо создать класс компаратора, который реализует этот интерфейс(или создать функцию с помощью лямбда выражения):

```java
class PersonComparator implements Comparator<Person>{
 
    public int compare(Person a, Person b){
     
        return a.getName().compareTo(b.getName());
    }
}
```
Лямбда :
```java
PersonComporator<Person> comporator = (a,b)->a.getName().compareTo(b.getName());
```

C JAVA 8 Comparator является функциональным интерфейсом. 

[К оглавлению](#start)

---

### <a name="sort"></a> 4. Searching and sorting

--- 

Начиная с JDK 8 в механизм работы компараторов были внесены некоторые дополнения. В частности, теперь мы можем применять сразу несколько компараторов по принципу приоритета. Например, изменим класс Person следующим образом:

```java
class Person{
      
    private String name;
    private int age;
    public Person(String n, int a){
          
        name=n;
        age=a;
    }
    String getName(){return name;}
    int getAge(){return age;}
}
```

Здесь добавлено поле для хранения возраста пользователя. И, допустим, нам надо отсортировать пользователей по имени и по возрасту. Для этого определим два компаратора:

```java
class PersonNameComparator implements Comparator<Person>{
  
    public int compare(Person a, Person b){
      
        return a.getName().compareTo(b.getName());
    }
}
class PersonAgeComparator implements Comparator<Person>{
  
    public int compare(Person a, Person b){
      
        if(a.getAge()> b.getAge())
            return 1;
        else if(a.getAge()< b.getAge())
            return -1;
        else
            return 0;
    }
}
```

Интерфейс компаратора определяет специальный метод по умолчанию thenComparing, который позволяет использовать цепочки компараторов для сортировки набора:

```java
Comparator<Person> pcomp = new PersonNameComparator().thenComparing(new PersonAgeComparator());
TreeSet<Person> people = new TreeSet(pcomp);
people.add(new Person("Tom", 23));
people.add(new Person("Nick",34));
people.add(new Person("Tom",10));
people.add(new Person("Bill",14));
 
for(Person  p : people){
 
    System.out.println(p.getName() + " " + p.getAge());
}
```

Консольный вывод :

```java
Bill 14
Nick 34
Tom 10
Tom 23
```

#### Sorting algorithms

#### Пузырьковая сортировка

| Сложность  |  Наилучший случай  | В среднем             | Наихудший случай |
|:----------:|:------------------:|:---------------------:|:----------------:|
| Время      | O(n)               |  O( n<sup>2</sup> )   | O(n<sup>2</sup>) |
| Память     | O(1)               |  O(1)                 | O(1)             |

Сортировка пузырьком — это самый простой алгоритм сортировки. Он проходит по массиву несколько раз, на каждом этапе перемещая самое большое значение из неотсортированных в конец массива.

Например, у нас есть массив целых чисел:

![data_structures_036.jpg](https://gitlab.com/ITokarev/java-core/raw/master/src/main/java/by/tokarev/core/paragraph/generics/img/data_structures_036.jpg)

При первом проходе по массиву мы сравниваем значения 3 и 7. Поскольку 7 больше 3, мы оставляем их как есть. После чего сравниваем 7 и 4. 4 меньше 7, поэтому мы меняем их местами, перемещая семерку на одну позицию ближе к концу массива. Теперь он выглядит так:

![data_structures_037.jpg](https://gitlab.com/ITokarev/java-core/raw/master/src/main/java/by/tokarev/core/paragraph/generics/img/data_structures_037.jpg)

Этот процесс повторяется до тех пор, пока семерка не дойдет почти до конца массива. В конце она сравнивается с элементом 8, которое больше, а значит, обмена не происходит. После того, как мы обошли массив один раз, он выглядит так:

![data_structures_038.jpg](https://gitlab.com/ITokarev/java-core/raw/master/src/main/java/by/tokarev/core/paragraph/generics/img/data_structures_038.jpg)

Поскольку был совершен по крайней мере один обмен значений, нам нужно пройти по массиву еще раз. В результате этого прохода мы перемещаем на место число 6.

![data_structures_039.jpg](https://gitlab.com/ITokarev/java-core/raw/master/src/main/java/by/tokarev/core/paragraph/generics/img/data_structures_039.jpg)

И снова был произведен как минимум один обмен, а значит, проходим по массиву еще раз.

При следующем проходе обмена не производится, что означает, что наш массив отсортирован, и алгоритм закончил свою работу.

```java
public class ExampleBubbleSort {
  
    public static void main(String[] args) {
  
        int[] num = { 3,6,1,7,2,8,10,4,9,5};
        int n = num.length;
         
        bubbleSort(num);
         
        for (int h = 0; h < n; h++)
            System.out.print(num[h]+ " ");
    }
  
    public static void bubbleSort(int[] numbers) {
  
        int n = numbers.length;
        int temp = 0;
  
        for (int i = 0; i < n; i++) {
            for (int j = 1; j < (n - i); j++) {
  
                if (numbers[j - 1] > numbers[j]) {
                    //swap elements
                    temp = numbers[j - 1];
                    numbers[j - 1] = numbers[j];
                    numbers[j] = temp;
                }
            }
        }
    }
}

```

#### Сортировка вставками

| Сложность  |  Наилучший случай  | В среднем             | Наихудший случай |
|:----------:|:------------------:|:---------------------:|:----------------:|
| Время      | O(n)               |  O( n<sup>2</sup> )   | O(n<sup>2</sup>) |
| Память     | O(1)               |  O(1)                 | O(1)             |

Сортировка вставками работает, проходя по массиву и перемещая нужное значение в начало массива. После того, как обработана очередная позиция, мы знаем, что все позиции до нее отсортированы, а после нее — нет.

Важный момент: сортировка вставками обрабатывает элементы массива по порядку. Поскольку алгоритм проходит по элементам слева направо, мы знаем, что все, что слева от текущего индекса — уже отсортировано. На этом рисунке показано, как увеличивается отсортированная часть массива с каждым проходом:

![data_structures_040.jpg](https://gitlab.com/ITokarev/java-core/raw/master/src/main/java/by/tokarev/core/paragraph/generics/img/data_structures_040.jpg)

Постепенно отсортированная часть массива растет, и, в конце концов, массив окажется упорядоченным.

Давайте взглянем на конкретный пример. Вот наш неотсортированный массив, который мы будем использовать:

![data_structures_041.jpg](https://gitlab.com/ITokarev/java-core/raw/master/src/main/java/by/tokarev/core/paragraph/generics/img/data_structures_041.jpg)

Алгоритм начинает работу с индекса 0 и значения 3. Поскольку это первый индекс, массив до него включительно считается отсортированным.

Далее мы переходим к числу 7. Поскольку 7 больше, чем любое значение в отсортированной части, мы переходим к следующему элементу.

На этом этапе элементы с индексами 0..1 отсортированы, а про элементы с индексами 2..n ничего не известно.

Следующим проверяется значение 4. Так как оно меньше семи, мы должны перенести его на правильную позицию в отсортированную часть массива. 

Итак, мы нашли индекс 1 (между значениями 3 и 7). Теперь массив выглядит так:

![data_structures_042.jpg](https://gitlab.com/ITokarev/java-core/raw/master/src/main/java/by/tokarev/core/paragraph/generics/img/data_structures_042.jpg)

Теперь часть массива, начиная от нулевого элемента и заканчивая элементом с индексом 2, отсортирована. Следующий проход начинается с индекса 3 и значения 4. По мере работы алгоритма мы продолжаем делать такие вставки.

![data_structures_043.jpg](https://gitlab.com/ITokarev/java-core/raw/master/src/main/java/by/tokarev/core/paragraph/generics/img/data_structures_043.jpg)

Когда больше нет возможностей для вставок, массив считается полностью отсортированным, и работа алгоритма закончена.

```
private void insertionSort(int[] elements) {
    for (int i = 1; i < elements.length; i++) {
        int key = elements[i];
        int j = i - 1;
        while (j >= 0 && key < elements[j]) {
            elements[j + 1] = elements[j];
            j--;
        }// end while loop
        elements[j + 1] = key;
    }// end for loop
}
```


#### Сортировка выбором

| Сложность  |  Наилучший случай  | В среднем             | Наихудший случай |
|:----------:|:------------------:|:---------------------:|:----------------:|
| Время      | O(n)               |  O( n<sup>2</sup> )   | O(n<sup>2</sup>) |
| Память     | O(1)               |  O(1)                 | O(1)             |

Сортировка выбором — это некий гибрид между пузырьковой и сортировкой вставками. Как и сортировка пузырьком, этот алгоритм проходит по массиву раз за разом, перемещая одно значение на правильную позицию. Однако, в отличие от пузырьковой сортировки, он выбирает наименьшее неотсортированное значение вместо наибольшего. Как и при сортировке вставками, упорядоченная часть массива расположена в начале, в то время как в пузырьковой сортировке она находится в конце.

Давайте посмотрим на работу сортировки выбором на нашем неотсортированном массиве.

![data_structures_044.jpg](https://gitlab.com/ITokarev/java-core/raw/master/src/main/java/by/tokarev/core/paragraph/generics/img/data_structures_044.jpg)

При первом проходе алгоритм пытается найти наименьшее значение в массиве и переместить его в начало.

Имея такой маленький массив, мы сразу можем сказать, что наименьшее значение — 3, и оно уже находится на правильной позиции. На этом этапе мы знаем, что на первой позиции в массиве (индекс 0) находится самое маленькое значение, следовательно, начало массива уже отсортировано. Поэтому мы начинаем второй проход — на этот раз по индексам от 1 до n — 1.

На втором проходе мы определяем, что наименьшее значение — 4. Мы меняем его местами со вторым элементом, семеркой, после чего 4 встает на свою правильную позицию.

![data_structures_045.jpg](https://gitlab.com/ITokarev/java-core/raw/master/src/main/java/by/tokarev/core/paragraph/generics/img/data_structures_045.jpg)

Теперь неотсортированная часть массива начинается с индекса 2. Она растет на один элемент при каждом проходе алгоритма. Если на каком-либо проходе мы не сделали ни одного обмена, это означает, что массив отсортирован.

После еще двух проходов алгоритм завершает свою работу:

![data_structures_046.jpg](https://gitlab.com/ITokarev/java-core/raw/master/src/main/java/by/tokarev/core/paragraph/generics/img/data_structures_046.jpg)

```java
public class ExampleSelectionSort {
  
    public static void main(String[] args) {       
  
        int[] numbers = { 3, 6, 1, 7, 2, 8, 10, 4, 9, 5 };
        int n = numbers.length;
         
        selectionSort(numbers, 0, n - 1);
  
        for (int i = 0; i < n; i++)
            System.out.print(numbers[i] + " ");
    }
  
    // Selection sort algorithm
    public static void selectionSort(int[] numbers, int low, int high) {
        for (int h = low; h <= high; h++)
            swap(numbers, h, getSmallest(numbers, h, high));
    }
  
    // Get the position of the smallest value from numbers[low] to numbers[high]
    public static int getSmallest(int[] numbers, int low, int high) {
        int small = low;
        for (int i = low + 1; i <= high; i++)
            if (numbers[i] < numbers[small])
                small = i;
        return small;
    }
  
    // swap numbers
    public static void swap(int[] numbers, int i, int j) {
        int temp = numbers[i];
        numbers[i] = numbers[j];
        numbers[j] = temp;
    } 
  
}
```

#### Сортировка слиянием

| Сложность  |  Наилучший случай  | В среднем             | Наихудший случай |
|:----------:|:------------------:|:---------------------:|:----------------:|
| Время      | O(n* log *n)       |  O(n* log *n)         | O(n* log *n)     |
| Память     | O(n)               |  O(n)                 | O(n)             |

При сортировке слиянием мы разделяем массив пополам до тех пор, пока каждый участок не станет длиной в один элемент. Затем эти участки возвращаются на место (сливаются) в правильном порядке.

Стоит отметить, что в отличие от линейных алгоритмов сортировки, сортировка слиянием будет делить и склеивать массив вне зависимости от того, был он отсортирован изначально или нет. Поэтому, несмотря на то, что в худшем случае он отработает быстрее, чем линейный, в лучшем случае его производительность будет ниже, чем у линейного. Поэтому сортировка слиянием — не самое лучшее решение, когда надо отсортировать частично упорядченный массив.

[Example with using ExecutorService and Fork/Join API](https://gitlab.com/ITokarev/java-core/tree/master/src/main/java/by/tokarev/core/multithreading)

#### Быстрая сортировка

| Сложность  |  Наилучший случай  | В среднем             | Наихудший случай   |
|:----------:|:------------------:|:---------------------:|:------------------:|
| Время      | O(n* log *n)       |  O(n* log *n)         | O(n <sup>2</sup> ) |
| Память     | O(1)               |  O(1)                 | O(1)               |

Быстрая сортировка — это еще один алгоритм типа «разделяй и властвуй». Он работает, рекурсивно повторяя следующие шаги:

* Выбрать ключевой индекс и разделить по нему массив на две части. Это можно делать разными способами, но в данной статье мы используем случайное число.
* Переместить все элементы больше ключевого в правую часть массива, а все элементы меньше ключевого — в левую. Теперь ключевой элемент находится в правильной позиции — он больше любого элемента слева и меньше любого элемента справа.
* Повторяем первые два шага, пока массив не будет полностью отсортирован.

```java
public class ExampleQuickSort {
  
    public static void main(String[] args) {
  
        int[] numbers = {3,6,1,7,2,8,10,4,9,5};
        int n = numbers.length;
        quicksort(numbers, 0, n-1);
         
        for (int h = 0; h < n; h++)
            System.out.print(numbers[h]+ " ");
    }
  
    // Quick sort algorithm
    public static void quicksort(int[] numbers, int low, int high) {
        if (low < high) {
            int dp = partition(numbers, low, high);
            quicksort(numbers, low, dp-1);
            quicksort(numbers, dp+1, high);
            }
    }
     
  
    // partition numbers[low] to numbers[high] using numbers[low] as the pivot
    private static int partition(int[] numbers, int low, int high) {
        int pivot = numbers[low];
        int i = low;
        for (int j = low + 1; j <= high; j++)
        if (numbers[j] < pivot) {
        ++i;
        swap(numbers, i, j);
        }
        //end for
        swap(numbers, low, i);
        return i; 
    } 
  
    // Exchange list[i] and list[j] values
    private static void swap(int[] list, int i, int j) {
        int temp = list[i];
        list[i] = list[j];
        list[j] = temp;
    }
}
```

[К оглавлению](#start)