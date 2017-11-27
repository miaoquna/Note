# PHPUnit 单元测试 #

- [1. 安装PHPUnit](#1-安装PHPUnit)
	- [1.1 Composer安装](#11-Composer安装) 
	- [1.2 可选的组件包](#12-可选的组件包)
- [2. 编写PHPUnit测试](#2-编写PHPUnit测试)
	- [2.1 测试的依赖关系](#21-测试的依赖关系)
	- [2.2 数据供给器](#22-数据供给器)
	- [2.3 对异常进行测试](#23-对异常进行测试)
	- [2.4 对PHP错误进行测试](#24-对PHP错误进行测试)
	- [2.5 对输出进行测试](#25-对输出进行测试)
	- [2.6 错误相关信息的输出](#26-错误相关信息的输出)
	- [2.7 边缘情况](#27-边缘情况)
- [3. 命令行测试执行器](#3-命令行测试执行器)
- 
## **1. 安装PHPUnit**

我自己选择的单元测试环境是 PHPUnit6.2 + PHP7
具体的版本搭配可以参考官方的环境配置

## 1.1 Composer安装

为了简单起见我选择使用 Composer 来管理项目的依赖关系，因此phpunit 可以通过 Composer 来直接安装到项目中去
```
composer require -dev phpunit/phpunit ^6.2

```
## 1.2 可选的组件包

PHP_Invoker 一个工具类，可以用带有超时限制的方式调用可调用内容。当需要在严格模式下保证测试的超时限制时，这个组件是必须的。PHPUnit的PHAR分发中已包含了此组件。可以用以下命令来经由 Composer 安装此组件包：
```
composer require -dev phpunit/php-invokere

```
DbUnit 移植到 PHP/PHPUnit 上的 DbUnit 用于提供对数据库交互测试的支持。PHPUnit 的 PHAR 分发中已包含了此组件包。可以用以下命令经由 Composer 安装此组件包：
```
composer require -dev phpunit/dbunit

```

## **2. 编写PHPUnit测试**

先来一个简单的单元测试了解PHPUnit是如何测试的。

**例 2.1 用PHPUnit测试数组操作**
```php
use PHPUnit\Framework\TestCase;
class StackTest extends TestCase {
    public function testPushAndPop() {
        $stack = [];
        $this->assertEquals(0, count($stack));
        array_push($stack, 'foo');
        $this->assertEquals('foo', $stack[count($stack) - 1]);
        $this->assertEquals(1, count($stack));

        $this->assertEquals('foo', array_pop($stack));
        $this->assertEquals(0, count($stack));
    }
}
```
上边这个案例中使用了单元测试的断言方法 `assertEquals()`，接收两个参数，用来比较两个参数是否相等

## 2.1 测试的依赖关系

PHPUnit支持对测试方法之间的显式依赖关系进行声明。这种依赖关系并不是定义在测试方法的执行顺序中，而是允许生产者返回一个测试基境（什么是基境后边再说）的实例，并将实例传递给依赖于它的消费者们。
- 生产者，是能生成被测试单元并将其作为返回值的测试方法。
- 消费者，是依赖于一个或多个生产者及其返回值的测试方法。

**例 2.2 用 @depends 标注来表达依赖关系**
```php
use PHPUnit\Framework\TestCase;
class StackTest extends TestCase {
    public function testEmpty() {
        $stack = [];
        $this->assertEmpty($stack);
        return $stack;
    }

    /**
     * 这个方法依赖于上边的 testEmtpy 返回值
     * @depends testEmpty
     */
    public function testPush(array $stack) {
        array_push($stack, 'foo');
        $this->assertEquals('foo', $stack[count($stack) - 1]);
        $this->assertEmpty($stack);
        return $stack;
    }
    /**
     * 这个方法依赖于上边的 testPush 返回值
     * @depends testPush
     */
    public function testPop(array $stack) {
        $this->assertEquals('foo', array_pop($stack));
        $this->assertEquals(0, count($stack));
    }
}
```
在上例中，第一个测试，testEmpty()，创建了一个新数组，并断言其为空。随后，此测试将此基境（我们可以理解为基础环境，也可以说是依赖环境）作为结果返回。第二个测试，testPush()，依赖于 testEmpty()，并将所依赖的测试结果作为参数传入。最后，testPop() 依赖于 testPush()。

`知识点`：
`@depends` 标注，你可以通过这个标注，来告诉测试方法的参数依赖于哪个基境所产生
`assertEmpty()` 方法用来判断一个数据是否为空

`注意：默认情况下，生产者所产生的返回值将“原样”传递给相应的消费者。这意味着，如果生产者返回的是一个对象，那么传递给消费者的将是一个指向此对象的引用。如果需要传递对象的副本而非引用，则应当用 @depends clone 替代 @depends`

传递对象副本的案例
```php
use PHPUnit\Framework\TestCase;
class StackTest extends TestCase {
    public function testCreateClass() {
        $class = new Name();
        return $class;
    }
	/**
	 * @depends clone testCreateClass
	 */
	public funtion testUpdateClass(Name $name) {
		$this->assertEmpty($name);
		$name->name = '1';
		$this->assertEquals('1', $name->name);
	}
}

```

为了快速定位缺陷，我们希望把注意力集中于相关的失败测试上。这就是为什么当某个测试所以来的测试失败时，PHPUnit会跳过这个测试。通过利用测试之间的依赖关系，缺陷定位得到了改进，如例 2.3

**例 2.3 利用测试之间的依赖关系**
```php
use PHPUnit\Framework\TestCase;
class DependencyFailureTest extends TestCase {
    public function testOne() {
        $this->assertTrue(false);
    }
    /**
     * @depends testOne
     */
    public function testTwo() {
    }
}
```

返回结果

	phpunit --verbose DependencyFailureTest
	PHPUnit 6.3.0 by Sebastian Bergmann and contributors.
	FS
	Time: 0 seconds, Memory: 5.00Mb
	There was 1 failure:
	1) DependencyFailureTest::testOne
	Failed asserting that false is true.
	/home/sb/DependencyFailureTest.php:6
	There was 1 skipped test:
	1) DependencyFailureTest::testTwo
	This test depends on "DependencyFailureTest::testOne" to pass.
	FAILURES!
	Tests: 1, Assertions: 1, Failures: 1, Skipped: 1.

测试结果可以看到 testOne 断言失败，跳过了 testTwo 的测试，因为他的依赖中产生了错误。

测试可以用多个 @depends 标注。PHPUnit 不会更改测试的运行顺序，因此你需要自行保证某个测试所依赖的所有测试均出现于这个测试之前。
拥有多个 @depends 标注的测试，其第一个参数是第一个生产者提供的基境，第二个参数生产者所提供的基境，以此类推

**例 2.4 有多重依赖的测试**
```php
use PHPUnit\Framework\TestCase;
class MultipleDependenciesTest extends TestCase {
    /**
     * 第一个生产者
     * @return string
     */
    public function testProducerFirst() {
        $this->assertTrue(true);
        return 'first';
    }

    /**
     * 第二个生产者
     * @return string
     */
    public function testProducerSecond() {
        $this->assertTrue(true);
        return 'second';
    }

    /**
     * 消费者
     * @depends testProducerFirst
     * @depends testProducerSecond
     */
    public function testConsumer() {
        $this->assertEquals(['first', 'second'], func_get_args());
    }
}
```
上例中的消费者依赖了两个产品，分别由第一和第二生产者提供。`func_get_args()` 会接收到多个生产者提供的参数数组，然后使用 equals 断言来进行比较接收到的产品是否符合要求。

## 2.2 数据供给器

测试方法可以接受任意参数。这些参数由数据供给器方法提供（后边会讲解什么叫数据供给器方法）。用 `@dataProvider` 标注来指定使用哪个数据供给器方法。

数据供给器方法必须声明为 public，其返回值要么是一个数组，其每个元素也是数组；要么是一个实习了 Iterator（又叫做迭代器）接口的对象，在对他进行迭代时每步产生一个数组。每个数组都是测试数据集的一部分，将以它的内容作为参数来调用测试方法

**例 2.5 使用返回数组的数组的数据供给器**
```php
use PHPUnit\Framework\TestCase;
class DataTest extends TestCase {
    public function additionProvider() {
        return [
            [0, 0, 0],
            [0, 1, 1],
            [1, 0, 1],
            [1, 1, 3],
        ];
    }

    /**
     * @dataProvider additionProvider
     * @param $a
     * @param $b
     * @param $expected
     */
    public function testAdd($a, $b, $expected) {
        //首先这个测试方法会根据 additionProvider 数据供给器提供的二维数组进行遍历
        //每次遍历会得到一个数组，并把这个数组中的三个元素分别作为 $a、$b、$expected 这三个参数
        //然后用前两个参数 $a + $b 进行相加然后和 $expected 进行比较是否等
        //你可能不明白这个断言的用处，请先看上边三部运算
        $this->assertEquals($expected, $a + $b);
    }
}
```
得到如下测试结果

	PHPUnit 6.2.4 by Sebastian Bergmann and contributors.
	
	...F                                                                4 / 4 (100%)
	
	Time: 61 ms, Memory: 8.00MB
	
	There was 1 failure:
	
	1) DataTest::testAdd with data set #3 (1, 1, 3)
	Failed asserting that 2 matches expected 3.
	
	/data/wwwroot/default/test.php:23
	
	FAILURES!
	Tests: 4, Assertions: 4, Failures: 1.

可以看到 测试方法执行了四次，断言执行了四次，断言失败了一次。这也就解释了消费者在接收到数据供给器提供的数据时会对数据进行遍历处理，并依次进行断言判断，至于断言内容就是自定义的东西了。
上例的断言内容实际上是：

	根据数据供给器提供的数据进行遍历，会遍历四次。每次是数据分别是如下
	[0, 0, 0] 第一次， 把前两个相加和后一个进行比较会得到：0 + 0 = 0
    [0, 1, 1] 第二次， 把前两个相加和后一个进行比较会得到：0 + 1 = 1
    [1, 0, 1] 第三次， 把前两个相加和后一个进行比较会得到：1 + 0 = 1
    [1, 1, 3] 第四次， 把前两个相加和后一个进行比较会得到：1 + 1 != 3
	最后一次不相等，所以会有一次断言失败的情况。


这里测试另外一种情况就是，数据供给器提供的数据最开始的时候就有错误，测试方法会停止执行吗？看下测试结果：

数据供给器提供的数据

	[
		[0, 0, 0],
	    [1, 1, 1],
	    [1, 0, 1],
	    [1, 1, 3]
	]

得到的测试结果

	PHPUnit 6.2.4 by Sebastian Bergmann and contributors.
	
	.F.F                                                                4 / 4 (100%)
	
	Time: 62 ms, Memory: 8.00MB
	
	There were 2 failures:
	
	1) DataTest::testAdd with data set #1 (1, 1, 1)
	Failed asserting that 2 matches expected 1.
	
	/data/wwwroot/default/test.php:27
	
	2) DataTest::testAdd with data set #3 (1, 1, 3)
	Failed asserting that 2 matches expected 3.
	
	/data/wwwroot/default/test.php:27
	
	FAILURES!
	Tests: 4, Assertions: 4, Failures: 2.

从结果可以看出测试方法把数据集遍历了一遍，并标注出有错误的数据

`注意：在使用大量数据集时，最好逐个用字符串键名对其命名，避免使用默认的数字键名。这样输出信息会更加详细些，其中将包含打断测试的数据集所对应的名称`

**例 2.6 使用带有命名数据集的数据供给器**

```php
use PHPUnit\Framework\TestCase;
class DataTest extends TestCase {
    public function additionProvider() {
        return [
            'data1' => [0, 0, 0],
            'data2' => [1, 1, 1],
            'data3' => [1, 0, 1],
            'data4' => [1, 1, 3],
        ];
    }

    /**
     * @dataProvider additionProvider
     * @param $a
     * @param $b
     * @param $expected
     */
    public function testAdd($a, $b, $expected) {
        //首先这个测试方法会根据 additionProvider 数据供给器提供的二维数组进行遍历
        //每次遍历会得到一个数组，并把这个数组中的三个元素分别作为 $a、$b、$expected 这三个参数
        //然后用前两个参数 $a + $b 进行相加然后和 $expected 进行比较是否等
        //你可能不明白这个断言的用处，请先看上边三部运算
        $this->assertEquals($expected, $a + $b);
    }
}
```
测试结果

	PHPUnit 6.2.4 by Sebastian Bergmann and contributors.
	
	.F.F                                                                4 / 4 (100%)
	
	Time: 54 ms, Memory: 8.00MB
	
	There were 2 failures:
	
	1) DataTest::testAdd with data set "data2" (1, 1, 1)
	Failed asserting that 2 matches expected 1.
	
	/data/wwwroot/default/test.php:27
	
	2) DataTest::testAdd with data set "data4" (1, 1, 3)
	Failed asserting that 2 matches expected 3.
	
	/data/wwwroot/default/test.php:27
	
	FAILURES!
	Tests: 4, Assertions: 4, Failures: 2.

从结果中可以清晰的看到错误数据的 key 为 data2和data4，这样你就可以快速定位问题所在
另外你还会发现结果第二行有个很有意思的东西 `.F.F` 这个其实代表断言执行的次数以及断言结果，大写的 F 代表断言失败，点代表断言成功，从结果对照来看刚好是 data2 和 data4 断言失败
如果把数据集改成如下结构你会看到不同的执行进度：

	[
        'data1' => [0, 0, 0],
        'data2' => [1, 3, 4],
        'data3' => [1, 0, 1],
        'data4' => [1, 1, 2],
    ];

结果

	PHPUnit 6.2.4 by Sebastian Bergmann and contributors.
	
	....                                                                4 / 4 (100%)
	
	Time: 65 ms, Memory: 8.00MB
	
	OK (4 tests, 4 assertions)

从第二行的进度就可以看出在断言失败和成功时的明显区别，这也是一个分析结果的一个小技巧（纯属自己瞎想，不喜勿喷）

**例 2.7 使用返回迭代器对象的数据供给器**

```php
use PHPUnit\Framework\TestCase;
/**
 * 自定义纯文本形式表格的迭代器
 * Class CsvFileIterator
 * ${DS}
 */
class CsvFileIterator implements Iterator {
    protected $file;    //文件资源
    protected $key = 0;
    protected $current;

    /**
     * CsvFileIterator constructor.
     * @param $file 文件地址
     */
    public function __construct($file) {
        //打开一个表格文件
        $this->file = fopen($file, 'r');
    }
    public function __destruct() {
        //关闭文件资源
        fclose( $this->file );
    }

    /**
     * 实现迭代器接口的 rewind 方法
     * 返回到迭代器的第一个元素
     */
    public function rewind() {
        //倒回文件指针的位置，注意这个方法和迭代器的 rewind 方法不是一个意思
        rewind( $this->file );
        //fgetcsv() 函数从文件指针中读入一行并解析 CSV 字段。
        $this->current = fgetcsv( $this->file );
        $this->key = 0;
    }

    /**
     * 实现迭代器接口的 valid 方法
     * 检查当前位置是否有效
     * @return bool
     */
    public function valid() {
        // 检测是否已到达文件末尾 (eof)。
        return !feof( $this->file );
    }

    /**
     * 实现迭代器接口的 key 方法
     * 返回当前元素的键
     * @return int
     */
    public function key() {
        return $this->key;
    }

    /**
     * 实现迭代器接口的 key 方法
     * 返回当前元素
     * @return mixed
     */
    public function current() {
        return $this->current;
    }

    /**
     * 实现迭代器接口的 next 方法
     * 向前移动到下一个元素
     */
    public function next() {
        //fgetcsv() 函数从文件指针中读入一行并解析 CSV 字段。
        $this->current = fgetcsv( $this->file );
        $this->key++;
    }
}
class DataTest extends TestCase {
    public function additionProvider() {
        return new CsvFileIterator('data.csv');
    }

    /**
     * @dataProvider additionProvider
     * @param $a
     * @param $b
     * @param $expected
     */
    public function testAdd($a, $b, $expected) {
        //首先这个测试方法会根据 additionProvider 数据供给器提供的二维数组进行遍历
        //每次遍历会得到一个数组，并把这个数组中的三个元素分别作为 $a、$b、$expected 这三个参数
        //然后用前两个参数 $a + $b 进行相加然后和 $expected 进行比较是否等
        //你可能不明白这个断言的用处，请先看上边三部运算
        $this->assertEquals($expected, $a + $b);
    }
}
```
另外的 data.csv 文件的内容是（如何创建csv文件就不用再说了）：

	0,0,0
	0,1,1
	1,0,1
	1,1,3

测试结果为：

	PHPUnit 6.2.4 by Sebastian Bergmann and contributors.
	
	...F                                                                4 / 4 (100%)
	
	Time: 51 ms, Memory: 8.00MB
	
	There was 1 failure:
	
	1) DataTest::testAdd with data set #3 ('1', '1', '3')
	Failed asserting that 2 matches expected '3'.
	
	/data/wwwroot/default/test.php:95
	
	FAILURES!
	Tests: 4, Assertions: 4, Failures: 1.

可以看到同样是第三行出现错误。以上就是 `使用返回迭代器对象的数据供给器`

如果测试同时从 `@dataProvider` 方法和一个或多个 `@depends` 测试接收数据，那么来自于数据供给器的参数将先于来自所依赖的测试的。来自所依赖的测试的参数对于每个数据集都是一样的。

**例 2.8 在同一个测试中组合使用 `@depends` 和 `@dataProvider`**

```php

use PHPUnit\Framework\TestCase;
class DependencyAndDataProviderComboTest extends TestCase {
    /**
     * 数据供给器
     * @return array
     */
    public function provider() {
        return [['provider1'], ['provider2']];
    }

    /**
     * 生产者1
     * @return string
     */
    public function testProducerFirst() {
        $this->assertTrue(true);
        return 'first';
    }

    /**
     * 生产者2
     * @return string
     */
    public function testProducerSecond() {
        $this->assertTrue(true);
        return 'second';
    }
    /**
     * 消费者 同时依赖 生产者1和生产者2以及数据供给器
     * @depends testProducerFirst
     * @depends testProducerSecond
     * @dataProvider provider
     */
    public function testConsumer() {
        $this->assertEquals(['provider1', 'first', 'second'], func_get_args());
    }
}

```
测试结果

	PHPUnit 6.2.4 by Sebastian Bergmann and contributors.
	
	...F                                                                4 / 4 (100%)
	
	Time: 63 ms, Memory: 8.00MB
	
	There was 1 failure:
	
	1) DependencyAndDataProviderComboTest::testConsumer with data set #1 ('provider2')
	Failed asserting that two arrays are equal.
	--- Expected
	+++ Actual
	@@ @@
	 Array (
	-    0 => 'provider1'
	+    0 => 'provider2'
	     1 => 'first'
	     2 => 'second'
	 )
	
	/data/wwwroot/default/test.php:39
	
	FAILURES!
	Tests: 4, Assertions: 4, Failures: 1.

解释一下测试结果，测试过程中共有四次断言，前两次都是在生产者中进行的断言这个不再说明，主要是在测试方法中会进行两次断言，因为数据供给器提供了两个数据，所以需要遍历两次，第一个次遍历时得到的测试方法会得到三个参数的数组
['provider1', 'first', 'second'], 所以第三次断言的时进行的数据比较是相等的，但是第四次断言时，参数数组第一个参数会是通过第二次遍历数据供给器所得到的 'provider2'，所以参数结构会变成 ['provider2', 'first', 'second']。因此最后一次的断言会发生错误。如果我们改变测试中的断言内容为以下方式

	$this->assertEquals(['provider2', 'first', 'second'], func_get_args());

会得到如下的测试结果

	PHPUnit 6.2.4 by Sebastian Bergmann and contributors.
	
	..F.                                                                4 / 4 (100%)
	
	Time: 68 ms, Memory: 8.00MB
	
	There was 1 failure:
	
	1) DependencyAndDataProviderComboTest::testConsumer with data set #0 ('provider1')
	Failed asserting that two arrays are equal.
	--- Expected
	+++ Actual
	@@ @@
	 Array (
	-    0 => 'provider2'
	+    0 => 'provider1'
	     1 => 'first'
	     2 => 'second'
	 )
	
	/data/wwwroot/default/test.php:39
	
	FAILURES!
	Tests: 4, Assertions: 4, Failures: 1.
你会发现这时候给出的测试结果会是在第三次断言的时候发生错误，因为此时参数数组是 ['provider1', 'first', 'second'] 和断言要比较的数组不相同。

这里需要注意的是为什么我测试依赖是放在最前边的，为什么参数结构中的第一个参数会是数据供给器中的数据？

	@depends testProducerFirst
	@depends testProducerSecond
	@dataProvider provider

这是最开始提到的 **如果测试同时从 `@dataProvider` 方法和一个或多个 `@depends` 测试接收数据，那么来自于数据供给器的参数将先于来自所依赖的测试的参数。**

`注意：如果一个测试依赖于另外一个使用了数据供给器的测试，仅当被依赖的测试至少能在一组数据上成功时，依赖于它的测试才会运行。使用了数据供给器的测试，其运行结果是无法注入到依赖于此测试的其他测试中的。如例2.9`


**例 2.9 一个测试依赖于另外一个使用了数据供给器的测试**

```php
use PHPUnit\Framework\TestCase;
class DependencyAndDataProviderComboTest extends TestCase {
    /**
     * 数据供给器
     * @return array
     */
    public function provider() {
        return [['provider1'], ['provider2']];
    }

    /**
     * 生产者1
     * @dataProvider provider
     * @return string
     */
    public function testProducerFirst() {
        $this->assertEquals(['provider1'], func_get_args());
        return 'first';
    }

    /**
     * 消费者 同时依赖 生产者1和生产者2以及数据供给器
     * @depends testProducerFirst
     */
    public function testConsumer() {
        $this->assertEquals(['first'], func_get_args());
    }
}
```
测试结果

	PHPUnit 6.2.4 by Sebastian Bergmann and contributors.
	
	.FF                                                                 3 / 3 (100%)
	
	Time: 58 ms, Memory: 8.00MB
	
	There were 2 failures:
	
	1) DependencyAndDataProviderComboTest::testProducerFirst with data set #1 ('provider2')
	Failed asserting that two arrays are equal.
	--- Expected
	+++ Actual
	@@ @@
	 Array (
	-    0 => 'provider1'
	+    0 => 'provider2'
	 )
	
	/data/wwwroot/default/test.php:21
	
	2) DependencyAndDataProviderComboTest::testConsumer
	Failed asserting that two arrays are equal.
	--- Expected
	+++ Actual
	@@ @@
	 Array (
	-    0 => 'first'
	+    0 => null
	 )
	
	/data/wwwroot/default/test.php:30
	
	FAILURES!
	Tests: 3, Assertions: 3, Failures: 2.

