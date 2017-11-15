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
- [3. 对象与设计](#3-对象与设计)


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

## **3. 对象与设计**