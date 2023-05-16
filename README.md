# Паттерны проектирования (design-patterns)

Основные паттерные проектированя.

## Стратегия (Strategy)

Поведенческий шаблон проектирования, предназначенный для определения семейства алгоритмов, инкапсуляции каждого из них и обеспечения их взаимозаменяемости. Это позволяет выбирать алгоритм путём определения соответствующего класса. Шаблон Strategy позволяет менять выбранный алгоритм независимо от объектов-клиентов, которые его используют.

### Задача

По типу клиента (или по типу обрабатываемых данных) выбрать подходящий алгоритм, который следует применить. Если используется правило, которое не подвержено изменениям, нет необходимости обращаться к шаблону «стратегия».

### Мотивы

+ Программа должна обеспечивать различные варианты алгоритма или поведения
+ Нужно изменять поведение каждого экземпляра класса
+ Необходимо изменять поведение объектов на стадии выполнения
+ Введение интерфейса позволяет классам-клиентам ничего не знать о классах, реализующих этот интерфейс и инкапсулирующих в себе конкретные алгоритмы

### Решение

Отделение процедуры выбора алгоритма от его реализации. Это позволяет сделать выбор на основании контекста.

### Участники

+ Класс Strategy определяет, как будут использоваться различные алгоритмы.
+ Конкретные классы ConcreteStrategy реализуют эти различные алгоритмы.
+ Класс Context использует конкретные классы ConcreteStrategy посредством ссылки на конкретный тип абстрактного класса Strategy. Классы Strategy и Context взаимодействуют с целью реализации выбранного алгоритма (в некоторых случаях классу Strategy требуется посылать запросы классу Context). Класс Context пересылает классу Strategy запрос, поступивший от его класса-клиента.

### Следствия

+ Шаблон Strategy определяет семейство алгоритмов.
+ Это позволяет отказаться от использования переключателей и/или условных операторов.
+ Вызов всех алгоритмов должен осуществляться стандартным образом (все они должны иметь одинаковый интерфейс).

### Реализация

Класс, который использует алгоритм (Context), включает абстрактный класс (Strategy), обладающий абстрактным методом, определяющим способ вызова алгоритма. Каждый производный класс реализует один требуемый вариант алгоритма.

Замечание: метод вызова алгоритма не должен быть абстрактным, если требуется реализовать некоторое поведение, принимаемое по умолчанию.

### Полезные сведения

+ и стратегия, и декоратор могут применяться для изменения поведения конкретных классов. Достоинство стратегии в том, что интерфейс кастомизации не совпадает с публичным интерфейсом и может быть куда более удобным, а недостаток в том, что для использования стратегии необходимо изначально проектировать класс с возможностью регистрации стратегий.

### Пример

```
// Класс реализующий конкретную стратегию, должен реализовывать этот интерфейс
// Класс контекста использует этот интерфейс для вызова конкретной стратегии
interface Strategy {
    int execute(int a, int b); 
}

// Реализуем алгоритм с использованием интерфейса стратегии
class ConcreteStrategyAdd implements Strategy {
 
    public int execute(int a, int b) {
        System.out.println("Called ConcreteStrategyAdd's execute()");
        return a + b;  // Do an addition with a and b
    }
}
 
class ConcreteStrategySubtract implements Strategy {
 
    public int execute(int a, int b) {
        System.out.println("Called ConcreteStrategySubtract's execute()");
        return a - b;  // Do a subtraction with a and b
    }
}
 
class ConcreteStrategyMultiply implements Strategy {
 
    public int execute(int a, int b) {
        System.out.println("Called ConcreteStrategyMultiply's execute()");
        return a * b;   // Do a multiplication with a and b
    }    
}

// Класс контекста использующий интерфейс стратегии
class Context {
 
    private Strategy strategy;
 
    // Constructor
    public Context() {
    }

    // Set new strategy
    public void setStrategy(Strategy strategy) {
        this.strategy = strategy;
    }

    public int executeStrategy(int a, int b) {
        return strategy.execute(a, b);
    }
}
 
// Тестовое приложение
class StrategyExample {
 
    public static void main(String[] args) {
 
        Context context = new Context();
 
        context.setStrategy(new ConcreteStrategyAdd());
        int resultA = context.executeStrategy(3,4);
 
        context.setStrategy(new ConcreteStrategySubtract());
        int resultB = context.executeStrategy(3,4);
 
        context.setStrategy(new ConcreteStrategyMultiply());
        int resultC = context.executeStrategy(3,4);

        System.out.println("Result A : " + resultA );
        System.out.println("Result B : " + resultB );
        System.out.println("Result C : " + resultC );
    }
}
```