数据供给器中有两个数据，在生产者1中会遍历产生两次断言，加上消费者的测试中有一个断言，总共三次断言，下面我们来分析这三次断言：
第一次断言：接收到遍历数据供给器所得到的参数数组 ['prvider1']，所以第一次断言执行成功
第二次断言：接收到遍历数据供给器所得到的参数数组 ['prvider2']，所以第二次断言执行失败
第三次断言：尝试从依赖的测试中获取返回的参数数据，但是得到了 null,和first进行比较，第三次断言执行失败

从第三次断言可以看出：使用了数据供给器的测试，其运行结果是无法注入到依赖于此测试的其他测试中的

另外如果改变`生产者1`的断言内容为 `$this->assertEquals(['provider'], func_get_args())`会得到如下的结果

	PHPUnit 6.2.4 by Sebastian Bergmann and contributors.
	
	FFS                                                                 3 / 3 (100%)
	
	Time: 60 ms, Memory: 8.00MB
	
	There were 2 failures:
	
	1) DependencyAndDataProviderComboTest::testProducerFirst with data set #0 ('provider1')
	Failed asserting that two arrays are equal.
	--- Expected
	+++ Actual
	@@ @@
	 Array (
	-    0 => 'provider'
	+    0 => 'provider1'
	 )
	
	/data/wwwroot/default/test.php:21
	
	2) DependencyAndDataProviderComboTest::testProducerFirst with data set #1 ('provider2')
	Failed asserting that two arrays are equal.
	--- Expected
	+++ Actual
	@@ @@
	 Array (
	-    0 => 'provider'
	+    0 => 'provider2'
	 )
	
	/data/wwwroot/default/test.php:21
	
	FAILURES!
	Tests: 3, Assertions: 2, Failures: 2, Skipped: 1.

