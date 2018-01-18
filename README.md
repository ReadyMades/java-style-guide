# Java Style Guide

* [Best practices](#best-practices)
* [Project Structure](#project-structure)
* [Java style](#java-style)
* [Tests style](#tests-style)
* [Xml style](#xml-style)
* [Gradle](#gradle)

## Best practices

### Уменьшай видимость полей

Держи методы как можно более закрытыми. Оставляй на виду только те методы, которые нужны клиентскому классу для выполнения задачи.

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

### Неизменяемые объекты

Предпочитай неизменяемый объекты: [Immutable objects](http://www.javapractices.com/topic/TopicAction.do?Id=29)

Отдавая изменяемый объект, мы должны убедиться, что вызвающий класс не изменит его, и мы не получим ложные данные. Поэтому, старайся ограничивать передачу изменяемых объектов.

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

### Nullable

Будь осторожен с null. Помечай поля аннотацией @Nullable.

### Не используй префиксы и суффиксы для интерфейсов

При создании интерфейса, у него не надо использовать префикс I или суффикс Interface. 

    // Bad
    public interface SimplePresenterInterface {
        void doSomething();
    }
    
    // Good
    public interface SimplePresenter {
        void doSomething();
    }

Использование такого подхода не загромождает клиентский код и упрощает понимание кода. 

    // Bad
    public class Client<SimplePresenterInterface> {
        private SimplePresenterInterface presenter;
        
        ...
    }
    
    // Good
    // client class shouldn't know about SimplePresenter implementation
    public class Client<SimplePresenter> {
        private SimplePresenter presenter;
        
        ...
    }

### Мертвый код

Удаляй мертвый код. Удаляй закомментированный код. Если считаешь, что старый код может пригодиться, оставь его в vsc и удали в актуальной версии.

### Комментарии

Не используй комментарии, если они не несут полезной информации.

    // Bad
    // This inforamtion can be accessed from vcs
    /**
     * Created by Alexey on 12/29/2016.
     */
     public class SomeClass {
         ...
     }
     
     // Bad
     // calculate X
     int x = a + b;
     
     // Bad
     // there is some magic
     byte b = (byte)(l >> (8 - i << 3));
     
     // Good
     byte offset = calculateOffsetByLength(length);

Оставляй комментарии у сложного кода и сложных методов.

### TODO

Оставляй TODO, если реализуешь какое-либо решение, но в текущий момент пропускаешь некоторые детали. Использование TODO позволяет сначала сконцентрироваться на написании основного кода, а позже - пройти по TODO и дополнить оставшиеся места.

При создании TODO указывай, кому она предназначена.

    // TODO(Verteletsky) write documentation
    
### Law of Demeter

[Wiki](https://ru.wikipedia.org/wiki/Закон_Деметры)

    // Bad
    // We access object from deep level
    // |this class
    // |-> Data
    // |---> Field value
    public void process(Data data) {
        data.field.getSomeValue();
    }
    
    // Good
    // We access same level objects and one level down object
    // |this class
    // |-> Data
    public void process(Data data) {
        data.getFieldValue();
    }

Более неочевидным пунктом этого закона является конструирование полей. Если есть возможность, то конструирование зависимостей лучше вынести из класса, это упростит код текущего класса.

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

### Don't repeat yourself

Выноси повторяющиеся элементы, будь то константы, методы или классы.

## Project structure

### Расположение файлов

    Project
    -> src
    ---> androidTest (optional)
    ---> main
    -----> java
    -----> res
    ---> test
    -----> java

### Именование файлов

Java классы - UpperCamelCase:

    SomeClass.java
    DetailsPresenter.java

Xml ресурсы - lowercase_underscore:

    screen_detail.xml
    ic_main.xml

Именование layout ресурсов:

| Component    | Class                         | Layout                         |
|--------------|-------------------------------|--------------------------------|
| Activity     | `MainActivity.java`           | `activity_main.xml`            |
| Fragment     | `ScreensFragment.java`        | `fragment_screens.xml`         |
| Screen View  | `TrainingDetailsView.java`    | `screen_training_details.xml`  |
| Dialog       | `RenameScreenshotDialog.java` | `dialog_rename_screenshot.xml` |
| Adapter Item | `LogViewHolder.java`          | `item_log.xml`                 |

Values файлы всегда именуются во множественном числе:

    strings.xml, styles.xml, colors.xml, dimens.xml, attrs.xml

## Java

### Форматирование

#### Отступы и переносы строки

[1TBS](https://en.wikipedia.org/wiki/Indentation_style#Variant:_1TBS_(OTBS))

    :::java
    // Like this.
    if (x < 0) {
      negative(x);
    } else {
      nonnegative(x);
    }
    
    // Not like this.
    if (x < 0)
      negative(x);
    
    // Also not like this.
    if (x < 0) negative(x);

Размер отступа - два пробела.

Каждый уровень вложенности добавляет 4 пробела.

    :::java
    // Bad.
    //   - Line breaks are arbitrary.
    //   - Scanning the code makes it difficult to piece the message together.
    throw new IllegalStateException("Failed to process request" + request.getId()
        + " for user " + user.getId() + " query: '" + query.getText()
        + "'");
    
    // Good.
    //   - Each component of the message is separate and self-contained.
    //   - Adding or removing a component of the message requires minimal reformatting.
    throw new IllegalStateException("Failed to process"
        + " request " + request.getId()
        + " for user " + user.getId()
        + " query: '" + query.getText() + "'");

При объявлении методов так же следует использовать переносы строки:

    :::java
    // Sub-optimal since line breaks are arbitrary and only filling lines.
    String downloadAnInternet(Internet internet, Tubes tubes,
        Blogosphere blogs, Amount<Long, Data> bandwidth) {
      tubes.download(internet);
      ...
    }

    // Acceptable.
    String downloadAnInternet(Internet internet, Tubes tubes, Blogosphere blogs,
        Amount<Long, Data> bandwidth) {
      tubes.download(internet);
      ...
    }

    // Nicer, as the extra newline gives visual separation to the method body.
    String downloadAnInternet(Internet internet, Tubes tubes, Blogosphere blogs,
        Amount<Long, Data> bandwidth) {

      tubes.download(internet);
      ...
    }

    // Also acceptable, but may be awkward depending on the column depth of the opening parenthesis.
    public String downloadAnInternet(Internet internet,
                                    Tubes tubes,
                                    Blogosphere blogs,
                                    Amount<Long, Data> bandwidth) {
      tubes.download(internet);
      ...
    }

    // Preferred for easy scanning and extra column space.
    public String downloadAnInternet(
        Internet internet,
        Tubes tubes,
        Blogosphere blogs,
        Amount<Long, Data> bandwidth) {

      tubes.download(internet);
      ...
    }

При цепочках вызовов так же следует использовать перенос строки:

    :::java
    // Bad.
    //   - Line breaks are based on line length, not logic.
    Iterable<Module> modules = ImmutableList.<Module>builder().add(new LifecycleModule())
        .add(new AppLauncherModule()).addAll(application.getModules()).build();

    // Better.
    //   - Calls are logically separated.
    //   - However, the trailing period logically splits a statement across two lines.
    Iterable<Module> modules = ImmutableList.<Module>builder().
        add(new LifecycleModule()).
        add(new AppLauncherModule()).
        addAll(application.getModules()).
        build();

    // Good.
    //   - Method calls are isolated to a line.
    //   - The proper location for a new method call is unambiguous.
    Iterable<Module> modules = ImmutableList.<Module>builder()
        .add(new LifecycleModule())
        .add(new AppLauncherModule())
        .addAll(application.getModules())
        .build();

#### Именование переменных

CamelCase для типов, camelCase для переменных, UPPER_SNAKE для констант.

#### Слишком короткие имена зарезервированы для индексов в цикле и подобных вещах

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

Избегай использования типов в имени переменной.

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
классов.

    :::java
    // Плохо.
    String _value;
    String mValue;

    // Хорошо.
    String value;

#### Окружай пробелами операции 

    :::java
    // Плохо.
    //   - Этот код тяжело читать
    int foo=a+b+1;

    // Хорошо.
    int foo = a + b + 1;

#### Используй явный порядок в операциях

Не заставляй читателя открывать 
[спецификацию](http://docs.oracle.com/javase/tutorial/java/nutsandbolts/operators.html)
чтобы он убедился в правильности этого кода. Используй скобки для 
определения порядка.

    :::java
    // Плохо.
    return a << 8 * n + 1 | 0xFF;

    // Хорошо.
    return (a << (8 * n) + 1) | 0xFF;

Лучше быть *очевидным*:

    :::java
    if ((values != null) && (10 > values.size())) {
      ...
    }

#### Используй общепринятые сокращения как слова

| Good             | Bad              |
| --------------   | --------------   |
| `XmlHttpRequest` | `XMLHTTPRequest` |
| `getCustomerId`  | `getCustomerID`  |
| `String url`     | `String URL`     |
| `long id`        | `long ID`        |

#### Аннотации

Всегда отделяй аннотации переносом строки.

    ::java
    // Bad
    @Inject private SomeField field;
    
    // Good
    @Inject
    private SomeField field;

## Tests style

Название класса теста должно совпадать с именем тестируемого класса с суффиксом `Test`.

    DatabaseHelper.java -> тестируемый класс
    DatabaseHelperTest.java -> класс теста

Название теста должно соответствовать следующему шаблону:

    @Test 
    void methodNamePreconditionExpectedBehaviour()

    // Example
    @Test 
    void signInWithEmptyEmailFails()

Исключениями являются тесты, написанные при создании интерфейса класса.


## Xml style

### Используй закрывающие теги

Если тег не содержит вложенных элементов - закрывай его.

    ::xml
    <!-- Bad -->
    <!-- Don\'t do this! -->
    <TextView
        android:id="@+id/username"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" >
    </TextView>

    <!-- Good -->
    <TextView
        android:id="@+id/username"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

### Именование

Id и имена ресурсов используют __lowercase_underscore__.

Используй короткие имена. Не включай данные о типе ресурса, если это не необходимо.

    :xml
    <!-- file: screen_profile.xml -->
    
    <!-- Bad -->
    <TextView
        android:id="@+id/text_username"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
    
    <!-- Good -->
    <!-- Username can't be an image or button -->
    <TextView
        android:id="@+id/username"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

Стили именуются с помощью __UpperCamelCase__.


## Gradle

Предпочитаемая структура gradle файлов:

    project
    -> module1
    -> module2
    -> android_module
    -> gradle (содержит все файлы, необходимые для сборки проекта)
    ---> module1
    ---> module2
    ---> android_module
    ---> buildscript.gradle
    -> settings.gradle
    
settings.gradle содержит информацию о плагинах и подключаемых модулях

    // example settings.gradle
    pluginManagement {
      resolutionStrategy {
        eachPlugin {
          if (requested.id.namespace != null && requested.id.namespace.startsWith('com.equeo.heart')) {
            useModule('com.equeo.heart.gradle.plugins:gradle-plugins:1.0.32')
          }
          if (requested.id.namespace != null && requested.id.namespace.startsWith('com.android')) {
            useModule('com.android.tools.build:gradle:2.3.3')
          }
        }
      }
      repositories {
        gradlePluginPortal()
        maven {
          url "https://jcenter.bintray.com"
        }
        maven {
          url "${artifactory_url}libs-release-local"
          credentials {
            username = "${artifactory_username}"
            password = "${artifactory_password}"
          }
        }
      }
    }

    include ':objectstore'
    include ':objectstore-android'

 
Внутри папки модуля содержатся следующие файлы:

* config.gradle

* deps.gradle

* publish.gradle

config.gradle отвечает за общую конфигурацию модуля и импортирует все остальные файлы

    // example config.gradle
    apply from: '../gradle/publish.gradle'
    apply from: '../gradle/deps.gradle'

    sourceCompatibility = "1.7"
    targetCompatibility = "1.7"

deps.gradle отвечает за зависимости

    // example deps.gradle
    def versions = [
      ormlite    : '5.0',
      rxjava     : '2.1.3',
      sqlite_jdbc: '3.20.1',
      junit      : '4.12'
    ]
    
    def libs = [
      ormlite: "com.j256.ormlite:ormlite-core:$versions.ormlite",
      rxjava : "io.reactivex.rxjava2:rxjava:$versions.rxjava",

      ormlite_jdbc: "com.j256.ormlite:ormlite-jdbc:$versions.ormlite",
      sqlite_jdbc: "org.xerial:sqlite-jdbc:$versions.sqlite_jdbc",
      junit: "junit:junit:$versions.junit",
    ]

    dependencies {
      compile libs.ormlite
      compile libs.rxjava

      testCompile libs.junit
      testCompile libs.ormlite_jdbc
      testCompile libs.sqlite_jdbc
    }

publish.gradle содержит информацию необходимую для публикации артефакта

    // example publish.gradle
    publication {
      group = 'com.r_mades'
      artifact = 'objectstore'
      version = '1.0.1'
      repoUrl = "${artifactory_url}libs-release-local"
      username = "${artifactory_username}"
      password = "${artifactory_password}"
    }
    