## Заместитель (Proxy)

Структурный шаблон проектирования, предоставляющий объект, который контролирует доступ к другому объекту, перехватывая все вызовы (выполняет функцию контейнера).

### Задача

+ Необходимо контролировать доступ к объекту, не изменяя при этом поведение клиента. 

+ Необходимо иметь доступ к объекту так, чтобы не создавать реальные объекты непосредственно, а через другой объект, который может иметь дополнительную функциональность.

### Решение

Внедрить паттерн Заместитель, создать класс-прокси. Он реализует интерфейс сервисного класса, имитируя его поведение для клиентского кода. Таким образом вместо реального объекта клиент взаимодействует с его заместителем. Как правило, все запросы передаются далее сервисному классу, но с дополнительными действиями до или после его вызова.

Проще говоря, этот прокси-объект — прослойка между клиентским кодом и целевым объектом.

Шаблон Proxy может применяться в случаях работы с сетевым соединением, с огромным объектом в памяти (или на диске) или с любым другим ресурсом, который сложно или тяжело копировать. Хорошо известный пример применения — объект, подсчитывающий число ссылок.

### Виды

+ Протоколирующий прокси: сохраняет в лог все вызовы «Субъекта» с их параметрами.
+ Удалённый заместитель (англ. remote proxies): обеспечивает связь с «Субъектом», который находится в другом адресном пространстве или на удалённой машине. Также может отвечать за кодирование запроса и его аргументов и отправку закодированного запроса реальному «Субъекту»,
+ Виртуальный заместитель (англ. virtual proxies): обеспечивает создание реального «Субъекта» только тогда, когда он действительно понадобится. Также может кэшировать часть информации о реальном «Субъекте», чтобы отложить его создание,
+ Копировать-при-записи: обеспечивает копирование «субъекта» при выполнении клиентом определённых действий (частный случай «виртуального прокси»).
+ Защищающий заместитель (англ. protection proxies): может проверять, имеет ли вызывающий объект необходимые для выполнения запроса права.
+ Кэширующий прокси: обеспечивает временное хранение результатов расчёта до отдачи их множественным клиентам, которые могут разделить эти результаты.
+ Экранирующий прокси: защищает «Субъект» от опасных клиентов (или наоборот).
+ Синхронизирующий прокси: производит синхронизированный контроль доступа к «Субъекту» в асинхронной многопоточной среде.
+ «Умная» ссылка (англ. smart reference proxy): производит дополнительные действия, когда на «Субъект» создается ссылка, например, рассчитывает количество активных ссылок на «Субъект».

### Пример

```
public class Main {
	
	public static void main(String[] args) {
		// Create math proxy
		IMath p = new MathProxy();

		// Do the math
		System.out.println("4 + 2 = " + p.add(4, 2));
		System.out.println("4 - 2 = " + p.sub(4, 2));
		System.out.println("4 * 2 = " + p.mul(4, 2));
		System.out.println("4 / 2 = " + p.div(4, 2));
	}
}

/**
 * "Subject"
 */
public interface IMath {

	public double add(double x, double y);

	public double sub(double x, double y);

	public double mul(double x, double y);

	public double div(double x, double y);
}

/**
 * "Real Subject"
 */
public class Math implements IMath {

	public double add(double x, double y) {
		return x + y;
	}

	public double sub(double x, double y) {
		return x - y;
	}

	public double mul(double x, double y) {
		return x * y;
	}

	public double div(double x, double y) {
		return x / y;
	}
}

/**
 * "Proxy Object"
 */
public class MathProxy implements IMath {

    private Math math;

    public double add(double x, double y) {
        lazyInitMath();
        return math.add(x, y);
    }

    public double sub(double x, double y) {
        lazyInitMath();
        return math.sub(x, y);
    }

    public double mul(double x, double y) {
        lazyInitMath();
        return math.mul(x, y);
    }

    public double div(double x, double y) {
        lazyInitMath();
        return math.div(x, y);
    }

    private void lazyInitMath() {
        if (math == null) {
            math = new Math();
        }
    }
}
```

## Абстрактная фабрика (Abstract Factory)

Порождающий шаблон проектирования, предоставляет интерфейс для создания семейств взаимосвязанных или взаимозависимых объектов, не специфицируя их конкретных классов. Шаблон реализуется созданием абстрактного класса Factory, который представляет собой интерфейс для создания компонентов системы.

