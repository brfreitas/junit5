# JUnit 5

## Annotations

|annotation            |description                                    |JUnit4 equivalent  |
|----------------------|-----------------------------------------------|-------------------|
|@BeforeAll            |Executes once before all tests run             |@BeforeClass       |
|@BeforeEach           |Executes before each test case run             |@Before            |
|@Disabled             |Not executes the annotated test case           |@Ignore            |
|@AfterEach            |Executes after each test case run              |@After             |
|@AfterAll             |Executes once after all tests was run          |@AfterClass        |

## Improvements

### Visibility
On JUnit 5 the tests methods can't be public. 
Only is necessary package visibility. 
Private visibility isn't allowed. 
The same is applied to the tests class.

### Life cycle

* **@Test** - The most basic JUnit 4 annotation, used to mark methods to run as tests.
Comparing with JUnit 4  version, was removed optionals arguments from this annotation.

* **@Before** e **@After** - To configure codes, before and after tests. We can use @BeforeAll, @BeforeEach, @AfterEach e @AfterAll.
This annotations @BeforeClass, @Before, @After e @AfterClass of JUnit 4 are identical. The methods annotated with @BeforeAll and @AfterAll 
must be static. 

* **@Disabled** - Used when you want deactivate a test. Is equivalent to @Ignored on JUnit 4.

*If different methods are annotated with the same annotation, the execution order is undefined.*

### Assertions
After all created and working, to check the desired behavior is used assertions. Occurred many incremental improvements as:

* Assertion's message was moved to last parameter.

* Using lambdas the assertion's massage can be lazy, improving the performance if this operation is slow.

* Boolean assertions accepts predicates.

Have a new *assertAll*, to check related invocation's group. If any assert fail this prints only her messages. example:
```java
@Test
void assertRelatedProperties() {
    Developer dev = new Developer("Johannes", "Link");

    assertAll("developer",
   		 () -> assertEquals("Marc", dev.firstName()),
   		 () -> assertEquals("Philipp", dev.lastName())
    );
}
```

It reproduces fail massages like:

```bash
org.opentest4j.MultipleFailuresError: developer (2 failures)
    expected:  but was: 
    expected:  but was:
```

* **assertThrows** e **expectThrows** both fail tests when expected exception isn't thrown. But to continue
checking the exception *expectThrows* returns the exception.

```java
@Test
void assertExceptions() {
    // assert that the method under test
    // throws the expected exception */
    assertThrows(Exception.class, unitUnderTest::methodUnderTest);

    Exception exception = expectThrows(
        Exception.class,
        unitUnderTest::methodUnderTest);
    assertEquals("This shouldn't happen.", exception.getMessage());
}
```

### Assumptions
Assumptions allow you to run tests if a given condition is expected.
As an assumption, it needs to be formulated as a Boolean expression, if the condition is not met the test terminates.
This can be used to reduce runtime and verbosity of test tools, especially in cases of failure.

```java
@Test
void exitIfFalseIsTrue() {
    assumeTrue(false);
    System.exit(1);
}

@Test
void exitIfTrueIsFalse() {
    assumeFalse(this::truism);
    System.exit(1);
}

private boolean truism() {
    return true;
}

@Test
void exitIfNullEqualsString() {
    assumingThat(
             // state an assumption (a false one in this case) ...
   		 "null".equals(null),
             // … and only execute the lambda if it is true
   		 () -> System.exit(1)
    );
}
```

##Architecture
The monolithic JUnit 4 architecture did make the development hard. To improve this the Junit 5 does:

### Separating concepts
A tests framework have two important tasks:

* allow developers writes tests;

* allow tools runs tests.

### JUnit as platform
JUnit is the most popular test framework. This success occurs because the strong integration with IDEs and tools.
At same time, others frameworks have exploring new interesting approaches to tests, however the lack of integration does the
developers back to JUnit. Maybe this tests frameworks can self benefit of JUnit success and use the integration offered. 

##Modularization
The modularization bring an decoupled architecture:

* An API to developers writes your tests;
* An engine to each API discover, shows and run the correspondent test;
* An API to be implemented by all engines to be used evenly;
* A mechanism that orchestrates the engines.

This separates "a tool JUnit" (1 and 2) from "platform JUnit" (3 and 4). To evidence this distinction
a project use following scheme:

* An new API called **JUnit Jupter**. (for developers)
* An platform called **JUnit Platform**. (for tools)
* An sub project called **JUnit vintage**.(for adapt JUnit 3 and 4 to be executed on JUnit 5)

The JUnit 5 is the sum of this three parts:

* **junit-jupiter-api** (1) - Contains the annotations, asserts, etc.

* **junit-jupiter-engine** (2) - To run the tests with JUnit 5.

* **junit-platform-engine** (3) - The API which all tests engines should implements.

* **junit-platform-launcher** (4) - Uses the ServiceLoader to discover the implementations of the test engines and to organize their execution. 
Provides an API for IDEs and building tools to interact with the execution of the test, for example running individual tests and presenting the results.

The benefits of this architecture are apparent; now we only need two more 
components to run the tests written with JUnit 4 tests:

* **junit-4.12** (1) - The JUnit 4 artifact functions as the API 
that the developer implements its tests, but also contains the main functionality of running the tests.
    
* **junit-vintage-motor** (2) - An implementation of the junit-platform-engine that runs the tests 
written with JUnit 4. It can be seen as a JUnit 4 adapter for version 5.
Other frameworks already provide the API for writing the tests, 
so what is missing for full integration with JUnit 5 is an implementation of the test engines.

![Alt](src/main/resources/assets/junit-5-architecture.png "JUnit 5 architecture")

##Reference

https://www.infoq.com/br/articles/JUnit-5-Early-Test-Drive/
https://junit.org/junit5/docs/current/user-guide/
