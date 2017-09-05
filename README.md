# Java Style Guide
Набор советов, основанных на
[Twitter java styleguide](https://github.com/twitter/commons/blob/master/src/java/com/twitter/common/styleguide.md)

Данный набор правил предоставлен для того чтобы обеспечить более чистый код.
Следование этому гайду может иногда мешать читаемости кода или создавать
костыли. Все нужно использовать в меру, но в любом случае, старайтесь писать
наиболее читабельный код.

## Список для чтения

- [Effective Java](http://www.amazon.com/Effective-Java-Edition-Joshua-Bloch/dp/0321356683)

- [Java Concurrency in Practice](http://jcip.net/)

- [Code Complete 2](http://www.stevemcconnell.com/cc.htm)


## Coding style

### Форматирование

#### Используй переносы строк мудро

Есть две причины для использование дополнительного переноса строки

1. Длина выражения превышает лимит символов

2. Нужно логически отделить части кода друг от друга

Написание кода подобно написанию литературного текста. 
Текст должен передавать смысл через главы, параграфы, знаки препинания.
Так же и код должен нести некий смысл и нужно подчеркнуть этот для 
человека, который будет читать этот код, используя те же самые конструкции.

#### Отступы
Мы используем "one true brace style" 
([1TBS](http://en.wikipedia.org/wiki/Indent_style#Variant:_1TBS)).
Размер отступа - 2 пробела.

    :::java
    // Вот так.
    if (x < 0) {
      negative(x);
    } else {
      nonnegative(x);
    }

    // Не так.
    if (x < 0)
      negative(x);

    // И не так.
    if (x < 0) negative(x);

Отступы для длинных выражений - 4 пробела. Каждый уровень вложенности 
добавляет 2 или 4 пробела.

    :::java
    // Плохо.
    //   - Переносы строк в произвольных местах
    //   - При чтении тяжело сложить в голове все части и понять, 
    // 	    что же будет выведено в сообщении
    throw new IllegalStateException("Failed to process request" + request.getId()
        + " for user " + user.getId() + " query: '" + query.getText()
        + "'");

    // Хорошо.
    //   - Каждый компонент отделен и самодостаточен
    //   - Добавить или удалить компонент легко, не требует дополнительного форматирования
    throw new IllegalStateException("Failed to process"
        + " request " + request.getId()
        + " for user " + user.getId()
        + " query: '" + query.getText() + "'");

Не разбивайте выражения без необходимости.

    :::java
    // Плохо.
    final String value =
        otherValue;

    // Хорошо.
    final String value = otherValue;

Пример описания метода.

    :::java
    // Так себе, потому что переносы в случайных местах создают 
    // впечатление что это сделано просто для заполнения места.
    String downloadAnInternet(Internet internet, Tubes tubes,
        Blogosphere blogs, Amount<Long, Data> bandwidth) {
      tubes.download(internet);
      ...
    }

    // Приемлимо.
    String downloadAnInternet(Internet internet, Tubes tubes, Blogosphere blogs,
        Amount<Long, Data> bandwidth) {
      tubes.download(internet);
      ...
    }

    // Лучше, так как дополнительная строка четко отделяет тело метода.
    String downloadAnInternet(Internet internet, Tubes tubes, Blogosphere blogs,
        Amount<Long, Data> bandwidth) {

      tubes.download(internet);
      ...
    }

    // Приемлимо, но может выглядеть ужасно, 
    // если открывающая скобка находится далеко от начала строки.
    public String downloadAnInternet(Internet internet,
                                     Tubes tubes,
                                     Blogosphere blogs,
                                     Amount<Long, Data> bandwidth) {
      tubes.download(internet);
      ...
    }

    // Хорошо. Легко понять все параметры. Дополнительная строка четко отделяет тело метода.
    public String downloadAnInternet(
        Internet internet,
        Tubes tubes,
        Blogosphere blogs,
        Amount<Long, Data> bandwidth) {

      tubes.download(internet);
      ...
    }

##### Chained method calls

    :::java
    // Плохо.
    //   - Переносы строки основаны на длине строки, а не на логике
    Iterable<Module> modules = ImmutableList.<Module>builder().add(new LifecycleModule())
        .add(new AppLauncherModule()).addAll(application.getModules()).build();

    // Лучше.
    //   - Вызовы логически разделены
    //   - Тем не менее, последняя точка в строке создает помеху для быстрого добавления нового вызова
    Iterable<Module> modules = ImmutableList.<Module>builder().
        add(new LifecycleModule()).
        add(new AppLauncherModule()).
        addAll(application.getModules()).
        build();

    // Хорошо.
    //   - Вызовы логически разделены
    //   - Правильное расположение нового вызова очевидно
    Iterable<Module> modules = ImmutableList.<Module>builder()
        .add(new LifecycleModule())
        .add(new AppLauncherModule())
        .addAll(application.getModules())
        .build();

#### Лимит ширины в 100 столбцов
Мы предпочитаем использовать такой лимит для того, чтобы не было слишком 
много продолжающихся строк(continuation lines), но тем не менее можно 
было бы легко уместить две вкладки редактора на одном мониторе.

#### CamelCase для типов, camelCase для переменных, UPPER_SNAKE для констант

#### Не использовать пробелы в конце строки

Пробелы невидимы, не добавляют ничего в программу, но тем не менее, они 
мешают при навигации по коду при помощи клавиатуры.

### Объявления полей, классов и методов

##### Порядок модификаторов

Мы используем 
[Java Language Specification](http://docs.oracle.com/javase/specs/).

    :::java
    // Плохо.
    final volatile private String value;

    // Хорошо.
    private final volatile String value;

### Именование переменных

#### Слишком короткие имена зарезервированы для индексов в цикле и подобных вещах.

    :::java
    // Плохо.
    //   - Имя поля не дает понимания, для чего преднозначено поле
    class User {
      private final int a;
      private final String m;

      ...
    }

    // Хорошо.
    class User {
      private final int ageInYears;
      private final String maidenName;

      ...
    }

#### Использование единиц измерения в именах

    :::java
    // Плохо.
    long pollInterval;
    int fileSize;

    // Хорошо.
    long pollIntervalMs;
    int fileSizeGb.

    // Лучше.
    //   - Единица измерения определена в типе
    //   - Легко можно изменить единицу измерения
    //   - Читабельность высокая
    Amount<Long, Time> pollInterval;
    Amount<Integer, Data> fileSize;

#### Не используй метаданные в именах переменных
Имя переменной должно описывать предназначение. 
Добавление лишней информации, такой как область видимости или уровень 
вложенности считается плохим стилем.

Избегай использования типов в имени переменной

    :::java
    // Плохо.
    Map<Integer, User> idToUserMap;
    String valueString;

    // Хорошо.
    Map<Integer, User> usersById;
    String value;

Также избегай информации о вложенности в переменной. 
Использование такого именования предпологает что класс слишком 
большой/сложный. Такой класс должен быть разделен на несколько других 
классов


    :::java
    // Плохо.
    String _value;
    String mValue;

    // Хорошо.
    String value;

### Окружай пробелами операции 

    :::java
    // Плохо.
    //   - Этот код тяжело читать
    int foo=a+b+1;

    // Хорошо.
    int foo = a + b + 1;

### Используй явный порядок в операциях.

Не заставляй читателя открывать 
[спецификацию](http://docs.oracle.com/javase/tutorial/java/nutsandbolts/operators.html)
чтобы он убедился в правильности этого кода. Используй скобки для 
определения порядка

    :::java
    // Плохо.
    return a << 8 * n + 1 | 0xFF;

    // Хорошо.
    return (a << (8 * n) + 1) | 0xFF;

Лучше быть *очевидным*

    :::java
    if ((values != null) && (10 > values.size())) {
      ...
    }

### Документация

Чем больше людей проходит через код, тем больше ему нужна документация.

#### "Я пишу отчет о..."
Никогда не начинайте писать предложения подобным образом.
Аналогично, не пишите так документацию.

    :::java
    // Плохо.
    /**
     * Это класс для кэширования.
     */
    class Cache {
      ...
    }

    // Хорошо.
    /**
     * Гибкое  хранилище для объектов с ключом, которое может быть очищено.
     */
    class Cache {
      ...
    }

#### Документирование класса
Документация класса может варьироваться от одного предложения до 
нескольких параграфов с примерами кода.
Документация нужна для того, чтобы убрать все пробелы при использовании 
API класса, чтобы пользователь вашего класса мог быстро и *корректно* его использовать.

    :::java
    /**
     * An RPC equivalent of a unix pipe tee.  Any RPC sent to the tee input is guaranteed to have
     * been sent to both tee outputs before the call returns.
     *
     * @param <T> The type of the tee'd service.
     */
    public class RpcTee<T> {
      ...
    }

#### Документирование метода
Документация метода должна описывать что метод *делает*. В зависимости 
от типов аргументов и их количества, нужно так же задокументировать 
параметры.

    :::java
    // Плохо.
    //   - Документация не говорит ничего нового, все сказано в названии метода
    //   - Это 'filler doc'. Код пройдет все проверки, но не поможет никому.
    /**
     * Splits a string.
     *
     * @param s A string.
     * @return A list of strings.
     */
    List<String> split(String s);

    // Лучше.
    //   - Документация говорит, по какому сиволу будет разделена входная строка
    //   - Есть случаи, которые документация не покарывает
    /**
     * Splits a string on whitespace.
     *
     * @param s The string to split.  An {@code null} string is treated as an empty string.
     * @return A list of the whitespace-delimited parts of the input.
     */
    List<String> split(String s);

    // Хорошо.
    //   - Документация покрывает частные случаи.
    /**
     * Splits a string on whitespace.  Repeated whitespace characters are collapsed.
     *
     * @param s The string to split.  An {@code null} string is treated as an empty string.
     * @return A list of the whitespace-delimited parts of the input.
     */
    List<String> split(String s);

#### Будь профессионалом
Все испытывают разочарование, когда доходит до использования чужих 
библиотек. Но если так случилось - не нойте, это не показывает вас в 
хорошем свете.

    :::java
    // Плохо.
    // I hate xml/soap so much, why can't it do this for me!?
    try {
      userId = Integer.parseInt(xml.getField("id"));
    } catch (NumberFormatException e) {
      ...
    }

    // Хорошо.
    // TODO(Jim): Tuck field validation away in a library.
    try {
      userId = Integer.parseInt(xml.getField("id"));
    } catch (NumberFormatException e) {
      ...
    }

#### Не документируй переопределенные методы (обычно)

    :::java
    interface Database {
      /**
       * Gets the installed version of the database.
       *
       * @return The database version identifier.
       */
      String getVersion();
    }

    // Плохо.
    //   - Документация не добавляет ничего нового.
    class PostgresDatabase implements Database {
      /**
       * Gets the installed version of the database.
       *
       * @return The database version identifier.
       */
      @Override
      public String getVersion() {
        ...
      }
    }

    // Хорошо.
    class PostgresDatabase implements Database {
      @Override
      public int getVersion();
    }

    // Отлично.
    //   - Документация описывает отличие переопределенного метода от метода родителя.
    class TwitterDatabase implements Database {
      /**
       * Semantic version number.
       *
       * @return The database version in semver format.
       */
      @Override
      public String getVersion() {
        ...
      }
    }

#### Используй фичи javadoc

##### Используй тег @author
Код может менятся довольно часто разными людьми. При изменении проверь 
документацию к классу и добавь туда свое имя с тегом @author.

### Импорты

#### Порядок импортов

    :::java
    import java.*
    import javax.*

    import all_other_imports

    import com.r_mades.*
    
    import com.equeo.*

    import static *

#### Не используй wildcard импорты
Wildcard импорт делают источник испортированного класса неясным

    :::java
    // Плохо.
    //   - Откуда взялся класс Foo?
    import com.twitter.baz.foo.*;
    import com.twitter.*;

    interface Bar extends Foo {
      ...
    }

    // Хорошо.
    import com.twitter.baz.foo.BazFoo;
    import com.twitter.Foo;

    interface Bar extends Foo {
      ...
    }

### Используй аннотации мудро

#### @Nullable (jsr305.jar)
По умолчанию - не используй `null`. Когда переменная, параметр, или 
возвращаемое значение метода может быть `null`, указывая явно, добавляя 
аннотацию [@Nullable](https://github.com/findbugsproject/findbugs/blob/master/findbugs/src/java/edu/umd/cs/findbugs/annotations/Nullable.java).

Указывай аннотацию и для private/protected методов

    :::java
    class Database {
      @Nullable private Connection connection;

      @Nullable
      Connection getConnection() {
        return connection;
      }

      void setConnection(@Nullable Connection connection) {
        this.connection = connection;
      }
    }

### Используй интерфейсы
Интерфейсы разделяют функционал от реализации, позволяя использовать 
несколько реализаций без изменения класса, который использует этот 
интерфейс.

Так же использование интерфейсов позволяет изолировать пакеты: 
интерфейсы доступны публично, реализации - package private.

Много маленьких интерфейсов могут выглядеть слишком тяжеловесно, 
поскольку увеличивают число файлов с кодом. Используйте следующий 
паттерн как альтернативу.

    :::java
    interface FileFetcher {
      File getFile(String name);

      // Все преимуществ интерфесов с небольшим оверхедом в исходниках
      // Это так же используется, когда ожидается одна реализация у интерфейса
      static class HdfsFileFetcher implements FileFetcher {
        @Override File getFile(String name) {
          ...
        }
      }
    }

#### Использование существующих интерфейсов
Иногда использование существующего интерфейса позволяет легко подключить
класс к другим соответствующим классам.

    :::java
    // Любой, кто захочет использовать этот класс, должен будет написать
    // прослойку для взаимодействия с массивом
    class Blobs {
      byte[] nextBlob() {
        ...
      }
    }

    // Намного лучше. Теперь клиент может использовать стандартный код 
    // для взаимодействия с коллециями
    class Blobs implements Iterable<byte[]> {
      @Override
      Iterator<byte[]> iterator() {
        ...
      }
    }

Однако, если интерфейс концептуально не подходит, не стоит его 
привязывать к коду насильно.

## Writing testable code
Writing unit tests doesn't have to be hard.  You can make it easy for yourself if you keep
testability in mind while designing your classes and interfaces.

### Fakes and mocks
When testing a class, you often need to provide some kind of canned functionality as a replacement
for real-world behavior.  For example, rather than fetching a row from a real database, you have
a test row that you want to return.  This is most commonly performed with a fake object or a mock
object.  While the difference sounds subtle, mocks have major benefits over fakes.

    :::java
    class RpcClient {
      RpcClient(HttpTransport transport) {
        ...
      }
    }

    // Bad.
    //   - Our test has little control over method call order and frequency.
    //   - We need to be careful that changes to HttpTransport don't disable FakeHttpTransport.
    //   - May require a significant amount of code.
    class FakeHttpTransport extends HttpTransport {
      @Override
      void writeBytes(byte[] bytes) {
        ...
      }

      @Override
      byte[] readBytes() {
        ...
      }
    }

    public class RpcClientTest {
      private RpcClient client;
      private FakeHttpTransport transport;

      @Before
      public void setUp() {
        transport = new FakeHttpTransport();
        client = new RpcClient(transport);
      }

      ...
    }

    interface Transport {
      void writeBytes(byte[] bytes);
      byte[] readBytes();
    }

    class RpcClient {
      RpcClient(Transport transport) {
        ...
      }
    }

    // Good.
    //   - We can mock the interface and have very fine control over how it is expected to be used.
    public class RpcClientTest {
      private RpcClient client;
      private Transport transport;

      @Before
      public void setUp() {
        transport = EasyMock.createMock(Transport.class);
        client = new RpcClient(transport);
      }

      ...
    }

### Let your callers construct support objects

    :::java
    // Bad.
    //   - A unit test needs to manage a temporary file on disk to test this class.
    class ConfigReader {
      private final InputStream configStream;
      ConfigReader(String fileName) throws IOException {
        this.configStream = new FileInputStream(fileName);
      }
    }

    // Good.
    //   - Testing this class is as easy as using ByteArrayInputStream with a String.
    class ConfigReader {
      private final InputStream configStream;
      ConfigReader(InputStream configStream){
        this.configStream = checkNotNull(configStream);
      }
    }

### Testing multithreaded code
Testing code that uses multiple threads is notoriously hard.  When approached carefully, however,
it can be accomplished without deadlocks or unnecessary time-wait statements.

If you are testing code that needs to perform periodic background tasks
(such as with a
[ScheduledExecutorService](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ScheduledExecutorService.html)),
consider mocking the service and/or manually triggering the tasks from your tests, and
avoiding the actual scheduling.
If you are testing code that submits tasks to an
[ExecutorService](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ExecutorService.html),
you might consider allowing the executor to be injected, and supplying a
[single-thread executor](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/Executors.html#newSingleThreadExecutor()) in tests.

In cases where multiple threads are inevitable,
[java.util.concurrent](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/package-summary.html)
provides some useful libraries to help manage lock-step execution.

For example,
[LinkedBlockingDeque](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingDeque.html)
can provide synchronization between producer and consumer when an asynchronous operation is
performed.
[CountDownLatch](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/CountDownLatch.html)
is useful for state/operation synchronization when a queue does not apply.

### Testing antipatterns

#### Time-dependence
Code that captures real wall time can be difficult to test repeatably, especially when time deltas
are meaningful.  Therefore, try to avoid `new Date()`, `System.currentTimeMillis()`, and
`System.nanoTime()`.  A suitable replacement for these is
[Clock](https://github.com/twitter/commons/blob/master/src/java/com/twitter/common/util/Clock.java); using
[Clock.SYSTEM_CLOCK](https://github.com/twitter/commons/blob/master/src/java/com/twitter/common/util/Clock.java#L32)
when running normally, and
[FakeClock](https://github.com/twitter/commons/blob/master/src/java/com/twitter/common/util/testing/FakeClock.java)
in tests.

#### The hidden stress test
Avoid writing unit tests that attempt to verify a certain amount of performance.  This type of
testing should be handled separately, and run in a more controlled environment than unit tests
typically are.

#### Thread.sleep()
Sleeping is rarely warranted, especially in test code.  Sleeping is expressing an expectation that
something else is happening while the executing thread is suspended.  This quickly leads to
brittleness; for example if the background thread was not scheduled while you were sleeping.

Sleeping in tests is also bad because it sets a firm lower bound on how fast tests can execute.
No matter how fast the machine is, a test that sleeps for one second can never execute in less than
one second.  Over time, this leads to very long test execution cycles.

### Avoid randomness in tests
Using random values may seem like a good idea in a test, as it allows you to cover more test cases
with less code.  The problem is that you lose control over which test cases you're covering.  When
you do encounter a test failure, it may be difficult to reproduce.  Pseudorandom input with a fixed
seed is slightly better, but in practice rarely improves test coverage.  In general it's better to
use fixed input data that exercises known edge cases.

## Best practices

### Defensive programming

#### Avoid assert
We avoid the assert statement since it can be
[disabled](http://docs.oracle.com/javase/7/docs/technotes/guides/language/assert.html#enable-disable)
at execution time, and prefer to enforce these types of invariants at all times.

*See also [preconditions](#preconditions)*

#### Preconditions
Preconditions checks are a good practice, since they serve as a well-defined barrier against bad
input from callers.  As a convention, object parameters to public constructors and methods should
always be checked against null, unless null is explicitly allowed.

*See also [be wary of null](#be-wary-of-null), [@Nullable](#nullable)*

    :::java
    // Bad.
    //   - If the file or callback are null, the problem isn't noticed until much later.
    class AsyncFileReader {
      void readLater(File file, Closure<String> callback) {
        scheduledExecutor.schedule(new Runnable() {
          @Override public void run() {
            callback.execute(readSync(file));
          }
        }, 1L, TimeUnit.HOURS);
      }
    }

    // Good.
    class AsyncFileReader {
      void readLater(File file, Closure<String> callback) {
        checkNotNull(file);
        checkArgument(file.exists() && file.canRead(), "File must exist and be readable.");
        checkNotNull(callback);

        scheduledExecutor.schedule(new Runnable() {
          @Override public void run() {
            callback.execute(readSync(file));
          }
        }, 1L, TimeUnit.HOURS);
      }
    }

#### Minimize visibility

In a class API, you should support access to any methods and fields that you make accessible.
Therefore, only expose what you intend the caller to use.  This can be imperative when
writing thread-safe code.

    :::java
    public class Parser {
      // Bad.
      //   - Callers can directly access and mutate, possibly breaking internal assumptions.
      public Map<String, String> rawFields;

      // Bad.
      //   - This is probably intended to be an internal utility function.
      public String readConfigLine() {
        ..
      }
    }

    // Good.
    //   - rawFields and the utility function are hidden
    //   - The class is package-private, indicating that it should only be accessed indirectly.
    class Parser {
      private final Map<String, String> rawFields;

      private String readConfigLine() {
        ..
      }
    }

#### Favor immutability

Mutable objects carry a burden - you need to make sure that those who are *able* to mutate it are
not violating expectations of other users of the object, and that it's even safe for them to modify.

    :::java
    // Bad.
    //   - Anyone with a reference to User can modify the user's birthday.
    //   - Calling getAttributes() gives mutable access to the underlying map.
    public class User {
      public Date birthday;
      private final Map<String, String> attributes = Maps.newHashMap();

      ...

      public Map<String, String> getAttributes() {
        return attributes;
      }
    }

    // Good.
    public class User {
      private final Date birthday;
      private final Map<String, String> attributes = Maps.newHashMap();

      ...

      public Map<String, String> getAttributes() {
        return ImmutableMap.copyOf(attributes);
      }

      // If you realize the users don't need the full map, you can avoid the map copy
      // by providing access to individual members.
      @Nullable
      public String getAttribute(String attributeName) {
        return attributes.get(attributeName);
      }
    }

#### Be wary of null
Use `@Nullable` where prudent, but favor
[Optional](http://docs.guava-libraries.googlecode.com/git-history/v11.0.2/javadoc/com/google/common/base/Optional.html)
over `@Nullable`.  `Optional` provides better semantics around absence of a value.

#### Clean up with finally

    :::java
    FileInputStream in = null;
    try {
      ...
    } catch (IOException e) {
      ...
    } finally {
      Closeables.closeQuietly(in);
    }

Even if there are no checked exceptions, there are still cases where you should use try/finally
to guarantee resource symmetry.

    :::java
    // Bad.
    //   - Mutex is never unlocked.
    mutex.lock();
    throw new NullPointerException();
    mutex.unlock();

    // Good.
    mutex.lock();
    try {
      throw new NullPointerException();
    } finally {
      mutex.unlock();
    }

    // Bad.
    //   - Connection is not closed if sendMessage throws.
    if (receivedBadMessage) {
      conn.sendMessage("Bad request.");
      conn.close();
    }

    // Good.
    if (receivedBadMessage) {
      try {
        conn.sendMessage("Bad request.");
      } finally {
        conn.close();
      }
    }


### Clean code

#### Disambiguate
Favor readability - if there's an ambiguous and unambiguous route, always favor unambiguous.

    :::java
    // Bad.
    //   - Depending on the font, it may be difficult to discern 1001 from 100l.
    long count = 100l + n;

    // Good.
    long count = 100L + n;

#### Remove dead code
Delete unused code (imports, fields, parameters, methods, classes).  They will only rot.

#### Use general types
When declaring fields and methods, it's better to use general types whenever possible.
This avoids implementation detail leak via your API, and allows you to change the types used
internally without affecting users or peripheral code.

    :::java
    // Bad.
    //   - Implementations of Database must match the ArrayList return type.
    //   - Changing return type to Set<User> or List<User> could break implementations and users.
    interface Database {
      ArrayList<User> fetchUsers(String query);
    }

    // Good.
    //   - Iterable defines the minimal functionality required of the return.
    interface Database {
      Iterable<User> fetchUsers(String query);
    }


#### Always use type parameters
Java 5 introduced support for
[generics](http://docs.oracle.com/javase/tutorial/java/generics/index.html). This added type
parameters to collection types, and allowed users to implement their own type-parameterized classes.
Backwards compatibility and
[type erasure](http://docs.oracle.com/javase/tutorial/java/generics/erasure.html) mean that
type parameters are optional, however depending on usage they do result in compiler warnings.

We conventionally include type parameters on every declaration where the type is parameterized.
Even if the type is unknown, it's preferable to include a wildcard or wide type.

#### Stay out of [Texas](http://en.wikipedia.org/wiki/Texas-sized)
Try to keep your classes bite-sized and with clearly-defined responsibilities.  This can be
*really* hard as a program evolves.

- texas imports
- texas constructors: Can the class be cleanly broken apart?<br />
If not, consider builder pattern.
- texas methods

We could do some science and come up with a statistics-driven threshold for each of these, but it
probably wouldn't be very useful.  This is usually just a gut instinct, and these are traits
of classes that are too large or complex and should be broken up.

#### Avoid typecasting
Typecasting is a sign of poor class design, and can often be avoided.  An obvious exception here is
overriding
[equals](http://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#equals(java.lang.Object)).

#### Use final fields
*See also [favor immutability](#favor-immutability)*

Final fields are useful because they declare that a field may not be reassigned.  When it comes to
checking for thread-safety, a final field is one less thing that needs to be checked.

#### Avoid mutable static state
Mutable static state is rarely necessary, and causes loads of problems when present.  A very simple
case that mutable static state complicates is unit testing.  Since unit tests runs are typically in
a single VM, static state will persist through all test cases.  In general, mutable static state is
a sign of poor class design.

#### Exceptions
##### Catch narrow exceptions
Sometimes when using try/catch blocks, it may be tempting to just `catch Exception`, `Error`,
or `Throwable` so you don't have to worry about what type was thrown.  This is usually a bad idea,
as you can end up catching more than you really wanted to deal with.  For example,
`catch Exception` would capture `NullPointerException`, and `catch Throwable` would capture
`OutOfMemoryError`.

    :::java
    // Bad.
    //   - If a RuntimeException happens, the program continues rather than aborting.
    try {
      storage.insertUser(user);
    } catch (Exception e) {
      LOG.error("Failed to insert user.");
    }

    try {
      storage.insertUser(user);
    } catch (StorageException e) {
      LOG.error("Failed to insert user.");
    }

##### Don't swallow exceptions
An empty `catch` block is usually a bad idea, as you have no signal of a problem.  Coupled with
[narrow exception](#catch-narrow-exceptions) violations, it's a recipe for disaster.

##### When interrupted, reset thread interrupted state
Many blocking operations throw
[InterruptedException](http://docs.oracle.com/javase/7/docs/api/java/lang/InterruptedException.html)
so that you may be awaken for events like a JVM shutdown.  When catching `InterruptedException`,
it is good practice to ensure that the thread interrupted state is preserved.

IBM has a good [article](http://www.ibm.com/developerworks/java/library/j-jtp05236/index.html) on
this topic.

    :::java
    // Bad.
    //   - Surrounding code (or higher-level code) has no idea that the thread was interrupted.
    try {
      lock.tryLock(1L, TimeUnit.SECONDS)
    } catch (InterruptedException e) {
      LOG.info("Interrupted while doing x");
    }

    // Good.
    //   - Interrupted state is preserved.
    try {
      lock.tryLock(1L, TimeUnit.SECONDS)
    } catch (InterruptedException e) {
      LOG.info("Interrupted while doing x");
      Thread.currentThread().interrupt();
    }

##### Throw appropriate exception types
Let your API users obey [catch narrow exceptions](#catch-narrow-exceptions), don't throw Exception.
Even if you are calling another naughty API that throws Exception, at least hide that so it doesn't
bubble up even further.  You should also make an effort to hide implementation details from your
callers when it comes to exceptions.

    :::java
    // Bad.
    //   - Caller is forced to catch Exception, trapping many unnecessary types of issues.
    interface DataStore {
      String fetchValue(String key) throws Exception;
    }

    // Better.
    //   - The interface leaks details about one specific implementation.
    interface DataStore {
      String fetchValue(String key) throws SQLException, UnknownHostException;
    }

    // Good.
    //   - A custom exception type insulates the user from the implementation.
    //   - Different implementations aren't forced to abuse irrelevant exception types.
    interface DataStore {
      String fetchValue(String key) throws StorageException;

      static class StorageException extends Exception {
        ...
      }
    }

### Use newer/better libraries

#### StringBuilder over StringBuffer
[StringBuffer](http://docs.oracle.com/javase/7/docs/api/java/lang/StringBuffer.html) is thread-safe,
which is rarely needed.

#### ScheduledExecutorService over Timer
Drawing from [Java Concurrency in Practice](#recommended-reading) (directly borrowed from
a stackoverflow
[question](http://stackoverflow.com/questions/409932/java-timer-vs-executorservice)).

- `Timer` can be sensitive to changes in the system clock, `ScheduledThreadPoolExecutor` is not

- `Timer` has only one execution thread, so long-running task can delay other tasks.

- `ScheduledThreadPoolExecutor` can be configured with multiple threads and a `ThreadFactory`<br />
  *See [manage threads properly](#manage-threads-properly)*

- Exceptions thrown in `TimerTask` kill the thread, rendering the `Timer` ineffective.

- ThreadPoolExecutor provides `afterExceute` so you can explicitly handle execution results.

#### List over Vector
`Vector` is synchronized, which is often unneeded.  When synchronization is desirable,
a [synchronized list](http://docs.oracle.com/javase/7/docs/api/java/util/Collections.html#synchronizedList(java.util.List))
can usually serve as a drop-in replacement for `Vector`.

### equals() and hashCode()
If you override one, you must implement both.
*See the equals/hashCode
[contract](http://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#hashCode())*

[Objects.equal()](http://docs.guava-libraries.googlecode.com/git-history/v11.0.2/javadoc/com/google/common/base/Objects.html#equal(java.lang.Object, java.lang.Object))
and
[Objects.hashCode()](http://docs.guava-libraries.googlecode.com/git-history/v11.0.2/javadoc/com/google/common/base/Objects.html#hashCode(java.lang.Object...))
make it very easy to follow these contracts.

### Premature optimization is the root of all evil.
Donald Knuth is a smart guy, and he had a few things to
[say](http://c2.com/cgi/wiki?PrematureOptimization) on the topic.

Unless you have strong evidence that an optimization is necessary, it's usually best to implement
the un-optimized version first (possibly leaving notes about where optimizations could be made).

So before you spend a week writing your memory-mapped compressed huffman-encoded hashmap, use the
stock stuff first and *measure*.

### TODOs

#### Leave TODOs early and often
A TODO isn't a bad thing - it's signaling a future developer (possibly yourself) that a
consideration was made, but omitted for various reasons.  It can also serve as a useful signal when
debugging.

#### Leave no TODO unassigned
TODOs should have owners, otherwise they are unlikely to ever be resolved.

    :::java
    // Bad.
    //   - TODO is unassigned.
    // TODO: Implement request backoff.

    // Good.
    // TODO(George Washington): Implement request backoff.

#### Adopt TODOs
You should adopt an orphan if the owner has left the company/project, or if you make
modifications to the code directly related to the TODO topic.

### Obey the Law of Demeter ([LoD](http://en.wikipedia.org/wiki/Law_of_Demeter))
The Law of Demeter is most obviously violated by breaking the
[one dot rule](http://en.wikipedia.org/wiki/Law_of_Demeter#In_object-oriented_programming), but
there are other code structures that lead to violations of the spirit of the law.

#### In classes
Take what you need, nothing more.  This often relates to [texas constructors](#stay-out-of-texas)
but it can also hide in constructors or methods that take few parameters.  The key idea is
to defer assembly to the layers of the code that know enough to assemble and instead just
take the minimal interface you need to get your work done.

    :::java
    // Bad.
    //   - Weigher uses hosts and port only to immediately construct another object.
    class Weigher {
      private final double defaultInitialRate;

      Weigher(Iterable<String> hosts, int port, double defaultInitialRate) {
        this.defaultInitialRate = validateRate(defaultInitialRate);
        this.weightingService = createWeightingServiceClient(hosts, port);
      }
    }

    // Good.
    class Weigher {
      private final double defaultInitialRate;

      Weigher(WeightingService weightingService, double defaultInitialRate) {
        this.defaultInitialRate = validateRate(defaultInitialRate);
        this.weightingService = checkNotNull(weightingService);
      }
    }

If you want to provide a convenience constructor, a factory method or an external factory
in the form of a builder you still can, but by making the fundamental constructor of a
Weigher only take the things it actually uses it becomes easier to unit-test and adapt as
the system involves.

#### In methods
If a method has multiple isolated blocks consider naming these blocks by extracting them
to helper methods that do just one thing.  Besides making the calling sites read less
like code and more like english, the extracted sites are often easier to flow-analyse for
human eyes.  The classic case is branched variable assignment.  In the extreme, never do
this:

    :::java
    void calculate(Subject subject) {
      double weight;
      if (useWeightingService(subject)) {
        try {
          weight = weightingService.weight(subject.id);
        } catch (RemoteException e) {
          throw new LayerSpecificException("Failed to look up weight for " + subject, e)
        }
      } else {
        weight = defaultInitialRate * (1 + onlineLearnedBoost);
      }

      // Use weight here for further calculations
    }

Instead do this:

    :::java
    void calculate(Subject subject) {
      double weight = calculateWeight(subject);

      // Use weight here for further calculations
    }

    private double calculateWeight(Subject subject) throws LayerSpecificException {
      if (useWeightingService(subject)) {
        return fetchSubjectWeight(subject.id)
      } else {
        return currentDefaultRate();
      }
    }

    private double fetchSubjectWeight(long subjectId) {
      try {
        return weightingService.weight(subjectId);
      } catch (RemoteException e) {
        throw new LayerSpecificException("Failed to look up weight for " + subject, e)
      }
    }

    private double currentDefaultRate() {
      defaultInitialRate * (1 + onlineLearnedBoost);
    }

A code reader that generally trusts methods do what they say can scan calculate
quickly now and drill down only to those methods where I want to learn more.

### Don't Repeat Yourself ([DRY](http://en.wikipedia.org/wiki/Don't_repeat_yourself))
For a more long-winded discussion on this topic, read
[here](http://c2.com/cgi/wiki?DontRepeatYourself).

#### Extract constants whenever it makes sense

#### Centralize duplicate logic in utility functions

### Manage threads properly
When spawning a thread, either directly or with a thread pool, you need to take special care that
you properly manage the lifecycle.  Please familiarize yourself with the concept
of daemon and non-daemon threads (and their effect on the JVM lifecycle) by reading the
documentation for [Thread](http://docs.oracle.com/javase/7/docs/api/java/lang/Thread.html).
Failing to understand these concepts can cause your application to hang at shutdown.

Shutting down an
[ExecutorService](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ExecutorService.html)
properly is a slightly tricky process (see javadoc).
If your code manages an executor service with non-daemon threads, you need to follow this procedure.
[ExecutorServiceShutdown]([ExecutorService](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ExecutorService.html))
very nicely contains this behavior for you.

If you want to automatically perform cleanup like this when the VM is shutting down, consider
registering with
[ShutdownRegistry](https://github.com/twitter/commons/blob/master/src/java/com/twitter/common/application/ShutdownRegistry.java).

### Avoid unnecessary code
#### Superfluous temporary variables.

    :::java
    // Bad.
    //   - The variable is immediately returned, and just serves to clutter the code.
    List<String> strings = fetchStrings();
    return strings;

    // Good.
    return fetchStrings();

#### Unneeded assignment.

    :::java
    // Bad.
    //   - The null value is never realized.
    String value = null;
    try {
      value = "The value is " + parse(foo);
    } catch (BadException e) {
      throw new IllegalStateException(e);
    }

    // Good
    String value;
    try {
      value = "The value is " + parse(foo);
    } catch (BadException e) {
      throw new IllegalStateException(e);
    }

### The 'fast' implementation
Don't bewilder your API users with a 'fast' or 'optimized' implementation of a method.

    :::java
    int fastAdd(Iterable<Integer> ints);

    // Why would the caller ever use this when there's a 'fast' add?
    int add(Iterable<Integer> ints);