由于生产者1中的两次断言都未成功，所以跳过了依赖它的测试中的断言，就印证了上边的注意事项：

`如果一个测试依赖于另外一个使用了数据供给器的测试，仅当被依赖的测试至少能在一组数据上成功时，依赖于它的测试才会运行。`


`注意：所有数据供给器方法的执行都是在对 setUpBeforeClass 静态方法的调用和第一次对 setUp 方法的调用之前完成的。因此，无法在数据供给器中使用创建于这两个方法内的变量。这是必须的，这样PHPUnit才能计算测试的总数量。你可能不知道 setUpBeforeClass 和 setUp 是什么意思，后边会讲到先不要着急。`


## 2.3 对异常进行测试

`@expectException` 标注来测试被测试代码中是否抛出了异常。

例 2.10 使用 expectException() 方法
```php
use PHPUnit\Framework\TestCase;
class ExceptionTest extends TestCase {
    public function testException() {
        $this->expectException(InvalidArgumentException::class);
    }
}
```
测试结果

	PHPUnit 6.2.4 by Sebastian Bergmann and contributors.
	
	F                                                                   1 / 1 (100%)
	
	Time: 62 ms, Memory: 8.00MB
	
	There was 1 failure:
	
	1) ExceptionTest::testException
	Failed asserting that exception of type "InvalidArgumentException" is thrown.
	
	FAILURES!
	Tests: 1, Assertions: 1, Failures: 1.

