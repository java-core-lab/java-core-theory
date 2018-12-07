##  Exceptions

### <a name="start"></a> Вопросы раздела

- [x] [1. Exceptions hierarchy](#hierarchy)

- [x] [2. Checked vs. Unchecked exceptions](#types)

- [x] [3. try-catch, throw, multi-catch and finally statements](#catch)

- [x] [4. try-with-resources and Auto-closeable interface](#resources)

[К оглавлению](https://gitlab.com/ITokarev/java-core/blob/master/README.md)


---

#### <a name="hierarchy"></a> 1. Exceptions hierarchy

---

![alt text](https://docstore.mik.ua/orelly/java/langref/figs/jlrf0901.gif)

Standard Java exception classes

![alt text](https://docstore.mik.ua/orelly/java/langref/figs/jlrf0902.gif)

Standard Java error classes

[More information](https://docstore.mik.ua/orelly/java/langref/ch09_04.htm)

[К оглавлению](#start)


---

#### <a name="types"></a> 2. Checked vs. Unchecked exceptions

---

Checked исключения, это те, которые должны обрабатываться блоком catch или описываться в сигнатуре метода. Unchecked могут не обрабатываться и не быть описанными.

Unchecked исключения в Java - наследованные от RuntimeException, checked - от Exception (не включая unchecked).
 
Пример unchecked исключения - NullPointerException, checked исключения - IOException

То есть если в вашем коде есть участок который может бросить checked исключение то вы обязаны либо заключить его в конструкцию try/catch либо объявить в сигнатуре метода "throws SomeException" но в данном случае обработка исключения делегируется на уровень выше. В любом случае его нужно будет перехватить. В противном случае программа просто не скомпилируется.

 * Throwable - проверяемое исключение
 * Error - не проверяемое
 * Exception - проверяемое
 * RuntimeException - не проверяемое

Все остальные исключения наследуют это свойство от данных классов. Например SQLException является наследником Exception и оно проверяемое. NullPointerException - наследник RuntimeException и оно не проверяемое. 

Так почему не все исключения являются проверяемыми? Дело в том, что если проверять каждое место, где теоретически может быть ошибка, то ваш код сильно разрастется, станет плохо читаемым. Например в любом месте, где происходит деление чисел, нужно было бы проверять на ArithmeticException, потому что возможно деление на ноль. Эту опцию(пере отлавливать не проверяемые исключения) создатели языка оставили программисту на его усмотрение.

1. RuntimeException
IndexOutOfBoundsException - выбрасывается, когда индекс некоторого элемента в структуре данных(массив/коллекция) не попадает в диапазон имеющихся индексов.
NullPointerException - ссылка на объект, к которому вы обращаетесь хранит null/
ClassCastException – Ошибка приведения типов. Всякий раз при приведении типов делается проверка на возможность приведения (проверка осуществляется с помощью instanceof.
ArithmeticException - бросается когда выполняются недопустимые арифметические операции, например деление на ноль.

2. Error
ThreadDeath - вызывается при неожиданной остановке потока посредством метода Thread.stop().
StackOverflowError - ошибка переполнение стека. Часто возникает в рекурсивных функциях из-за неправильного условия выхода.
OutOfMemoryError - ошибка переполнения памяти.

[К оглавлению](#start)

---

#### <a name="catch"></a> 3. try-catch, throw, multi-catch and finally statements

---

Одному блоку try может соответствовать сразу несколько блоков catch с разными классами исключений.

```java
class Main {  
    public static void main(String[] args) {  
         FileInputStream fis = null; 
try{ 
fis = new FileInputStream(fileName); 
}catch (FileNotFoundException ex){ 
System.out.println("Oops, FileNotFoundException caught"); 
}catch (IOException e) {  
System.out.println("IOEXCEOTION");  
} 
        }  
    }  
} 
```
Здесь если файл не найден, то будет выведено на экран "Oops, FileNotFoundException caught" и программа продолжит работать(в данном случае завершится), если же файл найден, но, например, он не доступен для записи то на экран будет выведено "IOEXCEOTION".
Важная особенность, последовательность блоков catch должна идти от частного к более общему. В противном случае будет ошибка компиляции.
```java
try{ 
fis = new FileInputStream(fileName); 
}catch (Exception ex){    
//... 
}catch (IOException e) { // <--- Ошибка 
//... 
} 
```

В Java 7 стала доступна новая конструкция, с помощью которой можно пере отлавливать несколько исключений одни блоком catch:

```java
try {  
 ... 
} catch( IOException | SQLException ex ) {  
  logger.log(ex); 
  throw ex; 
} 
```
Это удобно, если обработка ошибок не отличается.

[К оглавлению](#start)

---

#### <a name="resources"></a> 4. try-with-resources and Auto-closeable interface

---

The try-with-resources statement is a try statement that declares one or more resources. A resource is an object that must be closed after the program is finished with it. The try-with-resources statement ensures that each resource is closed at the end of the statement. Any object that implements java.lang.AutoCloseable, which includes all objects which implement java.io.Closeable, can be used as a resource.

The following example reads the first line from a file. It uses an instance of BufferedReader to read data from the file. BufferedReader is a resource that must be closed after the program is finished with it:
```java
static String readFirstLineFromFile(String path) throws IOException {
    try (BufferedReader br =
                   new BufferedReader(new FileReader(path))) {
        return br.readLine();
    }
}
```
In this example, the resource declared in the try-with-resources statement is a BufferedReader. The declaration statement appears within parentheses immediately after the try keyword. The class BufferedReader, in Java SE 7 and later, implements the interface java.lang.AutoCloseable. Because the BufferedReader instance is declared in a try-with-resource statement, it will be closed regardless of whether the try statement completes normally or abruptly (as a result of the method BufferedReader.readLine throwing an IOException).

Prior to Java SE 7, you can use a finally block to ensure that a resource is closed regardless of whether the try statement completes normally or abruptly. The following example uses a finally block instead of a try-with-resources statement:
```java
static String readFirstLineFromFileWithFinallyBlock(String path)
                                                     throws IOException {
    BufferedReader br = new BufferedReader(new FileReader(path));
    try {
        return br.readLine();
    } finally {
        if (br != null) br.close();
    }
}
```
However, in this example, if the methods readLine and close both throw exceptions, then the method readFirstLineFromFileWithFinallyBlock throws the exception thrown from the finally block; the exception thrown from the try block is suppressed. In contrast, in the example readFirstLineFromFile, if exceptions are thrown from both the try block and the try-with-resources statement, then the method readFirstLineFromFile throws the exception thrown from the try block; the exception thrown from the try-with-resources block is suppressed. In Java SE 7 and later, you can retrieve suppressed exceptions; see the section Suppressed Exceptions for more information.

You may declare one or more resources in a try-with-resources statement. The following example retrieves the names of the files packaged in the zip file zipFileName and creates a text file that contains the names of these files:

```java
public static void writeToFileZipFileContents(String zipFileName,
                                           String outputFileName)
                                           throws java.io.IOException {

    java.nio.charset.Charset charset =
         java.nio.charset.StandardCharsets.US_ASCII;
    java.nio.file.Path outputFilePath =
         java.nio.file.Paths.get(outputFileName);

    // Open zip file and create output file with 
    // try-with-resources statement

    try (
        java.util.zip.ZipFile zf =
             new java.util.zip.ZipFile(zipFileName);
        java.io.BufferedWriter writer = 
            java.nio.file.Files.newBufferedWriter(outputFilePath, charset)
    ) {
        // Enumerate each entry
        for (java.util.Enumeration entries =
                                zf.entries(); entries.hasMoreElements();) {
            // Get the entry name and write it to the output file
            String newLine = System.getProperty("line.separator");
            String zipEntryName =
                 ((java.util.zip.ZipEntry)entries.nextElement()).getName() +
                 newLine;
            writer.write(zipEntryName, 0, zipEntryName.length());
        }
    }
}
```
In this example, the try-with-resources statement contains two declarations that are separated by a semicolon: ZipFile and BufferedWriter. When the block of code that directly follows it terminates, either normally or because of an exception, the close methods of the BufferedWriter and ZipFile objects are automatically called in this order. Note that the close methods of resources are called in the opposite order of their creation.

The following example uses a try-with-resources statement to automatically close a java.sql.Statement object:

```java
public static void viewTable(Connection con) throws SQLException {


    String query = "select COF_NAME, SUP_ID, PRICE, SALES, TOTAL from COFFEES";

    try (Statement stmt = con.createStatement()) {
        ResultSet rs = stmt.executeQuery(query);

        while (rs.next()) {
            String coffeeName = rs.getString("COF_NAME");
            int supplierID = rs.getInt("SUP_ID");
            float price = rs.getFloat("PRICE");
            int sales = rs.getInt("SALES");
            int total = rs.getInt("TOTAL");

            System.out.println(coffeeName + ", " + supplierID + ", " + 
                               price + ", " + sales + ", " + total);
        }
    } catch (SQLException e) {
        JDBCTutorialUtilities.printSQLException(e);
    }
}
```
The resource java.sql.Statement used in this example is part of the JDBC 4.1 and later API.

Note: A try-with-resources statement can have catch and finally blocks just like an ordinary try statement. In a try-with-resources statement, any catch or finally block is run after the resources declared have been closed.

Suppressed Exceptions
An exception can be thrown from the block of code associated with the try-with-resources statement. In the example writeToFileZipFileContents, an exception can be thrown from the try block, and up to two exceptions can be thrown from the try-with-resources statement when it tries to close the ZipFile and BufferedWriter objects. If an exception is thrown from the try block and one or more exceptions are thrown from the try-with-resources statement, then those exceptions thrown from the try-with-resources statement are suppressed, and the exception thrown by the block is the one that is thrown by the writeToFileZipFileContents method. You can retrieve these suppressed exceptions by calling the Throwable.getSuppressed method from the exception thrown by the try block.

Classes That Implement the AutoCloseable or Closeable Interface
See the Javadoc of the AutoCloseable and Closeable interfaces for a list of classes that implement either of these interfaces. The Closeable interface extends the AutoCloseable interface. The close method of the Closeable interface throws exceptions of type IOException while the close method of the AutoCloseable interface throws exceptions of type Exception. Consequently, subclasses of the AutoCloseable interface can override this behavior of the close method to throw specialized exceptions, such as IOException, or no exception at all.
 
[К оглавлению](#start)