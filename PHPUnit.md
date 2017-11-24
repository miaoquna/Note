# PHPUnit 单元测试 #

- [1. 安装PHPUnit](#1-安装PHPUnit)
	- [1.1 Composer安装](#11-Composer安装) 
	- [1.2 可选的组件包](#12-可选的组件包)
- [2. 编写PHPUnit测试](#2-编写PHPUnit测试)
	- [2.1 测试的依赖关系](#21-测试的依赖关系)
	- [2.2 数据供给器](#22-数据供给器)
	- 
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

### 2.1 测试的依赖关系

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

### [2.2 数据供给器](#22-数据供给器)

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

例 2.7 使用返回迭代器对象的数据供给器

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

