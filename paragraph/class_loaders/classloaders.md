## CLASS LOADERS

### <a name="start"></a> Вопросы раздела

- [x] [1. Class loading in Java. How do class loaders work? java.lang.ClassLoader](#loading)

- [x] [2. Types of built-in ClassLoaders. Where do they load from?](#types)

- [x] [3. Phases of class loading](#phases)

- [x] [4. Static vs. Dynamic class loading. Unloading of classes](#static-vs-dynamic)

- [x] [5. Common class loading exceptions](#exceptions)

- [x] [6. Class loading in webapps by the example of Apache Tomcat](#webapps)

[К оглавлению](https://gitlab.com/ITokarev/java-core/blob/master/README.md)


---

### <a name="loading"></a> 1. Class loading in Java. How do class loaders work? java.lang.ClassLoader

---
 
 Class loader - это стандартный **абстрактный** java класс  java.lang.ClassLoader.
 
 Предназначен для загрузки  классов  в  память JVM и помещения их в собственный  кэш. Но сам, непосредственно, их не загружает,  это делают его наследники. Когда JVM запрашивает класс, то вовлекает в работу функцию loadClass у Classloader’a путем указания полного имени класса. После чего функция loadClass вызывает метод findLoadedClass() и проверяет, был ли загружен класс или нет. Это требуется для избежания загрузки одного и того же класса много раз. Если класс не был загружен, то загрузка данного класса делегируется к родительскому ClassLoader’у . Если и родительский ClassLoader не нашел класс, то он вызывает метод findClass() который ищет класс  в файловой системе. Создать собственный загрузчик классов, можно унаследовавшись от класса ClassLoader и переопределив  метод loadClass(String name).
 
 Класс имеет следующие методы:
 
 * private byte[] loadClassFileData(String name): читает класс из файловой системы и возвращает его в виде массива байт.
 * private Class<?> getClass(String name): метод вызывает loadClassFileData() функцию, путем вовлечения родительского метода defineClass(). Результатом работы метода является сгенерированный объект типа Class.
 * public Class<?> loadClass(String name): метод , ответственен  за загрузку класса .
 * public CCLoader(ClassLoader parent): Конструктов, отвественный за установку родительского ClassLoader’a.

 [К оглавлению](#start)
 
---

### <a name="types"></a> 2. Types of built-in ClassLoaders. Where do they load from?

---

Различают три вида загрузчиков в Java. Это — базовый загрузчик (bootstrap), системный загрузчик (System Classloader), загрузчик расширений (Extension Classloader).

Bootstrap — реализован на уровне JVM и не имеет обратной связи со средой исполнения. Данным загрузчиком загружаются классы из директории $JAVA_HOME/lib. Т.е. rt.jar загружается именно базовым загрузчиком. Поэтому, попытка получения загрузчика у классов java.* всегда заканчиватся null'ом. Это объясняется тем, что все базовые классы загружены базовым загрузчиком, доступа к которому из управляемой среды нет.

Управлять загрузкой базовых классов можно с помощью ключа -Xbootclasspath, который позволяет переопределять наборы базовых классов.

System Classloader — системный загрузчик, реализованный уже на уровне JRE. В Sun JRE — это класс sun.misc.Launcher$AppClassLoader. Этим загрузчиком загружаются классы, пути к которым указаны в переменной окружения CLASSPATH.

Управлять загрузкой системных классов можно с помощью ключа -classpath или системной опцией java.class.path.

Extension Classloader — загрузчик расширений. Данный загрузчик загружает классы из директории $JAVA_HOME/lib/ext. В Sun JRE — это класс sun.misc.Launcher$ExtClassLoader.

Управлять загрузкой расширений можно с помощью системной опции java.ext.dirs.

Каждый загрузчик классов (кроме Bootstrap) имеет родительский загрузчик, и в большинстве случаев он запрашивает родительского загрузчика загрузить указанный класс, перед тем как попробовать загрузить его самостоятельно.
Существует так же явный способ инициировать загрузку требуемого класса. Явное инициирование выполняться с помощью методов ClassLoader.loadClass() или Class.forName(). Например явное инициирование используется при загрузке JDBC драйверов: Class.forName("oracle.jdbc.driver.OracleDriver");
Процесс загрузки имеет одно важное свойство, а именно делегирование (рисунок 1). Это позволяет загружать классы тем загрузчиком, который находится ближе всего к базовому в иерархии делегирования. Как следствие поиск классов будет происходить в источниках в порядке их доверия: сначала в библиотеке core API, потом в папке расширений, потом в локальных файлах classpath. 

![alt text](http://www.techjava.de/wp-content/uploads/JavaClassLoading/clhierarchy.gif)

***Иерархия class loaders***

Каждый загрузчик имеет свое пространство имен для создаваемых классов. Т.е. если классы одинаковы и находятся в одном пакете, но загружаются разными загрузчиками - они считаются разными. Таким образом, можно например, создать два объекта синглтона.

Рассмотрим процесс загрузки более детально. Пусть в систем исполнения встретилась декларация переменной пользовательского класс Student.

1. Системный загрузчик попытается поискать в кеше класс Student.
    * Если класс найден, загрузка окончена.
    * Если класс не найден, загрузка делегируется загрузчику расширений.
2. Загрузчик расширений попытается поискать в кеше класс Student.
    * Если класс найден, загрузка окончена.
    * Если класс не найден, загрузка делегируется базовому загрузчику.
3. Базовый загрузчик попытается поискать в кеше класс Student.
    * Если класс найден, загрузка окончена.
    * Если класс не найден, базовый загрузчик попытается его загрузить.
        * Если загрузка прошла успешно, она закончена
        * Иначе управление предается загрузчику раширений.
    * Загрузчик расширений пытается загрузить класс. 
        * Если загрузка прошла успешно, она закончена 
        * Иначе управление предается системному загрузчику.
    * Системный загрузчик пытается загрузить класс. 
        * Если загрузка прошла успешно, она закончена 
        * Иначе генерируется исключение java.lang.ClassNotFoundException.

 [К оглавлению](#start)

---

### <a name="phases"></a> 3. Phases of class loading


---

Процесс загрузки класса состоит из трех частей:

1. Loading
2. Linking
3. Initialization

***Loading*** – на этой фазе происходит поиск и физическая загрузка файла класса в определенном источнике (в зависимости от загрузчика). Этот процесс определяет базовое представление класса в памяти. На этом этапе такие понятия как методы, поля и т.д. пока не известны.

***Linking*** – процесс, который может быть разбит на 3 части:

 * ***Bytecode verification*** – происходит несколько проверок байт-кода на соответствие ряду зачастую нетривиальных требований определенных в спецификации JVM.
 * ***Class preparation*** – на этом этапе происходит подготовки структуры данных, отображающей поля, методы и реализованные интерфейсы, которые определены в классе.
 * ***Resolving*** – разрешение всех классов, которые ссылаются на текущий класс.

***Initialization*** – происходит выполнение статических инициализаторов определенных в классе. Таким образом, статические поля инициализируются стандартными значениями.

![alt text](http://www.techjava.de/wp-content/uploads/JavaClassLoading/clphases.gif)

***Процесс загрузки класса в JAVA***

[К оглавлению](#start)

---

### <a name="static-vs-dynamic"></a> 4. Static vs. Dynamic class loading. Unloading of classes.

---

Динамическая загрузка классов производится через метод Class.forName(String className) или с использованием ClassLoader-а. Динамическая загрузка классов имеет смысл, когда требуется загрузить класс во время выполнения программы, когда нужно заменить класс, изменив, например, какую-то логику, не рестартуя приложения. Иногда может понадобиться загружать классы удаленно, например по http.

#### Процесс выгрузки классов.

В большинстве случаев, жизненный цикл класса в виртуальной машине схож с жизненным циклом объекта. JVM загружает, связывает и инициализирует классы, позволяя программе пользоваться ими, и выгружает, когда в приложении они более не используется. Важно заметить, что выгрузка классов не работает в том случае, если класс был загружен Bootstrap загрузчиком.

Загруженные классы, несмотря на то, что являются полноценными Java-объектами, хранятся в особой системной области памяти, называемой permanent generation (сокращенно, PermGen) и управляемой сборщиком мусора.

Выгрузка классов является важной частью механизма работы JVM, поскольку Java программы могут динамически расширяться во время работы, загружая пользовательские классы и, тем самым, занимать много места в оперативной памяти. Держать классы в памяти, которые больше не будут использоваться, нет никакого смысла.

Конкретная политика выгрузки классов во многом зависит от реализации виртуальной машины JVM.

Поскольку классы хранятся в PermGen to GC может не очистить классы ссылки на которые были утрачены в рантайме без прямого вызова System.gc(), который впрочем, не гарантирует то что очистка памяти будет запущена(актуально для java 7, в 8-ой изменено)

[К оглавлению](#start)

---

### <a name="exceptions"></a> 5. Common class loading exceptions

---

При работе загрузчиков классов наиболее часто встречаются следующие исключительные ситуации:
 
* **ClassNotFoundException** , бросается, когда приложение пытается загрузиться класс по его названию (String) с помощью таких средств:
forName метод в классе Class.
findSystemClass метод в классе ClassLoader.
loadClass метод в классе ClassLoader.
Но класса с таким именем не существует.
 
* **NoClassDefFoundError** , бросается в таких случаях:
   * Когда архив, директория, или другой источник необходимых классов не был добавлен в источники текущего загрузчика классов или его предка.
   * Загрузчик-предок не был установлен корректно.
   
* **ClassCastException**
    It is thrown as a result of incompatible types being found in a type comparison
* **UnsatisfiedLinkError**
The class loader plays an important role in linking a native call to its appropriate native definition. An UnsatisfiedLinkError occurs during the resolving stage of the linking phase when a program tries to load an absent or misplaced native library.
* **ClassCircularityError** 
he JVM specification says a ClassCircularityError is thrown if:

A class or interface could not be loaded because it would be its own superclass or superinterface.
This error is thrown during the resolving stage of the linking phase. It is a slightly odd error because the Java compiler does not allow such a circular situation to arise. However, the error could occur if you were to separately compile classes and then bring them together. Imagine the following scenario. First, compile the classes in Listings 7 and 8:

Listing 1 A.java
```java
public class A extends B {
}
```
Listing 2 B.java
```java
public class B {
}
```
Then, separately compile the classes in Listings 9 and 10:

Listing 3 A.java
```java
public class A {
}
```

Listing 4 B.java
```java
public class B extends A {
}
```
Finally, take class A from Listing 1 and class B from Listing 4 and run an application that tries to load either A or B. This may seem like an unlikely situation, but something very much like it could occur in a complex system where many different parts are brought together.

Obviously, to fix this problem, you must avoid the cyclic class hierarchy.

* **ClassFormatError** The JVM specification states that a ClassFormatError is thrown if:                       
The binary data that purports to specify a requested compiled class or interface is malformed.
This exception is thrown during the verification stage of the linking phase of class loading. The binary data can be malformed if the bytecodes have been changed -- if the major or minor number has been changed, for instance. This could occur if the bytecodes had been deliberately hacked, for example, or if an error had occurred when transferring the class file over a network.
The only way to fix this problem is to obtain a corrected copy of the bytecodes, possibly by recompiling.

* **ExceptionInInitializerError** According to the JVM specification, an ExceptionInInitializer is thrown:
    * If an initializer completes abruptly by throwing some exception E, and if the class of E is not Error or one of its subclasses, then a new instance of the class ExceptionInInitializerError, with E as the argument, is created and used in place of E.
    * If the Java Virtual Machine attempts to create a new instance of the class ExceptionInInitializerError but is unable to do so because an Out-Of-Memory-Error occurs, then the OutOfMemoryError object is thrown instead.

[К оглавлению](#start)

---

### <a name="webapps"></a> 6. Class loading in webapps by the example of Apache Tomcat.

---

For web application servers this slightly differs. There are generally different class loader for each web app deployed in a web application server like tomcat. 
 
So for web apps class loading resource happens in following order -
 1. Bootstrap classes of your JVM (Core java classes)
 2. /WEB-INF/classes of your web application
 3. /WEB-INF/lib/*.jar of your web application
 4. System class loader classes (Tomcat / Classpath specific classes)
 5. Common class loader classes (classes common to all web apps)
 
But note if web application class loader is configured with delegate="true" then order is changed:

 1. Bootstrap classes of your JVM (Core java classes)
 2. System class loader classes (Tomcat / Classpath specific classes)
 3. Common class loader classes (classes common to all web apps)
 4. /WEB-INF/classes of your web application
 5. /WEB-INF/lib/*.jar of your web application

For more details you can check Apache Tomcat's Class Loader HOW-TO page.

Tomcat creates the following class loaders as it is initialized:

 * Bootstrap — This class loader contains the basic runtime classes provided by the Java Virtual Machine, plus any classes from JAR files present in the System Extensions directory ($JAVA_HOME/jre/lib/ext). Note: some JVMs may implement this as more than one class loader, or it may not be visible (as a class loader) at all.
 * System — This class loader is normally initialized from the contents of the CLASSPATH environment variable. All such classes are visible to both Tomcat internal classes, and to web applications. However, the standard Tomcat startup scripts ($CATALINA_HOME/bin/catalina.sh or %CATALINA_HOME%\bin\catalina.bat) totally ignore the contents of the CLASSPATH environment variable itself, and instead build the System class loader from the following repositories:
    *  $CATALINA_HOME/bin/bootstrap.jar — Contains the main() method that is used to initialize the Tomcat server, and the class loader implementation classes it depends on.
    * $CATALINA_BASE/bin/tomcat-juli.jar or $CATALINA_HOME/bin/tomcat-juli.jar — Logging implementation classes. These include enhancement classes to java.util.logging API, known as Tomcat JULI, and a package-renamed copy of Apache Commons Logging library used internally by Tomcat. See logging documentation for more details.
    * If tomcat-juli.jar is present in $CATALINA_BASE/bin, it is used instead of the one in $CATALINA_HOME/bin. It is useful in certain logging configurations
    * $CATALINA_HOME/bin/commons-daemon.jar — The classes from Apache Commons Daemon project. This JAR file is not present in the CLASSPATH built by catalina.bat|.sh scripts, but is referenced from the manifest file of bootstrap.jar.
 * Common — This class loader contains additional classes that are made visible to both Tomcat internal classes and to all web applications.
 * Normally, application classes should NOT be placed here. The locations searched by this class loader are defined by the common.loader property in $CATALINA_BASE/conf/catalina.properties. The default setting will search the following locations in the order they are listed:
    * unpacked classes and resources in $CATALINA_BASE/lib
    * JAR files in $CATALINA_BASE/lib
    * unpacked classes and resources in $CATALINA_HOME/lib
    * JAR files in $CATALINA_HOME/lib
 * By default, this includes the following:
    * annotations-api.jar — JavaEE annotations classes.
    * catalina.jar — Implementation of the Catalina servlet container portion of Tomcat.
    * catalina-ant.jar — Tomcat Catalina Ant tasks.
    * catalina-ha.jar — High availability package.
    * catalina-tribes.jar — Group communication package.
    * ecj-*.jar — Eclipse JDT Java compiler.
    * el-api.jar — EL 2.2 API.
    * jasper.jar — Tomcat Jasper JSP Compiler and Runtime.
    * jasper-el.jar — Tomcat Jasper EL implementation.
    * jsp-api.jar — JSP 2.2 API.
    * servlet-api.jar — Servlet 3.0 API.
    * tomcat-api.jar — Several interfaces defined by Tomcat.
    * tomcat-coyote.jar — Tomcat connectors and utility classes.
    * tomcat-dbcp.jar — Database connection pool implementation based on package-renamed copy of Apache Commons Pool and Apache Commons DBCP.
    * tomcat-i18n-**.jar — Optional JARs containing resource bundles for other languages. As default bundles are also included in each individual JAR, they can be safely removed if no internationalization of messages is needed.
    * tomcat-jdbc.jar — An alternative database connection pool implementation, known as Tomcat JDBC pool. See documentation for more details.
    * tomcat-util.jar — Common classes used by various components of Apache Tomcat.
    * tomcat7-websocket.jar — WebSocket 1.1 implementation
    * websocket-api.jar — WebSocket 1.1 API
 * WebappX — A class loader is created for each web application that is deployed in a single Tomcat instance. All unpacked classes and resources in the /WEB-INF/classes directory of your web application, plus classes and resources in JAR files under the /WEB-INF/lib directory of your web application, are made visible to this web application, but not to other ones.
 
 [К оглавлению](#start)