除了 expectException() 方法，还有
expectExceptionCode()
expectExceptionMessage()
expectExceptionMessageRegExp()
可以用于为被测代码所抛出的异常建立预期。或者可以使用标注
@expectException
@expectExceptionCode
@expectExceptionMessage
@expectExceptionMessageRegExp

**例 2.11 使用 @expectedException 标注**

```php
use PHPUnit\Framework\TestCase;
class ExceptionTest extends TestCase {
    /**
     * @expectedException InvalidArgumentException
     */
    public function testException() {
    }
}
```

测试结果

	PHPUnit 6.2.4 by Sebastian Bergmann and contributors.
	
	F                                                                   1 / 1 (100%)
	
	Time: 48 ms, Memory: 8.00MB
	
	There was 1 failure:
	
	1) ExceptionTest::testException
	Failed asserting that exception of type "InvalidArgumentException" is thrown.
	
	FAILURES!
	Tests: 1, Assertions: 1, Failures: 1.

## 2.4 对PHP错误进行测试

默认情况下，PHPUnit将测试在执行中触发的PHP错误、警告、通知都转换为异常。利用这些异常，就可以，比如说预期测试将触发PHP错误。
`注意：PHP的error_reporting运行时配置会对PHPUnit将哪些错误转换为异常有所限制。如果在这个特性上碰到问题，请确认PHP的配置中没有抑制想要测试的错误类型`

