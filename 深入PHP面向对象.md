# 深入PHP面向对象

## 目录

- [1. 高级特性](#1-高级特性)
	- [1.1 静态方法和属性](#11-静态方法和属性)
	- [1.2 常量属性](#12-常量属性)
	- [1.3 抽象类](#13-抽象类)
	- [1.4 接口](#14-接口)
	- [1.5 延迟静态绑定：new static()](#15-延迟静态绑定：new-static())
	- [1.6 异常](#16-异常)
		- [1.6.1 抛出异常](#161-抛出异常)
		- [1.6.2 自定义异常类](#162-自定义异常类)
	- [1.7 Final 类和方法](#17-final-类和方法)
	- [1.8 使用拦截器](#18-使用拦截器)
- [2. 对象工具](#2-对象工具)
	- [2.1 PHP包和命名空间](#21-php包和命名空间)
		- [2.1.1 命名空间](#211-命名空间)
		- [2.1.2 文件包含](#212-文件包含)
		- [2.1.3 关于路径](#213-关于路径)
	- [2.2 自动加载](#22-自动加载)
	- [2.3 类函数和对象函数](#23-类函数和对象函数)
		- [2.3.1 查找类](#231-查找类)
	- [2.4 反射API](#24-反射API)
		- [2.4.1 反射类](#241-反射类) 
		- [2.4.2 ReflectionClass](#242-reflectionclass)
		- [2.4.3 检查类](#243-检查类)
		- [2.4.4 检查方法](#244-检查方法)
		- [2.4.5 检查方法参数](#245-检查方法参数)
		- [2.4.6 使用反射API](#246-使用反射api)
	- [2.5 Trait代码复用](#25-Trait代码复用)
		- [2.5.1 优先级](#251-优先级)
		- [2.5.2 多个 trait](#252-多个-trait)
		- [2.5.3 冲突的解决](#253-冲突的解决)
		- [2.5.4 修改方法的访问控制](#254-修改方法的访问控制)
		- [2.5.5 从 trait 来组成 trait](#255-从-trait-来组成-trait)
		- [2.5.6 Trait 的抽象成员](#256-trait-的抽象成员)
		- [2.5.7 Trait 的静态成员](#257-trait-的静态成员)
		- [2.5.8 属性](#258-属性)
- [3. 对象与设计](#3-对象与设计)
	- [3.1 面向对象设计和过程式编程](#31-面向对象设计和过程式编程)
		- [3.1.1 职责](#311-职责)
		- [3.1.2 内聚](#312-内聚)
		- [3.1.3 耦合](#313-耦合)
		- [3.1.4 正交](#314-正交)
	- [3.2 选择类](#32-选择类)
	- [3.3 多态](#33-多态)
	- [3.4 封装](#34-封装)
	- [3.5 忘记细节](#35-忘记细节)
	- [3.6 4个方向标](#36-4个方向标)
		- [3.6.1 代码重复](#361-代码重复)
		- [3.6.2 类知道的太多](#362-类知道的太多)
		- [3.6.3 万能的类](#363-万能的类)
		- [3.6.4 条件语句](#364-条件语句)
	- [3.7 UML 统一建模语言](#37-uml-统一建模语言)
		- [3.7.1 类图](#371-类图)
		- [3.7.2 时序图](#372-时序图)

- [4. 设计模式](#4-设计模式)
	- [4.1 设计模式概览](#41-设计模式概览) 
		- [4.2.1 命名](#421-命名)
		- [4.2.2 问题](#422-问题)
		- [4.2.3 解决方案](#423-解决方案)
		- [4.2.4 效果](#424-效果)
- [5. 模式原则](#5-模式原则)
	- [5.1 组合与继承](#51-组合与继承)
	- [5.2 使用组合](#52-使用组合)
	- [5.3 解耦](#53-解耦)
		- [5.3.1 问题](#531-问题)
		- [5.3.2 降低耦合](#532-降低耦合)
	- [5.4 针对接口编程，而不是针对实现编程](#54-针对接口编程而不是针对实现编程)
	- [5.5 变化的概念](#55-变化的概念)
	- [5.6 父子关系](#56-父子关系)
- [6. 生成对象](#6-生成对象)
	- [6.1 生成对象的问题和解决方法](#61-生成对象的问题和解决方法)
	- [6.2 单例模式](#62-单例模式)
		- [6.2.1 问题](#621-问题)
		- [6.2.2 实现](#622-实现)
		- [6.2.3 结果](#623-结果)
	- [6.3 工厂方法模式](#63-工厂方法模式)
		- [6.3.1 问题](#631-问题)
		- [6.3.2 实现](#632-实现)
		- [6.3.3 结果](#633-结果)
	- [6.4 抽象工厂方法模式](#64-抽象工厂方法模式)
		- [6.4.1 问题](#641-问题)
		- [6.4.2 实现](#642-实现)
		- [6.4.3 结果](#643-结果)
		- [6.4.4 原型模式](#644-原型模式)
- [7. 让面向对象编程更加灵活的模式](#7-让面向对象编程更加灵活的模式)
	- [7.1 组合模式](#71-组合模式)
		- [7.1.1 问题](#711-问题)
		- [7.1.2 实现](#712-实现)
		- [7.1.3 效果](#713-效果) 


## **1. 高级特性**

## 1.1 静态属性和方法

关于 static 关键字

静态方法是类作为作用域的函数。静态方法不能访问这个类中的普通属性，因为那些属性属于一个对象，但可以访问静态属性。如果修改了一个静态属性，那么这个类的所有实例都能访问到这个新值。

因为通过类而不是实例来访问静态元素，所以访问静态元素时不需要引用对象的变量，而是使用 :: (两个冒号)来连接类名和属性或者类名和方法。

MyClass::$MY_NAME
MyClass::createName($name);

要从当前类中访问静态方法或属性，可以使用 self 关键字。self 指向当前类，就像伪变量 $this 指向当前对象的实例一样。因此 StaticExample 类的外部可以使用其类名访问属性

StaticExample::$aNum;

而 StaticExample 类内部，可以使用self关键字：
class StaticExample {
static public $aNum = 0;
static public funtion sayHello(){
echo self::$aNum;
}
}

`注解：只有在使用 parent 关键字调用方法的时候，才能对一个非静态方法进行静态形式的调用（两个冒号）。除非是访问一个被覆写的方法，否则永远只能使用 :: 访问被声明为static的方法或属性。`

既然类中无法调用静态方法或属性，我们为什么要使用静态方法或属性呢？因为静态元素有很多有用的特性。首先我们在代码中的任何地方都可用。也就是说，你不需要在对象间传递类的实例，也不需要将实例存在在全局变量中，就可以访问类中的方法。其实，类的每个实例可以访问类中定义的静态属性而且每个实例访问到的值一样，所以你可以利用静态属性来设置值，该值可以被类的所有对象使用。最后，不需要实例对象就能访问静态属性或者静态方法，这样就不用为了获取一个简单的功能而实例话对象

## 1.2 常量属性

const 定义在类中的常量，所以又叫常量属性，只包好基本的数据类型的值，而且无法修改。像静态属性一样只能通过类而不能通过类的实例访问常量属性。常量一般全部大写

ShopProduct::WEB_URL;

当需要在类的所有实例中都能访问某个属性，并且属性值无需改变时，应该使用常量。

## 1.3 抽象类

抽象类不能被直接实例化，抽象类中只定义子类需要的方法。子类可以继承它并通过实现其中的抽象方法，是抽象类具体化。

```php
abstract class ShopProductWrite {
    protected $products = array();
    public function addProduct(ShopProduct $shopProduct) {
        $this->products[] = $shopProduct;
    }
}
```

和普通类一样，可以创建抽象类的方法和属性。但是切记不能实例化抽象类

$writer = new ShopProductWrite(); 这样是会报错的

大多数情况下，抽象类至少包含一个抽象方法。抽象方法用abstract关键字声明，其中不能有具体内容。你可以像声明普通类方法那样声明抽象方法，但要以分号而不是方法体结束，下边是添加了一个抽象方法 write()

```php
abstract class ShopProductWrite {
    protected $products = array();
    public function addProduct(ShopProduct $shopProduct) {
        $this->products[] = $shopProduct;
    }
    //抽象方法
    abstract public function write();
}
```

创建抽象方法后，要确保所有子类中都实现了该方法，但是实现细节可以先不确定.
如果像下面这样创建了一个继承自 ShopProductWrite 的类，但是不实现 write(),php会报致命错误

class ErroredWriter extends ShopProductWriter{}

所以,抽象类的每个子类都必须实现抽象类中的所有抽象方法，或者把它们自身也声明为抽象方法。扩展类不仅仅负责简单实现抽象类中的方法，还必须重新声明方法。新的实现方法的访问控制不能比抽象方法的访问控制更严格。新的实现方法的参数个数应该和抽象方法的参数个数一样，重新生成对应的类型提示。

```php
class XmlProductWrite extends ShopProductWrite{
    public function write()
    {
        // TODO: Implement write() method.
		//可以在这里边实现任何想实现的东西
    }
}
```

## 1.4 接口

抽象类提供了具体实现的标准，而接口则是纯粹的模板。接口只能定义功能而不包含实现的内容。接口可以用关键字 interface 来声明。接口可以包含属性和方法，但是方法体为空。

interface MyInter {
public function getPrice();
}

接口和类非常相似。任何实现接口的类都要实现接口中定义的所有方法，否则该类必须声明为 abstract

一个类可以在声明中使用 implements 关键字来实现某个接口。这么做之后，实现接口的具体过程和扩展一个仅包含抽象方法的抽象类是一样的。

class ShopProduct implements MyInter {
public function getPrice(){
....
}
}

## 1.5 延迟静态绑定：new static()

静态方法可以用作工厂方法，工厂方法是生成包含类的实例的一种方法

```php
abstract class DomainObject {
}
class User extends DomainObject {
    public static function create() {
        return new User();
    }
}
class Document extends DomainObject {
    public static function create() {
        return new Document();
    }
}
```

在编程过程中经常会遇到上边这种代码，子类的两个静态方法的作用是一样的，这时你可能会想把这个方法放到父类来实现，得到下边的代码

```php
abstract class DomainObject {
    public static function create() {
        return new self();
    }
}
class User extends DomainObject {
}
class Document extends DomainObject {
}
Document::create();
```

看起来是简洁不少，但是抽象父类的方法中返回的 self() 是作为对该类自身的引用。也就是你想用 self() 来实例化 DomainObject 抽象类， 但是实际上你想让他解析为 self 当前子类的实例，这时候可以使用 `延迟静态绑定`。该特性最明显的标志就是关键字 static。static 类似于 self，但它指的是被调用的类而不是包含类。在本例中，它的意思是调用 Document::create() 将生成一个 Document 对象，而不是试图实例化一个 DomainObject 对象
因此，现在在静态上下文中使用继承关系。

```php
abstract class DomainObject {
    public static function create() {
		/*
		static() static - PHP 5.3加进来的只得是当前这个类，有点像$this的意思，从堆内存中提取出来，访问的是当前实例化的那个类，那么 static 代表的就是那个类。
		*/
        return new static();
    }
}
class User extends DomainObject {
}
class Document extends DomainObject {
}
print_r(Document::create());
//输出 Document Object ( )
```

static关键字不仅仅可以用于实例化。和self和parent一样，static 还可以作为静态方法调用通用的标识符，甚至是从非静态上下文中调用。假设我想为 DomainOject 引入组的概念。默认情况下，所有类都属于default类别，但我想能为继承层次结构的某些分支重写类别。

```php
abstract class DomainObject {
    private $group;
    public function __construct()
    {
        $this->group = static::getGroup();
    }

    public static function create() {
        return new static();
    }

    static function getGroup() {
        return "default";
    }
}
class User extends DomainObject {
}
class Document extends DomainObject {
    static function getGroup()
    {
        return 'Document Class'; // TODO: Change the autogenerated stub
    }
}
class SpreaSheet extends Document {
}
print_r(User::create());
print_r(SpreaSheet::create());

//输出 User Object ( [group:DomainObject:private] => default ) 
//输出 SpreaSheet Object ( [group:DomainObject:private] => Document Class )
```

user 类不需要实现太多功能。DomainOjbect 构造函数调用了 getGroup 类，并在本地进行查找。对于SpreaSheet，虽然搜索从被调用的类SpreadSheet本身开始，但它没有提供任何实现，因此调用类  Document 中的getGroup()方法。

## 1.6 异常

异常是从 Exception 类实例化得到的特殊对象。Exception类型的对象用于存放和报告错误信息。
Exception类的构造方法接受两个可选的参数：消息字符串和错误代码

### 1.6.1 抛出异常

可以联合使用 throw 关键字和Exception对象来抛出异常。这会停止执行当前方法，并负责将错误返回给调用代码。
例如：
```php
if (!file_exists($file)) {
	throw new Exception("file is not exitsts");
}
```

抛出异常时，客户端代码怎么知道如何处理异常呢？如果调用可能会抛出异常的方法，那么可以把调用语句放在 try 子句中。try子句由关键字try及其后的括号组成。try 子句必须跟着至少一个 catch 子句才能处理错误

```php
try {
	$conf = new Conf();
	//假如上边的抛出异常是在这个conf() 类里边完成的，如果类里边抛出错误，那么这里是能捕捉到的。
} catch (Exctpiton $e) {
	//根据捕捉到异常进行相应的处理
}
```

### 1.6.2 自定义异常类

所有的自定义异常类都要继承自 Exception 最顶层的异常处理类

class FileException extends Exception {}
class ConfException extends Exception {}

## 1.7 Final 类和方法

继承为类层次内容带来了巨大的灵活性。通过覆写类或方法，根据调用的哪个类实例，调用同样的类方法可以得到完全不同的结果。但有时候，你可能需要类或方法保持不变。如果希望类和方法完成确定不变的功能，担心覆写会它会破坏这个功能，那么需要使用final关键字。

final关键字可以终止类的继承。final类不能有子类，final方法不能被覆写。

## 1.8 使用拦截器

PHP的内置拦截器方法，它可以拦截发送到未定义方法和属性的消息。它也被称为 重载，但是自从这个术语在Java和C++中被赋予不同的含义之后，我认为还是叫做拦截器比较好。

php内置的拦截器方法

| 方法 | 描述 |
| ----- |------|
| __get($property)	| 访问为定义的属性 |
| __set($propert)	| 给未定义的属性赋值时被调用 |
| __isset($propert) | 对未定义的属性调用 isset() 时被调用 |
| __unset($propert) | 对未定义的属性调用 unset() 时被调用 |
| __call($method, $arg_array) | 对未定义的属性调用 unset() 时被调用 |

__get() 和 __set() 方法用于处理类（或其父类）中未声明的属性。
当客服端代码视图访问未声明的属性时，__get() 会被调用，并带一个包含要访问的属性名称字符串参数。无论从 __get() 方法返回了什么，都会发送给客户端代码，就好像带有该值的目标属性存在一样。
```php
class Person {
    function __get($name)
    {
        // TODO: Implement __get() method.
        $method = "get{$name}";
        if (method_exists($this, $method)) {
            return $this->$method;
        }
    }
    function getName() {
        return "Bob";
    }
    function getAge() {
        return 44;
    }
}
```
访问未定义的属性时， __get() 方法被调用。我们实现了 __get() 来获得属性名并构造新的字符串，把 “get” 放在属性名之前，然后将组合成的类方法字符串传给 method_exists() 函数（它接受对象和方法名作为参数），检查方法是否确实存在。如果方法存在，就调用它并把返回值传递给调用者。
$p = new Person();
echo $p->name; //输出
echo $p->gender;//这个方法会不存在，什么也不处理。用户访问的找个熟悉被解析为NULL;

__isset() 和 __get() 方法相似。当客户在一个未定义的属性上调用 isset() 时， __isset()被调用。

```php
function __isset( $property ) {
	$method = "get{$property}";
	return (method_exists($this, $method));
}
```

__set() 方法在给未定义的属性赋值时被调用，它会接受两个参数：属性名和值，然后我们我们再决定如何使用这些参数。

```php
<?php
class Person {
    private $_name;
    private $_age;

    /**
     * __set() 拦截，当设置一个未知属性时，会拦截到这个操作，进入这个方法进行处理
     * @param $property
     * @param $value
     */
    function __set( $property, $value) {
        $method = "set{$property}";
        //校验一个方法是否存在，接收一个对象和一个方法名
        if (method_exists($this, $method)) {
            //方法存在的话，调用这个方法
            return $this->$method($value);
        }
    }

    /**
     * 设定名称方法
     * @param $name
     */
    function setName($name) {
        $this->_name = $name;
        if (!is_null($name)) {
            //所有字符转大写
            $this->_name = strtoupper($this->_name);
        }
    }

    /**
     * 设定年龄方法
     * @param $age
     */
    function setAge($age) {
        $this->_age = strtoupper($age);
    }
}
```

在这个例子中，我们使用了设置方法而不是获取方法。如果用户要给未定义的属性赋值，__set() 方法就会被调用，其参数为属性名及要赋的值。我们检查了指定设置的方法是否存在，如果存在就调用。这样我们可以过滤掉已经赋值的属性。

当创建 Person 对象并尝试设置一个名为Person::$name 的属性时，因为这个类没有定义 $name 属性，所以 __set() 方法会被调用，其参数为字符串 name 和要设定的值。这一值如何使用则取决于 __set() 的实现。本例中，我们将set字符串与属性名称链接在一起，构造了一个类方法setName()。我们发现 setName() 方法已经存在，并且立即调用它。这样就可以从外部得到值存储到一个真实存在的属性中。

__unset() 和 __set相对应。当unset()在一个未定义的属性上被调用时， __unset() 会被调用，并以该属性的名称作为参数，然后你可以根据属性名进行必要的处理。

__call() 方法可能是最有用的拦截器方法。当调用类中未定义的方法时， __call() 会被调用。 __call接受两个参数，一个是方法名，另一个是传递给要调用方法的所有参数（数组）。 __call() 方法返回的任何值都会返回给客户，就好像调用一个真实存在的方法一样。

__call() 方法对于实现委托也很有用。委托是指一个对象转发或者委托一个请求给另一个对象，被委托的一方替原先对象处理请求。这类似于继承，和在子类中调用父类的方法有点相似。但在继承时，父类与子类的关系是固定的，而使用委托则可以在代码运行时改变使用的对象，这意味着委托比继承具有更大的灵活性。下边的例子解释了如何使用委托：

```php
class PersonWriter {
    function writeName(Person $p) {
        print $p->getName."\n";
    }
    function writeAge(Person $p) {
        print $p->getAge()."\n";
    }
}
class Person {
    private $writer;

    function __construct(PersonWriter $writer)
    {
        $this->writer = $writer;
    }

    function __call($name, $arguments)
    {
        // TODO: Implement __call() method.
        if (method_exists($this->writer, $name)) {
            return $this->writer->$name($this);
        }
    }

    function getName() {
        return "Bob";
    }
    function getAge() {
        return 44;
    }
}
```

## 1.9 析构方法

__destruct() 方法，它只在对象被垃圾收集前自动调用。用来做最后的必要的清理工作。

## 1.10 使用 __clone() 复制对象
```php
class CopyMe{}
$first = new CopyMe();
$second = $first;
```
在PHP中，对象的赋值和传递都是通过引用进行的。 $first 和 $second 这两个变量包含指向同一个对象的引用，而不是各自保留一份相同的副本，如果你要获取一个对象的副本，而不是引用，可以通过 clone 关键字来达到这个目的。
```php
class CopyMe{}
$first = new CopyMe();
$second = clone $first;
//现在 $first 和 $second 是两个不同的对象
```
如果你想在克隆一个类的时候监听到克隆的动作，可以在类里边实现 __clone() 魔术方法来实现监听

```php
class CopyMe{

function __clone(){
//在使用clone这个对象的时候，会被 __clone 拦截到，然后你就可以在这个方法里边完成你想做的工作了
}

}
```

## 1.11 回调、匿名函数和闭包

```php
<?php
class Product {
    public $name;
    public $price;

    function __construct($name, $price)
    {
        $this->name = $name;
        $this->price = $price;
    }
}
class ProcessSale {
    private $callbacks;

    /**
     * 注册回调方法到类的属性中
     * @param $callback
     */
    function registerCallback($callback) {
        //验证变量的内容是否能够进行函数调用
        if (! is_callable($callback)) {
            //抛出异常
            throw new Exception("callback 不能进行函数调用");
        }
        $this->callbacks[] = $callback;
    }

    function sale($product) {
        print "{$product->name}:processiong \n";
        foreach ($this->callbacks as $callback) {
            //把 $callback 作为回调函数调用
            call_user_func($callback, $product);
        }
    }
}
```


## **2. 对象工具**

## 2.1 PHP包和命名空间

### 2.1.1 命名空间

从本质上说，命名空间就是一个容器，你可以将类、函数和变量放在其中。在命名空间中，你可以无条件地方法这些项目。在命名空间之外，必须导入或引用命名空间，才能访问它所包含的项。

namespace 关键字创建命名空间

```php
namespace com\getinstance\util; //声明当前类的命名空间

class Debug {
    static function helloWorld() {
        echo "hello bug";
    }
}
//这里可以直接使用 非限定名 调用，因为你已经处于 com\getinstance\util 命名空间中了，所以不必在类名前加任何种类的路径。
Debug::helloWorld();

//如果打算重名空间环境之外访问类，可以这样做：
// com\getinstance\util\Debug::helloWorld();
```

如果我使用下边的代码会提示找不到命名空间

```php
namespace main;
com\getinstance\util\Debug:helloWorld();
```
因为我使用了相对命名空间。PHP会在main命名空间中寻找com\instance\util，但没有找到。就像创建绝对URL和文件路径时，以分割符作为开始一样，你也可以使用这种方式创建 `绝对命名空间`，下边这个示例修正了前面的错误
```php
namespace main;
\com\getinstance\util\Debug::helloWorld();
```
这个最前边的 `反斜杠` 告诉PHP从根命名空间而不是从当前命名空间开始搜索。
但命名空间不应该在减少代码方面有所帮助吗？当前，Debug类声明很短，但这些调用和使用老旧的命名规范中的调用一样啰嗦。使用use关键字可以解决这一问题。利用该关键字，你可以为当前命名空间中的其他命名空间起别名
```php
namespace main;
use com\getinstance\util;
util\Debug::helloWorld();
```
导入 com\getinstance\util 命名空间，并隐式地为其使用了别名util。注意：没有使用前导反斜杠字符作为开始。这里从全局命名空间而不是从当前命名空间搜索要使用的参数。如果不想引用命名空间，可以导入Debug类本身：
```php
namespace main;
use com\getinstance\util\Debug;
util\Debug::helloWorld();
```
但是如果 main 命名空间中已经包好了Debug类，会发生什么？
```php
namespace main;
use com\getinstance\util\Debug;
class Debug {
    static function helloWorld() {
        print "hello from main";
    }
}
Debug::helloWorld();
```
`Fatal error: Cannot declare class main\Debug because the name is already in use in`

这时会遇到类命名冲突问题，命名空间不就是为了解决命名冲突的问题的吗？
这时候我们可以使用 `as` 子句来定义别名
```php
namespace main;
use com\getinstance\util\Debug as uDebug;
class Debug {
    static function helloWorld() {
        print "hello from main";
    }
}
Debug::helloWorld();
```
如果在命名空间中编写代码，而你想放的类保存在全局空间中（没有定义命名空间的类），那么你可以在该名称前加反斜杠。下面是在全局空间中声明的方法：
```php
//global.php 无命名空间
class Lister {
	public static function helloWorld() {
		print "hello from global";	
	}
}
```
下面是一些引用该类的命名空间代码：
```php
namespace com\getinstance\util;
require_once 'global.php';
class Lister {
	public static function helloWorld() {
		print "hello from ".__NAMESPACE__;
	}
}
Lister::helloWorld(); //（非限定名访问）当前命名空间访问
\Lister::helloWorld(); //全局空间的访问
```
命名空间的代码声明了它自己的Lister类。非限定名访问本地版本。使用单个反斜杠限定的名称将访问全局空间中的类。
__NAMESPACE__ 输出当前命名空间的名称

如果你必须将多个命名空间组合到一个文件中，那么推荐下面这种写法
```php
//定义命名空间
namespace com\getinstance\util {
    //名空间的中的类
    class Debug {
        static function helloWorle() {
            print "hello from Debug";
        }
    }
}
//定义命名空间
namespace main {
    \com\getinstance\util\Debug::helloWorle();
}
```
但通常认为每个文件定义一个命名空间是最好的做法。

命名空间后的`大括号语法`提供一项功能就是可以在一个文件中切换到全局空间。前面我曾使用 require_once 从全局空间获取代码。实际上，我也可以使用刚刚介绍的这种命名空间语法，并在文件中执行所有操作。

```PHP
//进入到全局空间，也就是根目录的意思
namespace {
    class Lister {
        //全局空间中的类
        static function helloWorld() {
            print "hello from Global";
        }
    }
}
namespace com\getinstance\util {
    class Lister {
        static function helloWorld() {
            
        }
    }
    Lister::helloWorld();
    \Lister::helloWorld();
}
```
`不能在同一个文件中同时使用大括号命名空间语法和行命名空间语法。你必须选择其中的一种，并且在整个文件中坚持使用这种语法，意思就是大括号命名空间和行命名空间不能同时存在`

### 2.1.2 文件包含

除了使用命名空间，你也可以使用文件包含引入一个类或者文件

include() 和 require() 语句的不同在于如何处理错误。

include 调用文件发生错误时，生成警告并停止执行包含文件，跳出包含语句继续向下执行
include_once 功能和 include 类似，但是一个文件只能被包含一次，如果包含两次将会报错

require 调用文件发生错误时，将会停止整个代码
require_once 功能和 require 类似，但是一个文件只能被包含一次，如果包含两次将会报错

`注意：包含文件时 require() 和 require_once() 都是语句而不是函数，这意味着使用它们的时候可以省略括号。我通常会喜欢加括号`

`关于性能一点思考：和使用 require() 相比，require_once() 需要额外的开销。如果想尽可能地减少系统执行时间，应该考虑使用   require()`

### 2.1.3 关于路径

组织组件时，必须记住两件事。第一：文件系统文件和目录存放的位置，第二：你要充分考虑组件之间互相访问的方式。

当我们包含一个文件时，可以使用相对路径（相对于当前工作目录），也可以使用绝对路径来引用该文件。

比如使用相对路径的例子

require_once('business/User.php');

但这需要当前的工作目录中包含 business 目录。如果以后 business 目录不存在了，就需要修改代码。使用相对路径包含库时，通常会使用冗长的 require_once() 语句：

require_once('../../projectlib/business/User.php');

当然也可以使用绝对路径

require_once('/home/john/projectlib/business/User.php');

没有哪种方法是绝对完美的。使用绝对路径太过绝对，并且把库文件固定在某个地方了。

使用绝对路径时，我们把库绑定到了特定的文件系统。无路何时在新服务器上安装写好的程序时，所有的 require 语句都需要更改文件路径

使用相对路径时，我们把脚本工作目录和类库目录的位置关系固定住了。这样如果不修改 require 语句，就无法重新定位文件系统中的文件，而且不通过复制文件很难实现项目间共享。

这时候你可以选择使用包，将包文件放置于 usr/local/lib/php-libraries 这种系统底层的公共包管理的地方

当然你也可以使用这两年流行起来 `composer包管理`，这个会做单独讲解

## 2.2 自动加载

**这节讲述最重要的自动加载实现原理**

在某些情况下，你可能希望一个文件定义一个类，一一对应，便于管理。这种方法会有额外的开销（包含文件会带来开销），但这种组织方式非常有用，特别是系统需要在运行时使用新类的情况下。使用这种方法，每个类文件的名称都和它所包含的类名相关，因此你可以再名为ShopProduct.php 的文件中定义 ShopProduct类。另外也可以使用 PEAR 命名规则，把文件命名为 ShopProduct.php,但是类可能要根据它的路径命名为 business_ShopProduct

PHP5引入了 __autoload() 拦截器方法来自动包含类文件。 __autoload() 应该被写成单个参数的方法。当PHP引擎遇到视图实例化未知类的操作时，会调用 __autoload() 方法（如果已定义），并将类名当作字符参数传递给它。 __autoload() 的编写者应该自己定义一种策略来定位和包含缺失的类文件。

```php
function __autoload( $className ) {
	//你会看到无论如何都会输出 123，这就意味着只要你实例化一个未知的类都被 __autoload() 拦截到这个实例化请求
    echo 123;
    include_once "$className.php";
}
$product = new ShopProduct('The Dark', 12.99);
```

如果使用PEAR命名规则，则可以这样实现

```php
function __autoload( $className ) {
    //DIRECTORY_SEPARATOR 表示当前环境下的目录分隔符
    $path = str_replace('_', DIRECTORY_SEPARATOR, $className);
    // business/ShopProduct.php
    include_once "$path.php";
}
$product = new business_ShopProduct('The Dark', 12.99);
```

当然我们不会经常使用上边这种方式，我个人绝对会导致类名太长，而且如果类名 business_Shop_Product 也包含下划线，会出现引用文件错误的问题。

那是用命名空间如何？它需要测试反斜线字符，如果该字符存在的话，就添加一个转换：
```php
function __autoload( $className ) {
    if (preg_match('/\\\\/', $className)) {
        $path = str_replace('\\', DIRECTORY_SEPARATOR, $className);
    } else {
        $path = str_replace('_', DIRECTORY_SEPARATOR, $className)；
    }
    require_once $path;
}
```

同样，我又对类文件和目录以及他们与命名空间或PEAR风格的类名之间的关系做了一些假设。考虑到导入和使用别名的灵活性，你关系的可能是在命名空间中调用类的各种方式。毕竟我可以对 business\ShopProduct 使用任意别名，例如Percy，传递给 __autoload() 的值总被规范化成完全限定的名称，没有前导的反斜杠，这是好事

__autoload() 方法是一种根据类和文件的结构，管理类库文件包含的有效方法。

`注意：__autoload()是一个强大的工具，但也存在一些限制。尤其是，在一个进程中你只能定义它一次。如果需要动态的修改自动加载函数，那么你应该看一下 spl_autoload_register 函数，它提供了动态修改功能`

`sql_autoload_register` 是更高级的 __autoload()，它可以动态注册自动加载函数，而且可以加载多个，按顺序执行


```php
//a.class.php
class a
{
    function __construct($name)
    {
        echo "$name 这是从 A 创建的<br>";
    }
}

//b.php
class b
{
    function __construct($name)
    {
        echo "$name 这是从 B 创建的<br>";
    }
}

//AutoLoad.php
class LOAD1
{
    static function loadClass($class_name)
    {
        $filename = $class_name . ".class.php";
        if (is_file($filename)) return include_once $filename;
    }
}

class LOAD2
{
    static function loadClass($class_name)
    {
        $filename = $class_name . ".php";
        if (is_file($filename)) return include_once $filename;
    }
}

//实现自动加载，很多框架就用这种方法自动加载类
/**
 * 设置对象的自动载入
 * spl_autoload_register — Register given function as __autoload() implementation
 */
spl_autoload_register(array('LOAD1', 'loadClass'));
spl_autoload_register(array('LOAD2', 'loadClass'));
$a = new a('a'); //来自于
$b = new b('b');
```
上边注册了两个自动加载函数，在注册多个的情况下，是按顺序执行的


## 2.3 类函数和对象函数

PHP在运行时你可能无法知道正在使用的类是哪个。例如，你要设计一个能够透明地被第三方类使用的系统。在这种情况下，你很可能需要实例化一个只给出类名的对象。PHP允许使用字符串来动态的引用类。
```php
//Task.php
namespace tasks;
class Task {
    function doSpeak() {
        print "hello<br>";
    }
}

//TaskRunner.php
$classname = "Task";
require_once "tasks/{$classname}.php";
$classname = "tasks\\$classname";
$myObj = new $classname();
$myObj->doSpeak();
```
你可以从配置文件或通过比较Web请求和目录内容获得赋值给 $classname 的字符，然后利用该字符串来加载文件，实例化需要的对象。注意，在这个代码中，我已经构造了一个命名空间限定条件

如果想让系统能运行用户创建的插件，这个功能非常有用。在开发实际项目时，需要检查类是否存在、它是否拥有将要使用的方法等，以避免可能存在的风险。

### 2.3.1 查找类

class_exists() 函数接受表示类的字符串，检查并返回布尔值。如果类存在则返回 true，否则返回false，使用该函数，可以使之前的代码段更安全一些

```php
//Task.php
namespace tasks;
class Task {
    function doSpeak() {
        print "hello<br>";
    }
}

//TaskRunner.php
$classname = "Task";
$path = "tasks/{$classname}.php";
if (!file_exists($path)) {
	throw new Exception("No such file as {$path}");
}
require_once $path;
$classname = "tasks\\$classname";
if (!class_exists($classname)) {
	throw new Exception("No such class as {$path}");
}
$myObj = new $classname();
$myObj->doSpeak();
```

当然，我们无法确定该类的构造方法是否需要参数。在这个级别的安全上，应该求助于本章稍候介绍的反射API

检查类的类型常用的方法有：`get_class`、`instanceof`

常用的类操作方法

| 方法名 | 解释 |
| ----- | ----- |
|get_declared_classes(); | //获取脚本进程中定义的所有类的数组 |
|get_class();| //简单地实例化并返回一个对象 |
|get_class_methods(); | //获取一个类中所有方法的列表 |
|is_callable();  |//检查方法存在且可被调用 |
|method_exists();  |//检查方法是否存在（private、protected、public方法都会返回true） |
|get_class_vars();  |//获取类的属性 |
|get_parent_class(); | //获取类的继承关系 |
|is_subclass_of();  |//检测类是否是另个类的派生类
|class_implements(); | //返回一个类实现的接口 |
|call_user_func(); | //动态调用方法或函数 |

## 2.4 反射API

### 2.4.1 反射类

| 类 | 描述 |
| ----- | ----- |
|Reflection(); | //为类的摘要信息提供静态函数 export() |
|ReflectionClass();| //类信息和工具 |
|ReflectionMethod(); | //类方法信息和工具 |
|ReflectionParameter();  |//方法参数信息和工具 |
|ReflectionProperty();  |//类属性信息 |
|ReflectionFunction();  |//函数信息和工具 |
|ReflectionExtension(); | //PHP扩展信息 |
|ReflectionException();  |//错误类

利用反射API中的这些类，可以在运行时访问对象、函数和脚本中的扩展信息
由于反射API非常强大，你应该经常使用反射API而少用类和对象函数。它是不可缺少的类测试工具。例如，你也许想要生成类结构的图标或文档，或者想保存对象信息到数据库，检查对象的访问方法来提取字段名。反射的另一用途是根据命名规则创建一个调用模板类中方法的框架。

### 2.4.2 ReflectionClass

ReflectionClass 提供揭示给定类所有信息的方法，无论这个类是用户定义的还是PHP自带的内置类。ReflectionClass 的构造方法接受类名作为它的唯一参数：

```php

class CdProduct {
    public $value;
    public function api() {

    }
}
$prod_class = new ReflectionClass('CdProduct');
Reflection::export($prod_class);

```

创建 ReflectionClass 对象后，就可以使用 Reflection 工具类输出 CdProduct 类的相关信息。Reflection有一个静态方法 export()，用于格式化和输出 Reflection 对象管理的数据（即任何实现Reflector接口的类的实例）。下面是调用 Reflection::export() 所生成的输出摘要：


```php
Class [ <user> class CdProduct ] {
  @@ I:\wwwroot\www.ci.com\test.php 2-7

  - Constants [0] {
  }

  - Static properties [0] {
  }

  - Static methods [0] {
  }

  - Properties [1] {
    Property [ <default> public $value ]
  }

  - Methods [1] {
    Method [ <user> public method api ] {
      @@ I:\wwwroot\www.ci.com\test.php 4 - 6
    }
  }
}
```

可以看到，Reflection::export() 可以提供类的相关信息。 Reflection::export() 提供了 CdProduct类几乎所有的信息，包括属性和方法的访问控制状态、每个方法需要的参数以及每个方法在脚本文档中的位置。将该函数与调试函数 var_dump() 相比较，var_dump() 函数是汇总数据的通用工具，但使用 var_dump() 在提取摘要前必须实例化一个对象，而且它也无法提供像 Reflection::export 提供的那么多的细节。

```php
$cd = new CdProduct();
var_dump($cd);
```
输出结果：

object(CdProduct)[2]
  public 'value' => null

var_dump() 和它的系列函数 print_r 是检测PHP代码中数据的利器，但对于类和函数，反射API提供了更高层次的功能。

### 2.4.3 检查类

Reflection::export() 可以为调试提供大量有用的信息，我们还可以通过特定的方式使用API。下面直接使用 Reflection 类。

使用 ReflectionClass 对象来研究脚本中的 CdProduct。这个类属于哪一类型呢？可以创建实例吗？下面这个函数回答了这些问题：

```php
function classData(ReflectionClass $class) {
    $detail = "";
    $name = $class->getName();
    //检查一个类是否由用户定义，和内置相对。
    if ($class->isUserDefined()) {
        $detail .= "$name 是用户自己创建的类<br>";
    }
    //检查类是否由扩展或核心在内部定义，与用户定义相反。
    if ($class->isInternal()) {
        $detail .= "$name 是内部类<br>";
    }
    //检查类是否是一个接口
    if ($class->isInterface()) {
        $detail .= "$name 是接口类<br>";
    }
    //检查类是否是抽象类
    if ($class->isAbstract()) {
        $detail .= "$name 是抽象类<br>";
    }
    //检查类是否声明为 final。（final类不能被继承）
    if ($class->isFinal()) {
        $detail .= "$name 是final类,不能被继承<br>";
    }
    //检查这个类是否可实例化。
    if ($class->isInstantiable()) {
        $detail .= "$name 可以被实例化<br>";
    } else {
        $detail .= "$name 不能被实例化<br>";
    }
    return $detail;
}

$prod_class = new ReflectionClass('CdProduct');
print classData($prod_class);
```

ReflectionClass 提供自定义类所在的文件名及文件中类的起始和终止行。

下面是利用 ReflectionClass 来获取源代码的简单实例：

```php
class CdProduct {
    public $value;
    public function api() {

    }
}

class ReflectionUtil {
    //获取自定类的源码
    static function getClassSource(ReflectionClass $class) {
        //返回类所定义的文件名。如果这个类是在 PHP 核心或 PHP 扩展中定义的，则返回 FALSE。
        $path = $class->getFileName();
        //把整个文件读入一个数组中。
        $lines = @file($path);
        //获取自定类的起始行号
        $from = $class->getStartLine();
        //获取自定义类的结束行号
        $to = $class->getEndLine();
        //获取总行数
        $len = $to-$from+1;
        //implode 数组元素组合为字符串
        //array_slice() 从指定位置截取数组
        return implode(array_slice($lines, $from-1, $len));
    }
}

print ReflectionUtil::getClassSource(new ReflectionClass('Reflection'));
```
上例会输出:
```php
class CdProduct {
    public $value;
    public function api() {

    }
}
```
你会发现通过 ReflectionClass 类可以实现获取一个类的源代码

### 2.4.4 检查方法

已经了解过 ReflectionClass 可以用于检查类一样，ReflectionMethod 对象可以用于检查类中的方法。
获得 ReflectionMethod 对象的方法有两种：从 ReflectionClass::getMethods() 获得 ReflectionMethod 对象的数组；如果需要使用特定的类方法， ReflectionClass::getMethod() 可以接受一个方法名作为参数并返回相应的 ReflectionMethod 对象。

下面使用 ReflectionClass::getMethods() 来获取对象的所有方法并用 ReflectionMethod 类来逐一检查这些方法的细节信息。

```php

class CdProduct
{
    public $value;

    public function __construct()
    {
    }

    private function api()
    {

    }

    protected function inter()
    {
        return true;
    }
}

function methodData(ReflectionMethod $method)
{
    $detail = "";
    //获取方法名
    $name = $method->getName();
    //判断函数是否是用户自定义函数
    if ($method->isUserDefined()) {
        $detail .= "$name 是用户自定义的函数<br>";
    }
    // 判断函数是否是内置函数
    if ($method->isInternal()) {
        $detail .= "$name 是内置函数<br>";
    }
    //判断方法是否是抽象方法
    if ($method->isAbstract()) {
        $detail .= "$name 是抽象方法<br>";
    }
    //判断方法是否是公开方法
    if ($method->isPublic()) {
        $detail .= "$name 是公开方法<br>";
    }
    //判断方法是否是受保护方法
    if ($method->isProtected()) {
        $detail .= "$name 是受保护方法<br>";
    }
    //判断方法是否是私有方法
    if ($method->isPrivate()) {
        $detail .= "$name 是私有方法<br>";
    }
    //判断方法是否是静态方法
    if ($method->isStatic()) {
        $detail .= "$name 是静态方法<br>";
    }
    //判断方法是否定义 final（final方法是不能被子类覆盖）
    if ($method->isFinal()) {
        $detail .= "$name 是final方法，不能被子类覆盖<br>";
    }
    //判断方法是否是构造方法
    if ($method->isConstructor()) {
        $detail .= "$name 是构造方法<br>";
    }
    //检查是否返回参考信息
    if ($method->returnsReference()) {
        $detail .= "$name 返回参考信息<br>";
    }
    return $detail;
}


$prod_class = new ReflectionClass('CdProduct');
//获取 CdProduct 类的所有方法
$methods = $prod_class->getMethods();
foreach ($methods as $method) {
    print methodData($method);
    print "<br>----<br>";
}
```
上边案例输出：

__construct 是用户自定义的函数
__construct 是公开方法
__construct 是构造方法

----
api 是用户自定义的函数
api 是私有方法

----
inter 是用户自定义的函数
inter 是受保护方法

----

和 ReflectionClass 一样 ReflectionMethod 也提供了 getFileName()、getStartLine()、getEndLine()方法，因此可以通过这几个方法获取方法的源代码

### 2.4.5 检查方法参数

声明类方法时可以限制参数中对象的类型，因此检查方法的参数变得非常必要。反射API提供了 ReflectionParameter 类。要获得 ReflectionParameter 对象，需要 ReflectionMethod 对象的帮助。 ReflectionMethod::getParameters() 方法可返回 ReflectionParameter 对象数组。

ReflectionParameter 可以告诉你参数的名称，变量是否可以按引用传递（即变量声明前有一个&符号），还可以告诉你参数类型提示和方法是否接受空值作为参数。

下边不再举例，方法的使用和 ReflectionParameter、ReflectionMethod 类的方法适用方法一样

### 2.4.6 使用反射API

了解了反射API的基础知识，下面详细介绍如何使用反射API

假设我们要创建一个类来动态调用 Module 对象，即该类可以自由加载第三方插件并集成进已有系统，而不需要把第三方的代码硬编码进原有的代码。要达到这个目的，可以在 Module 接口或抽象类中定义一个 execute() 方法，强制要求所有的子类都必须实现该方法。可以允许在外部XML配置文件中列出所有 Module 类。系统可以使用XML提供的信息来加载一定数目的 Module对象，然后对每个 Module 对象调用 execute()

然而，如果每个 Module 需要不同的信息来完成任务，应该怎么做呢？在这种情况下，XML文件可以为每个Module提供属性键和值，Module的创建者可以为每个属性名提供setter方法。代码要确保根据某个属性名调用正确的setter方法。

下面是Module接口和几个实现类的基础：

```php
class Person
{
    public $name;

    function __construct($name)
    {
        $this->name = $name;
    }
}

/**
 * Interface Module 接口
 */
interface Module
{
    function execute();
}

/**
 * 实现Module接口
 * Class FtpModule
 * ${DS}
 */
class FtpModule implements Module
{
    function setHost( $host )
    {
        print "FtpModule::setHost：$host\n";
    }
    function setUser( $user )
    {
        print "FtpModule::setUser：$user\n";
    }
    //实现接口的方法
    function execute()
    {
        // TODO: Implement execute() method.
        //执行一些操作
    }
}
class PersonModule implements Module
{
    function setPerson( Person $person ) {
        print "PersonModule::setPerson：{$person->name}\n";
    }
    //实现接口方法
    function execute()
    {
        // TODO: Implement execute() method.
    }
}

```
这里，PersonModule 和 FtpModule 都提供了 execute() 方法的空实现。每个类也实现了 setter 方法。这些方法除了报告自己被调用之外不做任何事情。我们的系统规定所有的 setter 方法必须带有单个参数：要么是一个字符串，要么是可以用字符串参数来实例化的对象。PersonModule::setPerson() 方法希望有一个 Person 对象作为参数，所以在例子中设计了 Person 类。

要使用 PersonModule 和 FtpModule，下一步是创建 ModuleRunner 类。它使用索引为模块名的多维数组来展示XML文件提供的配置信息。下面是代码：

```php
class ModuleRunner
{
    private $configData = array(
        array(
            "PersonModule" => array('person' => 'bob'),
            "FtpModule" => array('host' => 'example.com', 'user' => 'anon'),
        )
    );
    private $modules = array();
}

```

ModuleRunner::$configData 属性包括了对两个 Module 类的引用。对于每个模块元素，代码保存一个包含属性集的子数组。 ModuleRunner 的init() 方法用于创建正确的 Module 对象，如下所示


```php

class Person
{
    public $name;

    function __construct($name)
    {
        $this->name = $name;
    }
}

/**
 * Interface Module 接口
 */
interface Module
{
    function execute();
}

/**
 * 实现Module接口
 * Class FtpModule
 * ${DS}
 */
class FtpModule implements Module
{
    function setHost( $host )
    {
        print "FtpModule::setHost：$host\n";
    }
    function setUser( $user )
    {
        print "FtpModule::setUser：$user\n";
    }
    //实现接口的方法
    function execute()
    {
        // TODO: Implement execute() method.
        //执行一些操作
    }
}
class PersonModule implements Module
{
    function setPerson( Person $person ) {
        print "PersonModule::setPerson：{$person->name}\n";
    }
    //实现接口方法
    function execute()
    {
        // TODO: Implement execute() method.
    }
}

class ModuleRunner
{
    private $configData = array(
        array(
            "PersonModule" => array('person' => 'bob'),
            "FtpModule" => array('host' => 'example.com', 'user' => 'anon'),
        )
    );
    private $modules = array();

    public function init()
    {
        $interface = new ReflectionClass('Module');
        foreach ($this->configData as $moduleName => $params) {
            $module_class = new ReflectionClass( $moduleName );
            //判断配置文件中的 Module 是否是实现了 Module
            if ( ! $module_class->isSubclassOf($interface)) {
                throw new Exception("未知 module ：$moduleName");
            }
            //创建类的新的实例。给出的参数将会传递到类的构造函数
            $module = $module_class->newInstance();
            foreach ($module_class->getMethods() as $method) {
                //传入实例、方法、参数
                $this->handleMethod($module, $method, $params);
            }
            array_push($this->modules, $module);
        }
    }

    public function handleMethod(Module $module, ReflectionMethod $method, $params)
    {

    }
}

$test = new ModuleRunner();
$test->init();
```

init() 方法循环遍历 ModuleRunner::$configData 数组，它尝试为对每个模块元素创建 ReflectionClass 对象。以不存在的类名调用 ReflectionClass 的构造方法时，会抛出异常，所以在真是情况下，我们应该在这里包含更多的错误处理。我们使用 ReflectionClass::isSubclassOf() 方法来确保模块类属于 Module 类型。

在调用每个 Module 的 execute() 方法前，我们必须先创建 Module 对象的实例，而这正是 ReflectionClass::newInstance 方法的设计目的。newInstance 方法可接受任意数目的参数，这些参数将被传递到相应类的构造方法。如果一切正常，它返回类的实例（对于要正式发布的产品，应确保代码的安全：在实例化对象前，需要确认每个 Module 对象的构造方法是否需要参数）

ReflectionClass::getMethods() 返回一个包含所有可用 ReflectionMethod 对象的数组。对于数组中的每个元素，代码调用 ModuleRunner::handleMethod() 方法，它有三个参数：一个Module实例、ReflectionMethod对象以及一个和Module关联的属性数组。handleMethod() 检验并调用 Module 对象的 setter 方法。

```php
class Person
{
    public $name;

    function __construct($name)
    {
        $this->name = $name;
    }
}

/**
 * Interface Module 接口
 */
interface Module
{
    function execute();
}

/**
 * 实现Module接口
 * Class FtpModule
 * ${DS}
 */
class FtpModule implements Module
{
    function setHost( $host )
    {
        print "FtpModule::setHost：$host\n";
    }
    function setUser( $user )
    {
        print "FtpModule::setUser：$user\n";
    }
    //实现接口的方法
    function execute()
    {
        // TODO: Implement execute() method.
        //执行一些操作
    }
}
class PersonModule implements Module
{
    function setPerson( Person $person ) {
        print "PersonModule::setPerson：{$person->name}\n";
    }
    //实现接口方法
    function execute()
    {
        // TODO: Implement execute() method.
    }
}

class ModuleRunner
{
    private $configData = array(
        "PersonModule" => array('person' => 'bob'),
        "FtpModule" => array('host' => 'example.com', 'user' => 'anon'),
    );
    private $modules = array();

    public function init()
    {
        //获取 Module 类的相关信息
        $interface = new ReflectionClass('Module');
        foreach ($this->configData as $moduleName => $params) {
            //获取配置文件中的 childModule 类的有关信息
            $module_class = new ReflectionClass( $moduleName );
            //判断配置文件中的 childModule 是否是 Module 的一个子类
            if ( ! $module_class->isSubclassOf($interface)) {
                //如果不是子类抛出异常
                throw new Exception("未知 module ：$moduleName");
            }
            //创建类的新的实例
            $module = $module_class->newInstance();
            //获取 ReflectionMethod 对象的数组
            foreach ($module_class->getMethods() as $method) {
                //传入实例、方法、参数
                $this->handleMethod($module, $method, $params);
            }
            //追加实例化的 Module 到 module 数组中
            array_push($this->modules, $module);
        }
    }

    public function handleMethod(Module $module, ReflectionMethod $method, $params)
    {
        //从 ReflectionMethod 对象中获取函数名
        $name = $method->getName(); //获取方法名
        //通过 ReflectionParameter 数组返回参数列表
        $args = $method->getParameters(); //一组 ReflectionParameter 对象表示的参数
        //判断参数是否是一个且函数名以 set 开头
        if ( count($args) != 1 || substr($name, 0, 3) != "set") {
            return false;
        }
        //从第三个字符截取函数名并转小写
        $property = strtolower(substr($name, 3));
        //判断后半段的函数名和配置文件中的函数名key是否一致
        if ( !isset($params[$property])) {
            return false;
        }

        //ReflectionClass 获取参数类型可能是一个类，例如 Person $person
        $arg_class = $args[0]->getClass();
        //如果没有获取到参数直接执行反射调用函数
        if ( empty( $arg_class )) {
            //调用反射函数，第一个参数：对象，第二个参数方法名
            $method->invoke($module, $params[$property]);
        } else {
            //存在方法参数情况下，调用反射函数，第一个参数：对象，第二个参数根据 ReflectionClass $arg_class 实例化参数
            $method->invoke($module, $arg_class->newInstance( $params[$property] ));
        }
    }
}

$test = new ModuleRunner();
$test->init();
``` 
## 2.5 Trait代码复用

Trait是为类似PHP的单继承语言而准备的一种代码复用机制。Trait为了减少单继承语言的限制，使开发人员能够自由地在不同层次结构内独立的类中复用 method。
Trait和Class组合的语义定义了一种减少复杂性的方式，避免传统多继承和Mixin类相关典型问题。
Trait和Class相似，但仅仅旨在用细粒度和一致的方式来组合功能。无法通过trait自身来实例化。它为传统继承增加了水平特性的组合；也就是说，应用的几个Class之间不需要继承。

例 1
```php
<?php
trait ezcReflectionReturnInfo {
    function getReturnType() { /*1*/ }
    function getReturnDescription() { /*2*/ }
}

class ezcReflectionMethod extends ReflectionMethod {
    use ezcReflectionReturnInfo;
    /* ... */
}

class ezcReflectionFunction extends ReflectionFunction {
    use ezcReflectionReturnInfo;
    /* ... */
}
?>
```
使用 use 关键字后，就可以在当前类中使用这个外置的方法了

### 2.5.1 优先级

从基类继承的成员会被 trait 插入的成员所覆盖。优先顺序是来自当前类的成员覆盖了 trait 的方法，而 trait 则覆盖了被继承的方法。`

例 2
```php
<?php
trait HelloWorld {
    public function sayHello() {
        echo 'Hello World!';
    }
}

class TheWorldIsNotEnough {
    use HelloWorld;
    public function sayHello() {
        echo 'Hello Universe!';
    }
}

$o = new TheWorldIsNotEnough();
$o->sayHello();
?>
```

### 2.5.2 多个 trait

通过逗号分隔，在 use 声明列出多个 trait，可以都插入到一个类中。

```php
<?php
trait Hello {
    public function sayHello() {
        echo 'Hello ';
    }
}

trait World {
    public function sayWorld() {
        echo 'World';
    }
}

class MyHelloWorld {
    use Hello, World;
    public function sayExclamationMark() {
        echo '!';
    }
}

$o = new MyHelloWorld();
$o->sayHello();
$o->sayWorld();
$o->sayExclamationMark();
?>
```

###　2.5.3 冲突的解决

如果两个 trait 都插入了一个同名的方法，如果没有明确解决冲突将会产生一个致命错误。

为了解决多个 trait 在同一个类中的命名冲突，需要使用 insteadof 操作符来明确指定使用冲突方法中的哪一个。

以上方式仅允许排除掉其它方法，as 操作符可以 为某个方法引入别名。 注意，as 操作符不会对方法进行重命名，也不会影响其方法。

```php
<?php
trait A {
    public function smallTalk() {
        echo 'a';
    }
    public function bigTalk() {
        echo 'A';
    }
}

trait B {
    public function smallTalk() {
        echo 'b';
    }
    public function bigTalk() {
        echo 'B';
    }
}

class Talker {
    use A, B {
        B::smallTalk insteadof A;
        A::bigTalk insteadof B;
    }
}

class Aliased_Talker {
    use A, B {
        B::smallTalk insteadof A;
        A::bigTalk insteadof B;
        B::bigTalk as talk;
    }
}
?>
```

###　2.5.4 修改方法的访问控制

使用 as 语法还可以用来调整方法的访问控制。

```php
<?php
trait HelloWorld {
    public function sayHello() {
        echo 'Hello World!';
    }
}

// 修改 sayHello 的访问控制
class MyClass1 {
    use HelloWorld { sayHello as protected; }
}

// 给方法一个改变了访问控制的别名
// 原版 sayHello 的访问控制则没有发生变化
class MyClass2 {
    use HelloWorld { sayHello as private myPrivateHello; }
}
?>
```

### 2.5.5 从 trait 来组成 trait

正如 class 能够使用 trait 一样，其它 trait 也能够使用 trait。在 trait 定义时通过使用一个或多个 trait，能够组合其它 trait 中的部分或全部成员。

```php
<?php
trait Hello {
    public function sayHello() {
        echo 'Hello ';
    }
}

trait World {
    public function sayWorld() {
        echo 'World!';
    }
}

trait HelloWorld {
    use Hello, World;
}

class MyHelloWorld {
    use HelloWorld;
}

$o = new MyHelloWorld();
$o->sayHello();
$o->sayWorld();
?>
```

### 2.5.6 Trait 的抽象成员

为了对使用的类施加强制要求，trait 支持抽象方法的使用。

```php
<?php
trait Hello {
    public function sayHelloWorld() {
        echo 'Hello'.$this->getWorld();
    }
    abstract public function getWorld();
}

class MyHelloWorld {
    private $world;
    use Hello;
	//必须要实现 Trait 中的抽象方法
    public function getWorld() {
        return $this->world;
    }
    public function setWorld($val) {
        $this->world = $val;
    }
}
?>
```
### 2.5.7 Trait 的静态成员

Traits 可以被静态成员静态方法定义。

**例 1 静态变量**
```php
<?php
trait Counter {
    public function inc() {
        static $c = 0;
        $c = $c + 1;
        echo "$c\n";
    }
}

class C1 {
    use Counter;
}

class C2 {
    use Counter;
}

$o = new C1(); $o->inc(); // echo 1
$p = new C2(); $p->inc(); // echo 1
?>
```

**例 2 静态方法**
```php
<?php
trait StaticExample {
    public static function doSomething() {
        return 'Doing something';
    }
}

class Example {
    use StaticExample;
}

Example::doSomething();
?>
```

### 2.5.8 属性

Trait 同样可以定义属性。

```php
<?php
trait PropertiesTrait {
    public $x = 1;
}

class PropertiesExample {
    use PropertiesTrait;
}

$example = new PropertiesExample;
$example->x;
?>
```

Trait 定义了一个属性后，类就不能定义同样名称的属性，否则会产生 fatal error。 有种情况例外：属性是兼容的（同样的访问可见度、初始默认值）。 在 PHP 7.0 之前，属性是兼容的，则会有 E_STRICT 的提醒。

例 1 解决冲突

```php
<?php
trait PropertiesTrait {
    public $same = true;
    public $different = false;
}

class PropertiesExample {
    use PropertiesTrait;
    public $same = true; // PHP 7.0.0 后没问题，之前版本是 E_STRICT 提醒
    public $different = true; // 致命错误
}
?>
```


## **3. 对象与设计**

## 3.1 面向对象设计和过程式编程

先来看一段用来读写 key/value 键值对文件的过程式编程：
```php
/**
 * 从文件中读取键值对参数
 * @param $sourceFile   文件名
 * @return array
 */
function readParams($sourceFile)
{
    $params = array();
    return $params;
}

/**
 * 把键值对数组写入文件
 * @param $params   键值对数组
 * @param $sourceFile   文件名
 */
function writeParams($params, $sourceFile)
{
    //写入文本参数到 $sourceFile
}

$file = './param.txt';
$array['key1'] = "val1";
$array['key2'] = "val2";
$array['key3'] = "val3";
writeParams($array, $file); //将数组写到文件
$outPut = readParams($file); //从文件读取键值对到数组
print_r($outPut); //输出键值对数组
```

上边这段过程式编程看似流程比较合理，如果被告知这个功能要支持xml格式、json格式等其他格式时，可能会使我们的代码更难维护。下面是扩展了xml格式的读写功能
```php
/**
 * 从文件中读取键值对参数
 * @param $sourceFile   文件名
 * @return array
 */
function readParams($sourceFile)
{
    $params = array();
    if (preg_match('/\.xml$/i', $sourceFile)) {
        //从xml中读取参数
    } else {
        //从文本文件中读取参数
    }
    return $params;
}

/**
 * 把键值对数组写入文件
 * @param $params   键值对数组
 * @param $sourceFile   文件名
 */
function writeParams($params, $sourceFile)
{
    if (preg_match('/\.xml$/i', $sourceFile)) {
        //把参数写入xml文件
    } else {
        //把参数写入txt文本文件
    }
}
```
如上所示，我们在两个函数中都要检测XML扩展名，这样重复性代码会产生问题。如果我们还要被要求支持其他格式的参数，就要始终保持 readParams 和 writeParams 函数的一致性。

下面尝试使用面向对象的方式来处理。首先，创建一个抽象的基类来定义类型接口：

```php
/**
 * 键值对管理的抽象类
 * Class ParamHandler
 * ${DS}
 */
abstract class ParamHandler
{
    protected $source;  //来源
    protected $params = array(); //键值对数组
    public function __construct( $source )
    {
        //初始化来源
        $this->source = $source;
    }
    /**
     * 追加键值对
     * @param $key
     * @param $val
     */
    public function addParam($key, $val)
    {
        $this->params[$key] = $val;
    }

    /**
     * 获取所有键值对
     * @return array
     */
    public function getAllParams()
    {
        return $this->params;
    }

    /**
     * 检测文件名，返回不同的实现类
     * @param $filename
     * @return TextParamHandler|XmlParamHandler
     */
    static function getInstance( $filename ) {
        //判断文件类型
        if (preg_match('/\.xml$/i', $filename)) {
            return new XmlParamHandler( $filename );
        } else {
            return new TextParamHandler( $filename );
        }
    }

    /**
     * 抽象的写入类，由实现这个类的子类来实现
     * @return mixed
     */
    abstract function write();
    /**
     * 抽象的读取类，由实现这个类的子类来实现
     * @return mixed
     */
    abstract function read();
}

class TextParamHandler extends ParamHandler
{
    //实现父类的写入方法
    public function write()
    {
        // TODO: Implement write() method.
        //实现txt格式的写入
    }
    //实现父类的读取方法
    public function read()
    {
        // TODO: Implement read() method.
        //实现txt格式的读取
    }
}
class XmlParamHandler extends ParamHandler
{
    //实现父类的写入方法
    public function write()
    {
        // TODO: Implement write() method.
        //实现xml格式的写入
    }
    //实现父类的读取方法
    public function read()
    {
        // TODO: Implement read() method.
        //实现xml格式的读取
    }
}

//xml格式的写入
$test = ParamHandler::getInstance("./params.xml");
$test->addParam('key1', 'val1');
$test->addParam('key2', 'val2');
$test->addParam('key3', 'val3');
$test->write(); //写入xml格式
//txt格式的读取
$test = ParamHandler::getInstance("./params.txt");
$test->read(); //从文本格式中读取
```

从上面面向对象的实现方式，我们可以明显的发现我们多增加了两种子类用类对应不同的文件操作，相信你很快会明白，在以后你想要增加其他文件格式的读取的时候只需要继承父类，实现读写方法即可增加扩展。这就是我理解的面向对象的好处。

`注意：我们在扩展其他格式的时候，也会遇到需要手动去修改父类的 getInstance() 方法增加对应的子类实现，我们会在后边的章节中讨论对象创建的问题，看看能否更好的解决这个问题`

### 3.1.1 职责

在过程式编程时，控制代码的职责是判断文件格式，它判断了两次而不是一次。条件语句被绑定到函数中，但这仅是将判断的流程隐藏起来。对readParams 的调用和对 readParams 调用必须发生在不同的地方，因此我们不得不在每个函数中重复检测文件扩展名。

在面向对象编程时，我们在静态方法 getInstance() 中进行文件格式的选择，并且仅在 getInstance() 中检测文件扩展名一次，就可以决定使用哪一个合适的子类。抽象类并不负责实现读写功能。它不需要知道自己属于哪个子类就可以使用给定的对象。它只需要知道自己在使用 ParamHandler 对象，并且 ParamHandler 对象支持 write() 和 read() 方法。过程代码忙于处理细节，而面向对象代码只需要一个接口即可工作，并不用考虑实现的细节。由于实现由对象负责，而不是抽象类负责，所以我们能够很方便地增加对新格式的支持。

### 3.1.2 内聚

`内聚` 是一个模块内部各成分之间相关联程度的度量。理想情况下，各组件职责清晰、分工明确。如果代码间的关联范围太广，维护就会很困难。因为你需要在修改某部分代码的同时修改相关的代码。

### 3.1.3 耦合

`耦合` 当系统各部分代码紧密绑定在一起时，就会产生紧密耦合，这时在一个组件中的变化会迫使其他组件随之改变。紧密耦合并不是过程式代码特有的，但是过程式代码比较容易产生耦合问题。

例如：刚才过程编程中出现的 writeParams 和 readParams 函数中，使用了相同的文件扩展名判断来决定如何处理数据，因此，我们要改写一个函数，就不得不同时改写另一个函数。而面向对象的示例中则将每个子类彼此分开，也将其与父类代码分开。如果需要增加新的参数格式，只需要简单地创建响应的子类，并在父类的静态方法 getInstance() 中增加一行文件检测代码即可

`面向对象编程六字口诀：高内聚、低耦合`

### 3.1.4 正交

`正交` 指将职责相关的组件紧紧组合在一起，而与外部系统环境隔开，保持独立。

正交主张重用组件，期望不需要任何特殊配置就能把一个组件插入到新系统中。这样的组件有明确的与环境无关的输入和输出。正交代码使修改变的简单，因为修改一个实现只会影响到被改动的组件本身

## 3.2 选择类

选择类就是把需求转换成实体类，可以模拟真实世界。最主要的就是定义类的界限往往会比我们想象的更加困难。面向对象系统通常反映真实的事物，如Person、Shop等。

在定义一个类的时候最好让一个类只有一个主要的职责，并且任务要尽可能独立。

## 3.3 多态

`多态` 是指在一个公用接口后面维护多个实现。

在 readParams() 函数中我们不难发现，每一个if条件都暗示着我们最终生成的一个子类：XmlParmaHandler 或 TextParamHandler，他们继承了抽象基类 ParamHandler 的 write 和 read 方法。

要特别注意的是多态并没有消除条件语句。像 ParamHandler::getInstance() 这样的方法经常通过 switch 或 if 决定要返回的对象，但多态可以把条件代码集中到一个地方。

多态的体现形式就是 由子类继承父类，实现多种不同的处理。

## 3.4 封装

封装就是对代码隐藏数据和功能。要实现封装最简单的办法就是将属性定义为 private 或 protected。通过对使用者隐藏属性，我们创建了一个接口并防止在偶然情况下污染对象中的数据。

`多态` 是另外一种封装。通过把不同的实现放在公共接口之后，我们对调用者隐藏了功能的实现。也就是说，任何在接口背后发生的改变对外界的系统来说都是可忽略的。我们可以增加新类或改变类中的代码，而不会产生错误。

从某种程度上说，封装是面向对象编程的关键。我们的目标是使系统中的每一部分都尽可能独立

## 3.5 忘记细节

在设计阶段，让大脑空白一段时间，只考虑系统的关键参与者：项目需要的对象类型和这些对象的接口。不被“数据库代码需要用户名和密码”这样的经验所左右，而要让代码中的结构和关系类引导你，你会发现定义一个良好的接口之后加入实现代码很容易。接着你可以灵活地选择、改进或扩展一个可能需要的实现，而不会影响到外界的系统。

## 3.6 4个方向标

没有人能在设计的时候绝对正确。修改代码时，很容易失去控制。在这里加个方法，在那里加一个新类，慢慢的导致系统变的混乱。下边给出了 4 个说明代码需要检查的方式

### 3.6.1 代码重复

请认真查看系统中代码重复的地方，很可能他们本应该放在一起。重复通常意味着紧密耦合。如果你改变了一个地方的代码，是否需要同时修改另一个地方的代码？如果需要修改，那么这两处代码很可能本该放在同一个地方。

### 3.6.2 类知道的太多

从一个方法传递参数到另一个方法时可能会产生问题。为什么不使用全局变量减少麻烦呢？

全局变量有它们自己的作用，但是使用时一定要慎重考虑。使用全局变量或者允许一个类知道它之外的作用域的内容，你就可以把这个类绑定到外部环境中，让它很难重用，并无法保持独立。我们要尽量把一个类限制在自己的环境中。

### 3.6.3 万能的类

你的类是否尝试一次完成很多工作？如果是的话，就要检查类的职责列表了。你会发现一些功能可以提取出来，成为一个基类。

如果类的职责过多，那么在创建子类的时候会产生问题。你要用子类扩展什么样的功能？如果想要让子类负责更多的事情，该怎么办？这样可能会产生太多的子类或者过度依赖于条件语句。

### 3.6.4 条件语句

在项目中我们常有非常好的理由来使用 if 和 switch语句，但有时这样的结构会让我们不得不使用多态。

如果你发现在一个类中频繁地进行特定条件的判断，特使是当你发现这条件判断在多个方法中出现时，就说名这个类需要拆分成两个或者更多。检查一下条件代码的结构，看看是否应该将某些功能独立出来放在独立的类中。拆分出来的几个类应该有一个共享的抽象基类，这时你需要知道如何传递正确的子类给调用者。

## 3.7 UML 统一建模语言

强大的图形化语法来描述面向对象系统。

上学的时候都接触过 统一建模语言 但是估计都没有深入过。这次好好看看

### 3.7.1 类图

类图可以清晰地描述结构和模式。通过类图我们可以清楚地看出结构和模式的意图，而如果只阅读代码片段和项目符号列表，则很难做到这一点。

不再详细说明，可以参考 深入PHP++面向对象 书里边给出的设计图

### 3.7.2 时序图

不再详细说明，可以参考 深入PHP++面向对象 书里边给出的设计图


## **4. 设计模式**

## 4.1 设计模式概览

一个设计模式的核心由4部分组成：命名、问题、解决方案、效果

### 4.2.1 命名

命名非常重要。命名丰富了程序员的语言，少许简短的文字便可表示相当复杂的问题和解决方案。命名必须兼顾简洁性和描述性。找到一个好名字，成了我们在开发模式目录中最困难的部分之一。

### 4.2.2 问题

无论解决方案如何优雅，问题及问题发生的环境都是一个模式的基础。
找出问题比使用模式目录中的解决方案更难。这正是某些模式的解决方案被误用或过度使用的原因之一。
模式会小心谨慎地描述问题的空间。问题会被置于环境中简明扼要地描述，通常还会有典型的范例及一个或多个图标。问题会被分解为不同细节和各种表现。同时任何可以帮助发现问题的警告标志都会在模式中被描述

### 4.2.3 解决方案

解决方案最初是和问题放在一起的，并常用UML类图和交互图更详细地进行描述。而模式通常也包含一个代码范例。
尽管代码也许是现成的，但解决方案从来不是简单剪切及粘贴。

### 4.2.4 效果

在设计代码的时候，你所做的每一个决定都会带来不同的结果。当然我们总是希望能得到令人满意的针对问题的解决方案。解决方案一旦被部署，理想情况下它也许会非常适合与其他模式一同工作，但也要留心是否会带来风险。

## 4.3 设计模式格式

## **5. 模式原则**
## 5.1 组合与继承

```php
abstract class Lesson
{
    protected $duration;    //课程时间
    const FIXED = 1;      //固定收费
    const TIMED = 2;    //按时收费
    private $costType; // 收费方式
    function __construct($duration, $costType = 1)
    {
        $this->duration = $duration;
        $this->costType = $costType;
    }
    //计算费用
    function cost() {
        switch ( $this->costType ) {
            case self::TIMED :
                return (5 * $this->duration);
                break;
            case self::FIXED :
                return 30;
                break;
            default:
                $this->costType = self::FIXED;
                return 30;
        }
    }
    function chargeType() {
        switch ( $this->costType ) {
            case self::TIMED :
                return "按小时收费";
                break;
            case self::FIXED :
                return "固定收费";
            default:
                $this->costType = self::FIXED;
                return "固定收费";
        }
    }
}
class Lecture extends Lesson
{

}
class Seminar extends Lesson
{

}

$lecture = new Lecture(5, Lesson::FIXED);
print "{$lecture->cost()}({$lecture->chargeType()})<br>";
$seminar = new Seminar(3, Lesson::TIMED);
print "{$seminar->cost()}({$seminar->chargeType()})<br>";
```

上边这段代码为课程提供了不同的收费机制，利用 继承 Lesson 抽象类这种继承模式，我们可以在课程的实现之间切换。而客户端代码只知道它是在处理一个 Lesson 课程对象，因此收费细节就会变得透明。

可是如果引入一组新的课程，又会怎么样呢？比如我们要处理演讲和研讨会。因为演讲和研讨会会以不同的方式注册登记和教授课程，所以他们会要求独立的类。因此在设计上现在会有两个分支。我们需要处理不同的定价策略并区分演讲和研讨会。

上边的这段代码在增加需求的时候就会不灵活，而且要在抽象类中使用一堆的判断语句，这样就违背的类的多态设计原则，所以我们考虑使用下边的组合方式来区分 课程 和 计费功能，这样就可以灵活组合你想要的课程以及收费模式

## 5.2 使用组合

`应用策略模式` 的典型案例:

```php
/**
 * 费用计算组件
 * Class CostStrategy
 * ${DS}
 */
abstract class CostStrategy
{
    /**
     * 计算费用
     * @param Lesson $lesson    科目类型
     * @return mixed
     */
    abstract function cost(Lesson $lesson);

    /**
     * 判断类型
     * @return mixed
     */
    abstract function chargeType();
}

/**
 * 费用计算组件的多态实现
 * Class TimedCostStrategy  计时费用
 * ${DS}
 */
class TimedCostStrategy extends CostStrategy
{
    function cost(Lesson $lesson)
    {
        // TODO: Implement cost() method.
        return ($lesson->getDuration() * 5);
    }
    function chargeType()
    {
        // TODO: Implement chargeType() method.
        return "计时费用";
    }
}

/**
 * 费用计算组件的多态实现
 * Class TimedCostStrategy  固定费用
 * ${DS}
 */
class FixedCostStrategy extends CostStrategy
{
    function cost(Lesson $lesson)
    {
        // TODO: Implement cost() method.
        return 30;
    }
    //收费类型
    function chargeType()
    {
        // TODO: Implement chargeType() method.
        return "固定费用";
    }
}

/**
 * 课程类
 * Class Lesson
 * ${DS}
 */
abstract class Lesson
{
    private $duration;
    private $costStrategy;
    function __construct($duration, CostStrategy $strategy)
    {
        $this->duration = $duration;   //课程时间
        $this->costStrategy = $strategy; //课程计费方式
    }
    //计算费用
    function cost()
    {
        return $this->costStrategy->cost( $this );
    }
    //获取收费方式
    function chargeType()
    {
        return $this->costStrategy->chargeType();
    }
    //获取课程时长
    function getDuration()
    {
        return $this->duration;
    }
    //Lesson 的更多方法
}

/**
 * 演讲课
 * Class Lecture
 * ${DS}
 */
class Lecture extends Lesson {
    //Lecture 特定实现
}

/**
 * 培训课
 * Class Seminar
 * ${DS}
 */
class Seminar extends Lesson {
    //Seminar特定的实现
}

//培训课类需要两个参数，课程时长 和 按时计算费用收费的类的实例
$lessons[] = new Seminar( 4, new TimedCostStrategy());
//演讲课类需要两个参数，课程时长 和 按固定费用收费的类的实例
$lessons[] = new Lecture( 4, new FixedCostStrategy());

foreach ($lessons as $lesson) {
    print "收费标准：{$lesson->cost()}，收费方式：{$lesson->chargeType()}<br>";
}
```

从上边代码可以看出，组合使用对象比使用继承体系更灵活，而且不需要进行一大堆的选择判断，因为组合可以以多种方式动态的处理任务，不过这可能导致代码的可读性下降。因为组合需要更多的对象类型，而这些类型的关系并不像在继承关系中那般有固定的可预见性，所以要理解系统中类和对象的关系会有些困难。

## 5.3 解耦

我们在了解了创建独立组件的意义后发现，如果类之间有非常强的依赖性，那么这样的系统就很难维护，因为系统里的一个改动会引起一连串的相关改动。

### 5.3.1 问题

重用性是面向对象设计的主要目标之一，而紧耦合便是它的敌人。当我们看到系统中一个组件的改变迫使系统其他许多地方也发生改变的时候，就可诊断为紧耦合了。为了能安全地做变动，我们总是期望创建能够独立存在的组件。在修改组件式，其独立程度会决定你的修改对系统中其他组件的影响程度，系统的其他组件甚至有可能会因此失败。

在 [5.1 节](#51-组合与继承) 中，我们看到过紧耦合的例子。因为费用计算逻辑在I Lecture 和 Seminar 类型中都存在，所以对 TimedPriceLecture 的一个改变将会迫使在 TimedPriceSeminar 中同样逻辑的响应变化。如果仅改动一个类而不改动其他类的代码，系统将无法正常工作，而且没有来自PHP的任何错误。而我们第一个解决方案（使用条件语句）在 cost() 和 chargeType() 方法之间生成了一个类似的依赖关系。

通过应用策略模式，我们将费用算法提取为 CostStragey 类型，将算法放置在共同接口后，并且每个算法只需要实现一次。

不过当系统中许多类都显示嵌入到一个平台或环境中时，其他类型的耦合仍时有发生。比如建立了一个基于Mysql数据库的系统。你可能会用一些诸如 mysql_connect() 和 mysql_query() 的函数来与数据库服务器交互。

如果现在你被要求在不支持Mysql的服务器上部署系统，比如要把这个项目都转化成使用 SQLite，那么你可能被迫要改变整个代码，并且面临维护应用程序的两个并行版本的状况。

这里的问题不再与系统对外部平台的依赖。这样的依赖是无法避免的。我们确实需要使用与数据库交互的代码。但这样的代码散布在整个项目中时，问题就来了。与数据库交互不是系统中大部分类的首要责任，因此最好的策略就是提取这样的代码并将其组合在公共接口后。这可以使类之间互相独立。同时，通过在一个地方集中你的`入口`代码，就能更轻松地切换到一个新的平台而不会影响到系统中更大的部分。这个把具体实现隐藏在一个干净的接口后面的过程，正是大家所知道的 `封装`。

PEAR中的一个 MDB2数据库连接包里的 MDB2类 提供了一个静态方法 connect()， 它接受一个 DSN 字符串参数。根据这个字符串的构成，它返回 MDB2_Driver_Common 类的一个特定实现。因此对于字符串 `mysql://`，connect() 方法返回一个 MDB2_Driver_mysql对象，而对于一个以 `sqlite://` 开头的字符串，它将返回一个 MDB2_Driver_sqlite 对象。

下面是 MDB2 的类结构：
```php
class MDB2
{
    /**
     * 链接数据库
     * @param $dsn  链接dsn字符串
     */
    public function connect( $dsn )
    {
        //创建 MDB2_Driver_Common 类
    }
}
class MDB2_Driver_Common
{
    //根据 dsn 字符串判断选择实例化数据库类
}
class MDB2_Driver_mysql extends MDB2_Driver_Common
{

}
class MDB2_Driver_sqlite extends MDB2_Driver_Common
{

}
```

### 5.3.2 降低耦合

为了灵活处理数据库代码，我们应该将应用逻辑从数据库平台的特殊性中解耦出来。

例如，课程系统中应该包含注册组件，从而向系统中添加新课程。添加了新课程后，应该通知管理员，这是注册程序的一部分。对于应该通过邮件发送通知还是通过文本消息发送通知，系统用户的意见不一致。实际上，他们太挑剔了，以至于你怀疑将来他们会想使用一种新的信息传达模式。此外，他们希望发生任何事情都会收到通知。所以，修改了通知模式的一处意味着要对多处做同样的修改。

如果已经硬编码了对 Mailer 类或 Texter 类的调用，那么系统就与特殊的通知模式紧密相关了。就像利用专门的数据库API时，系统就与某数据库平台紧密相关一样。

下面的这些代码对使用通知程序的系统隐藏了通知程序的实现细节：
```php
class RegistrationMgr
{
    public function register(Lesson $lesson)
    {
        //处理该课程
        //发送通知
        $notifier = Notifier::getNotifier(); //获取消息通知方式
        $notifier->inform("创建了新课程");
    }
}
abstract class Notifier
{
    /**
     * 该方法获取具体的 Notifier 对象
     * 在实际项目中，Notifier对象的选择是由一种灵活的机制（比如配置文件）所决定的。而不是这里的写的随机数进行判断选择哪种发送方式
     * @return MailNotifier|TextNotifier
     */
    static function getNotifier()
    {
        //根据配置或其他逻辑获得具体的类
        if (rand(1, 2) == 1) {
            return new MailNotifier();
        } else {
            return new TextNotifier();
        }
    }

    /**
     * 发送消息
     * @param $message
     * @return mixed
     */
    abstract function inform( $message );
}
class MailNotifier extends Notifier
{
    public function inform( $message )
    {
        // TODO: Implement inform() method.
        print "发送了邮件消息通知<br>";
    }
}

class TextNotifier extends Notifier
{
    public function inform($message)
    {
        // TODO: Implement inform() method.
        print "发送了文本消息通知<br>";
    }
}

//Seminar 类可参考上边 5.2 使用组合 里边实现的 Lesson 类
$lessons1 = new Seminar( 4, new TimedCostStrategy() );
$mgr = new RegistrationMgr();
$mgr->register($lessons1);   //注册课程
```

## 5.4 针对接口编程，而不是针对实现编程

把不同的实现隐藏在父类所定义的共同接口下。然后客户端代码需要一个父类的对象而不是一个子类的对象，从而使客户端代码可以不用关心它实际得到的是哪个具体实现。

我们在 Lesson::cost() 和 Lesson::chargeType() 中创建的并行条件语句，就是需要多态的常见标志。这样的条件语句使代码很难维护，因为条件表达式的改变必然要求与之对应的代码主体也随之改变，所以条件语句有时会被称作实现了一个 `模拟继承`。

而通过把计费算法放置在一个实现 CostStrategy 的独立的类中，我们可以移除重复代码，也可以使在未来加入新的计费策略变得更加容易。

从客户端代码的角度看，类方法参数为抽象或通用类型通常都是不错的主意。如果参数对对象类型要求过于严格，就会限制代码在运行时的灵活性。

当然，如何使用参数类型提示来调整参数对象的“通用性”是需要仔细权衡的。选择过于通用，则会降低方法的安全性。而如果需要某个子类型的特有功能，那么方法接受另一个子类类型则可能会有风险。

尽管如此，若参数的类型匹配限制过于严格，那么将无法得到多态带来的好处。下面是修改过的 Lesson 类里的一段代码：
```php
function __construct( $duration, FixedPriceStrategy $strategy) {
	$this->duration = $duration;
	$this->costStrategy = $strategy;
}
```
这个示例中的设计有两个问题。第一，Lesson 对象现在与一个特定的费用策略绑定，这使我们丧失了组合动态组件的能力。第二，对 FixedPriceStrategy 类的显式引用迫使我们必须维护这个特殊实现。

而通过要求一个公共的接口，你能将任何 CostStrategy 实现合并到一个 Lesson 对象:
```php
function __construct( $duration, CostStrategy $strategy) {
	$this->duration = $duration;
	$this->costStrategy = $strategy;
}
```
换句话说，我们把 Lesson 类从具体的费用计算中分离出来了。我们所做的就是提供接口并保证所提供的对象会实现接口。

当然，面向接口编程无法回答如何实例化它的子类的问题。你会选择实例化哪个子类来对应相应的条件呢？

## 5.5 变化的概念

一旦作出设计决定，解释它就很容易。但是你如何决定从哪里开始设计呢？

《设计模式》建议你 “把变化的概念封装起来”。在我们的示例中，“变化的概念”便是费用算法。计费不仅仅是示例中两个可能的策略之一，而且它也是明显需要扩充的：如特殊供应、海外学生费用、推介性折扣等各种各样的可能性。

我们发现为这个变化直接创建子类是不合适的，于是使用了条件语句。通过把变化的元素放入同一个类中，我们强调了封装的适用性。

《设计模式》建议积极搜索类中变化的元素，并评估它们是否适合使用新类型来封装。根据一定条件，变化的元素可被提取出来形成子类（TiemdCostStrategy 和 FixedCostStrategy）,而这些元素共同拥有一个抽象的父类（CostStrategy）。而这个新类型（CostStrategy）能被其他类使用。这么做有以下好处：

`专注于职责`、`通过组合提高灵活性`、`使用继承层级体系更紧凑和集中`、`减少重复`

那么如何发现变化的元素呢？误用继承便是一个标志。误用的表现可能包括一次实现不同分支的继承，也可能包括子类化某个算法，而该算法对于该对象类型的核心职责是偶然的。当然，适合封装“变化元素”的另一个标志便是出现了条件表达式。

## 5.6 父子关系

模式的一个问题便是不必要或不恰当地使用模式。这让模式在某些领域名声不佳。因为模式解决方案很棒，所以它会引诱你把模式应用在任何你认为合适的地方，无论他们是否真的的适合用来达到目标。

`极限编程`提供了几个可以使用的相关原则。第一个是“你还不需要它”。这通常被应用在应用程序的功能上，但是对于模式来说也有意义。

当使用PHP开发较大项目时，会把应用程序分离到各个层中，把应用程序逻辑从表现层和持久化层中分离开来。通常联合使用各种核心模式和企业模式。

然而当为一个小型商务网站建立一个用户反馈表单时，我可能只在单一页面脚本中使用过程式代码。此时，不需要大量的灵活性，不需要以后基于最初版本进行大量扩展，也不需要使用那些在更庞大的系统中解决问题的模式，而是应用极限编程的第二个原则：“用最简单的方式来完成任务”

当使用模式目录时，通过示例代码可能巩固你脑中解决方案的结构和流程。然而在应用模式之前，要特别注意目录中“问题”或者“何时使用”那部分，并且熟读模式的效用。

## **6.生成对象**

## 6.1 生成对象的问题和解决方法

对象创建有时会成为面向对象设计的一个薄弱环节。在前一章中，我们看到“针对接口编程，而不是针对实现编程”的原则。就此而言，鼓励在类中使用抽象的超类。这使代码更具有灵活性，可以让你在运行时使用不同的具体子类中实例化的对象。但这样做也有副作用，那就是对象实例化被推迟。

类 Employee 的构造方法以姓名字符串为参数，实例化了一个特定对象：

```php
abstract class Employee
{
    protected $name;
    function __construct($name)
    {
        $this->name = $name;
    }
    abstract function fire();
}
class Minion extends Employee
{
    function fire()
    {
        // TODO: Implement fire() method.
        print "{$this->name}:I'll clear my desk<br>";
    }
}
class NastyBoss
{
    private $employees = array();
    function addEmployee( $employeeName )
    {
        $this->employees[] = new Minion( $employeeName );
    }
    function projectFails() {
        if ( count($this->employees ) > 0 ) {
            //删除数组最后一个元素
            $emp = array_pop( $this->employees );
            $emp->fire();
        }
    }
}
$boss = new NastyBoss();
$boss->addEmployee("harry");
$boss->addEmployee("bob");
$boss->addEmployee("mary");
$boss->projectFails();

```

如你所看到的，我们定义了一个抽象基类 Employee（雇员）以及一个受压迫员工的具体实现 Minion。NastyBoss::addEmployee() 方法通过接受的名字字符串来实例化新的 Minion 对象。一旦 NastyBoss(苛刻的老板)对象遇到了麻烦（通过 NastyBoss::projectFails()方法），就会解雇一个 Minion对象。

由于在 NastyBoss 类中直接实例化 Minion 对象，代码的灵活性受到了限制。如果 NastyBoss 对象可以使用 Employee 类的任何实例，那么代码在运行时就能应付更多特殊的 Employee。

如果 NastyBoss 类不实例化 Minion 对象，那么 Minion 对象从何而来？许多人通常在方法声明中限制参数类型来巧妙避开这个问题，然后除了在测试时实例化对象，在其他时候尽量避免提及。

```php
abstract class Employee
{
    protected $name;
    function __construct($name)
    {
        $this->name = $name;
    }
    abstract function fire();
}
class Minion extends Employee
{
    function fire()
    {
        // TODO: Implement fire() method.
        print "{$this->name}:我立马走人<br>";
    }
}
//新的 Employee 实现类
class CluedUp extends Employee
{
    function fire()
    {
        // TODO: Implement fire() method.
        print "{$this->name}:我会打电话给我的律师<br>";
    }
}
class NastyBoss
{
    private $employees = array();
    function addEmployee(Employee $employee)
    {
        $this->employees[] = $employee;
    }
    function projectFails() {
        if ( count($this->employees ) > 0 ) {
            //删除数组最后一个元素
            $emp = array_pop( $this->employees );
            $emp->fire();
        }
    }
}
$boss = new NastyBoss();
$boss->addEmployee( new Minion("harry") );
$boss->addEmployee( new CluedUp("bob") );
$boss->addEmployee( new Minion("mary") );
$boss->projectFails();
$boss->projectFails();
$boss->projectFails();
```

虽然这个版本的 NastyBoss 类能与 Employee 类型一起工作，而且也能从多态中获益，但我们仍旧没有定义创建对象的策略。实例化对象是一件麻烦事，但是我们又不得不去做。

如果说这里存在一个原则的话，那便是 “把对象实例化的工作委托出来”。之前的示例已然隐含了这个原则，即要求将一个 Employee 对象传递给 NastyBoss::addEmployee() 方法。然而我们可以委托一个独立的类或方法类生产 Employee 对象，其效果是一样的。下面给 Employee 类添加了一个实现了对象创建策略的静态方法。

```php

abstract class Employee
{
    protected $name;
    private static $types = array('minion', 'clueup', 'wellconnected');

	//随机生成不同类型的对象
    static function recruit( $name )
    {
        $num = rand( 1, count( self::$types ) ) -1;
        $class = self::$types[$num];
        return new $class( $name );
    }
    function __construct($name)
    {
        $this->name = $name;
    }
    abstract function fire();
}

```

正如你所看到的，这里通过一个姓名字符串来随机实例化具体的 Employee 子类。现在我们可以将实例化的细节委托给 Employee 类的 recruit方法

```php
$boss = new NastyBoss();
$boss->addEmployee( Employee::recruit("harry") );
$boss->addEmployee( Employee::recruit("bob") );
$boss->addEmployee( Employee::recruit("mary") );
```

我们之前曾在 ShopProduct 类中放置了一个静态方法 getInstance() 。getInstance() 负责以数据库查询为基础生成正确的 ShopProduct 子类。因此 ShopProduct 类具有双重角色。它定义了 ShopProduct 类型，同时它也作为实体 ShopProduct 对象的工厂

`注解：本章中常常提到的 “工厂” 这个术语。工厂就是负责生成对象的类或方法。`

```php
//ShopProduct 类
public static function getInstance( $id, PDO $dbh)
    {
        $query = "select * from products where id = ?";
        $stmt = $dbh->prepare( $query );
        if ( ! $stmt->execute( array( $id)) ) {
            $error = $dbh->errorInfo();
            exit("failed：".$error[1]);
        }
        $row = $stmt->fetch();
        if (empty( $row ) ) {
            return null;
        }
        if ( $row['type'] == "book" ) {
            //实例化一个 BookProduct 对象
        } else if ( $row['type'] == "cd" ) {
            //实例化一个 CdProduct 对象
            $product = new CdProduct();
        } else {
            //实例化一个 ShopProduct 对象
        }
    }
```

getInstance() 方法使用一系列 if/else 语句来决定实例化哪个子类。像这样的条件语句在工厂代码中十分常见。尽管我们尝试从项目中消除大量的条件语句，但生成对象确实需要使用这些条件条件语句。一般来说这不是一个严重问题，因为我们将代码中并行的条件语句转移到 getInstance() 来，有getInstance() 来决定对象生成。




## 6.2 单例模式

全局变量是面向对象程序员遇到的引发 bug 的主要原因之一。这是因为全局变量将类捆绑于特定的环境，破坏了封装。如果新的应用程序无法保证一开始就定义了相同的全局变量，那么一个依赖于全局变量的类就无法从一个应用程序中提取出来并应用到新应用程序中。

尽管这并不是我们想要的，但全局变量不受保护的本质的确是个很大的问题。一旦开始依赖全局变量，那么某个类库中声明的全局变量和其他地方声明的全局变量迟早会发生冲突。我们已经看到过 PHP易受到类名冲突的影响，但全局变量的冲突更加糟糕，PHP并不会对全局变量冲突发出任何警告。你也许只是发现代码行为有些古怪。更糟糕的是，在开发环境中你也许根本发现不了任何问题。因此如果在类库中使用全局变量，用户可能在尝试将你的类库与其他类库一同部署时遇到冲突。

然而全局变量仍是一个诱惑。因为有时我们为了使所有类都能访问某个对象，会不惜忍受全局访问的缺陷。

我提到过，命名空间在一定程度上避免了命名冲突。你至少可以将变量的作用域定义在包中，这意味着第三方的库与你的系统产生冲突的可能性大大降低了。即便如此，命名空间内部还是存在命名冲突。

### 6.2.1 问题

经过良好设计的系统一般通过方法调用来传递对象实例。每个类都会与背景环境保持独立，并通过清晰的通信方式来与系统中其他部分进行协作。有时你需要使用一些作为对象间沟通渠道的类，此时就不得不引入依赖关系。

假设有一个用于保护应程序信息的 Preferences 类。我们可能会使用一个 Preferences 对象来保存诸如 DSN 字符串， URL根目录、文件路径等数据。这些信息在你每次部署程序时都可能会有所不同。该对象也可被用作一个公告板，它是可以被系统中其他无关对象设置和获取的消息的中心。

但在对象中传递 Preferences 对象并不总是个好主意。你可以让原来并不使用 Preferences 对象的类强制性地接受 Preferences 对象，以便这些类能传递 Preferences 对象给其他对象。但这样做产生了另一种形式的耦合。

我们还需要保证系统中的所有对象都是用同一个 Preferences 对象。我们不希望一些对象在一个 Preferences 对象上设直接，而其他对象从另外一个完全不同的 Preferences 对象上读取数据。

让我们提炼出这个问题的几个关键点：
Preferences 对象应该可以被系统中的任何对象使用。
Preferences 对象不应该被存储在会被覆写的全局变量中。
系统中不应超过一个 Preferences 对象。也就是说，Y对象可设置 Preferences 对象的一个属性，而 Z 对象不需要通过其他对象就可以直接获取该属性的值

### 6.2.2 实现

为了解决这个问题，我们可以从强制控制对象的实例化开始。下面创建了一个无法从其自身外部来创建实例的类。听起来似乎有些难，其实只要简单的定义一个私有的构造方法即可：
```php
class Preferences
{
    private $props = array();
    private function __construct() {}
    public function setProperty($key, $val)
    {
        $this->props[$key] = $val;
    }
    public function getProperty( $key )
    {
        return $this->props[$key];
    }
}
```
当然，目前 Preferences类是完全不能用的。我们设置了一个不合常理的访问机制。由于构造方法被声明为 private，客户端代码无法实例化对象。因此 setProperty() 和 getProperty() 方法目前也是多余的。
不过我们可以使用静态方法和静态属性来间接实例化对象
```php
class Preferences
{
    private $props = array();
    private static $instance;

    private function __construct() {}

    public function getInstance() {
        if ( empty( self::$instance ) ) {
            self::$instance = new Preferences();
        }
        return self::$instance;
    }

    public function setProperty($key, $val)
    {
        $this->props[$key] = $val;
    }
    public function getProperty( $key )
    {
        return $this->props[$key];
    }
}
```

$instance 属性设置为 private 及 static，因此不能在类外部被访问。而 getInstance() 方法在类内部，因此可以访问 $instance 属性。因为 getInstance()方法是 public 且 static 的，所以在脚本的任何地方都可被调用。

```php
$pref = Preferences::getInstance();
$pref->setProperty("name", "matt");
unset($pref); //移除引用
$pref2 = Preferences::getInstance();
print $pref2->getProperty( "name" ); //改属性值并没有丢失
```
静态方法不能访问普通对象属性，因为根据静态的定义，它只能被类而不是对象调用。但静态方法可以访问一个静态属性。所以当 getInstance() 被调用时，我们会检查 Preferences::$instance 属性。如果为空，那么创建一个 Preferences 对象实例并把它保存在 $instance 属性中，然后我们把实例返回给调用代码。因为静态方法 getInstance() 是 Preferences类的一部分，所以尽管构造方法是私有的，但是实例化 Preferences 对象完全没有问题。

所以，`单例模式`的核心就是，为了保证一个类只有一个实例，所以外部不能对他进行实例化，只能私有化构造方法然后通过自定义内部方法生成一个实例，并保存为类的一个静态属性，这样就保证了一个类在全局中只有一个实例。

### 6.2.3 结果

使用单例对象与使用全局变量相比，又如何呢？首先坏的一面。单例和全局变量可能被误用。因为单例在系统任何地方都可以被访问，所以它们可能会导致很难调试的依赖关系。如果改变一个单例，那么所有使用该单例的类可能都会受到影响。在这里，依赖本身并不是问题。毕竟，我们在每次声明一个有特定类型参数的方法时，也就创建了依赖关系。问题是，单例对象的全局化的性质会使程序员绕过类接口定义的通信路线。当单例被使用时，依赖便会隐藏在方法内部，而并不会出现在方法声明中。这使得系统中的依赖关系更加难以追踪，因此需要谨慎小心地部署单例类。
适度地使用单例模式可以改进系统的设计。在系统中传递哪些不必要的对象令人厌烦，而单例可以让你从中解放出来。
在面向对象的开发环境中，单例模式是一种对于全局变量的改进。你无法用错误类型的数据覆写一个单例。这种保护在不支持命名空间的PHP版本里尤其重要。因为PHP中命名冲突会在编译时被捕获，并使脚本停止运行。

## 6.3 工厂方法模式

面向对象设计强调“抽象类高于实现”。也就是说，我们要尽量一般化而不是特殊化。工厂方法模式解决了`当代码关注于抽象类型时如何创建对象实例的问题`。答案便是用特定的类来处理实例化。

### 6.3.1 问题

假设有一个关于个人事务管理的项目，其功能之一是管理 Appointment（预约）对象。我们的业务团队和另一个公司建立了关系，目前需要一个叫做 BloggsCal 的格式来和他们交流预约相关的数据。但是业务团队提醒我们将来可能要面对更多的数据格式。

在接口级别上，我们可以立即定义两个类。其一是需要一个数据编码器来把 Appointment 对象转换成一个专有的格式，将这个编码器命名为 ApptEncoder 类；另外需要一个管理员类来获得编码器，并使用编码器与第三方进行通信，我们将这个管理类名为 CommsManager 类。使用模式术语来描述的话，CommsManager 便是创建者（creator），而 ApptEncoder是产品（product）.

但是如何得到一个具体的 ApptEncoder 对象？
我们可以要求传递 ApptEncoder 给 CommsManager，但这只是延缓了问题，而我们希望问题可以彻底解决。因此，先在CommsManger 类中直接实例化 BloggsApptEncoder 对象。

```php
/**
 * Class ApptEncoder    编码器产品
 * ${DS}
 */
abstract class ApptEncoder
{
    //转换编码
    abstract function encode();
}

/**
 * Class BloggsApptEncoder 基于ApptEncoder编码器的bloggs编码实现
 * ${DS}
 */
class BloggsApptEncoder extends ApptEncoder
{
    function encode()
    {
        // TODO: Implement encode() method.
        return "Appointment 的数据使用BloggsCal文件格式进行编码";
    }
}
class MegaApptEncoder extends ApptEncoder
{
    function encode()
    {
        // TODO: Implement encode() method.
        return "Appointment 的数据使用MegaCal文件格式进行编码";
    }
}

/**
 * Class CommsManger    创建者
 * ${DS}
 */
class CommsManger
{
    //获取 编码器的一种实现
    function getApptEncoder() {
        return new BloggsApptEncoder();
    }
}
```

CommsManager类负责生成 BloggApptEncoder对象。当团队合作关系发生改变，而我们被要求转换系统来使用一个新的格式 MegaCal时，可以只添加一个条件语句到 CommsManger::getApptEncoder() 方法。毕竟，这是我们以前用过的策略。下面来建立一个新的可以同时处理 BloggsCal 和 MegaCal 格式的 CommsManger 的实现。

```php
/**
 * Class CommsManger    创建者
 * ${DS}
 */
class CommsManger
{
    const BLOGGS = 1;
    const MEGA = 2;
    private $mode = 1;
    function __construct($mode)
    {
        $this->mode = $mode;
    }

    //获取 编码器的一种实现
    function getApptEncoder() {
        switch ( $this->mode ) {
            case (self::MEGA):
                return new MegaApptEncoder();
            default:
                return new BloggsApptEncoder();
        }
    }
}
$comms = new CommsManger( CommsManger::MEGA );
$apptEncoder = $comms->getApptEncoder();
print $apptEncoder->encode();
```
在类中我们使用常量标志定义了脚本可能运行的两个模式：MEGA 和 BLOGGS。在 getApptEncoder() 方法中使用 switch 语句来检查 $mode 属性，并实例化适当的 ApptEncoder

这种方式仍有一点小小的缺陷。通常情况下，创建对象的确需要指定条件，但有时候条件语句会被当做坏的“代码味道”的象征。如果重复的条件语句蔓延在代码中，我们不应该感到乐观。CommsManager 类已经够提供交流日历数据的功能。但是，假设我们所使用的协议要求提供页眉和页脚数据来描述每次预约，情况又将如何呢？下面扩展之前的例子来支持 getHeaderText() 方法
```php
/**
 * Class CommsManger    创建者
 * ${DS}
 */
class CommsManger
{
    const BLOGGS = 1;
    const MEGA = 2;
    private $mode = 1;
    function __construct($mode)
    {
        $this->mode = $mode;
    }

    //获取 编码器的一种实现
    function getApptEncoder() {
        switch ( $this->mode ) {
            case (self::MEGA):
                return new MegaApptEncoder();
            default:
                return new BloggsApptEncoder();
        }
    }

    function getHeaderText() {
		//每实现一个方法就要重复进行判断
        switch ( $this->mode ) {
            case (self::MEGA):
                return "Mega header";
            default:
                return "Bloggs header";
        }
    }
}
```
正如你看到的，支持页眉输出的需求迫使我们重复条件判断语句。如果我们加入新协议，这种做法就变的不适用了。如果还需要加入 getFooterText()方法，工作量就会更大。
总结一下我们发现的问题：
- 在代码运行时我们才知道要生存的对象类型（BloggsApptEncoder 或者 MegaApptEncoder）
- 我们需要能够轻松地加入一些新的产品类型（如一种新业务处理方式 SyncML）
- 每一个产品类型都可定制特定的功能（如 getHeaderText() 和 getFooterText()）
另外，请注意我们正在使用条件语句，并且之前已经介绍过这些条件语句可以被多态代替，而工厂方法模式恰好能让我们使用继承和多态来封装具体产品的创建。换句话说，我们要为每种协议创建 CommsManager 的一个子类，而每一个子类都要实现 getApptEncoder() 方法。

### 6.3.2 实现

工厂方法模式把创建者类与要生产的产品类分离开来。创建者是一个工厂类，其中定义了用于生成产品对象的类方法。如果没有提供默认实现，那么就由创建者类的子类来执行实例化。一般情况下，就是创建者类的每个子类实例化一个相应的产品子类。

把 CommsManager 重新定义为抽象类。这样我们可以得到一个灵活的父类，并把所有特定协议相关的代码放到具体的子类中。下面是简化过的代码：
```php
/**
 * Class CommsManger    创建者
 * ${DS}
 */
abstract class CommsManger
{
    //获取 编码器的一种实现
    abstract function getApptEncoder();
    abstract function getHeaderText();
    abstract function getFooterText();
}
class BloggsCommsManager extends CommsManger
{
    function getHeaderText()
    {
        // TODO: Implement getHeaderText() method.
        return "BloggsCal header";
    }
    function getApptEncoder()
    {
        // TODO: Implement getApptEncoder() method.
        return new BloggsApptEncoder();
    }
    function getFooterText()
    {
        // TODO: Implement getFooterText() method.
        return "BloggsCal footer";
    }
}
```
BloggsCommsManger::getApptEncoder() 方法返回一个BloggsApptEncoder对象。调用 getApptEncoder() 的客户端代码可以预期得到一个 ApptEncoder 类型的对象，并且不需要知道被给予的是什么具体产品。在一些编程语言中，方法的返回类型是强制规定的，因此调用诸如 getApptEncoder() 之类方法的客户端代码可以完全确定它将接收到的是 ApptEncoder 对象。而在PHP中，这只能靠人为约定。因此，在代码中注明返回类型或者通过命名约定类传达返回类型的信息相当重要。
现在当我们被要求实现 MegaCal 时，只需要给CommsManager 抽象类写一个新实现。
```php
class MegaCommsManager extends CommsManger
{
    function getHeaderText()
    {
        // TODO: Implement getHeaderText() method.
        return "Mega header";
    }
    function getApptEncoder()
    {
        // TODO: Implement getApptEncoder() method.
        return new BloggsApptEncoder();
    }
    function getFooterText()
    {
        // TODO: Implement getFooterText() method.
        return "Mega footer";
    }
}
```

### 6.3.3 结果

请注意我们的创建者类与产品的层次结构是非常相似的。这是使用工厂方法模式的常见结果，这形成了一种特殊的代码重复，因而被一些人所不喜欢。另一个问题是该模式可能会导致不必要的子类化。如果你为创建者类创建子类的原因只是为了实现工厂方法模式，那么最好再考虑一下（这就是为什么在例子中引进页眉和页脚的方法）

在示例中我们只关注“预约”功能。如果想扩展一下功能，使其能够处理“待办事宜”及联系人，那么将会遇到新问题。我们需要一个可以同时处理一组相关实现的架构。正如我们在下一节中将看到的，工厂方法模式经常和抽象工厂模式一起使用。

## 6.4 抽象工厂模式

在大型应用中，我们可能需要工厂来产生一组相关联的类。抽象工厂模式可解决这个问题。

### 6.4.1 问题

再来看一下之前实现的个人事务管理的示例。我们以 BloggsCal 和 MegaCal 这两种格式管理编码。我们可以通过加入更多编码格式来使此结构“横向”增长，但如何通过给不同的类型的PIM对象加入编码器使它“纵向”增长呢？事实上，我们一直在应用该模式。
下边是重新改造后的代码：
```php
//预约
abstract class ApptEncoder{
    //转换编码
    abstract function encode();
}
class BloggsApptEncoder extends ApptEncoder {
    function encode() {
    }
}
class MegaApptEncoder extends ApptEncoder {
    function encode() {
    }
}

//代办事宜
abstract class TtdEncoder {
    //处理代办事宜
    abstract function encode();
}
class BloggsTtdEncoder extends TtdEncoder {
    function encode() {
    }
}
class MegaTtdEncoder extends TtdEncoder {
    function encode() {
    }
}

//联系人
abstract class ContactEncoder {
    abstract function encode();
}
class BloggsContactEncoder extends ContactEncoder {
    function encode() {
    }
}
class BegaContactEncoder extends ContactEncoder {
    function encode() {
    }
}
```
Bloggs的类与其他格式（如MegaCal）互相没有关联（尽管它们实现了一个公共的结构），但它们的功能相似。所以如果系统目前正在使用 BloggsTtdEncoder，那么它应该也能使用 BloggsContactEncoder。
那么我们如何使用抽象类工厂模式进行优化？

### 6.4.2 实现

CommsManager 抽象类定义用于生成3个产品（ApptEncoder、TtdEncoder、ContactEncoder）的接口。我们需要先实现一个具体的创建者，然后才能创建一个特定类型的具体产品。
下面是 CommsManager 和 BloggsCommsManager 的代码：
```php
abstract class CommsManger {
    abstract function getApptEncoder(); //Appt
    abstract function getTtdEncoder();  //Ttd
    abstract function getContactEncoder(); //Contact
    abstract function getHeaderText();
    abstract function getFooterText();
}
class BloggsCommsManager extends CommsManger
{
    function getApptEncoder() {
    }
    function getTtdEncoder() {
    }
    function getContactEncoder() {
    }
    function getHeaderText() {
    }
    function getFooterText() {
    }
}
```
在这个例子中使用了工厂方法模式。getContact() 是 CommsManager 的抽象方法，并在 BloggsCommsManager 中被实现。设计模式间经常会这样协议：一个模式创建可以把它自己引入到另一个模式的上下文环境。下边是我们加入了对MegaCal格式的支持。
```php
abstract class CommsManger {
    abstract function getApptEncoder(); //Appt
    abstract function getTtdEncoder();  //Ttd
    abstract function getContactEncoder(); //Contact
    abstract function getHeaderText();
    abstract function getFooterText();
}
class BloggsCommsManager extends CommsManger
{
    function getApptEncoder() {
    }
    function getTtdEncoder() {
    }
    function getContactEncoder() {
    }
    function getHeaderText() {
    }
    function getFooterText() {
    }
}
class MegaCommsManager extends CommsManger
{
    function getApptEncoder() {
    }
    function getTtdEncoder() {
    }
    function getContactEncoder() {
    }
    function getHeaderText() {
    }
    function getFooterText() {
    }
}
```

### 6.4.3 结果

那这个模式带来了什么？
- 首先，将系统与实现的细节分离开来。我们可以在示例中添加或移除任意数目的编码格式而不会影响系统
- 对系统中功能相关的元素强制进行组合。因此，通过使用 BloggsCommsManager，可以确保只使用与 BloggsCal相关的类。
- 添加新产品将会令人苦恼。因为不仅要创建新产品的具体实现，而且为了支持它，我们必须修改抽象创建者和它的每一个具体实现

抽象工厂模式的许多实现都会使用工厂方法模式，这可能因为大多数范例都是用Java或者C++写的。但是PHP并不会强制规定方法的返回类型，这给了我们一些可能利用的灵活性。

我们可以创建一个使用标志参数来决定返回什么对象的单一的 make() 方法，而不用给每个工厂方法创建独立的方法。
```php
abstract class CommsManager {
    const APPT    = 1;
    const TTD     = 2;
    const CONTACT = 3;
    abstract function make( $flag_int );
    abstract function getHeaderText();
    abstract function getFooterText();
}
class BloggsCommsManager extends CommsManager {
    function make($flag_int) {
        switch ( $flag_int ) {
            case self::APPT:
                return new BloggsApptEncoder();
            case self::CONTACT:
                return new BloggsContactEncoder();
            case self::TTD:
                return new BloggsTtdEncoder();
        }
    }
    function getHeaderText() {
    }
    function getFooterText() {
    }
}
```
可以看出，类的接口变得更加紧凑。但是这样做也有一定代价。在使用工厂方法时，我们定义了一个清晰的接口并强制所有具体的工厂对象遵循它。而使用单一的 make() 方法，我们必须在所有的具体创建者中支持所有的产品对象。同时，我们也引入了平行条件，因为每个具体创建者都必须实现相同的标志监测（flag test）。客户类无法确定具体的创建者是否可以产生所有产品，因为 make() 方法的内部需要对每种情况都进行考虑并做出选择。

另一方面，我们可以创建更灵活的创建者。创建者基类可以提供 make() 方法来保证每个家族有一个默认实现。具体子类可以选择性地改变这一行为。子类可以实现自己的 make() 方法并调用，由此决定生成何种对象。这些创建者子类可以自行决定是否在执行自己的 make() 方法后调用父类的 make() 方法。

### 6.4.4 原型模式

平行继承层次的出现是工厂方法模式带来的一个问题。这是一种让一些程序员不舒服的耦合。每次添加产品家族时，你就被迫去创建一个相关的具体创建者（比如编码器 BloggsCal和BloggsCommsManager匹配）。在一个快速增长的系统里会包含越来越多的产品，而维护这种关系便会很快令人厌烦。
一种避免这种依赖的办法是使用PHP的 clone 关键词复制已存在的具体产品。然后，具体产品类本身便成为它们自己生成的基础。这便是`原型模式`。使用该模式我们可以用组合代替继承。这样的转变则促进了代码运行时的灵活性，并减少了必须创建的类。

#### **问题**

假设有一款“文明”风格的网络游戏，可在区块组成的格子中操作战斗单元。每个区块分别代表海洋、平原、森林。地形的类别约束了占有区块的单元的移动和格斗能力。我们可以有一个 TerrainFactoy 对象来提供 Sea、Forest、Plains 对象，我们决定允许用户在完全不同的环境里选择，于是 Sea 可能是 MarSea 和 EarthSea 的抽象父类。Froest（森林）和 Plains（平原）对象也会有相似的实现。这里的分支便构成了抽象工厂模式。我们有截然不同的产品体系（Sea、Plains、Forest），而这些产品家族间有超越继承的紧密联系，如 Earth（地球） 和 Mars（火星），它们都同时存在海洋、森林和平原地形。
下边的案例说明了如何对这些产品应用抽象工厂和工厂方法模式：
```php
//区块生成工厂
abstract class TerrainFactory {
    abstract function getSea();
    abstract function getPliains();
    abstract function getForest();
}
//地球上的工厂
class EarthTerrinFactory extends TerrainFactory {
    function getSea() {
    }
    function getPliains() {
    }
    function getForest() {
    }
}
//火星上的工厂
class MarsTerrainFactory extends TerrainFactory {
    function getSea() {
    }
    function getPliains() {
    }
    function getForest() {
    }
}
//大海
class Sea {
}
//火星上的海
class Marsea extends Sea {
}
//地球上的海
class EarthSea extends Sea {
}
//平原
class Plains {
}
//火星上的平原
class MarsPlains extends Plains {
}
//地球上的平原
class EarthPlains extends Plains {
}
//森林
class Forest {
}
//火星上的森林
class MarsForest extends Forest {
}
//地球上的森林
class EarthForest extends Forest {
}
```
你可以看到，我们依赖继承来组合工厂生成的地形家族产品。这的确是一个可行的解决方案，但这需要有一个大型的继承体系，并且相对来说不那么灵活。当你不想要平行的继承体系而需要最大化运行时的灵活性时，你可以使用抽象工厂模式的强大变形--`原型模式`

#### **实现**

当使用抽象工厂模式或工厂方法模式时，必须决定使用哪个具体的创建者，这很可能是通过检查配置值来决定的。既然必须这样做，那为什么不简单地创建一个保存具体产品的工厂类，并在初始化时就加入这种做法呢？这样除了可以减少类的数量，还有其他好处。下面是在工厂中使用原型模式的简单代码：

`原型模式案例`
```php
class Sea {} //大海
class Marsea extends Sea {} //火星上的海
class EarthSea extends Sea {}   //地球上的海

class Plains {} //平原
class MarsPlains extends Plains {}  //火星上的平原
class EarthPlains extends Plains {} //地球上的平原

class Forest {} //森林
class MarsForest extends Forest {}  //火星上的森林
class EarthForest extends Forest {} //地球上的森林

//区块生成工厂
class TerrainFactory {
    private $sea;
    private $forest;
    private $plains;
    function __construct(Sea $sea, Plains $plains, Forest $forest) {
        $this->sea = $sea;
        $this->plains = $plains;
        $this->forest = $forest;
    }
    function getSea() {
        return clone $this->sea;
    }
    function getPliains() {
        return clone $this->sea;
    }
    function getForest() {
        return clone $this->forest;
    }
}
$factory = new TerrainFactory(new EarthSea(), new Plains(), new EarthForest());
print_r( $factory->getSea() );
print_r( $factory->getPliains() );
print_r( $factory->getForest() );
```
可以看到，我们加载了一个带有产品对象实例的具体的 TerrainFactory 对象。当客户端代码调用 getSea() 时，返回在初始化时缓存的 Sea 对象的一个副本。我们不仅仅省掉了一些类，而且有了额外的灵活性。想要在一个有类似地球海洋和森林并有类似火星平原的星球上玩游戏吗？你没必要写一个新的创建者类--只需要简单改变添加到 TerrainFactory 的参数即可
```php
$factory = new TerrainFactory( new EarthSea(), new MarsPlains(), new EarthForest() );
```
因此原型模式使我们可利用组合所提供的灵活性，不过我们得到的可不止这个。因为在运行时保持和克隆对象，所以当生成新产品时，可以重新设定对象状态。设想 Sea 对象有 $navigability 属性（可导航性），该属性影响船在海洋区块的移动能量值，并可以被设置来调整游戏的难度登记。
```php
class Sea {
	private $navibability = 0;
	function __construct( $navibability ) {
		$this->navibability = $navibability;
	}
}
```
现在当我们初始化 TerrainFactory 对象时，可以加入一个具有导航修改仪的 Sea 对象，而这对 TerrainFactory 处理的所有 Sea 对象都有效。
```php
$factory = new TerrainFactory( new EarthSea(-1), new MarsPlains(), new EarthForest() );
```
如果你想生成的对象是由其他对象组合而成的，以上代码显然颇具灵活性。假设所有的 Sea 对象都能包含 Resource 对象。通过配置的值，可以给所有的 Sea 对象都默认提供一个 FishResouce 对象。要记住的是如果产品对象引用了其他对象，那么你应该实现 __clone() 方法来保证你得到的是深复制。
```php
class Contained {}
class Container {
    public $contained;
    function __construct() {
        $this->contained = new Contained();
    }
    function __clone() {
        //确保被克隆的对象持有的是 self::$contained 的克隆而不是引用
        $this->contained = clone $this->contained;
    }
}
```

## **7. 让面向对象编程更加灵活的模式**

## 7.1 组合模式

组合模式也许是将继承用于组合对象的最极端的例子。组合模式的设计思想简单而又非常优雅，并且非常使用。但要注意的是，因为这个模式很好用，你也许会经不起诱惑而过度使用它。
组合模式可以很好地聚合和管理许多相似的对象，因而对客户端代码来说，一个独立对象和一个对象集合是没有差别的。虽然该模式实际上非常简单，但还是经常令人感到迷惑。其中一个原因就是模式中类的结构和对象的组织结构非常相似。组合模式中继承的层级结构是树形结构，由根部的基类开始，分支到各个不同的子类，在其继承树中可以轻松地生成枝叶，也可以很容易地遍历整个对象树中的对象。
如果你还对这个模式不熟悉，那就举例说明，单个物体有时可以被当成一个集合来看待。例如，用谷类和肉类可以制作一种食物--香肠。然后我们将香肠当作单个物体来处理。就像我们可以食用、烹调、购买或出售肉类一样，我们也可以食用、烹调、购买或出售由肉类组成的香肠。我们也许会用香肠和其他组合成分类制作馅饼，从而将一个组合物合成一个更大的组合物。在这个例子中，我们处理组件的方式和处理集合的方式是一样的。组合模式有助于我们为集合和组件之间的关系建立模型。

### 7.1.1 问题

管理一组对象是很复杂的，当对象中可能还包含着它们自己的对象时尤其如此。这类问题在开发中非常常见。比如发票中会包含描述产品或服务的条目，或者待办事宜列表会包含多个子任务。在CMS中，我们无法离开页面、文章、多媒体组件等。从外部来管理这些结构相当困难。

用之前的虚拟场景，我们以一个叫做“文明”的游戏为基础设计一个系统。玩家可以在一个由大量区块所组成的地图上移动战斗单元。独立的单元可被组合起来一起移动、战斗、防守。我们先定义一些战斗单元的类型。
```php
abstract class Unit {
    abstract function bombardStrength();
}
class Archer extends Unit {
    function bombardStrength() {
        return 4;
    }
}
class LaserCannonUnit extends Unit {
    function bombardStrength() {
        return 44;
    }
}
```
Unit 类定义了一个抽象方法 bombardStrength，用于设置战斗单元对邻近区块的攻击强度，然后我们在 Archer(射手)和 LaserCannonUnit（激光炮）这两个类中都实现了 bombardStrength 方法。当然，这3个类也应该包含移动能力和防御能力等内容，但为了简单起见。我们暂时先省略这些功能。现在我们可以这样定义一个独立的类来组合战斗单元：
```php
class Army {
    private $units = array();
    function addUnit( Unit $unit) {
        array_push( $this->units, $unit );
    }
    function bombardStrength() {
        $ret = 0;
        foreach ( $this->units as $unit ) {
            $ret += $unit->bombardStrength();
        }
        return $ret;
    }
}
```
Army(军队)类有一个 addUint() 方法，用于接收 Uint对象。Uint对象被保存在数组属性 $units 中。同时我们在 Army 的 bombardStrength 方法中通过一个简单的迭代遍历所聚合的 Uint 对象并调用 bombardStrength 方法，计算出总的攻击强度。

如果问题一直这么简单，那么这样的模型还是非常令人满意的。但是当我们加入一些新的需求，会发生什么呢？比如军队应该可以与其他军队进行合并。同时，每个军队都会有它自己的ID，这样军队以后还能从整编中解散出来。比如今天大公爵（ArchDuke）和Soames将军的勇士们可能一起并肩作战，但是当国内发生叛乱时，公爵可能会在任何时候将它的军队调回。因此我们不能仅支持将军队与军队合并，还能够在必要的时候再把原来的军队抽离出来。

我们可以修改 Army 类，使之可以像添加 Unit 对象一样添加 Army 对象：
```php
class Army {
    private $units = array();
    private $armies = array();
    function addUnit( Unit $unit) {
        array_push( $this->units, $unit );
    }
    function addArmy( Army $army) {
        array_push( $this->armies, $army );
    }
    function bombardStrength() {
        $ret = 0;
        foreach ( $this->units as $unit ) {
            $ret += $unit->bombardStrength();
        }
        foreach ( $this->armies as $army ) {
            $ret += $army->bombardStrength();
        }
        return $ret;
    }
}
```
此时，这个新的需求还不算太复杂。但是记住，我们还需要对 defensiveStrength(） 等方法做相似的修改。慢慢地，我们的游戏会变得越来越完善。现在客户提出了新的续期：运兵船可以支持最多10个战斗单元以改进他们在某些地形上的活动范围。显然，运兵船与用于组合战斗单元的 Army 对象相似，但它也有一些自己的特性。虽然我们能够通过改进 Army 类来处理 TroopCarrier 对象，但是我们知道以后可能会有更多对部队进行组合的需求。显然，我们需要一个更灵活的模型。

### 7.1.2 实现

组合模式定义了一个单根继承体系，使具有截然不同职责的集合可以并肩工作。在之前的例子中我们已看到以上两点。组合模式中的类必须支持一个共同的操作集，以将其作为它们的首要职责。对我们来说，bombardStrength() 方法便支持这样的操作。类同时必须拥有添加和删除子对象的方法。

重写 Uint 类
```php
abstract class Unit {
    abstract function addUnit( Unit $unit );
    abstract function removeUnit( Unit $unit);
    abstract function bombardStrength();
}
```
可以看到，我们为所有的 Unit对象设计了基本功能。现在看看一个组合对象是如何实现这些抽象方法的：
```php
class Army extends Unit {
    private $units = array();
    function addUnit( Unit $unit) {
        //第三个参数用来做 全值匹配 ===
        if ( in_array( $unit, $this->units, true)) {
            return;
        }
        $this->units[] = $unit;
    }
    function removeUnit(Unit $unit) {
        $this->units = array_udiff( $this->units, array( $unit ), function( $a, $b ) { return ($a === $b) ? 0 : 1; });
    }
    function bombardStrength() {
        $ret = 0;
        foreach ( $this->units as $unit ) {
            $ret += $unit->bombardStrength();
        }
        return $ret;
    }
}
```
在我们将 Unit 对象加入 $units 数组属性前， addUnit() 方法会先检查是否已经添加过同一个 Unit 对象，而 removeUnit() 也会使用类似的检查在属性中移除指定的 Unit 对象。

然后 Army 对象可保存任何类型的 Unit 对象，包括 Army 对象本身或者 Archer和 LaserCannonUnit 这样的局部对象。因为所有的部队单位都保证支持 bombardStrength() 方法，所以我们的 Army::bombardStrength() 方法只需遍历 $units 属性，调用每个 Unit 对象的 bombardStrength() 方法，就可以计算出整支军队总的攻击强度。

组合模式的一个问题是如何实现 add 和 remove 方法。一般的组合模式会在抽象超类中添加 add 和 remove 方法。这可以确保模式中的所有的类都共享同一个接口，但这同时也意味着局部类必须实现这些方法：
```php
class UnitException extends Exception {}
class Archer extends Unit {
    function addUnit(Unit $unit) {
        throw new UnitException( get_class( $this ) . ' is a leaf');
    }
    function removeUnit(Unit $unit) {
        throw new UnitException( get_class( $this ) . ' is a leaf');
    }
    function bombardStrength() {
        return 4;
    }
}
```
实际上，我们并不希望在 Archer 对象中添加 Unit 对象，所以在 addUnit 或 removeUnit 方法被调用时会抛出异常。这要求修改所有局部类的 add/remove 方法，因为我们可以修改之前设计的代码，在 Unit 类的 addUnit()/removeUnit() 方法中抛出异常。
```php
abstract class Unit {
    function addUnit( Unit $unit ) {
        throw new UnitException( get_class( $this ) . ' is a leaf');
    }
    function removeUnit( Unit $unit) {
        throw new UnitException( get_class( $this ) . ' is a leaf');
    }
    abstract function bombardStrength();
}
class Archer extends Unit {
    function bombardStrength() {
        return 4;
    }
}
```
这样做可以去除局部类中的重复代码，但是同时组合类不再需要强制性地实现 addUnit() 和 removeUnit() 方法了，这可能会带来问题。

回顾一下组合模式所带来的益处：
- 灵活：因为组合模式中的一切类都共享了同一个父类型，所以可以轻松地在设计中添加新的组合对象或者局部对象，而无需大范围地修改代码。
- 简单：使用组合结构的客户端代码只需要设计简单的接口。客户端代码没有必要区分一个对象是组合对象还是局部对象。客户端代码调用 Army::bombardStrength() 方法时也许会产生一些幕后的委托调用，但是对于客户端代码而言，无论是过程还是效果，都和调用 Archer::bombardStrength() 方法是完全相同的。
- 隐式到达：组合模式中的对象通过树型结构组织。每个组合对象中都保存着对子对象的引用。因此对树中某部分的一个小操作可能会产生很大的影响。比如，我们可能会将一个父 Army 对象的某个子 Army 对象删除，并将其添加到另外一个父 Army 对象上去。这个简单的动作看似只对子 Army 对象产生作用，但实际却影响了子 Army 对象中所引用的 Unit 对象及其子对象的状态。
- 显示到达：树型结构可轻松遍历。可以通过迭代树型结构来获取组合对象和局部对象的信息，或对组合对象和局部对象执行批量处理。在下一章中，我们在处理访问者模式时会看到一个与此相关的强大技术。

通过代码体会组合模式的好处：
```php
//创建一个 Army 对象
$main_army = new Army();

//添加一些 Unit对象
$main_army->addUnit( new Archer() );
$main_army->addUnit( new LaserCannonUnit() );

//创建一个新的 Army 对象
$sub_army = new Army();

//添加一些 Unit 对象
$sub_army->addUnit( new Archer() );
$sub_army->addUnit( new Archer() );
$sub_army->addUnit( new Archer() );

//把第二个 Army 对象添加到第一个 Army 对象中去
$main_army->addUnit( $sub_army );

//所有攻击强度计算都在幕后进行
print "attackin with strength : {$main_army->bombardStrength()}";
```
我们创建了一个新的 Army 对象，并为它添加了一些简单的 Unit对象，然后我们创建了另一个 Army 对象，并将其添加到第一个 Army 对象中。当我们调用第一个 Army 对象的 Unit::bombardStrength() 方法时，就可以在幕后计算各个局部对象的攻击强度并得出总的攻击强度。组合结构的所有复杂性都被完全隐藏了。

### 7.1.3 效果

为什么我们要把 addUnit 和 removeUnit 这样的冗余方法加到并不需要它们的局部类中？答案在于 Unit 类型的透明性。
客户端代码在得到一个 Unit 对象时，知道其中一定包含 addUnit 方法。组合模式的原则便是局部类和组合类具有同样的接口。不过这未必能真正帮助我们，因为我们仍然不知道调用 Unit 对象的 addUnit 方法是否安全。
如果我们将 add/remove 方法降到下一级对象中，以便这些方法仅在组合类中使用，那么又会产生另一个问题--使用 Unit 对象时，我们并不知道该对象是否默认支持 addUnit() 方法。但是，将冗余的类方法留在局部类中让人感到不舒服。这样做毫无意义而且给系统设计带来歧义，因为接口应该关注于自己独有的功能。
我们可以十分轻松地将组合类分解为 CompositeUnit 子类型。首先删除 Unit的 add/remove 动作：
```php
abstract class Unit {
    function getComposite() {
        return null;
    }
    abstract function bombardStrength();
}
```
注意一下新增的 getComposite() 方法，不过我们稍候再来看这个方法。现在我们需要一个新的拥有 addUnit() 和 removeUnit() 方法的抽象类。我们甚至可以给这两个方法加上默认实现：
```php
abstract class CompositeUnit extends Unit {
    private $units = array();
    function getComposite()
    {
        return $this;
    }
    protected function units() {
        return $this->units;
    }
    function addUnit( Unit $unit) {
        //第三个参数用来做 全值匹配 ===
        if ( in_array( $unit, $this->units, true)) {
            return;
        }
        $this->units[] = $unit;
    }
    function removeUnit(Unit $unit) {
        $this->units = array_udiff( $this->units, array( $unit ), function( $a, $b ) { return ($a === $b) ? 0 : 1; });
    }
}
```
因为 CompositeUnit 类继承自 Unit 类，并没有实现抽象的 bombardStrength() 方法，所以 CompositeUnit 类也被声明为抽象的，虽然它本身并没有抽象方法。 Army 类现在可以扩展 CompositeUnit 了。
虽然我们删除了局部类中冗余、烦人的 add/remove 方法，但客户端代码在使用 addUnit() 方法前却仍必须检查对象是否为一个 CompositeUnit 类型 的对象。

```php
abstract class Unit {
    function getComposite() {
        return null;
    }
    abstract function bombardStrength();
}
class Archer extends Unit {
    function bombardStrength() {
        return 4;
    }
}
class LaserCannonUnit extends Unit {
    function bombardStrength() {
        return 44;
    }
}
abstract class CompositeUnit extends Unit {
    private $units = array();
    function getComposite()
    {
        return $this;
    }
    protected function units() {
        return $this->units;
    }
    function addUnit( Unit $unit) {
        //第三个参数用来做 全值匹配 ===
        if ( in_array( $unit, $this->units, true)) {
            return;
        }
        $this->units[] = $unit;
    }
    function removeUnit(Unit $unit) {
        $this->units = array_udiff( $this->units, array( $unit ), function( $a, $b ) { return ($a === $b) ? 0 : 1; });
    }
}

class TroopCarrier extends CompositeUnit {
    function bombardStrength() {
    }
}
class Army extends CompositeUnit {
    function bombardStrength() {
    }
}
```

这就是添加 getComposite() 方法的原因。该方法默认返回一个 null 值，它仅在 CompositeUnit 类中才返回 CompositeUnit 本身。所以如果该方法返回一个对象，那么便可以调用它的 addUnit 方法。下面是使用这种思路的客户端代码：
```php
class UnitScript {
    static function joinExisting( Unit $newUnit, Unit $occupyingUnit ) {
        $comp = '';
        if ( ! is_null( $comp = $occupyingUnit->getComposite()) ) {
            $comp->addUnit( $newUnit );
        } else {
            $comp = new Army();
            $comp->addUnit( $occupyingUnit );
            $comp->addUnit( $newUnit );
        }
        return $comp;
    }
}
```
joinExisting() 方法有两个 Unit 对象参数。第一个参数是一个区块的新来者，第二个参数是之前的占据者。如果第二个 Unit 对象是一个 CompositeUnit 对象，那么第一个 Unit 对象被添加给它。但如果不是的话，方法将会创建一个新Army 对象并将这两个对象添加到 Army 对象中。我们一开始并不知道 $occupyingUnit 参数是否包含 CompositeUnit 对象，但是通过调用 getComposite() 可以解决这个问题。若 getComposite 返回对象，那么我们可以直接添加新的 Unit 对象。如果不是，那么我们创建一个新的 Army 对象，并将两个 Unit 对象都添加给它。
我们还可以进一步简化该模型，让 Unit::getComposite() 方法返回一个添加当前 Unit 的 Army 对象。或者也可以返回前面的模型，并用 Unit::addUnit() 方法来做同样的事情：创建一个 Army 对象，并将两个 Unit 对象都添加给它。虽然这样代码很简洁，但是这样假设你已经预先知道了部队单元所要聚合的组合类型。当你设计如 getComposite() 和 addUnit() 这样的方法时，业务逻辑将决定你所能做的假设。
而这正是组合模式缺陷的症状之一。简化的前提是使所有的类都继承同一个基类。简化的好处有时会以降低对象类型安全为代价。模型变得越复杂，你就不得不手动进行越多的类型检查。比如我们有一个 Cavalry（骑兵）对象，假设游戏规则规定不能将一匹马放到运兵船上，在组合模式中我们并没有自动化的方式来强制执行这个规则：
```php
class TroopCarrier extends CompositeUnit {
    function addUnit(Unit $unit) {
        if ( $unit instanceof Cavalry ) {
            //骑兵不能上运兵船，因为他携带的有马匹
            throw new UnitException("不能携带马匹上运兵船");
        }
        parent::addUnit( $unit );
    }

    function bombardStrength() {
        return 0;
    }
}
```
这里我们不得不使用 instanceof 操作符来检查传给 addUnit() 方法的对象类型。特殊对象越来越多，组合模式开始渐渐弊大于利。`在大部分局部对象可互换的情况下，组合模式才最适用。`
需要担心的另外一个问题是组合操作的成本。 Army::bombardStrength() 方法便是典型的例子，该方法会逐级调用对象树中下级分支的方法。如果一个对象树中有大量子 Army 对象，一个简单的调用可能会导致系统崩溃。虽然 bombardStrength() 方法自身并不一定会带来多大的系统开销，但是如果一些局部对象为了获得返回值而执行一系列复杂的计算，那么会发生什么呢？解决问题的一个办法是在父级对象中缓存计算结果，这样可使接下来的调用减少系统开销。尽管如此，你还是需要小心地保证缓存值不会失效。因此你需要设计一个当对树进行操作时可移除过期缓存的策略，而这也许会要求你给子对象加上父对象的引用。

### 7.1.4 组合模式小结

如果你想像对待单个对象一样对待组合对象，那么组合模式十分有用。可能是因为组合对象本质上和局部对象相似，也可能 因为在环境中组合对象与局部对象的特征相同。因为组合模式是树型结构，所以对整体的操作会影响到局部，而通过组合，对客户端代码来说局部的数据又是透明的。组合模式使这些操作和查询对客户端代码透明。对象树可以方便地进行遍历。你也可以轻松地在组合结构中加入新的组件类型。但另一方面，组合模式又依赖于其组成部分的简单性。`组合模式不能很好地在关系数据库中保存数据，但却非常适合使用 XML 持久化`

## 模式这一章实现进行不下去了，准备去参考 大话设计模式 一书

## **8. 用 phpDocumentor 生成文档**

搜索 phpDocumentor 扩展包

## **9. 使用 PHPUnit 进行测试**

[PHPUnit下载地址](http://www.phpunit.cn/ "PHPUnit地址")