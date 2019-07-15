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