**例 2.12 用 @expectedException 来预期PHP错误**

```php
use PHPUnit\Framework\TestCase;
use PHPUnit\Framework\Error; //注意引入错误类
class ExpectedErrorTest extends TestCase {
    /**
     * 使用PHPUnit的错误类
     * @expectedException Error
     */
    public function testFailingInclude() {
        include 'not_existing_file.php';
    }
}
```
测试结果

	PHPUnit 6.2.4 by Sebastian Bergmann and contributors.
	
	E                                                                   1 / 1 (100%)
	
	Time: 58 ms, Memory: 8.00MB
	
	There was 1 error:
	
	1) ExpectedErrorTest::testFailingInclude
	include(not_existing_file.php): failed to open stream: No such file or directory
	
	/data/wwwroot/default/test.php:13
	/data/wwwroot/default/test.php:13
	
	ERRORS!
	Tests: 1, Assertions: 0, Errors: 1.

除了 Error 类还有 `PHPUnit\Framework\Error\Notice` 和 `PHPUnit\Framework\Error\Warning` 分
别代表 PHP 通知与 PHP 警告。

`注意：对异常进行测试是越明确越好的。对太笼统的类似进行测试有可能导致不良副作用。因此，不再允许用 @expectedException 或 setExpectedException() 对 Exception 类进行测试`