Другими словами, эта модель позволяет нам создавать объекты, которые следуют общему шаблону.

### Пример

```
// Создаем простой интерфейс Animal
public interface Animal {
    String getAnimal();
    String makeSound();
}

// Создаем конкретный класс Duck и наследуемся от интерфейса Animal
public class Duck implements Animal {

    @Override
    public String getAnimal() {
        return "Duck";
    }

    @Override
    public String makeSound() {
        return "Squeks";
    }
}

// Создаем простой интерфейс Colour
public interface Colour {
     String getColour();
}

// Создаем конкретный класс White и наследуемся от интерфейса Colour
public class White implements Colour {

    @Override
    public String getColour() {
        return "White";
    }
}

// Создаем интерфейс AbstractFactory
public interface AbstractFactory<T> {
    T create(String animalType) ;
}

// Реализуем шаблон Фабрика для Animal
public class AnimalFactory implements AbstractFactory<Animal> {

    @Override
    public Animal create(String animalType) {
        if ("Dog".equalsIgnoreCase(animalType)) {
            return new Dog();
        } else if ("Duck".equalsIgnoreCase(animalType)) {
            return new Duck();
        }

        return null;
    }
}

// Реализуем шаблон Фабрика для Colour
public class ColourFactory implements AbstractFactory<Colour> {

    @Override
    public Colour create(String colourType) {
        if ("White".equalsIgnoreCase(colourType)) {
            return new White();
        } else if ("Red".equalsIgnoreCase(colourType)) {
            return new Red();
        }

        return null;
    }
}

// Так будет выглядеть шаблон Абстрактной фабрики для наших фабрик Animal и Colour - по сути это фабрика фабрик
public class FactoryProvider {
    public static AbstractFactory getFactory(String choice){
        
        if("Animal".equalsIgnoreCase(choice)){
            return new AnimalFactory();
        }
        else if("Color".equalsIgnoreCase(choice)){
            return new ColorFactory();
        }
        
        return null;
    }
}
```

## Цепочка обязанностей (Chain of Responsibility)

Поведенческий шаблон проектирования, предназначенный для организации в системе уровней ответственности.

Состоит из источника командных объектов и ряда обрабатывающих объектов.

Каждый обрабатывающий объект в цепочке отвечает за определенный тип команды, и когда обработка завершена, он пересылает команду следующему процессору в цепочке.

Шаблон рекомендован для использования в условиях:

+ в разрабатываемой системе имеется группа объектов, которые могут обрабатывать сообщения определенного типа;
+ все сообщения должны быть обработаны хотя бы одним объектом системы;
+ сообщения в системе обрабатываются по схеме «обработай сам либо перешли другому», то есть одни сообщения обрабатываются на том уровне, где они получены, а другие пересылаются объектам иного уровня.

### Пример

Предположим, что мы пишем приложение для аутентификации пользователей.

```
// Создаем абстрактный базовый класс AuthenticationProcessor
public abstract class AuthenticationProcessor {

    public AuthenticationProcessor nextProcessor;
    
    // standard constructors

    public abstract boolean isAuthorized(AuthenticationProvider authProvider);
}

// Создаем конкретные классы и наследуемся от AuthenticationProcessor
public class OAuthProcessor extends AuthenticationProcessor {

    public OAuthProcessor(AuthenticationProcessor nextProcessor) {
        super(nextProcessor);
    }

    @Override
    public boolean isAuthorized(AuthenticationProvider authProvider) {
        if (authProvider instanceof OAuthTokenProvider) {
            return true;
        } else if (nextProcessor != null) {
            return nextProcessor.isAuthorized(authProvider);
        }
        
        return false;
    }
}

// В каждом классе мы переопределяем метод isAuthorized
public class UsernamePasswordProcessor extends AuthenticationProcessor {

    public UsernamePasswordProcessor(AuthenticationProcessor nextProcessor) {
        super(nextProcessor);
    }

    @Override
    public boolean isAuthorized(AuthenticationProvider authProvider) {
        if (authProvider instanceof UsernamePasswordProvider) {
            return true;
        } else if (nextProcessor != null) {
            return nextProcessor.isAuthorized(authProvider);
        }
    return false;
    }
}
```

### Когда применять

+ Когда программа должна обрабатывать разнообразные запросы несколькими способами, но заранеен не известно, какие именно запросы будут приходить и какие обработчики для них понадобяться.
+ Когда важно, чтобы обработчики обрабатывались один за другим в строгом порядке.
+ Когда набор объектов, способных обработать запрос, должен задаваться динамически.
