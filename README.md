# Задача "Понимание JVM"

## Код для исследования

```java
public class JvmComprehension {
    public static void main(String[] args) {
        int i = 1;                      // 1
        Object o = new Object();        // 2
        Integer ii = 2;                 // 3
        printAll(o, i, ii);             // 4
        System.out.println("finished"); // 7
    }

    private static void printAll(Object o, int i, Integer ii) {
        Integer uselessVar = 700;                   // 5
        System.out.println(o.toString() + i + ii);  // 6
    }
}
```

- Сначала осуществляется загрузка информации о классах (JvmComprehension.class, system classes и др.) в блок памяти
  Metaspace при помощи ClassLoader'ов.
- В StackMemory открывается фрейм main(). 
- //1 - Создается переменная типа int с именем 'i' и ей
  присваивается значение '1' (StackMemory). 
- //2 - Тут присутствуют три действия (присваивание =, new, конструктор).
  Сначала срабатывает оператор new, он выделяет в Heap память под объект Object. После этого отрабатывает конструктор
  Object, т.е. создается в "куче" (Heap) экземпляр объекта. После этого отрабатывается оператор присваивания, т.е.
  создается переменная 'o' в StackMemory, в которую присваивается ссылка на созданный в "куче" экземпляр объекта Object.
  
- //3 - В "куче" создается объект Integer, затем в StackMemory создается переменная 'ii', которой присваивается
  значение '2'. 
- //4 - Здесь происходит вызов нового метода, в StackMemory будет создан новый фрейм printAll(). При этом,
  т.к. передаются параметры (o, i, ii), будут созданы новые ссылки на созданные ранее объекты в "куче" (в пунктах
  1,2,3). 
- //5 - Создается в "куче" еще один объект Integer, затем в StackMemory (в фрейме printAll()) переменная '
  useless' с ссылкой (на созданный объект Integer), которой присваивается значение '700'. 
- //6 - Тут вызывается метод
  вывода на экран. Внутри printAll() создается фрейм System.out.println(), в котором будет производиться вся эта работа.
  Т.к. используются параметры, будут созданы ссылки на созданные ранее объекты. Кроме того, toString создаст в "куче"
  объект String. Затем происходит выход из фрейма System.out.println(), затем выход из фрейма printAll(). 
- //7 - новый
  фрейм System.out.println(), в куче новый объект String, в StackMemory ссылка на него и значение 'finished'. Выходим из
  фрейма.
- Когда выходим из очередного метода, фрейм помечается как ненужный, автоматически очищаются значимые типы и ссылки на
  объекты "кучи". В "куче" же объекты остаются. Для их очищения используется "Сборщик мусора", который определяет
  неиспользуемые объекты методом обхода графа достижимых объектов с учетом времени жизни объекта. Во время работы "
  Сборщика мусора" происходит приостановка работы программ. JVM сама определяет необходимость запуска "Сборщика мусора"
  в зависимости от памяти, ее потребления.. Мы этим процессом не управляем, только можем мониторить (с помощью
  VisualVM).