如果测试依靠会触发错误的PHP函数，例如 fopen，有时候在测试中使用错误抑制符会很有用。通过抑制住错误通知，就能对返回值进行检查，否则错误通知将会导致抛出 PHPUnit\Framework\Error\Notice

**例 2.13 对会引发PHP错误的代码的返回值进行测试**

```php
use PHPUnit\Framework\TestCase;

class ErrorSuppressionTest extends TestCase {
    public function testFileWriting() {
        $writer = new FileWriter();
        $this->assertFalse(@$writer->write('/is-not-writeable/file', 'stuff'));
    }
}
class FileWriter {
    public function write($file, $content) {
        $file = fopen($file, 'w');
        if ( $file == false ) {
            return false;
        }
    }
}
```
测试结果
	
	PHPUnit 6.2.4 by Sebastian Bergmann and contributors.
	
	.                                                                   1 / 1 (100%)
	
	Time: 72 ms, Memory: 8.00MB
	
	OK (1 test, 1 assertion)

如果不使用 @ 错误抑制符，此测试将会失败，并报告 fopen(....):failed to open stream:No such file or ....

## 2.5 对输出进行测试

有时候，想要断言某方法的运行过程中生成了预期的输出（例如，通过 echo 或 print）。PHPUnit\Framework\TestCase 类使用 PHP 的输出缓冲特性来为此提供必要的功能支持。

**例 2.14 对函数或方法的输出进行测试**

```php
use PHPUnit\Framework\TestCase;
class OutputTest extends TestCase {
    public function testExpectFooActualFoo() {
		//设定所预期的输出。如果没有产生预期的输出，测试将计为失败。
        $this->expectOutputString('foo');
        print 'foo';
    }
    public function testExpectBarActualBaz() {
		//设定所预期的输出。如果没有产生预期的输出，测试将计为失败。
        $this->expectOutputString('bar');
        print 'baz';
    }
}
```
测试结果

	PHPUnit 6.2.4 by Sebastian Bergmann and contributors.
	
	.F                                                                  2 / 2 (100%)
	
	Time: 56 ms, Memory: 8.00MB
	
	There was 1 failure:
	
	1) OutputTest::testExpectBarActualBaz
	Failed asserting that two strings are equal.
	--- Expected
	+++ Actual
	@@ @@
	-'bar'
	+'baz'
	
	FAILURES!
	Tests: 2, Assertions: 2, Failures: 1.

从测试结果可以看出，在 testExpectBarActualBaz 测试中，预期的输出为 bar 但是输出了 baz，所以测试结果提示失败

