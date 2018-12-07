## JDK, JRE, JVM

### <a name="start"></a> Вопросы раздела 

- [x] [1. Понятия JDK, JRE, JVM.](#jdk) 

- [x] [2. JDK Tools](#tools)

- [x] [3. main() метод и как скомпилировать и запустить консольное приложение](#main)

- [x] [4. Что такое компилятор? Какие есть типы компиляторов? Как работает в java?](#compiler)

- [x] [5. Структура .class файла.](#class)


[К оглавлению](https://gitlab.com/ITokarev/java-core/blob/master/README.md)

---
### <a name="jdk"></a> 1. Понятия JDK, JRE, JVM. 

---
The Java Development Kit (JDK) is a software development environment used for developing Java applications and applets. 

The Java Runtime Environment provides the minimum requirements for executing a Java application; it consists of the Java Virtual Machine (JVM), core classes, and supporting files.


Java Virtual Machine is:

* A specification where working of Java Virtual Machine is specified. But implementation provider is independent to choose the algorithm. Its implementation has been provided by Sun and other companies.
* An implementation is a computer program that meets the requirements of the JVM specification
* Runtime Instance Whenever you write java command on the command prompt to run the java class, an instance of JVM is created.

![alt text](https://cdncontribute.geeksforgeeks.org/wp-content/uploads/JDK_JRE_JVM_x.jpg)

* JDK – Java Development Kit (in short JDK) is Kit which provides the environment to develop and execute(run) the Java program. JDK is a kit(or package) which includes two things
Development Tools(to provide an environment to develop your java programs)
JRE (to execute your java program).
* JRE – Java Runtime Environment (to say JRE) is an installation package which provides environment to only run(not develop) the java program(or application)onto your machine. JRE is only used by them who only wants to run the Java Programs i.e. end users of your system.
* JVM – Java Virtual machine(JVM) is a very important part of both JDK and JRE because it is contained or inbuilt in both. Whatever Java program you run using JRE or JDK goes into JVM and JVM is responsible for executing the java program line by line hence it is also known as interpreter.

#### What does JRE consists of?
JRE consists of the following components:

* Deployment technologies, including deployment, Java Web Start and Java Plug-in.
* User interface toolkits, including Abstract Window Toolkit (AWT), Swing, Java 2D, Accessibility, Image I/O, Print Service, Sound, drag and drop (DnD) and input methods.
* Integration libraries, including Interface Definition Language (IDL), Java Database Connectivity (JDBC), Java Naming and Directory Interface (JNDI), Remote Method Invocation (RMI), Remote Method Invocation Over Internet Inter-Orb Protocol (RMI-IIOP) and scripting.
* Other base libraries, including international support, input/output (I/O), extension mechanism, Beans, Java Management Extensions (JMX), Java Native Interface (JNI), Math, Networking, Override Mechanism, Security, Serialization and Java for XML Processing (XML JAXP).
* Lang and util base libraries, including lang and util, management, versioning, zip, instrument, reflection, Collections, Concurrency Utilities, Java Archive (JAR), Logging, Preferences API, Ref Objects and Regular Expressions.
* Java Virtual Machine (JVM), including Java HotSpot Client and Server Virtual Machines.

Реализации JVM - HotSpot, Avian.

[К оглавлению](#start)

---

### <a name="tools"></a> 2. JDK Tools. 

---

Standard JDK Tools and Utilities

* Basic Tools (appletviewer, apt, extcheck, jar, java, javac, javadoc, javah, javap, jdb)
* Security Tools (keytool, jarsigner, policytool, kinit, klist, ktab)
* Internationalization Tools (native2ascii)
* Remote Method Invocation (RMI) Tools (rmic, rmiregistry, rmid, serialver)
* Java IDL and RMI-IIOP Tools (tnameserv, idlj, orbd, servertool)
* Java Deployment Tools (javafxpackager, pack200, unpack200)
* Java Web Start Tools (javaws)
* Java Troubleshooting, Profiling, Monitoring and Management Tools (jcmd, jconsole, jmc, jvisualvm)
* Java Web Services Tools (schemagen, wsgen, wsimport, xjc)
 
[Documentation](https://docs.oracle.com/javase/7/docs/technotes/tools/index.html)

[К оглавлению](#start)

---

### <a name="main"></a> 3. main() метод и как скомпилировать и запустить консольное приложение. 

---

Для компиляции одного файла, используя консоль в директории с .java файлом:
```
javac New.java
```
Запуск из этой же директории:
```
java New
```

#### Указание интерпретатору где искать классы

Интерпретатору нужно знать все места, где он может найти классы. Что является таким местом? Директория и/или библиотека – jar-файл. Иначе говоря, если у вас файл javaee.jar находится в директории E:\java\lib\ – интерпретатору нужен полный путь к файлу, а именно – E:\java\lib\javaee.jar. Простого указания директории НЕДОСТАТОЧНО!

* Способ 1. Переменная окружения CLASSPATH. 
Заводится переменная окружения CLASSPATH, и в ней через разделитель (';' для Win, ':' для *NIX) прописываются все полные пути к файлам библиотек и к директориям, в которых лежат деревья классов. Директория – это точка, в которой лежит КОРЕНЬ всех скомпилированных классов. Т.е., если классы из пакета mypackage.test компилировались в директорию c:\myproject\classes, то в CLASSPATH должна фигурировать именно c:\myproject\classes, а не c:\myproject\classes\mypackage и не c:\myproject\classes\mypackage\test.
* Способ 2. Указание ключа -classpath интерпретатору (компилятору).
Пример. Допустим, в проектной директории есть поддиректория classes, в которой находятся скомпилированные классы, и поддиректория lib, в которой лежат библиотеки velocity-1.4.jar и log4j-1.2.8.jar. Тогда командная строка для запуска класса ru.skipy.myproject.Main будет выглядеть так:
```
java -classpath ./classes;./lib/velocity-1.4.jar;./lib/log4j-1.2.8.jar ru.skipy.myproject.Main
```
По пунктам: указываем интерпретатору, что классы надо искать в директории ./classes ('.' – текущая директория), в библиотеке ./lib/velocity-1.4.jar и в библиотеке ./lib/log4j-1.2.8.jar, в порядке их перечисления.

Если же мне не нужно использовать внешние библиотеки, то командная строка превращается в следующую:
```
java -classpath ./classes ru.skipy.myproject.Main
```

* Способ 3. Директория <JRE_INSTALLATION_DIRECTORY>/lib/ext .
Пригоден только для подключения jar-файлов. Файл кладется в указанную директорию. При старте виртуальной машины библиотека подключается.

#### Пример компиляции, запуска, создания .jar

 1. Создание структуры директорий
```
mkdir src
mkdir classes
cd src
mkdir mypackage
cd ..
```

 2. Создание исходного файла/файлов .java в директории src/mypackage. Допустим, что существует Main.java с методом main()
 3. Компиляция 
 ```
 javac -classpath ./classes -d ./classes src/mypackage/Main.java
 ```

 Ключ -d указывает компилятору, где находится корень для скомпилированных классов. Если не указать его, корнем будет считаться текущая директория. При компиляции нескольких классов в пакете одновременно можно вместо имени конкретного файла указать *.java.

 В результате компиляции в директории ./classes создается поддиректория mypackage, в которую помещается файл Main.class
 
 4. Выполнение программы.
 ```
 java -classpath ./classes mypackage.Main
 ```

Имя класса указывается вместе с пакетом! И указывается именно имя класса, а не файла! Т.е. .class в конце добавлять не надо.

Логика поиска файла с байткодом:

* Все точки в полном имени класса заменяются на разделители – '/'
* К полученной строке приписывается .class
* Файл ищется в библиотеках и указанных директориях со скомпилированными классами в порядке их указания. Файл ищется по полученному на прошлом шаге относительному пути.
Т.е. в нашем случае имя класса mypackage.Main преобразуется в относительный путь к файлу mypackage/Main.class. Виртуальная машина находит файл по данному относительному пути в директории ./classes.

 5. Создание архива (jar-файла).
 Собираем архив, который можно запускать с помощью команды java -jar <имя архива>. Для начала создаем файл manifest.mf, в котором будет указан главный исполняемый класс:
 ```
 Manifest-Version: 1.0
 Created-By: 1.6.0_19 (Sun Microsystems Inc.)
 Main-Class: mypackage.Main
 ```

Важный момент! После последней строки – в данном случае это указание Main-Class – должен быть перевод строки. Если его не будет – последняя строка не будет прочитана.

Выполняем команду по сборке архива:
```
jar cvmf manifest.mf main.jar -C ./classes mypackage
```

Порядок ключей важен! С каком порядке стоят m и f – в таком же порядке должны стоять имена manifest- и jar- файлов соответственно. manifest.mf – это имя файла, в котором мы указали исполняемый класс. В принципе, этот файл может называться как угодно, как правило, имя такое (так этот файл называется внутри jar-архива, и это имя уже фиксировано). main.jar – имя создаваемого архива. Конструкция "-C ./classes mypackage" означает "сменить директорию на classes и взять там директорию mypackage" (все команды выполняются из корневой директории проекта).

В результате должен быть создан файл main.jar со следующей структурой:

```
META-INF/MANIFEST.MF
mypackage/Main.class
```

 6. Выполнение программы запуском jar-файла.
 
 ```
 java -jar main.jar
 ```

Эту команду необязательно выполнять из той директории, где находится корень проекта. Можно указать полный путь, например, так:

```
java -jar c:\myprojects\Main\main.jar
```

Разница только в том, что текущей директорией приложения при запуске будет считаться та, из которой вы запустили приложение на выполнение. При использовании полного пути она может быть любой, это иногда бывает полезно.

Еще один важный момент!. При запуске приложения через jar-файл, командой java -jar ..., в classpath попадает только этот jar-файл. Никакие попытки указать ключ -classpath ни к чему не приведут. Однако не всё так страшно, необходимые библиотеки указать можно. Для этого необходимо в манифесте (manifest.mf) указать относительные пути к библиотекам. Т.е., если нам надо подключить, например, библиотеки velocity-1.4.jar и log4j-1.2.8.jar, лежащие в директории lib рядом с нашим jar-файлом, то в манифест надо добавить строчку:

```
Manifest-Version: 1.0
Created-By: 1.6.0_19 (Sun Microsystems Inc.)
Main-Class: mypackage.HelloWorld
Class-Path: lib/velocity-1.4.jar lib/log4j-1.2.8.jar
```

Указываются пути относительно jar-файла, разделитель – пробел. И в этом случае в classpath будет включен не только наш jar-файл, но и обе указанные библиотеки.

[Текст взят отсюда](http://www.skipy.ru/technics/likbez.html)

[К оглавлению](#start)

---
### <a name="compiler"></a> 4. Что такое компилятор? Какие есть типы компиляторов? Как работает в java? 

---

Компилятор — программа или техническое средство, выполняющее компиляцию.

Компиляция — трансляция программы, составленной на исходном языке высокого уровня, в эквивалентную программу на низкоуровневом языке, близком машинному коду (абсолютный код, объектный модуль, иногда на язык ассемблера). Входной информацией для компилятора (исходный код) является описание алгоритма или программы на предметно-ориентированном языке, а на выходе компилятора — эквивалентное описание алгоритма на машинно-ориентированном языке (объектный код).

Компилировать — проводить трансляцию машинной программы с предметно-ориентированного языка на машинно-ориентированный язык.

JIT-компиляция (англ. Just-in-time compilation, компиляция «на лету»), динамическая компиляция (англ. dynamic translation) — технология увеличения производительности программных систем, использующих байт-код, путём компиляции байт-кода в машинный код или в другой формат непосредственно во время работы программы. Таким образом достигается высокая скорость выполнения по сравнению с интерпретируемым байт-кодом (сравнимая с компилируемыми языками) за счёт увеличения потребления памяти (для хранения результатов компиляции) и затрат времени на компиляцию. JIT базируется на двух более ранних идеях, касающихся среды исполнения: компиляции байт-кода и динамической компиляции.

Так как JIT-компиляция является, по сути, одной из форм динамической компиляции, она позволяет применять такие технологии, как адаптивная оптимизация и динамическая рекомпиляция. Благодаря этому JIT-компиляция может показывать лучшие результаты в плане производительности, чем статическая компиляция. Интерпретация и JIT-компиляция особенно хорошо подходят для динамических языков программирования, при этом среда исполнения справляется с поздним связыванием типов и гарантирует безопасность исполнения.

Ahead-of-Time (AOT) компилятор — это вид транслятора, который использует метод компиляции перед исполнением (англ. ahead-of-time compilation). 

### How does JRE works?
To understand how the JRE works let us consider a Java source file saved as Example.java. The file is compiled into a set of Byte Code that is stored in a “.class” file. Here it will be “Example.class“.


![alt text](https://cdncontribute.geeksforgeeks.org/wp-content/uploads/JRE_JDK_JVM.jpg)

The following diagram depicts what is done at compile time.

The following actions occur at runtime.

* Class Loader.
The Class Loader loads all necessary classes needed for the execution of a program. It provides security by separating the namespaces of the local file system from that imported through the network. These files are loaded either from a hard disk, a network or from other sources.

* Byte Code Verifier.
The JVM puts the code through the Byte Code Verifier that checks the format and checks for an illegal code. Illegal code, for example, is code that violates access rights on objects or violates the implementation of pointers.

The Byte Code verifier ensures that the code adheres to the JVM specification and does not violate system integrity.

![alt text](https://cdncontribute.geeksforgeeks.org/wp-content/uploads/JRE_JDK_JVM_1.jpg)

* Intrepreter.
At runtime the Byte Code is loaded, checked and run by the interpreter. The interpreter has the following two functions:

 1. Execute the Byte Code
 2. Make appropriate calls to the underlying hardware
 
Both operations can be shown as:

![alt text](https://cdncontribute.geeksforgeeks.org/wp-content/uploads/JRE_JDK_JVM_2.jpg)

To understand the interactions between JDK and JRE consider the following diagram.

![alt text](https://cdncontribute.geeksforgeeks.org/wp-content/uploads/JRE_JDK_JVM_4.jpg)

[К оглавлению](#start)

---
### <a name="class"></a> 5. Структура .class файла. 

---

A class file consists of a single ClassFile structure:

```
ClassFile {
    u4             magic;
    u2             minor_version;
    u2             major_version;
    u2             constant_pool_count;
    cp_info        constant_pool[constant_pool_count-1];
    u2             access_flags;
    u2             this_class;
    u2             super_class;
    u2             interfaces_count;
    u2             interfaces[interfaces_count];
    u2             fields_count;
    field_info     fields[fields_count];
    u2             methods_count;
    method_info    methods[methods_count];
    u2             attributes_count;
    attribute_info attributes[attributes_count];
}
```

The items in the ClassFile structure are as follows:

* magic. The magic item supplies the magic number identifying the class file format; it has the value 0xCAFEBABE.

* minor_version, major_version. The values of the minor_version and major_version items are the minor and major version numbers of this class file. Together, a major and a minor version number determine the version of the class file format. If a class file has major version number M and minor version number m, we denote the version of its class file format as M.m. Thus, class file format versions may be ordered lexicographically, for example, 1.5 < 2.0 < 2.1. A Java Virtual Machine implementation can support a class file format of version v if and only if v lies in some contiguous range Mi.0 ≤ v ≤ Mj.m. The release level of the Java SE platform to which a Java Virtual Machine implementation conforms is responsible for determining the range.
Oracle's Java Virtual Machine implementation in JDK release 1.0.2 supports class file format versions 45.0 through 45.3 inclusive. JDK releases 1.1.* support class file format versions in the range 45.0 through 45.65535 inclusive. For k ≥ 2, JDK release 1.k supports class file format versions in the range 45.0 through 44+k.0 inclusive.

* constant_pool_count. The value of the constant_pool_count item is equal to the number of entries in the constant_pool table plus one. A constant_pool index is considered valid if it is greater than zero and less than constant_pool_count, with the exception for constants of type long and double.

* constant_pool[] The constant_pool is a table of structures representing various string constants, class and interface names, field names, and other constants that are referred to within the ClassFile structure and its substructures. The format of each constant_pool table entry is indicated by its first "tag" byte.
The constant_pool table is indexed from 1 to constant_pool_count-1.

* access_flags.
The value of the access_flags item is a mask of flags used to denote access permissions to and properties of this class or interface.

Table Class access and property modifiers

```
Flag Name	Value	Interpretation
ACC_PUBLIC	0x0001	Declared public; may be accessed from outside its package.
ACC_FINAL	0x0010	Declared final; no subclasses allowed.
ACC_SUPER	0x0020	Treat superclass methods specially when invoked by the invokespecial instruction.
ACC_INTERFACE	0x0200	Is an interface, not a class.
ACC_ABSTRACT	0x0400	Declared abstract; must not be instantiated.
ACC_SYNTHETIC	0x1000	Declared synthetic; not present in the source code.
ACC_ANNOTATION	0x2000	Declared as an annotation type.
ACC_ENUM	0x4000	Declared as an enum type.
```

* this_class. The value of the this_class item must be a valid index into the constant_pool table. The constant_pool entry at that index must be a CONSTANT_Class_info structure representing the class or interface defined by this class file.

* super_class. For a class, the value of the super_class item either must be zero or must be a valid index into the constant_pool table. If the value of the super_class item is nonzero, the constant_pool entry at that index must be a CONSTANT_Class_info structure representing the direct superclass of the class defined by this class file. Neither the direct superclass nor any of its superclasses may have the ACC_FINAL flag set in the access_flags item of its ClassFile structure.
If the value of the super_class item is zero, then this class file must represent the class Object, the only class or interface without a direct superclass.
For an interface, the value of the super_class item must always be a valid index into the constant_pool table. The constant_pool entry at that index must be a CONSTANT_Class_info structure representing the class Object.

* interfaces_count. The value of the interfaces_count item gives the number of direct superinterfaces of this class or interface type.

* interfaces[]. Each value in the interfaces array must be a valid index into the constant_pool table. The constant_pool entry at each value of interfaces[i], where 0 ≤ i < interfaces_count, must be a CONSTANT_Class_info structure representing an interface that is a direct superinterface of this class or interface type, in the left-to-right order given in the source for the type.

* fields_count. The value of the fields_count item gives the number of field_info structures in the fields table. The field_info structures represent all fields, both class variables and instance variables, declared by this class or interface type.

* fields[]. Each value in the fields table must be a field_info structure giving a complete description of a field in this class or interface. The fields table includes only those fields that are declared by this class or interface. It does not include items representing fields that are inherited from superclasses or superinterfaces.

* methods_count. The value of the methods_count item gives the number of method_info structures in the methods table.

* methods[]. Each value in the methods table must be a method_info structure giving a complete description of a method in this class or interface. If neither of the ACC_NATIVE and ACC_ABSTRACT flags are set in the access_flags item of a method_info structure, the Java Virtual Machine instructions implementing the method are also supplied.
The method_info structures represent all methods declared by this class or interface type, including instance methods, class methods, instance initialization methods, and any class or interface initialization method. The methods table does not include items representing methods that are inherited from superclasses or superinterfaces.

* attributes_count. The value of the attributes_count item gives the number of attributes in the attributes table of this class.

* attributes[]. Each value of the attributes table must be an attribute_info structure.

[DOC](https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-4.html)

[К оглавлению](#start)