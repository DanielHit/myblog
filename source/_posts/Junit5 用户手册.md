---
title: Junit 5 用户手册
date: 2016-08-10 14:48:30
tags: 编程, junit5
categories: 笔记
---

Junit 是Java中使用最多的第三方库, 在Java测试体系中占据了极为重要的位置. 目前JUnit 5 已经发版,以下是官方用户使用手册的中文翻译, 以供参考.

<!-- more -->

<!-- ## 目录
[TOC]
 -->
## 1. 概述

本文档是为了提供全面的 参考手册, 程序员编写测试用例，插件作者，引擎作者，同样提供 工具和IDE的开发者.

### 1.1 Junit5 有什么特点

不像之前的Junit的版本, Junit5 是有由来自3个不同的子项目组合起来的.

> ### Junit5 = *JUnit Platform* + *JUnit Jupiter* + *JUnit Vintage*

* **Junit Platform** Junit平台在JVM的平台上提供了 基础的测试框架. 它同样提供了  [TestEngine](http://junit.org/junit5/docs/current/api/org/junit/platform/engine/TestEngine.html)的API接口,以用于 在平台上开发测试框架. 更重要的是，这个Junit平台提供了一个命令行应用程序和在maven以及Gradle商家编程插件, 来进行单元测试

* **JUnit Jupiter** 是一个 Junit5 新的单元测试编程模型. Jupiter 子项目提供了 TestEngine 以用于运行 Juniter. 

* **Junit Vintage** 提供了一个 运行 Junit3 和 Junit4 的TestEngine的平台.

### 1.2 支持的Java版本
Junit5 在运行时需要依赖Java8. 但是, 你依然可以测试通过之前JDK版本号编译的代码. 

## 2 安装

对于final release 和 milestones版本的构件(Artifacts)都部署到了Maven Central.
Snapshot的构建部署到了 Sonatype的 仓库,路径在 /org/junit.

### 2.1. Dependency Metadata
####  2.1.1. JUnit Platform
Group ID: org.junit.platform
Version: 1.0.0-M2

Artifact IDs:
junit-platform-commons
junit-platform-console
junit-platform-engine
junit-platform-gradle-plugin
junit-platform-launcher
junit-platform-runner
junit-platform-surefire-provider

####  2.1.2. JUnit Jupiter
Group ID: org.junit.jupiter
Version: 5.0.0-M2
Artifact IDs:
junit-jupiter-api
junit-jupiter-engine

####  2.1.3. JUnit Vintage
Group ID: org.junit.vintage
Version: 4.12.0-M2
Artifact ID: junit-vintage-engine

### 2.2. JUnit Jupiter Sample Projects
[Junit5-samples](https://github.com/junit-team/junit5-samples) 项目代码仓库托管了一系列的 基于 `Junit Jupiter` 和 `Junit Vintage` 示例项目. 你将会在一下的项目中发现 相对应的 `build.gradle` 和 `pom.xml`文件:

- 对于Gradle项目, 检查 `junit5-gradle-consumer` 项目
- 对于Maven项目, 检查 `junit5-maven-consumer` 项目

## 3 编写测试用例
*第一个测试用例*

``` java
import static org.junit.jupiter.api.Assertions.assertEquals;

import org.junit.jupiter.api.Test;

class FirstJUnit5Tests {

    @Test
    void myFirstTest() {
        assertEquals(2, 1 + 1);
    }

}
```

### 3.1 注解

`Junit Jupiter` 支持以下的注解用于测试配置 和 拓展 框架

所有核心的注解都位于  `junit-jupiter-api` 模块的 `org.junit.jupiter.api`的包下面.

| 注解名称     | 描述                                                                                                                          |
| :----------  | ------------------------------------------------------------:                                                                 |
| @Test        | 声明这个方法为测试方法                                                                                                        |
| @TestFactory | 声明这个方法是针对于[dynamic tests](http://junit.org/junit5/docs/current/user-guide/#writing-tests-dynamic-tests)测试工厂     |
| @DisplayName | 给这个类或者方法设定一个特殊的名字                                                                                            |
| @BeforeEach  | 声明这个方法将在每一个测试方法之前进行执行,类似于 `Junit4`中的 `@Before`                                                      |
| @AfterEach   | 声明这个方法将在每一个测试方法之后进行执行,类似于 `Junit4`中的 `@After`                                                       |
| @BeforeAll   | 声明这个方法将在所有的测试方法之前进行执行, 类似于 `Junit4` 中的 `@BeforeClass`, 所有被注解的方法都必须是`静态的`和*可继承的* |
| @AfterAll    | 声明这个方法将在所有的测试方法之后进行执行, 类似于 `Junit4` 中的 `@AfterClass`, 所有被注解的方法都必须是`静态的`和*可继承的*  |
| @Nested      | 声明这个方法是 一个嵌套的, 非静态的方法, 由于Java语言的限制, @BeforeAll 和 @AfterAll 方法都不能被用在 @Nested注解的方法上     |
| @Tag         | 被用于通过声明 标签来过滤测试方法, 既不是在方法级别也不是在类的级别上. 相当于 Junit 4中的 测试组                              |
| @Disabled    | 用于声明一个测试方法或者测试类失效, 相当于 Junit 4 中的 @Ignore                                                               |
| @ExtendWith  | 被用于使用在 用户定制的扩展组件上                                                                                             |

#### 3.1.1 元注解 和 组合注解
`Junit Jupiter` 注解可以被用于元注解. 这意味着, 你可以定义自己的定制的注解, 定制的注解就可以从 元注解进行继承.

举个例子, 与其不断的通过代码复制和粘贴 `@Tag("fast")` , 你可以创建如下定制化的组合注解 `@Fast`. `@Fast`可以被用于 替代 `@Tag("fast")`.

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

import org.junit.jupiter.api.Tag;

@Target({ ElementType.TYPE, ElementType.METHOD })
@Retention(RetentionPolicy.RUNTIME)
@Tag("fast")
public @interface Fast {
}

```

### 3.2 标准的测试类
*一个标准的单元测试*

```java
import static org.junit.jupiter.api.Assertions.fail;

import org.junit.jupiter.api.AfterAll;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Disabled;
import org.junit.jupiter.api.Test;

class StandardTests {

    @BeforeAll
    static void initAll() {
    }

    @BeforeEach
    void init() {
    }

    @Test
    void succeedingTest() {
    }

    @Test
    void failingTest() {
        fail("a failing test");
    }

    @Test
    @Disabled("for demonstration purposes")
    void skippedTest() {
        // not executed
    }

    @AfterEach
    void tearDown() {
    }

    @AfterAll
    static void tearDownAll() {
    }

}
```

> 任何测试类和测试方法都必须是 `public`

### 3.3 展示名称(DisplayName)
测试类和测试方法都可以声明定制的特殊的名称, 通过 空间/特殊的字符, 甚至是emojis表情. 定制的名称将被用于在展示测试任务和测试报告之中.

```java
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

@DisplayName("A special test case")
class DisplayNameDemo {

    @Test
    @DisplayName("Custom test name containing spaces")
    void testWithDisplayNameContainingSpaces() {
    }

    @Test
    @DisplayName("╯°□°）╯")
    void testWithDisplayNameContainingSpecialCharacters() {
    }

    @Test
    @DisplayName("😱")
    void testWithDisplayNameContainingEmoji() {
    }

}

```

### 3.4 断言(Assertions)
`Junit Jupiter` 继承了 许多 `Junit 4`中的断言方法, 同时增加了 一些适配 Java 8 lambdas特点的方法. 所有的 `Junit Jupiter` 都是静态方法, 在 [org.junit.jupiter.Assertions](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/Assertions.html)类中.

```java
import static org.junit.jupiter.api.Assertions.assertAll;
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertTrue;
import static org.junit.jupiter.api.Assertions.expectThrows;

import org.junit.jupiter.api.Test;

class AssertionsDemo {

    @Test
    void standardAssertions() {
        assertEquals(2, 2);
        assertEquals(4, 4, "The optional assertion message is now the last parameter.");
        assertTrue(2 == 2, () -> "Assertion messages can be lazily evaluated -- "
                + "to avoid constructing complex messages unnecessarily.");
    }

    @Test
    void groupedAssertions() {
        // In a grouped assertion all assertions are executed, and any
        // failures will be reported together.
        assertAll("address",
            () -> assertEquals("John", address.getFirstName()),
            () -> assertEquals("User", address.getLastName())
        );
    }

    @Test
    void exceptionTesting() {
        Throwable exception = expectThrows(IllegalArgumentException.class, () -> {
            throw new IllegalArgumentException("a message");
        });
        assertEquals("a message", exception.getMessage());
    }

}
```


### 3.5 假定(Assumptions)
`Junit Jupiter` 继承了Junit 4中 一部分假定方法, 同时提供了 部分借用Java 8 lambdas的特性. 所有的 `Junit Jupiter` assumptions都是静态的方法,在 [org.junit.jupiter.Assumptions](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/Assumptions.html)类中进行定义. 

```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assumptions.assumeTrue;
import static org.junit.jupiter.api.Assumptions.assumingThat;

import org.junit.jupiter.api.Test;

public class AssumptionsDemo {

    @Test
    void testOnlyOnCiServer() {
        assumeTrue("CI".equals(System.getenv("ENV")));
        // remainder of test
    }

    @Test
    void testOnlyOnDeveloperWorkstation() {
        assumeTrue("DEV".equals(System.getenv("ENV")),
            () -> "Aborting test: not on developer workstation");
        // remainder of test
    }

    @Test
    void testInAllEnvironments() {
        assumingThat("CI".equals(System.getenv("ENV")),
            () -> {
                // perform these assertions only on the CI server
                assertEquals(2, 2);
            });

        // perform these assertions in all environments
        assertEquals("a string", "a string");
    }

}

```

### 3.6 禁用测试(Disabling Tests)
以下是一个禁用测试的测试单元.

```java
import org.junit.jupiter.api.Disabled;
import org.junit.jupiter.api.Test;

@Disabled
class DisabledClassDemo {
    @Test
    void testWillBeSkipped() {
    }
}
```

同样, 这里也可以禁用单个测试方法

```java
import org.junit.jupiter.api.Disabled;
import org.junit.jupiter.api.Test;

class DisabledTestsDemo {

    @Disabled
    @Test
    void testWillBeSkipped() {
    }

    @Test
    void testWillBeExecuted() {
    }
}
```


### 3.7 标签和过滤(Tagging and Filtering)

测试类和方法可以被打上标签. 这些标签可以被之后用来过滤 [test discovery and execution](http://junit.org/junit5/docs/current/user-guide/#running-tests)

```java
import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.Test;

@Tag("fast")
@Tag("model")
class TaggingDemo {

    @Test
    @Tag("taxes")
    void testingTaxCalculation() {
    }

}
```

### 3.8 嵌套测试(Nested Tests)
嵌套测试给我们的测试者提供 表达不同测试组之间关系的功能. 以下是一个定制的例子:

*嵌套测试单元以用来测试一个堆栈*
```java
    import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertThrows;
import static org.junit.jupiter.api.Assertions.assertTrue;

import java.util.EmptyStackException;
import java.util.Stack;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Nested;
import org.junit.jupiter.api.Test;

@DisplayName("A stack")
class TestingAStackDemo {

    Stack<Object> stack;

    @Test
    @DisplayName("is instantiated with new Stack()")
    void isInstantiatedWithNew() {
        new Stack<>();
    }

    @Nested
    @DisplayName("when new")
    class WhenNew {

        @BeforeEach
        void createNewStack() {
            stack = new Stack<>();
        }

        @Test
        @DisplayName("is empty")
        void isEmpty() {
            assertTrue(stack.isEmpty());
        }

        @Test
        @DisplayName("throws EmptyStackException when popped")
        void throwsExceptionWhenPopped() {
            assertThrows(EmptyStackException.class, () -> stack.pop());
        }

        @Test
        @DisplayName("throws EmptyStackException when peeked")
        void throwsExceptionWhenPeeked() {
            assertThrows(EmptyStackException.class, () -> stack.peek());
        }

        @Nested
        @DisplayName("after pushing an element")
        class AfterPushing {

            String anElement = "an element";

            @BeforeEach
            void pushAnElement() {
                stack.push(anElement);
            }

            @Test
            @DisplayName("it is no longer empty")
            void isEmpty() {
                assertFalse(stack.isEmpty());
            }

            @Test
            @DisplayName("returns the element when popped and is empty")
            void returnElementWhenPopped() {
                assertEquals(anElement, stack.pop());
                assertTrue(stack.isEmpty());
            }

            @Test
            @DisplayName("returns the element when peeked but remains not empty")
            void returnElementWhenPeeked() {
                assertEquals(anElement, stack.peek());
                assertFalse(stack.isEmpty());
            }
        }
    }
}

```

> 只有非静态的嵌套类(如:内部类) 可以被标记为 `@Nested` 测试. 嵌套可以是任意的深度, 并且这些内部类可以被认为是测试类中全部的组成部分, 除了一个例外: `@BeforeAll` 和 `@AfterAll` 不起作用, 因为 Java 并不允许 `static` 成员在内部勒类中. 

### 3.9 对于构造函数和方法的依赖注入

在之前所有的 Junit版本中, 测试构造函数或者方法并不允许有参数(至少在标准的测试应用中). 作为`Junit Jupiter`的一个主要变化, 测试构造函数和方法现在都被允许拥有参数. 这就提高了测试的灵活性, 同时构造函数和方法的依赖注入.

- [ParameterResolver](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/extension/ParameterResolver.html)定义了在运行期间动态的解析参数值的API接口. 如果一个测试单元的构造函数或者 一个 @Test, @TestFactory, @BeforeEach, @AfterEach, @BeforeAll, 和 @AfterAll 注解的方法接受了一个参数, 这个参数必须在 运行的时候通过一个注册的 `ParameterResolver` 进行解析.这里有两个内置的自动注册的解析器:
`TestInfoParameterResolver`: 如果一个方法参数 是一种 `TestInfo`类型, 则`TestInfoParameterResolver` 将会自动的 获取一个 `TestInfo` 实例获取值. 这个 `TestInfo`可以被用于 获取一个测试的 展示名称, 测试的类, 测试的方法 或者对应的标签tags. 展示名称要么是一个技术名称, 比如 测试类的或者测试方法的名字, 要么是一个 通过 `@DisplayName` 定制化的名称. 

[TestInfo](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/TestInfo.html) 作为一个替换 Junit 4 中 `TestName`的方法. 以下是具体的使用例子:

```java
    import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertTrue;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.TestInfo;

class TestInfoDemo {

    @BeforeEach
    void init(TestInfo testInfo) {
        String displayName = testInfo.getDisplayName();
        assertTrue(displayName.equals("TEST 1") || displayName.equals("test2()"));
    }

    @Test
    @DisplayName("TEST 1")
    @Tag("my tag")
    void test1(TestInfo testInfo) {
        assertEquals("TEST 1", testInfo.getDisplayName());
        assertTrue(testInfo.getTags().contains("my tag"));
    }

    @Test
    void test2() {
    }

}
```

- [TestReporterParameterResolver](https://github.com/junit-team/junit5/blob/r5.0.0-M2/junit-jupiter-engine/src/main/java/org/junit/jupiter/engine/extension/TestReporterParameterResolver.java): 如果一个方法的参数 是一个 [TestReport](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/TestReporter.html) 类型, 这个 `TestReporterParameterResolver` 将会 获取一个 TestReport实例. 这个 TestReport将会被用于输出目前测试运行中的数据. 这些数据将被可以用于 [TestExecutionListener](http://junit.org/junit5/docs/current/api/org/junit/platform/launcher/TestExecutionListener.html).reportingEntryPublished() 同时将会被IDEs使用, 或者被包含在测试报告中. 

在`Junit Jupiter` 中, 你应该使用 TestReport 来打印Junit 4的`stdout`和`stderr`. 通过使用 @RunWith(JUnitPlatform.class)将会将所有的报告都进入到 `stdout`. 

```java
import java.util.HashMap;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.TestReporter;

class TestReporterDemo {

    @Test
    void reportSingleValue(TestReporter testReporter) {
        testReporter.publishEntry("a key", "a value");
    }

    @Test
    void reportSeveralValues(TestReporter testReporter) {
        HashMap<String, String> values = new HashMap<>();
        values.put("user name", "dk38");
        values.put("award year", "1974");

        testReporter.publishEntry(values);
    }

}
```

> 其他的参数解析器可以通过 使用 @ExtendWith注解来进行扩展开发. 

检出的 [MockitoExtension](https://github.com/junit-team/junit5-samples/blob/r5.0.0-M2/junit5-mockito-extension/src/main/java/com/example/mockito/MockitoExtension.java)类, 比如 一个定制的 ParameterResolver解析器, 同时还没有完全达到生产环境的标准, 它演示了 扩展模型和参数解析流程的 简洁性和直观性. MyMockitoTest显示了如何注入一个Mockito 到  @BeforeEach and @Test 方法中.

```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.when;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import com.example.Person;
import com.example.mockito.MockitoExtension;

@ExtendWith(MockitoExtension.class)
class MyMockitoTest {

    @BeforeEach
    void init(@Mock Person person) {
        when(person.getName()).thenReturn("Dilbert");
    }

    @Test
    void simpleTestWithInjectedMock(@Mock Person person) {
        assertEquals("Dilbert", person.getName());
    }

}
```


### 3.10 接口默认的方法(Interface Default Methods)

`Junit Jupiter` 允许  @Test, @TestFactory, @BeforeEach, 和 @AfterEach 成为接口默认的方法. 一个可能的使用场景就是 这样我们就可以为接口协议写测试方法了. 举个例子, 你可以为如何 实现Object.equals和Comparable.compareTo 制定标准,从而编写测试,具体如下:

```java
    public interface Testable<T> {

    T createValue();

}
```

```java
public interface EqualsContract<T> extends Testable<T> {

    T createNotEqualValue();

    @Test
    default void valueEqualsItself() {
        T value = createValue();
        assertEquals(value, value);
    }

    @Test
    default void valueDoesNotEqualNull() {
        T value = createValue();
        assertFalse(value.equals(null));
    }

    @Test
    default void valueDoesNotEqualDifferentValue() {
        T value = createValue();
        T differentValue = createNotEqualValue();
        assertNotEquals(value, differentValue);
        assertNotEquals(differentValue, value);
    }

}
```

```java
public interface ComparableContract<T extends Comparable<T>> extends Testable<T> {

    T createSmallerValue();

    @Test
    default void returnsZeroWhenComparedToItself() {
        T value = createValue();
        assertEquals(0, value.compareTo(value));
    }

    @Test
    default void returnsPositiveNumberComparedToSmallerValue() {
        T value = createValue();
        T smallerValue = createSmallerValue();
        assertTrue(value.compareTo(smallerValue) > 0);
    }

    @Test
    default void returnsNegativeNumberComparedToSmallerValue() {
        T value = createValue();
        T smallerValue = createSmallerValue();
        assertTrue(smallerValue.compareTo(value) < 0);
    }

}
```

在你的测试类中, 你可以实现contract接口, 这样就相应的继承了相应的测试单元. 当然你必须实现这些抽象的方法.

```java
class StringTests implements ComparableContract<String>, EqualsContract<String> {

    @Override
    public String createValue() {
        return "foo";
    }

    @Override
    public String createSmallerValue() {
        return "bar"; // 'b' < 'f' in "foo"
    }

    @Override
    public String createNotEqualValue() {
        return "baz";
    }

}
```

上面这些测试仅仅是样例,因此没有完全写完. 

### 3.11 动态测试(Dynamic Tests)

在`JUnit Jupiter`中标准的 `@Test` 注解和Junit 4中的 @Test注解非常的相似. 同样都是为了描述 这是测试单元的方法. 这些测试类在编译期间定义, 因此他们无法再运行期间改变. *Assumptions提供了一种动态行为的基本模式, 但是被限制与他们的表达形式* 

除了 一些测试程序中完全新的测试类型在 `JUnit Jupiter` 中被介绍. 还有一种新的动态测试方法被介绍, 可以通过 `@TestFactory` 注解的工厂方法在运行时产生. 

和 `@Test` 方法相反, 一个 `@TestFactory`方法 并不是测试本身, 而是一个测试单元的工厂. 这样, 一个动态的测试方法就是这个工厂的产品了. 技术上讲, `@TestFactry` 方法必须返回 `Stream`, `Collection`, `Iterable` 或者 `DynamicTest` 实例. 这些 动态测试实例将会被 
懒加载, 确保动态和不确定的测试实例产生. 

相比于`@Test`方法, `@TestFactory` 方法不能使 `private` 或者 `static`. 并且可以选择性的通过 `ParameterResolvers` 来解析参数. 

`DynamicTest` 是运行时生成的测试. 它由 *展示名称(display name)* 和 `Executable`组成. `Executable` 是一个 `@FunctionalInterface`的实现,这意味着 这些动态测试的实现可以通过  *lambda expressions* 或者 方法引用.

> *动态测试的生命周期*

`JUnit Jupiter` 5.0.0-M2版本, 动态测试一定需要通过工厂方法创造出来; 然而, 这可以通过在之后的版本中通过注册机制实现. 

#### 3.11.1. 动态测试例子

以下的 `DynamicTestsDemo` 的例子展示了测试工厂和动态测试的不同的几个例子. 

第一个方法返回了一个不合法的类型. 因为不合法的返回类型无法再编译器期间发现,在运行时就会抛出 `JUnitException` 异常. 

接下来的五个方法是非常简单的例子, 用来说明 产生 Collection, Iterable, Iterator, or Stream of DynamicTest 实例. 大多数的例子并没有 展示出动态的行为而仅仅是展示了支持的合法的返回类型. 然而, `dynamicTestsFromStream()` 和 `dynamicTestsFromIntStream()` 展示了对于给定的Strings 或者 输入的一定范围的数字,是非常容易生成测试的.

最后一个方法是完全的动态生成. `generateRandomNumberOfTests()`方法实现了 生成随机数, 生成展示名称, 和 测试执行器 的接口, 并且将这三者都提供给了 `DynamicTest.stream()`方法.尽管`generateRandomNumberOfTests()`的行为是不明确的,要非常小心的使用并且在重复测试中会产生冲突, 但是它依然体现了 动态测试的强大功能. 


```java

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertTrue;
import static org.junit.jupiter.api.DynamicTest.dynamicTest;

import java.util.Arrays;
import java.util.Collection;
import java.util.Iterator;
import java.util.List;
import java.util.Random;
import java.util.function.Consumer;
import java.util.function.Function;
import java.util.stream.IntStream;
import java.util.stream.Stream;

import org.junit.jupiter.api.DynamicTest;
import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.TestFactory;

class DynamicTestsDemo {

    // This will result in a JUnitException!
    @TestFactory
    List<String> dynamicTestsWithInvalidReturnType() {
        return Arrays.asList("Hello");
    }

    @TestFactory
    Collection<DynamicTest> dynamicTestsFromCollection() {
        return Arrays.asList(
            dynamicTest("1st dynamic test", () -> assertTrue(true)),
            dynamicTest("2nd dynamic test", () -> assertEquals(4, 2 * 2))
        );
    }

    @TestFactory
    Iterable<DynamicTest> dynamicTestsFromIterable() {
        return Arrays.asList(
            dynamicTest("3rd dynamic test", () -> assertTrue(true)),
            dynamicTest("4th dynamic test", () -> assertEquals(4, 2 * 2))
        );
    }

    @TestFactory
    Iterator<DynamicTest> dynamicTestsFromIterator() {
        return Arrays.asList(
            dynamicTest("5th dynamic test", () -> assertTrue(true)),
            dynamicTest("6th dynamic test", () -> assertEquals(4, 2 * 2))
        ).iterator();
    }

    @TestFactory
    Stream<DynamicTest> dynamicTestsFromStream() {
        return Stream.of("A", "B", "C").map(
            str -> dynamicTest("test" + str, () -> { /* ... */ }));
    }

    @TestFactory
    Stream<DynamicTest> dynamicTestsFromIntStream() {
        // Generates tests for the first 10 even integers.
        return IntStream.iterate(0, n -> n + 2).limit(10).mapToObj(
            n -> dynamicTest("test" + n, () -> assertTrue(n % 2 == 0)));
    }

    @TestFactory
    Stream<DynamicTest> generateRandomNumberOfTests() {

        // Generates random positive integers between 0 and 100 until
        // a number evenly divisible by 7 is encountered.
        Iterator<Integer> inputGenerator = new Iterator<Integer>() {

            Random random = new Random();
            int current;

            @Override
            public boolean hasNext() {
                current = random.nextInt(100);
                return current % 7 != 0;
            }

            @Override
            public Integer next() {
                return current;
            }
        };

        // Generates display names like: input:5, input:37, input:85, etc.
        Function<Integer, String> displayNameGenerator = (input) -> "input:" + input;

        // Executes tests based on the current input value.
        Consumer<Integer> testExecutor = (input) -> assertTrue(input % 7 != 0);

        // Returns a stream of dynamic tests.
        return DynamicTest.stream(inputGenerator, displayNameGenerator, testExecutor);
    }

}
```












