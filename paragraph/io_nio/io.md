## Java Core: IO/NIO.2

###  <a name="start"></a> Вопросы раздела

- [x] [1. File system, Files and Directories](#file-system)

- [x] [2. Class File. Working with File object](#class-file)

- [x] [3. Streams (java.io package)](#streams)

- [x] [4. Serialization](#serialization)

- [x] [5. Class Path](#class-path)

- [x] [6. Files attributes](#attributes)

- [x] [7. Comparing File and NIO.2.](#nio2)

[К оглавлению](https://gitlab.com/ITokarev/java-core/blob/master/README.md)

---

#### <a name="file-system"></a> 1. File system, Files and Directories

---

#### File system

A file is a collection of related information that is recorded on secondary storage. Or file is a collection of logically related entities. From user’s perspective a file is the smallest allotment of logical secondary storage.

FILE DIRECTORIES:
Collection of files is a file directory. 

The directory contains information about the files, including attributes, location and ownership. Much of this information, especially that is concerned with storage, is managed by the operating system. The directory is itself a file, accessible by various file management routines.

Information contained in a device directory are:

* Name
* Type
* Address
* Current length
* Maximum length
* Date last accessed
* Date last updated
* Owner id
* Protection information

Operation performed on directory are:

* Search for a file
* Create a file
* Delete a file
* List a directory
* Rename a file
* Traverse the file system

Advantages of maintaining directories are:

* Efficiency: A file can be located more quickly.
* Naming: It becomes convenient for users as two users can have same name for different files or may have different name for same file.
* Grouping: Logical grouping of files can be done by properties e.g. all java programs, all games etc.

#### SINGLE-LEVEL DIRECTORY

In this a single directory is maintained for all the users.

* Naming problem: Users cannot have same name for two files.
* Grouping problem: Users cannot group files according to their need.

![SINGLE-LEVEL DIRECTORY](https://cdncontribute.geeksforgeeks.org/wp-content/uploads/directory.jpg)

#### TWO-LEVEL DIRECTORY
In this separate directories for each user is maintained.
     
* Path name:Due to two levels there is a path name for every file to locate that file.
* Now,we can have same file name for different user.
* Searching is efficient in this method.

![TWO-LEVEL DIRECTORY](https://cdncontribute.geeksforgeeks.org/wp-content/uploads/directory-user-level.jpg)

#### TREE-STRUCTURED DIRECTORY
Directory is maintained in the form of a tree. Searching is efficient and also there is grouping capability. We have absolute or relative path name for a file.

![TREE-STRUCTURED DIRECTORY](https://cdncontribute.geeksforgeeks.org/wp-content/uploads/directory-tree-format.jpg)

#### FILE ALLOCATION METHODS

#### Continuous Allocation 
A single continuous set of blocks is allocated to a file at the time of file creation. Thus, this is a pre-allocation strategy, using variable size portions. The file allocation table needs just a single entry for each file, showing the starting block and the length of the file. This method is best from the point of view of the individual sequential file. Multiple blocks can be read in at a time to improve I/O performance for sequential processing. It is also easy to retrieve a single block. For example, if a file starts at block b, and the ith block of the file is wanted, its location on secondary storage is simply b+i-1.

![Continuous Allocation](https://cdncontribute.geeksforgeeks.org/wp-content/uploads/directory-file-allocation.jpg)

Disadvantage

* External fragmentation will occur, making it difficult to find contiguous blocks of space of sufficient length. Compaction algorithm will be necessary to free up additional space on disk.
* Also, with pre-allocation, it is necessary to declare the size of the file at the time of creation.

#### Linked Allocation(Non-contiguous allocation) 

Allocation is on an individual block basis. Each block contains a pointer to the next block in the chain. Again the file table needs just a single entry for each file, showing the starting block and the length of the file. Although pre-allocation is possible, it is more common simply to allocate blocks as needed. Any free block can be added to the chain. The blocks need not be continuous. Increase in file size is always possible if free disk block is available. There is no external fragmentation because only one block at a time is needed but there can be internal fragmentation but it exists only in the last disk block of file.

Disadvantage:

* Internal fragmentation exists in last disk block of file.
* There is an overhead of maintaining the pointer in every disk block.
* If the pointer of any disk block is lost, the file will be truncated.
* It supports only the sequencial access of files.

#### Indexed Allocation

It addresses many of the problems of contiguous and chained allocation. In this case, the file allocation table contains a separate one-level index for each file: The index has one entry for each block allocated to the file. Allocation may be on the basis of fixed-size blocks or variable-sized blocks. Allocation by blocks eliminates external fragmentation, whereas allocation by variable-size blocks improves locality. This allocation technique supports both sequential and direct access to the file and thus is the most popular form of file allocation.

![Indexed Allocation](https://cdncontribute.geeksforgeeks.org/wp-content/uploads/directory-indexing.jpg)

### Disk Free Space Management

Just as the space that is allocated to files must be managed ,so the space that is not currently allocated to any file must be managed. To perform any of the file allocation techniques,it is necessary to know what blocks on the disk are available. Thus we need a disk allocation table in addition to a file allocation table.The following are the approaches used for free space management.

* Bit Tables : This method uses a vector containing one bit for each block on the disk. Each entry for a 0 corresponds to a free block and each 1 corresponds to a block in use.
For example: 00011010111100110001
In this vector every bit correspond to a particular vector and 0 implies that, that particular block is free and 1 implies that the block is already occupied. A bit table has the advantage that it is relatively easy to find one or a contiguous group of free blocks. Thus, a bit table works well with any of the file allocation methods. Another advantage is that it is as small as possible.

* Free Block List : In this method, each block is assigned a number sequentially and the list of the numbers of all free blocks is maintained in a reserved block of the disk.

[Original text](https://www.geeksforgeeks.org/file-system-operating-systems/)

[К оглавлению](#start)

---

#### <a name="class-file"></a> 2. Class File. Working with File object.

---

#### Class File

В отличие от большинства классов ввода/вывода, класс File работает не с потоками, а непосредственно с файлами. Данный класс позволяет получить информацию о файле: права доступа, время и дата создания, путь к каталогу. А также осуществлять навигацию по иерархиям подкаталогов.

Класс java.io.File может представлять имя определённого файла, а также имена группы файлов, находящихся в каталоге. Если класс представляет каталог, то его метод list() возвращает массив строк с именами всех файлов.

Для создания объектов класса File можно использовать один из следующих конструкторов.

```
File(File dir, String name) - указывается объекта класса File (каталог) и имя файла 

File(String path) - указывается путь к файлу без указания имени файла

File(String dirPath, Sring name) - указывается путь к файлу и имя файла

File(URI uri) - указывается объекта URI, описывающий файл
```

#### Методы класса File

Класс File может использоваться для создания каталога или дерева каталогов. Также можно узнать свойства файлов (размер, дату последнего изменения, режим чтения/записи), определить к какому типу (файл или каталог) относится объект File, удалить файл.
 
У класса очень много методов, перечислим некоторые: 
```
getAbsolutePath() - абсолютный путь файла, начиная с корня системы. 
В Android корневым элементом является символ слеша (/)

canRead() - доступно для чтения

canWrite() - доступно для записи

exists() - файл существует или нет

getName() - возвращает имя файла

getParent() - возвращает имя родительского каталога

getPath() - путь

lastModified() - дата последнего изменения

isFile() - объект является файлом, а не каталогом

isDirectory - объект является каталогом

isAbsolute() - возвращает true, если файл имеет абсолютный путь

renameTo(File newPath) - переименовывает файл. 

В параметре указывается имя нового имени файла. Если переименование прошло неудачно, то возвращается false

delete() - удаляет файл. Также можно удалить пустой каталог
```

#### Каталог

Каталог - это объект класса File, который содержит список других файлов и каталогов. После создания объекта класса File, являющего каталогом, его метод isDirectory() вернёт значение true. И тогда вы можете вызывать метод list().

Для создания каталога можно использовать метод mkdir(), который вернёт true в успешном случае. Если указанный путь уже существует или каталог нельзя создать из-за отсутствия полного пути к нему, то вернётся false.

Метод mkdirs() создаёт сам каталог, так и всех его родителей.

#### Список каталогов

Если вы хотите получить содержимое каталога, то можно вызвать метод list() без аргументов. Вам вернётся полный список (массив) имён файлов и каталогов, содержащихся в данном каталоге. Есть ещё похожий метод listFiles(), который возвращает массив файлов (объектов, а не их имён), о котором поговорим отдельно.

```
String dirPath = "/";
File file = new File(dirPath);
File[] files = file.listFiles();
```

Если вам нужен только список файлов с расширением .java, то можно использовать специальный фильтр-класс, который описывает критерии отбора объектов File с помощью интерфейса FilenameFilter.

```
String[] list(FilenameFilter filter)
```

Интерфейс FilenameFilter определяет единственный метод accept(), вызываемый по одному разу с каждым файлом в списке.

```
boolean accept(File dir, String filename)
```

Метод возвращает true для файлов каталога, указанного в dir, которые должны быть включены в список, и false - для файлов, которые следует исключить.

#### Метод listFiles()

Метод имеет несколько перегруженных версий. Он возвращает список файлов в виде массива объектов класса File. Одна из версий метода также использует интерфейс FilenameFilter. Другая версия использует интерфейс FileFilter и возвращает те файлы, путевые имена которых соответствует интерфейсу.


[К оглавлению](#start)

---

#### <a name="streams"></a> 3. Streams (java.io package).

---

Programs read inputs from data sources (e.g., keyboard, file, network, memory buffer, or another program) and write outputs to data sinks (e.g., display console, file, network, memory buffer, or another program). In Java standard I/O, inputs and outputs are handled by the so-called streams. A stream is a sequential and contiguous one-way flow of data (just like water or oil flows through the pipe). It is important to mention that Java does not differentiate between the various types of data sources or sinks (e.g., file or network) in stream I/O. They are all treated as a sequential flow of data. Input and output streams can be established from/to any data source/sink, such as files, network, keyboard/console or another program. The Java program receives data from a source by opening an input stream, and sends data to a sink by opening an output stream. All Java I/O streams are one-way (except the RandomAccessFile, which will be discussed later). If your program needs to perform both input and output, you have to open two streams - an input stream and an output stream.

Stream I/O operations involve three steps:

* Open an input/output stream associated with a physical device (e.g., file, network, console/keyboard), by constructing an appropriate I/O stream instance.
* Read from the opened input stream until "end-of-stream" encountered, or write to the opened output stream (and optionally flush the buffered output).
* Close the input/output stream.

Java's I/O operations is more complicated than C/C++ to support internationalization (i18n). Java internally stores characters (char type) in 16-bit UCS-2 character set. But the external data source/sink could store characters in other character set (e.g., US-ASCII, ISO-8859-x, UTF-8, UTF-16, and many others), in fixed length of 8-bit or 16-bit, or in variable length of 1 to 4 bytes. As a consequence, Java needs to differentiate between byte-based I/O for processing raw bytes or binary data, and character-based I/O for processing texts made up of characters.

![Streams](http://www.ntu.edu.sg/home/ehchua/programming/java/images/IO_StreamVsCharacter.png)

#### Byte-Based I/O & Byte Streams

Byte streams are used to read/write raw bytes serially from/to an external device. All the byte streams are derived from the abstract superclasses InputStream and OutputStream, as illustrated in the class diagram.

![Byte streams diagram](http://www.ntu.edu.sg/home/ehchua/programming/java/images/IO_InputOutputStreams.png)

The abstract superclass InputStream declares an abstract method read() to read one data-byte from the input source:
```
public abstract int read() throws IOException
```
The read() method:

* returns the input byte read as an int in the range of 0 to 255, or
* returns -1 if "end of stream" condition is detected, or
* throws an IOException if it encounters an I/O error.

The read() method returns an int instead of a byte, because it uses -1 to indicate end-of-stream.

The read() method blocks until a byte is available, an I/O error occurs, or the "end-of-stream" is detected. The term "block" means that the method (and the program) will be suspended. The program will resume only when the method returns.

Two variations of read() methods are implemented in the InputStream for reading a block of bytes into a byte-array. It returns the number of bytes read, or -1 if "end-of-stream" encounters.

Similar to the input counterpart, the abstract superclass OutputStream declares an abstract method write() to write a data-byte to the output sink. write() takes an int. The least-significant byte of the int argument is written out; the upper 3 bytes are discarded. It throws an IOException if I/O error occurs (e.g., output stream has been closed).
```
public void abstract void write(int unsignedByte) throws IOException
```
Similar to the read(), two variations of the write() method to write a block of bytes from a byte-array are implemented:
```
public void write(byte[] bytes, int offset, int length) throws IOException
// Write "length" number of bytes, from the bytes array starting from offset of index.
public void write(byte[] bytes) throws IOException
// Same as write(bytes, 0, bytes.length)
```

You open an I/O stream by constructing an instance of the stream. Both the InputStream and the OutputStream provides a close() method to close the stream, which performs the necessary clean-up operations to free up the system resources.
```
public void close() throws IOException  // close this Stream
```

In addition, the OutputStream provides a flush() method to flush the remaining bytes from the output buffer.
```
public void flush() throws IOException  // Flush the output
```

InputStream and OutputStream are abstract classes that cannot be instantiated. You need to choose an appropriate concrete subclass to establish a connection to a physical device. For example, you can instantiate a FileInputStream or FileOutputStream to establish a stream to a physical disk file.

The I/O streams are often layered or chained with other I/O streams, for purposes such as buffering, filtering, or data-format conversion (between raw bytes and primitive types). For example, we can layer a BufferedInputStream to a FileInputStream for buffered input, and stack a DataInputStream in front for formatted data input (using primitives such as int, double), as illustrated in the following diagrams.

![layering](http://www.ntu.edu.sg/home/ehchua/programming/java/images/IO_LayeredInput.png)

The read()/write() method in InputStream/OutputStream are designed to read/write a single byte of data on each call. This is grossly inefficient, as each call is handled by the underlying operating system (which may trigger a disk access, or other expensive operations). Buffering, which reads/writes a block of bytes from the external device into/from a memory buffer in a single I/O operation, is commonly applied to speed up the I/O.

FileInputStream/FileOutputStream is not buffered. It is often chained to a BufferedInputStream or BufferedOutputStream, which provides the buffering. To chain the streams together, simply pass an instance of one stream into the constructor of another stream. For example, the following codes chain a FileInputStream to a BufferedInputStream, and finally, a DataInputStream:

```
FileInputStream fileIn = new FileInputStream("in.dat");
BufferedInputStream bufferIn = new BufferedInputStream(fileIn);
DataInputStream dataIn = new DataInputStream(bufferIn);
// or
DataInputStream in = new DataInputStream(
                        new BufferedInputStream(
                           new FileInputStream("in.dat")));
```

The DataInputStream and DataOutputStream can be stacked on top of any InputStream and OutputStream to parse the raw bytes so as to perform I/O operations in the desired data format, such as int and double.

To use DataInputStream for formatted input, you can chain up the input streams as follows:
```
DataInputStream in = new DataInputStream(
                        new BufferedInputStream(
                           new FileInputStream("in.dat")));
```
DataInputStream implements DataInput interface, which provides methods to read formatted primitive data and String, such as:
```
// 8 Primitives
public final int readInt() throws IOExcpetion;       // Read 4 bytes and convert into int
public final double readDoube() throws IOExcpetion;  // Read 8 bytes and convert into double 
public final byte readByte() throws IOExcpetion;
public final char readChar() throws IOExcpetion;
public final short readShort() throws IOExcpetion;
public final long readLong() throws IOExcpetion;
public final boolean readBoolean() throws IOExcpetion;    // Read 1 byte. Convert to false if zero
public final float readFloat() throws IOExcpetion;
 
public final int readUnsignedByte() throws IOExcpetion;   // Read 1 byte in [0, 255] upcast to int
public final int readUnsignedShort() throws IOExcpetion;  // Read 2 bytes in [0, 65535], same as char, upcast to int
public final void readFully(byte[] b, int off, int len) throws IOException;
public final void readFully(byte[] b) throws IOException;
 
// Strings
public final String readLine() throws IOException;
     // Read a line (until newline), convert each byte into a char - no unicode support.
public final String readUTF() throws IOException;
    // read a UTF-encoded string with first two bytes indicating its UTF bytes length
 
public final int skipBytes(int n)  // Skip a number of bytes
```
Similarly, you can stack the DataOutputStream as follows:
```
DataOutputStream out = new DataOutputStream(
                          new BufferedOutputStream(
                             new FileOutputStream("out.dat")));
```
DataOutputStream implements DataOutput interface, which provides methods to write formatted primitive data and String. For examples,
```
// 8 primitive types
public final void writeInt(int i) throws IOExcpetion;      // Write the int as 4 bytes
public final void writeFloat(float f) throws IOExcpetion;
public final void writeDoube(double d) throws IOExcpetion; // Write the double as 8 bytes
public final void writeByte(int b) throws IOExcpetion;     // least-significant byte
public final void writeShort(int s) throws IOExcpetion;    // two lower bytes
public final void writeLong(long l) throws IOExcpetion;
public final void writeBoolean(boolean b) throws IOExcpetion;
public final void writeChar(int i) throws IOExcpetion;
 
// String
public final void writeBytes(String str) throws IOExcpetion;  
     // least-significant byte of each char
public final void writeChars(String str) throws IOExcpetion;
     // Write String as UCS-2 16-bit char, Big-endian (big byte first)
public final void writeUTF(String str) throws IOException;   
     // Write String as UTF, with first two bytes indicating UTF bytes length

public final void write(byte[] b, int off, int len) throws IOException
public final void write(byte[] b) throws IOException
public final void write(int b) throws IOException     // Write the least-significant byte
```

#### Character-Based I/O & Character Streams

Java internally stores characters (char type) in 16-bit UCS-2 character set. But the external data source/sink could store characters in other character set (e.g., US-ASCII, ISO-8859-x, UTF-8, UTF-16, and many others), in fixed length of 8-bit or 16-bit, or in variable length of 1 to 4 bytes. [Read "Character Sets and Encoding Schemes"]. Hence, Java has to differentiate between byte-based I/O for processing 8-bit raw bytes, and character-based I/O for processing texts. The character streams needs to translate between the character set used by external I/O devices and Java internal UCS-2 format. For example, the character '您' is stored as "60 A8" in UCS-2 (Java internal), "E6 82 A8" in UTF8, "C4 FA" in GBK/GB2312, and "B1 7A" in BIG5. If this character is to be written to a file uses UTF-8, the character stream needs to translate "60 A8" to "E6 82 A8". The reserve takes place in a reading operation.

The byte/character streams refer to the unit of operation within the Java programs, which does not necessary correspond to the amount of data transferred from/to the external I/O devices. This is because some charsets use fixed-length of 8-bit (e.g., US-ASCII, ISO-8859-1) or 16-bit (e.g., UCS-16), while some use variable-length of 1-4 bytes (e.g., UTF-8, UTF-16, UTF-16-BE, UTF-16-LE, GBK, BIG5). When a character stream is used to read an 8-bit ASCII file, an 8-bit data is read from the file and put into the 16-bit char location of the Java program.

![Readers and writers](http://www.ntu.edu.sg/home/ehchua/programming/java/images/IO_InputOutputReadersWriters.png)

Other than the unit of operation and charset conversion (which is extremely complex), character-based I/O is almost identical to byte-based I/O. Instead of InputStream and OutputStream, we use Reader and Writer for character-based I/O.

The abstract superclass Reader operates on char. It declares an abstract method read() to read one character from the input source. read() returns the character as an int between 0 to 65535 (a char in Java can be treated as an unsigned 16-bit integer); or -1 if end-of-stream is detected; or throws an IOException if I/O error occurs. There are also two variations of read() to read a block of characters into char-array.

```
public abstract int read() throws IOException
public int read(char[] chars, int offset, int length) throws IOException
public int read(char[] chars) throws IOException
```
The abstract superclass Writer declares an abstract method write(), which writes a character to the output sink. The lower 2 bytes of the int argument is written out; while the upper 2 bytes are discarded.
```
public void abstract void write(int aChar) throws IOException
public void write(char[] chars, int offset, int length) throws IOException
public void write(char[] chars) throws IOException
```

FileReader and FileWriter are concrete implementations to the abstract superclasses Reader and Writer, to support I/O from disk files. FileReader/FileWriter assumes that the default character encoding (charset) is used for the disk file. The default charset is kept in the JVM's system property "file.encoding". You can get the default charset via static method java.nio.charset.Charset.defaultCharset() or System.getProperty("file.encoding"). It is probable safe to use FileReader/FileWriter for ASCII texts, provided that the default charset is compatible to ASCII (such as US-ASCII, ISO-8859-x, UTF-8, and many others, but NOT UTF-16, UTF-16BE, UTF-16LE and many others). Use of FileReader/FileWriter is NOT recommended as you have no control of the file encoding charset.

BufferedReader and BufferedWriter can be stacked on top of FileReader/FileWriter or other character streams to perform buffered I/O, instead of character-by-character. BufferedReader provides a new method readLine(), which reads a line and returns a String (without the line delimiter). Lines could be delimited by "\n" (Unix), "\r\n" (Windows), or "\r" (Mac).

As mentioned, Java internally stores characters (char type) in 16-bit UCS-2 character set. But the external data source/sink could store characters in other character set (e.g., US-ASCII, ISO-8859-x, UTF-8, UTF-16, and many others), in fixed length of 8-bit or 16-bit, or in variable length of 1 to 4 bytes. The FileReader/FileWriter introduced earlier uses the default charset for decoding/encoding, resulted in non-portable programs.

To choose the charset, you need to use InputStreamReader and OutputStreamWriter. InputStreamReader and OutputStreamWriter are considered to be byte-to-character "bridge" streams.

You can choose the character set in the InputStreamReader's constructor:
```
public InputStreamReader(InputStream in)   // Use default charset
public InputStreamReader(InputStream in, String charsetName) throws UnsupportedEncodingException
public InputStreamReader(InputStream in, Charset cs)
```
You can list the available charsets via static method java.nio.charset.Charset.availableCharsets(). The commonly-used Charset names supported by Java are:

* "US-ASCII": 7-bit ASCII (aka ISO646-US)
* "ISO-8859-1": Latin-1
* "UTF-8": Most commonly-used encoding scheme for Unicode
* "UTF-16BE": Big-endian (big byte first) (big-endian is usually the default)
* "UTF-16LE": Little-endian (little byte first)
* "UTF-16": with a 2-byte BOM (Byte-Order-Mark) to specify the byte order. FE FF indicates big-endian, FF FE indicates little-endian.

As the InputStreamReader/OutputStreamWriter often needs to read/write in multiple bytes, it is best to wrap it with a BufferedReader/BufferedWriter.

![printStream printWriter](http://www.ntu.edu.sg/home/ehchua/programming/java/images/IO_PrintStreamWriter.png)

The byte-based java.io.printSteam supports convenient printing methods such as print() and println() for printing primitives and text string. Primitives are converted to their string representation for printing. The printf() and format() were introduced in JDK 1.5 for formatting output with former specifiers. printf() and format() are identical.

A PrintStream never throws an IOException. Instead, it sets an internal flag which can be checked via the checkError() method. A PrintStream can also be created to flush the output automatically. That is, the flush() method is automatically invoked after a byte array is written, one of the println() methods is invoked, or after a newline ('\n') is written.

The standard output and error streams (System.out and System.err) belong to PrintStream.

All characters printed by a PrintStream are converted into bytes using the default character encoding. The PrintWriter class should be used in situations that require writing characters rather than bytes.

The character-stream PrintWriter is similar to PrintStream, except that it write in characters instead of bytes. The PrintWriter also supports all the convenient printing methods print(), println(), printf() and format(). It never throws an IOException and can optionally be created to support automatic flushing.


[К оглавлению](#start)

---

#### <a name="serialization"></a> 4. Serialization.

---

Data streams (DataInputStream and DataOutputStream) allow you to read and write primitive data (such as int, double) and String, rather than individual bytes. Object streams (ObjectInputStream and ObjectOutputStream) go one step further to allow you to read and write entire objects (such as Date, ArrayList or any custom objects).

Object serialization is the process of representing a "particular state of an object" in a serialized bit-stream, so that the bit stream can be written out to an external device (such as a disk file or network). The bit-stream can later be re-constructed to recover the state of that object. Object serialization is necessary to save a state of an object into a disk file for persistence or sent the object across the network for applications such as Web Services, Distributed-object applications, and Remote Method Invocation (RMI).

In Java, object that requires to be serialized must implement java.io.Serializable or java.io.Externalizable interface. Serializable interface is an empty interface (or tagged interface) with nothing declared. Its purpose is simply to declare that particular object is serializable.

The ObjectInputStream and ObjectOutputStream can be used to serialize an object into a bit-stream and transfer it to/from an I/O streams, via these methods:
```
public final Object readObject() throws IOException, ClassNotFoundException;
public final void writeObject(Object obj) throws IOException;
```
ObjectInputStream and ObjectOutputStream must be stacked on top of a concrete implementation of InputStream or OutputStream, such as FileInputStream or FileOutputStream.

For example, the following code segment writes objects to a disk file. The ".ser" is the convention for serialized object file type.
```
ObjectOutputStream out =
   new ObjectOutputStream(
      new BufferedOutputStream(
         new FileOutputStream("object.ser")));
out.writeObject("The current Date and Time is "); // write a String object
out.writeObject(new Date());                      // write a Date object
out.flush();
out.close();
```
To read and re-construct the object back in a program, use the method readObject(), which returns an java.lang.Object. Downcast the Object back to its original type.
```
ObjectInputStream in = 
   new ObjectInputStream(
      new BufferedInputStream(
         new FileInputStream("object.ser")));
String str = (String)in.readObject();
Date d = (Date)in.readObject(new Date());  // downcast
in.close();
```

Primitive types and array are, by default, serializable.

The ObjectInputStream and ObjectOutputStream implement DataInput and DataOutput interface respectively. You can used methods such as readInt(), readDouble(), writeInt(), writeDouble() for reading and writing primitive types.

transient & static
* static fields are not serialized, as it belongs to the class instead of the particular instance to be serialized.
* To prevent certain fields from being serialized, mark them using the keyword transient. This could cut down the amount of data traffic.
* The writeObject() method writes out the class of the object, the class signature, and values of non-static and non-transient fields.

#### java.io.Serializable & Externalizable Interfaces

When you create a class that might be serialized, the class must implement java.io.Serializable interface. The Serializable interface doesn't declare any methods. Empty interfaces such as Serializable are known as tagging interfaces. They identify implementing classes as having certain properties, without requiring those classes to actually implement any methods.

Most of the core Java classes implement Serializable, such as all the wrapper classes, collection classes, and GUI classes. In fact, the only core Java classes that do not implement Serializable are ones that should not be serialized. Arrays of primitives or serializable objects are themselves serializable.

The serialization runtime uses a number (called serialVersionUID) to ensure that the object read into the program (during deserialization) is compatible with the class definition, and not belonging to another version. It throws an InvalidClassException otherwise.

The Serializable has a sub-interface called Externalizable, which you could used if you want to customize the way a class is serialized. Since Externalizable extends Serializable, it is also a Serializable and you could invoke readObject() and writeObject().
 
Externalizable declares two abstract methods:
```
void writeExternal(ObjectOutput out) throws IOException
void readExternal(ObjectInput in) throws IOException, ClassNotFoundException
```
ObjectOutput and ObjectInput are interfaces that are implemented by ObjectOutputStream and ObjectInputStream, which define the writeObject() and readObject() methods, respectively. When an instance of Externalizable is passed to an ObjectOutputStream, the default serialization procedure is bypassed; instead, the stream calls the instance's writeExternal() method. Similarly, when an ObjectInputStream reads a Exteranlizabled instance, it uses readExternal() to reconstruct the instance.
 
Externalizable is useful if you want complete control on how your objects shall be serialized/deserialized. For example, you could encrypt sensitive data before the object is serialized.

[К оглавлению](#start)

---

#### <a name="class-path"></a> 5. Class Path.

---

#### Interface java.nio.file.Path

A path string could be used to locate a file, a directory or a symbolic link. A symbolic link (or symlink) is a special file that references another file. A path string is system dependent, e.g., "c:\myproject\java\Hello.java" in Windows or "/myproject/java/Hello.java" in Unix. Windows uses back-slash '\' as the directory separator; while Unixes use forward-slash '/'. Windows uses semi-colon ';' as path separator; while Unixes use colon ':'. The "c:\" or "\" is called the root. Windows supports multiple roots, each maps to a drive (e.g., "c:\", "d:\"). Unix has single root ("\"). A path could be absolute (beginning from the root) or relative (which is relative to the current working directory). Special notations "." and ".." denote the current directory and the parent directory, respectively.

A java.nio.file.Path instance specifies the location of a file, or a directory, or a symbolic link. Path replaces java.io.File (of the standard I/O), which is less versatile and buggy.

#### Helper class java.nio.file.Paths

To create a Path, use the static method get() of the helper class java.nio.file.Paths. The helper class Paths contains exclusively static methods for creating Path objects. Paths.get() returns a Path object by converting a given path string or URI.
```
public static Path get(String first, String... more)
// This method accepts variable number of arguments (varargs).
// It converts a path string, or a sequence of strings that when joined form a path string, to a Path object.
// The location of the Path may or may not exist.  
  
public static Path get(URI uri)
// Converts the given URI to a Path object.
```
For example,
```
Path p1 = Paths.get("in.txt");      // A file in current directory, relative
Path p2 = Paths.get("c:\\myproejct\\java\\Hello.java");  // File, absolute, need escape sequence for '\'
Path p3 = Paths.get("/use/local");  // A directory
```
As the directory-separator is system dependent, for writing portable and more flexible program, it is recommended to use an existing Path instance as an anchor to resolve the filename, e.g.,
```
Path dir = ...
Path file = dir.resolve("filename");  // Joins the filename to the dirname with a directory-separator
```

#### Helper Class java.nio.file.Files
Properties of a File/Directory
```
public static long size(Path path)  // Returns the size of the file
 
public static boolean exists(Path path, LinkOption... options)
// Verifies whether the given Path exists, as a file/directory/symlink.
// It returns false if the file does not exist or status is unknown.
// LinkOption specifies how symlink should be handled, 
//   e.g., NOFOLLOW_LINKS: Do not follow symlinks.
public static boolean notExists(Path path, LinkOption... options)     // Not exists?
 
public static boolean isDirectory(Path path, LinkOption... options)   // a directory?
public static boolean isRegularFile(Path path, LinkOption... options) // a file?
public static boolean isSymbolicLink(Path path)                       // a symblink?
 
public static boolean isReadable(Path path)    // readable?
public static boolean isWritable(Path path)    // writable?
public static boolean isExecutable(Path path)  // executable?
```

Deleting a File/Directory
```
public static void delete(Path path) throws IOException
public static boolean deleteIfExists(Path path) throws IOException
```
Copying/Moving a File/Directory

You can use static methods copy(Path, Path, CopyOption) or move(Path, Path, CopyOption) to copy or move a file or directory. The methods return the target Path.

The methods accepts an optional third argument of CopyOption. For examples: CopyOption.REPLACE_EXISTING replaces the target if it exists; CopyOption.COPY_ATTRIBUTES copies the file attributes such as the dates; CopyOption.NOFOLLOW_LINKS specifies not to follow symlinks.
```
public static Path copy(Path source, Path target, CopyOption... options) throws IOException
public static Path move(Path source, Path target, CopyOption... options) throws IOException
```

Reading/Writing Small Files

For small files, you can use static methods Files.readAllBytes(Path) (byte-based) and Files.readAllLines(Path, Charset) (character-based) to read the entire file. You can use Files.write(Path, byte[]) (byte-based) or Files.write(Path, Iterable, Charset) (character-based) to write to a file.

The optional OpenOption includes: WRITE, APPEND, TRUNCATE_EXISTING (truncates the file to zero bytes), CREATE_NEW (creates a new file and throws an exception if the file already exists), CREATE (opens the file if it exists or creates a new file if it does not), among others.

Byte-based Operation
```
public static byte[] readAllBytes(Path path) throws IOException
// Reads all bytes and returns a byte array. 
public static Path write(Path path, byte[] bytes, OpenOption... options) throws IOException
// Default options are: CREATE, TRUNCATE_EXISTING, and WRITE
```
Character-based Operation
```
public static List<String> readAllLines(Path path, Charset cs) throws IOException
// Reads all lines and returns a list of String.
// Line terminator could be "\n", "\r\n" or "\r".
public static Path write(Path path, Iterable<? extends CharSequence> lines, 
                         Charset cs, OpenOption... options) throws IOException
```

Creating a New File/Directory/Symlink

Beside using the Files.write() method with OpenOption of CREATE or CREATE_NEW, you can also use Files.createFile() method to create an empty file. You can use the default file attributes or optionally define the initial attributes of the file.
```
public static Path createFile(Path path, FileAttribute<?>... attrs)
```

Similarly, you can create a new directory, symlink as follows:
```
public static Path createDirectory(Path dir, FileAttribute<?>... attrs) throws IOException
// Creates a new directory.

public static Path createDirectories(Path dir, FileAttribute<?>... attrs) throws IOException
// Creates a directory by creating all nonexistent "parent" directories first

public public static Path createSymbolicLink(Path link, Path target,
   FileAttribute<?>... attrs) throws IOException
// Creates a symbolic link to a target
```

[К оглавлению](#start)

---

#### <a name="attributes"></a> 6. Files attributes.

---

You can use one of the Files.readAttributes() methods to read all the basic attribute of a path.
```
public static Map<String,Object> readAttributes(
      Path path, String attributes, LinkOption... options) throws IOException
// Read Reads a set of file attributes.

public static Object getAttribute(
      Path path, String attribute, LinkOption... options) throws IOException
// Get a given attribute

public static Path setAttribute(
      Path path, String attribute, Object value, LinkOption... options) throws IOException
// Set a given attribute
```

The java.nio.attribute package contains the attribute-related classes. It bundles the file attributes in the following six types of views.

BasicFileAttributeView manages the basic file attributes such as creation time, last access time, last modified time, size, file type (regular file, directory, symbolic link, or other), and file key (a unique number for a file). This view is supported on all platforms.

DosFileAttributeView extends the BasicFileAttributeView accesses the file attributes that are specific to DOS. It provides the support to check if a file is a hidden file, a system file, an archive file, and a read-only file. It is available only on the systems that support DOS such as Microsoft Windows.

POSIX stands for Portable Operating System Interface for UNIX. PosixFileAttributeView extends the BasicFileAttributeView and adds support for attributes that are available on the systems that support POSIX standards such as UNIX. It lets we manage owner, group, and [related access] permissions.

FileOwnerAttributeView manages the owner of a file.

ACL stands for Access Control List. AclFileAttributeView manages the ACL for a file.

UserDefinedFileAttributeView manages a set of user-defined attributes for a file. The name of an attribute is a String. The value of an attribute could be of any data type.

To read or update one file attribute, use Files class.

Files class has the following two static methods to read and update a file attribute using the attribute name as a string:
```
Object getAttribute(Path path, String attribute, LinkOption... options)
Path setAttribute(Path path, String attribute, Object value, LinkOption... options)
```
To read or update multiple attributes of a file, work with a specific file attribute view.

For most of the file attribute views, we have to work with two interfaces named as TypeAttributes and TypeAttributeView.

For the basic file attributes, we have the BasicFileAttributes and BasicFileAtrributeView interfaces.

The TypeAttributes reads the attributes. The TypeAttributeView reads/updates the attributes.

The following two methods of the Files class read the file attributes in a bulk.
```
<A extends BasicFileAttributes> A readAttributes(Path path, Class<A> type, LinkOption... options)

Map<String,Object> readAttributes(Path path, String attributes, LinkOption... options)
```
The last argument of both methods specify how a symbolic link is handled. By default, if a file is a symbolic link, the attributes of the target of the symbolic link are read.

If we specify NOFOLLOW_LINKS as the option, the attributes of the symbolic link are read, not the attributes of its target.

The first readAttributes() method returns all file attributes of a specified type in an TypeAttributes object.

To create the Path object representing the path of the file
```
Path  path   = Paths.get("C:\\Java_Dev\\test1.txt");
```
To Read the basic file attributes
```
BasicFileAttributes bfa  = Files.readAttributes(path, BasicFileAttributes.class);
```
To Get the last modified time
```
FileTime lastModifiedTime  = bfa.lastModifiedTime();
```
To Get the size of the file
```
long  size = bfa.size();
```
The second readAttributes() method returns all or some of the attributes of a specific type.


The list of attributes to read is supplied in a string form. The string form of an attribute list uses the following syntax:

view-name:comma-separated-attributes
The view-name is the name of the attribute view that we want to read, such as basic, posix, acl, etc.

If view-name is omitted, it defaults to basic. If view-name is present, it is followed by a colon.

We can read all attributes of a specific view type by specifying an asterisk as the attributes list. For example, we can specify "basic:*" or "*" to read all basic file attributes.

To read the size and the last modified time of the basic view, we would use

"basic:size,lastModifiedTime" or "size,lastModifiedTime". 
To read the owner attribute of a file using an ACL view, we would use a string "acl:owner".

To read all posix attributes of a file, we would use "posix:*".

The following code prints the size and the last modified time of the file C:\Java_Dev\test1.txt.
```java
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.Map;

public class Main {
  public static void main(String[] args) throws Exception {
    Path path = Paths.get("C:\\Java_Dev\\test1.txt");

    // Prepare the attribute list
    String attribList = "basic:size,lastModifiedTime";

    // Read the attributes
    Map<String, Object> attribs = Files.readAttributes(path, attribList);

    System.out.format("Size:%s, Last   Modified   Time:%s %n",
        attribs.get("size"), attribs.get("lastModifiedTime"));

  }
}
```

[К оглавлению](#start)

---

#### <a name="nio2"></a> 7. Comparing File and NIO.2. 

---

#### Comparing IO and NIO

| IO                    | NIO                 | 
|:---------------------:|:-------------------:|
| Stream oriented       | Buffer oriented     |  
| Blocking IO           | Non blocking IO     |  
|                       | Select              |

Основное отличие между двумя подходами к организации ввода/вывода в том, что Java IO является потокоориентированным, а Java NIO – буфер-ориентированным. Разберем подробней. 

Потокоориентированный ввод/вывод подразумевает чтение/запись из потока/в поток одного или нескольких байт в единицу времени поочередно. Данная информация нигде не кэшируются. Таким образом, невозможно произвольно двигаться по потоку данных вперед или назад. Если вы хотите произвести подобные манипуляции, вам придется сначала кэшировать данные в буфере.

Потокоориентированный ввод:

![Потокоориентированный ввод](https://habrastorage.org/files/4b1/680/d3d/4b1680d3d86a4bc0b5468482865df4aa.gif)

Потокоориентированный вывод:

![Потокоориентированный вывод](https://habrastorage.org/files/9fb/b54/e86/9fbb54e869eb4c088901b071e9014bf1.gif)

Подход, на котором основан Java NIO немного отличается. Данные считываются в буфер для последующей обработки. Вы можете двигаться по буферу вперед и назад. Это дает немного больше гибкости при обработке данных. В то же время, вам необходимо проверять содержит ли буфер необходимый для корректной обработки объем данных. Также необходимо следить, чтобы при чтении данных в буфер вы не уничтожили ещё не обработанные данные, находящиеся в буфере

	
Потоки ввода/вывода (streams) в Java IO являются блокирующими. Это значит, что когда в потоке выполнения (tread) вызывается read() или write() метод любого класса из пакета java.io.*, происходит блокировка до тех пор, пока данные не будут считаны или записаны. Поток выполнения в данный момент не может делать ничего другого. 

Неблокирующий режим Java NIO позволяет запрашивать считанные данные из канала (channel) и получать только то, что доступно на данный момент, или вообще ничего, если доступных данных пока нет. Вместо того, чтобы оставаться заблокированным пока данные не станут доступными для считывания, поток выполнения может заняться чем-то другим. 

Тоже самое справедливо и для неблокирующего вывода. Поток выполнения может запросить запись в канал некоторых данных, но не дожидаться при этом пока они не будут полностью записаны.

Таким образом неблокирующий режим Java NIO позволяет использовать один поток выполнения для решения нескольких задач вместо пустого прожигания времени на ожидание в заблокированном состояний. Наиболее частой практикой является использование сэкономленного времени работы потока выполнения на обслуживание операций ввода/вывода в другом или других каналах.

#### Селекторы

Java NIO's selectors allow a single thread to monitor multiple channels of input. You can register multiple channels with a selector, then use a single thread to "select" the channels that have input available for processing, or select the channels that are ready for writing. This selector mechanism makes it easy for a single thread to manage multiple channels.

![Selectors](https://habrastorage.org/files/a94/67c/af7/a9467caf787342a5989544393b1af488.png)


#### Comparing java.io.File and java.nio.file.Path

| java.io.File (class)	| java.nio.file.Path (interface) |
|:---------------------:|:-------------------------------:|
|file = new File("path/to/file.txt") | path = Paths.get("path/to/file.txt") |
|file = new File(parentFile, "file.txt") | path = parentPath.resolve("file.txt")|
|file.getFileName() |path.getFileName().toString()|
|file.getParentFile() | path.getParent()|
|file.mkdirs()| Files.createDirectories(path)|
|file.length()| Files.size(path)|
|file.exists()| Files.exists(path)|
|file.delete()| Files.delete(path)|
|new FileOutputStream(file)| Files.newOutputStream(path)|
|new FileInputStream(file)| Files.newInputStream(path)|
|file.listFiles(filter) | Files.list(path) | 
|.filter(filter) | .collect(toList()) |

Some additional notes:

* Path throws IOException more often than File, and rarely return a boolean to tell if something was done (mkdirs(), delete())

* File is more object oriented than Path: I regret that size(), exists()…​ methods are not on the Path interface. This is probably due to the fact that this API was added in Java 7, but default methods on interfaces were added later in Java 8.

* Path based InputStream/OutputStream`s are less expensive from a GC point view. 

java.nio.file.Files allows to read, write, copy files in a single line:

```
Files.write(Paths.get("image.png"), bytes); //Write a binary file


List<String> lines = Files.readAllLines(Paths.get("letter.txt"), StandardCharsets.UTF_8); //Read a text file

Files.lines(Paths.get("letter.txt"), StandardCharsets.UTF_8)
		.forEach(System.out::println);
```


When the File is only for local files, Path can also be used to access remote files. A Path is associated to a FileSystem.

To create a new Path instances, there is not constructor (Path is interface), we need to call a factory method. The above 2 lines are the same:
```
path = Paths.get("path/to/file.txt");
path = FileSystems.getDefault().getPath("path/to/file.txt");
```
As the default file system is the local one, you get a path to a local file. Depending on the underlying file system, you’ll get a different implementation: sun.nio.fs.UnixPath, sun.nio.fs.WindowsPath…​

With this trick in mind, we can read the content of a Zip file, as if we had extracted it:
```
URI zipUri = new URI("jar:file:/path/to/archive.zip")
try(FileSystem zipFS = FileSystems.newFileSystem(zipUri, emptyMap())) {  //"Mount" the Zip file as a file system
    Path zipPath = zipFS.getPath("/archive") //zipPath is of type com.sun.nio.zipfs.ZipPath
	Files.list(zipPath)
		.map(Path::toString)
		.forEach(System.out::println);
}
```





[К оглавлению](#start)


#### References



[File and Directory](http://www.ntu.edu.sg/home/ehchua/programming/java/j5b_io.html)

[Oracle lessons](https://docs.oracle.com/javase/tutorial/essential/io/index.html)

[Java NIO vs. IO](http://tutorials.jenkov.com/java-nio/nio-vs-io.html)

[К оглавлению](#start)