**表 2.1 用于对输出进行测试的方法**
|方法|含义|
|------|------|
|void expectOutputString(string $regularExpression);|设置输出预期应当与$expectedString 相等|
|void expectOutputRegex( string $expectedString );|设置输出预期应当匹配正则表达式 $expectedString|
|bool setOutputCallback( callable $callback );|设置回调函数，用来做诸如将实际输出规范化之类的动作|
|string getActualOutput()|获取实际输出|

`注意：在严格模式下，本身产生输出的测试将会失败`

## 2.6 错误相关信息的输出

当测试失败时，PHPUnit全力提供尽可能多的有助于找出问题所在的上下文信息

**例 2.15 数组比较失败时生成的错误相关信息输出**

```php
use PHPUnit\Framework\TestCase;
class ArrayDiffTest extends TestCase {
    public function testEquality() {
        $this->assertEquals(
            [1, 2, 3, 4, 5, 6],
            [1, 2,33, 4, 5, 6]
        );
    }
}
```
测试结果

	PHPUnit 6.2.4 by Sebastian Bergmann and contributors.
	
	F                                                                   1 / 1 (100%)
	
	Time: 51 ms, Memory: 8.00MB
	
	There was 1 failure:
	
	1) ArrayDiffTest::testEquality
	Failed asserting that two arrays are equal.
	--- Expected
	+++ Actual
	@@ @@
	 Array (
	     0 => 1
	     1 => 2
	-    2 => 3
	+    2 => 33
	     3 => 4
	     4 => 5
	     5 => 6
	 )
	
	/data/wwwroot/default/test.php:10
	
	FAILURES!
	Tests: 1, Assertions: 1, Failures: 1.

在这个例子中只有一个地方不一样，但是错误信息会显示上下文，帮助你发现问题所在

当生成的输出很长而难以阅读时，PHPUnit 将对其进行分割，并在每个差异附近提供少数几行上下文信息。

**例 2.16 长数组比较失败时生成的错误相关信息输出**

```php
```

测试结果

	PHPUnit 6.2.4 by Sebastian Bergmann and contributors.
	
	F                                                                   1 / 1 (100%)
	
	Time: 69 ms, Memory: 8.00MB
	
	There was 1 failure:
	
	1) LongArrayDiffTest::testEquality
	Failed asserting that two arrays are equal.
	--- Expected
	+++ Actual
	@@ @@
	     12 => 2
	-    13 => 3
	+    13 => 33
	     14 => 4
	     15 => 5
	     16 => 6
	 )
	
	/data/wwwroot/default/test.php:10
	
	FAILURES!
	Tests: 1, Assertions: 1, Failures: 1.

由于数组较大，所以错误信息不会显示完整的数组信息，只会获取上下文，供你参考

## 2.7 边缘情况

当比较失败时，PHPUnit为输入值建立文本表示，然后以此进行对比。这种实现导致存在差异指示中显示出来的问题可能比实际上存在的多。
这种情况只出现在对数组或者对象使用 assertEquals 或其他 “弱” 比较函数时。

例 2.17 当使用弱比较时在生成的差异结果中出现的边缘情况

```php
use PHPUnit\Framework\TestCase;
class ArrayWeakComparisonTest extends TestCase {
    public function testEquality() {
        $this->assertEquals(1, '1');
        $this->assertEquals(
            [1, 2, 3, 4, 5, 6],
            ['1', 2,33, 4, 5, 6]
        );
    }
}
```

测试结果

	PHPUnit 6.2.4 by Sebastian Bergmann and contributors.

	F                                                                   1 / 1 (100%)
	
	Time: 81 ms, Memory: 8.00MB
	
	There was 1 failure:
	
	1) ArrayWeakComparisonTest::testEquality
	Failed asserting that two arrays are equal.
	--- Expected
	+++ Actual
	@@ @@
	 Array (
	-    0 => 1
	+    0 => '1'
	     1 => 2
	-    2 => 3
	+    2 => 33
	     3 => 4
	     4 => 5
	     5 => 6
	 )
	
	/data/wwwroot/default/test.php:10
	
	FAILURES!
	Tests: 1, Assertions: 2, Failures: 1.

在这个例子中，第一个索引项中的 1 和 ‘1’ 在报告中视为不同过，虽然 assertEquals 认为这两个值是匹配的


## **3. 命令行测试执行器**

PHPUnit 命令行测试执行器可通过 phpunit 命令调用。下面的代码展示了如何使用 PHPUnit 命令行测试执行器类运行测试：

```
phpunit ArrayTest
```
测试结果

	PHPUnit 6.2.4 by Sebastian Bergmann and contributors.
	
	..                                                                  2 / 2 (100%)
	
	Time: 55 ms, Memory: 8.00MB
	
	OK (2 tests, 3 assertions)

上面这个调用例子中，PHPUnit 命令行测试执行器将在当前工作目录中寻找 ArrayTest.php 源文件并执行。而在此源文件中应当能找到 ArrayTest 测试用例类，此类中的测试将被执行。
对于每个测试的运行，PHPUnit命令行工具输出一个字符来指示进展：
`.` 当一个测试方法执行成功时输出（每一个 . 代表一个测试方法）
`F` 当测试方法运行过程中一个断言失败时输出。
`E` 当测试方法运行过程中产生一个错误时输出。
`R` 当测试被标记为有风险时输出（参考第 6 章 有风险的测试）
`S` 当测试被跳过时输出（参考第 7 章 未完成的测试与跳过的测试）
`I` 当测试被标记为不完整或为实现时输出（参考第 7 章 未完成的测试与跳过的测试）

PHPUnit 区分 失败（failure）与错误（error）。失败指的是被违背了的 PHPUnit 断言，例如一个失败的 arrsertEquals() 调用。错误指的是意料之外的异常（exception）或PHP错误。这种差异已被证明在某些时候是非常有用的，因为错误往往比失败更容易修复。如果得到了一个非常长的问题列表，那么最好先对付错误，当错误全部修复了之后再试一次瞧瞧还有没有失败。

## **3.1 命令行选项**

	PHPUnit 6.2.4 by Sebastian Bergmann and contributors.
	
	Usage: phpunit [options] UnitTest [UnitTest.php]
	       phpunit [options] <directory>
	
	Code Coverage Options:
	
	  --coverage-clover <file>    Generate code coverage report in Clover XML format.
	  --coverage-crap4j <file>    Generate code coverage report in Crap4J XML format.
	  --coverage-html <dir>       Generate code coverage report in HTML format.
	  --coverage-php <file>       Export PHP_CodeCoverage object to file.
	  --coverage-text=<file>      Generate code coverage report in text format.
	                              Default: Standard output.
	  --coverage-xml <dir>        Generate code coverage report in PHPUnit XML format.
	  --whitelist <dir>           Whitelist <dir> for code coverage analysis.
	  --disable-coverage-ignore   Disable annotations for ignoring code coverage.
	
	Logging Options:
	
	  --log-junit <file>          Log test execution in JUnit XML format to file.
	  --log-teamcity <file>       Log test execution in TeamCity format to file.
	  --testdox-html <file>       Write agile documentation in HTML format to file.
	  --testdox-text <file>       Write agile documentation in Text format to file.
	  --testdox-xml <file>        Write agile documentation in XML format to file.
	  --reverse-list              Print defects in reverse order
	
	Test Selection Options:
	
	  --filter <pattern>          Filter which tests to run.
	  --testsuite <name,...>      Filter which testsuite to run.
	  --group ...                 Only runs tests from the specified group(s).
	  --exclude-group ...         Exclude tests from the specified group(s).
	  --list-groups               List available test groups.
	  --list-suites               List available test suites.
	  --test-suffix ...           Only search for test in files with specified
	                              suffix(es). Default: Test.php,.phpt
	
	Test Execution Options:
	
	  --dont-report-useless-tests Do not report tests that do not test anything.
	  --strict-coverage           Be strict about @covers annotation usage.
	  --strict-global-state       Be strict about changes to global state
	  --disallow-test-output      Be strict about output during tests.
	  --disallow-resource-usage   Be strict about resource usage during small tests.
	  --enforce-time-limit        Enforce time limit based on test size.
	  --disallow-todo-tests       Disallow @todo-annotated tests.
	
	  --process-isolation         Run each test in a separate PHP process.
	  --globals-backup            Backup and restore $GLOBALS for each test.
	  --static-backup             Backup and restore static attributes for each test.
	
	  --colors=<flag>             Use colors in output ("never", "auto" or "always").
	  --columns <n>               Number of columns to use for progress output.
	  --columns max               Use maximum number of columns for progress output.
	  --stderr                    Write to STDERR instead of STDOUT.
	  --stop-on-error             Stop execution upon first error.
	  --stop-on-failure           Stop execution upon first error or failure.
	  --stop-on-warning           Stop execution upon first warning.
	  --stop-on-risky             Stop execution upon first risky test.
	  --stop-on-skipped           Stop execution upon first skipped test.
	  --stop-on-incomplete        Stop execution upon first incomplete test.
	  --fail-on-warning           Treat tests with warnings as failures.
	  --fail-on-risky             Treat risky tests as failures.
	  -v|--verbose                Output more verbose information.
	  --debug                     Display debugging information.
	
	  --loader <loader>           TestSuiteLoader implementation to use.
	  --repeat <times>            Runs the test(s) repeatedly.
	  --teamcity                  Report test execution progress in TeamCity format.
	  --testdox                   Report test execution progress in TestDox format.
	  --testdox-group             Only include tests from the specified group(s).
	  --testdox-exclude-group     Exclude tests from the specified group(s).
	  --printer <printer>         TestListener implementation to use.
	
	Configuration Options:
	
	  --bootstrap <file>          A "bootstrap" PHP file that is run before the tests.
	  -c|--configuration <file>   Read configuration from XML file.
	  --no-configuration          Ignore default configuration file (phpunit.xml).
	  --no-coverage               Ignore code coverage configuration.
	  --no-logging                Ignore logging configuration.
	  --no-extensions             Do not load PHPUnit extensions.
	  --include-path <path(s)>    Prepend PHP's include_path with given path(s).
	  -d key[=value]              Sets a php.ini value.
	  --generate-configuration    Generate configuration file with suggested settings.
	
	Miscellaneous Options:
	
	  -h|--help                   Prints this usage information.
	  --version                   Prints the version and exits.
	  --atleast-version <min>     Checks that version is greater than min and exits.
	  --check-version             Check whether PHPUnit is the latest version.


