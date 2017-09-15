# Clean Code PHP

## 目录

  1. [介绍](#介绍)
  2. [变量](#变量)
     * [使用见字知意的变量名](#使用见字知意的变量名)
     * [同一个实体要用相同的变量名](#同一个实体要用相同的变量名)
     * [使用便于搜索的名称 (part 1)](#使用便于搜索的名称part-1)
     * [使用便于搜索的名称 (part 2)](#使用便于搜索的名称part-2)
     * [使用自解释型变量](#使用自解释型变量)
     * [少用无意义的变量名](#少用无意义的变量名)
     * [Don't add unneeded context](#dont-add-unneeded-context)
     * [合理使用参数默认值，没必要在方法里再做默认值检测](#合理使用参数默认值，没必要在方法里再做默认值检测)
  3. [函数](#函数)
     * [Function arguments (2 or fewer ideally)](#function-arguments-2-or-fewer-ideally)
     * [Functions should do one thing](#functions-should-do-one-thing)
     * [Function names should say what they do](#function-names-should-say-what-they-do)
     * [Functions should only be one level of abstraction](#functions-should-only-be-one-level-of-abstraction)
     * [Don't use flags as function parameters](#dont-use-flags-as-function-parameters)
     * [Avoid Side Effects](#avoid-side-effects)
     * [Don't write to global functions](#dont-write-to-global-functions)
     * [Don't use a Singleton pattern](#dont-use-a-singleton-pattern)
     * [Encapsulate conditionals](#encapsulate-conditionals)
     * [Avoid negative conditionals](#avoid-negative-conditionals)
     * [Avoid conditionals](#avoid-conditionals)
     * [Avoid type-checking (part 1)](#avoid-type-checking-part-1)
     * [Avoid type-checking (part 2)](#avoid-type-checking-part-2)
     * [Remove dead code](#remove-dead-code)
  4. [对象和数据结构 Objects and Data Structures](#objects-and-data-structures)
     * [Use getters and setters](#use-getters-and-setters)
     * [Make objects have private/protected members](#make-objects-have-privateprotected-members)
  5. [Classes](#classes)
     * [Use method chaining](#use-method-chaining)
     * [Prefer composition over inheritance](#prefer-composition-over-inheritance)
  6. [类的SOLID原则 SOLID](#solid)
     * [S: 单一功能 Single Responsibility Principle (SRP)](#single-responsibility-principle-srp)
     * [O: 开闭原则 Open/Closed Principle (OCP)](#openclosed-principle-ocp)
     * [L: 里氏替换 Liskov Substitution Principle (LSP)](#liskov-substitution-principle-lsp)
     * [I: 接口隔离 Interface Segregation Principle (ISP)](#interface-segregation-principle-isp)
     * [D: 依赖反转 Dependency Inversion Principle (DIP)](#dependency-inversion-principle-dip)
  7. [Don’t repeat yourself (DRY)](#dont-repeat-yourself-dry)
  8. [Translations](#translations)

## 介绍

本文由 php-cpm 基于 yangweijie 的[clen php code](https://github.com/jupeter/clean-code-php)翻译并同步大量原文内容，欢迎大家指正。

本文参考自 Robert C. Martin的[*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)  书中的软件工程师的原则
,适用于PHP。 这不是风格指南。 这是一个关于开发可读、可复用并且可重构的PHP软件指南。

并不是这里所有的原则都得遵循，甚至很少的能被普遍接受。 这些虽然只是指导，但是都是*Clean Code*作者多年总结出来的。

本文受到 [clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript) 的启发

## **变量**
### 使用见字知意的变量名

**坏:**

```php
$ymdstr = $moment->format('y-m-d');
```

**好:**

```php
$currentDate = $moment->format('y-m-d');
```

**[⬆ 返回顶部](#目录)**

### 同一个实体要用相同的变量名

**坏:**

```php
getUserInfo();
getUserData();
getUserRecord();
getUserProfile();
```

**好:**

```php
getUser();
```
**[⬆ 返回顶部](#目录)**

### 使用便于搜索的名称 (part 1)
写代码是用来读的。所以写出可读性高、便于搜索的代码至关重要。
命名变量时如果没有有意义、不好理解，那就是在伤害读者。
请让你的代码便于搜索。

**坏:**
```php
// What the heck is 448 for?
$result = $serializer->serialize($data, 448);
```

**好:**

```php
$json = $serializer->serialize($data, JSON_UNESCAPED_SLASHES | JSON_PRETTY_PRINT | JSON_UNESCAPED_UNICODE);
```

**[⬆ 返回顶部](#目录)**

### 使用便于搜索的名称 (part 2)

**坏:**

```php
// What the heck is 4 for?
if ($user->access & 4) {
    // ...
}
```

**好:**

```php
class User
{
    const ACCESS_READ = 1;
    const ACCESS_CREATE = 2;
    const ACCESS_UPDATE = 4;
    const ACCESS_DELETE = 8;
}

if ($user->access & User::ACCESS_UPDATE) {
    // do edit ...
}
```

**[⬆ 返回顶部](#目录)**


### 使用自解释型变量

**坏:**
```php
$address = 'One Infinite Loop, Cupertino 95014';
$cityZipCodeRegex = '/^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/';
preg_match($cityZipCodeRegex, $address, $matches);

saveCityZipCode($matches[1], $matches[2]);
```

**不错:**

好一些，但强依赖于正则表达式的熟悉程度

```php
$address = 'One Infinite Loop, Cupertino 95014';
$cityZipCodeRegex = '/^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/';
preg_match($cityZipCodeRegex, $address, $matches);

list(, $city, $zipCode) = $matches;
saveCityZipCode($city, $zipCode);
```

**好:**

使用带名字的子规则，不用懂正则也能看的懂

```php
$address = 'One Infinite Loop, Cupertino 95014';
$cityZipCodeRegex = '/^[^,\\]+[,\\\s]+(?<city>.+?)\s*(?<zipCode>\d{5})?$/';
preg_match($cityZipCodeRegex, $address, $matches);

saveCityZipCode($matches['city'], $matches['zipCode']);
```

**[⬆ 返回顶部](#目录)**

### 避免深层潜逃，尽早返回

太多的if else语句通常会是你的代码难以阅读，直白优于隐晦

**糟糕:**

```php
function isShopOpen($day)
{
    if ($day) {
        if (is_string($day)) {
            $day = strtolower($day);
            if ($day === 'friday') {
                return true;
            } elseif ($day === 'saturday') {
                return true;
            } elseif ($day === 'sunday') {
                return true;
            } else {
                return false;
            }
        } else {
            return false;
        }
    } else {
        return false;
    }
}
```

**好的:**

```php
function isShopOpen($day)
{
    if (empty($day) && ! is_string($day)) {
        return false;
    }

    $openingDays = [
        'friday', 'saturday', 'sunday'
    ];

    return in_array(strtolower($day), $openingDays);
}
```

**糟糕的:**

```php
function fibonacci($n)
{
    if ($n < 50) {
        if ($n !== 0) {
            if ($n !== 1) {
                return fibonacci($n - 1) + fibonacci($n - 2);
            } else {
                return 1;
            }
        } else {
            return 0;
        }
    } else {
        return 'Not supported';
    }
}
```

**好的:**

```php
function fibonacci($n)
{
    if ($n === 0) {
        return 0;
    }

    if ($n === 1) {
        return 1;
    }

    if ($n > 50) {
        return 'Not supported';
    }

    return fibonacci($n - 1) + fibonacci($n - 2);
}
```

**[⬆ back to top](#table-of-contents)**

### 少用无意义的变量名

别让读你的代码的人猜你写的变量是什么意思。
写清楚好过模糊不清。

**坏:**

```php
$l = ['Austin', 'New York', 'San Francisco'];

for ($i = 0; $i < count($l); $i++) {
    $li = $l[$i];
    doStuff();
    doSomeOtherStuff();
    // ...
    // ...
    // ...
  // 等等, `$li` 又代表什么?
    dispatch($li);
}
```

**好:**

```php
$locations = ['Austin', 'New York', 'San Francisco'];

foreach ($locations as $location) {
    doStuff();
    doSomeOtherStuff();
    // ...
    // ...
    // ...
    dispatch($location);
}
```

**[⬆ 返回顶部](#目录)**

### 不要添加不必要上下文

如果从你的类名、对象名已经可以得知一些信息，就别再在变量名里重复。

**坏:**

```php
class Car
{
    public $carMake;
    public $carModel;
    public $carColor;

    //...
}
```

**好:**

```php
class Car
{
    public $make;
    public $model;
    public $color;

    //...
}
```

**[⬆ 返回顶部](#目录)**

### 合理使用参数默认值，没必要在方法里再做默认值检测

**Not good:**

This is not good because `$breweryName` can be `NULL`.

```php
function createMicrobrewery($breweryName = 'Hipster Brew Co.')
{
    // ...
}
```

**Not bad:**

This opinion is more understandable than the previous version, but it better controls the value of the variable.

```php
function createMicrobrewery($name = null)
{
    $breweryName = $name ?: 'Hipster Brew Co.';
    // ...
}
```

**好:**

If you support only PHP 7+, then you can use [type hinting](http://php.net/manual/en/functions.arguments.php#functions.arguments.type-declaration) and be sure that the `$breweryName` will not be `NULL`.

```php
function createMicrobrewery(string $breweryName = 'Hipster Brew Co.')
{
    // ...
}
```

**[⬆ 返回顶部](#目录)**

## **函数**
### 函数参数（最好少于2个）
限制函数参数个数极其重要，这样测试你的函数容易点。有超过3个可选参数参数导致一个爆炸式组合增长，你会有成吨独立参数情形要测试。

无参数是理想情况。1个或2个都可以，最好避免3个。再多就需要加固了。通常如果你的函数有超过两个参数，说明他要处理的事太多了。 如果必须要传入很多数据，建议封装一个高级别对象作为参数。

**坏:**

```php
function createMenu($title, $body, $buttonText, $cancellable)
{
    // ...
}
```

**好:**
```php
class MenuConfig
{
    public $title;
    public $body;
    public $buttonText;
    public $cancellable = false;
}

$config = new MenuConfig();
$config->title = 'Foo';
$config->body = 'Bar';
$config->buttonText = 'Baz';
$config->cancellable = true;

function createMenu(MenuConfig $config)
{
    // ...
}
```

**[⬆ 返回顶部](#目录)**


### 函数应该只做一件事
这是迄今为止软件工程里最重要的一个规则。当一个函数做超过一件事的时候，他们就难于实现、测试和理解。当你把一个函数拆分到只剩一个功能时，他们就容易被重构，然后你的代码读起来就更清晰。如果你光遵循这条规则，你就领先于大多数开发者了。

**坏:**
```php
function emailClients($clients)
{
    foreach ($clients as $client) {
        $clientRecord = $db->find($client);
        if ($clientRecord->isActive()) {
            email($client);
        }
    }
}
```

**好:**

```php
function emailClients($clients)
{
    $activeClients = activeClients($clients);
    array_walk($activeClients, 'email');
}

function activeClients($clients)
{
    return array_filter($clients, 'isClientActive');
}

function isClientActive($client)
{
    $clientRecord = $db->find($client);

    return $clientRecord->isActive();
}
```
**[⬆ 返回顶部](#目录)**

### 函数名应该是有意义的动词（或表明具体做了什么事）

**坏:**

```php
class Email
{
    //...

    public function handle()
    {
        mail($this->to, $this->subject, $this->body);
    }
}

$message = new Email(...);
// 啥？handle处理一个消息干嘛了？
$message->handle();
```

**好:**

```php
class Email 
{
    //...

    public function send()
    {
        mail($this->to, $this->subject, $this->body);
    }
}

$message = new Email(...);
// 简单明了
$message->send();
```

**[⬆ 返回顶部](#目录)**

### 函数应当只有一层抽象abstraction

当你抽象层次过多时时，函数处理的事情太多了。需要拆分功能来提高可重用性和易用性，以便简化测试。
（译者注：这里从示例代码看应该是指嵌套过多）

**坏:**

```php
function parseBetterJSAlternative($code)
{
    $regexes = [
        // ...
    ];

    $statements = explode(' ', $code);
    $tokens = [];
    foreach ($regexes as $regex) {
        foreach ($statements as $statement) {
            // ...
        }
    }

    $ast = [];
    foreach ($tokens as $token) {
        // lex...
    }

    foreach ($ast as $node) {
        // parse...
    }
}
```

**坏:**

我们把一些方法从循环中提取出来，但是`parseBetterJSAlternative()`方法还是很复杂，而且不利于测试。

```php
function tokenize($code)
{
    $regexes = [
        // ...
    ];

    $statements = explode(' ', $code);
    $tokens = [];
    foreach ($regexes as $regex) {
        foreach ($statements as $statement) {
            $tokens[] = /* ... */;
        }
    }

    return $tokens;
}

function lexer($tokens)
{
    $ast = [];
    foreach ($tokens as $token) {
        $ast[] = /* ... */;
    }

    return $ast;
}

function parseBetterJSAlternative($code)
{
    $tokens = tokenize($code);
    $ast = lexer($tokens);
    foreach ($ast as $node) {
        // parse...
    }
}
```

**好:**

最好的解决方案是把 `parseBetterJSAlternative()`方法的依赖移除。

```php
class Tokenizer
{
    public function tokenize($code)
    {
        $regexes = [
            // ...
        ];

        $statements = explode(' ', $code);
        $tokens = [];
        foreach ($regexes as $regex) {
            foreach ($statements as $statement) {
                $tokens[] = /* ... */;
            }
        }

        return $tokens;
    }
}

class Lexer
{
    public function lexify($tokens)
    {
        $ast = [];
        foreach ($tokens as $token) {
            $ast[] = /* ... */;
        }

        return $ast;
    }
}

class BetterJSAlternative
{
    private $tokenizer;
    private $lexer;

    public function __construct(Tokenizer $tokenizer, Lexer $lexer)
    {
        $this->tokenizer = $tokenizer;
        $this->lexer = $lexer;
    }

    public function parse($code)
    {
        $tokens = $this->tokenizer->tokenize($code);
        $ast = $this->lexer->lexify($tokens);
        foreach ($ast as $node) {
            // parse...
        }
    }
}
```

这样我们可以对依赖做mock，并测试`BetterJSAlternative::parse()`运行是否符合预期。

**[⬆ 返回顶部](#目录)**


### 不要用flag作为函数的参数
flag就是在告诉大家，这个方法里处理很多事。前面刚说过，一个函数应当只做一件事。 把不同flag的代码拆分到多个函数里。

**坏:**
```php
function createFile($name, $temp = false)
{
    if ($temp) {
        touch('./temp/'.$name);
    } else {
        touch($name);
    }
}
```

**好:**

```php
function createFile($name)
{
    touch($name);
}

function createTempFile($name)
{
    touch('./temp/'.$name);
}
```
**[⬆ 返回顶部](#目录)**

### 避免副作用
一个函数做了比获取一个值然后返回另外一个值或值们会产生副作用如果。副作用可能是写入一个文件，修改某些全局变量或者偶然的把你全部的钱给了陌生人。

现在，你的确需要在一个程序或者场合里要有副作用，像之前的例子，你也许需要写一个文件。你想要做的是把你做这些的地方集中起来。不要用几个函数和类来写入一个特定的文件。用一个服务来做它，一个只有一个。

重点是避免常见陷阱比如对象间共享无结构的数据，使用可以写入任何的可变数据类型，不集中处理副作用发生的地方。如果你做了这些你就会比大多数程序员快乐。

**坏:**

```php
// Global variable referenced by following function.
// If we had another function that used this name, now it'd be an array and it could break it.
$name = 'Ryan McDermott';

function splitIntoFirstAndLastName()
{
    global $name;

    $name = explode(' ', $name);
}

splitIntoFirstAndLastName();

var_dump($name); // ['Ryan', 'McDermott'];
```

**好:**
```php
function splitIntoFirstAndLastName($name)
{
    return explode(' ', $name);
}

$name = 'Ryan McDermott';
$newName = splitIntoFirstAndLastName($name);

var_dump($name); // 'Ryan McDermott';
var_dump($newName); // ['Ryan', 'McDermott'];
```

**[⬆ 返回顶部](#目录)**

### 不要写全局函数
在大多数语言中污染全局变量是一个坏的实践，因为你可能和其他类库冲突并且你api的用户不明白为什么直到他们获得产品的一个异常。让我们看一个例子：如果你想配置一个数组，你可能会写一个全局函数像`config()`，但是可能和试着做同样事的其他类库冲突。这就是为什么单例设计模式和简单配置会更好的原因。

**坏:**

```php
function config()
{
    return  [
        'foo' => 'bar',
    ]
}
```

**好:**

```php
class Configuration
{
    private $configuration = [];

    public function __construct(array $configuration)
    {
        $this->configuration = $configuration;
    }

    public function get($key)
    {
        return isset($this->configuration[$key]) ? $this->configuration[$key] : null;
    }
}
```

Load configuration and create instance of `Configuration` class 

```php
$configuration = new Configuration([
    'foo' => 'bar',
]);
```

And now you must use instance of `Configuration` in your application.


**[⬆ 返回顶部](#目录)**

### 不要使用单例模式

单例是一种 [anti-pattern](https://en.wikipedia.org/wiki/Singleton_pattern).

**坏:**

```php
class DBConnection
{
    private static $instance;

    private function __construct($dsn)
    {
        // ...
    }

    public static function getInstance()
    {
        if (self::$instance === null) {
            self::$instance = new self();
        }

        return self::$instance;
    }

    // ...
}

$singleton = DBConnection::getInstance();
```

**好:**

```php
class DBConnection
{
    public function __construct(array $dsn)
    {
        // ...
    }

     // ...
}
```

Create instance of `DBConnection` class and configure it with [DSN](http://php.net/manual/en/pdo.construct.php#refsect1-pdo.construct-parameters).

```php
$connection = new DBConnection($dsn);
```

And now you must use instance of `DBConnection` in your application.

**[⬆ 返回顶部](#目录)**

### 封装条件语句

**坏:**

```php
if ($article->state === 'published') {
    // ...
}
```

**好:**

```php
if ($article->isPublished()) {
    // ...
}
```
**[⬆ 返回顶部](#目录)**

### 避免用反义条件判断

**坏:**

```php
function isDOMNodeNotPresent($node)
{
    // ...
}

if (!isDOMNodeNotPresent($node))
{
    // ...
}
```

**好:**

```php
function isDOMNodePresent($node)
{
    // ...
}

if (isDOMNodePresent($node)) {
    // ...
}
```

**[⬆ 返回顶部](#目录)**

### 避免条件声明

这看起来像一个不可能任务。当人们第一次听到这句话是都会这么说。
"没有一个`if声明`" 答案是你可以使用多态来达到许多case语句里的任务。第二个问题很常见， “那么为什么我要那么做？” 答案是前面我们学过的一个整洁代码原则：一个函数应当只做一件事。当你有类和函数有很多`if`声明,你自己知道你的函数做了不止一件事。记住，只做一件事。

**坏:**

```php
class Airplane
{
    // ...

    public function getCruisingAltitude()
    {
        switch ($this->type) {
            case '777':
                return $this->getMaxAltitude() - $this->getPassengerCount();
            case 'Air Force One':
                return $this->getMaxAltitude();
            case 'Cessna':
                return $this->getMaxAltitude() - $this->getFuelExpenditure();
        }
    }
}
```

**好:**

```php
interface Airplane
{
    // ...

    public function getCruisingAltitude();
}

class Boeing777 implements Airplane
{
    // ...

    public function getCruisingAltitude()
    {
        return $this->getMaxAltitude() - $this->getPassengerCount();
    }
}

class AirForceOne implements Airplane
{
    // ...

    public function getCruisingAltitude()
    {
        return $this->getMaxAltitude();
    }
}

class Cessna implements Airplane
{
    // ...

    public function getCruisingAltitude()
    {
        return $this->getMaxAltitude() - $this->getFuelExpenditure();
    }
}
```

**[⬆ 返回顶部](#目录)**

### 避免类型检查 (part 1)
PHP是弱类型的,这意味着你的函数可以接收任何类型的参数。
有时候你为这自由所痛苦并且在你的函数渐渐尝试类型检查。
有很多方法去避免这么做。第一种是统一API。

**坏:**

```php
function travelToTexas($vehicle)
{
    if ($vehicle instanceof Bicycle) {
        $vehicle->peddleTo(new Location('texas'));
    } elseif ($vehicle instanceof Car) {
        $vehicle->driveTo(new Location('texas'));
    }
}
```

**好:**

```php
function travelToTexas(Traveler $vehicle)
{
    $vehicle->travelTo(new Location('texas'));
}
```
**[⬆ 返回顶部](#目录)**

### 避免类型检查 (part 2)
如果你正使用基本原始值比如字符串、整形和数组，要求版本是PHP 7+，不用多态，需要类型检测，
那你应当考虑[类型声明](http://php.net/manual/en/functions.arguments.php#functions.arguments.type-declaration)或者严格模式。
提供了基于标准PHP语法的静态类型。 手动检查类型的问题是做好了需要好多的废话，好像为了安全就可以不顾损失可读性。
保持你的PHP 整洁，写好测试，做好代码回顾。做不到就用PHP严格类型声明和严格模式来确保安全。

**坏:**

```php
function combine($val1, $val2)
{
    if (!is_numeric($val1) || !is_numeric($val2)) {
        throw new \Exception('Must be of type Number');
    }

    return $val1 + $val2;
}
```

**好:**

```php
function combine(int $val1, int $val2)
{
    return $val1 + $val2;
}
```
**[⬆ 返回顶部](#目录)**

### 移除僵尸代码
僵尸代码和重复代码一样坏。没有理由保留在你的代码库中。如果从来没被调用过，就删掉！
因为还在代码版本库里，因此很安全。

**坏:**
```php
function oldRequestModule($url)
{
    // ...
}

function newRequestModule($url)
{
    // ...
}

$request = newRequestModule($requestUrl);
inventoryTracker('apples', $request, 'www.inventory-awesome.io');
```

**好:**

```php
function requestModule($url)
{
    // ...
}

$request = requestModule($requestUrl);
inventoryTracker('apples', $request, 'www.inventory-awesome.io');
```

**[⬆ 返回顶部](#目录)**


## 对象和数据结构

### 使用 getters 和 setters
在PHP中你可以对方法使用`public`, `protected`, `private` 来控制对象属性的变更
* 当你想对对象属性做获取之外的操作时，你不需要在代码中去寻找并修改每一个该属性访问方法

* Makes adding validation simple when doing a `set`.
* 当有`set`对应的属性方法时，易于增加参数的验证
* Encapsulates the internal representation.
* 封装内部的表示
* Easy to add logging and error handling when getting and setting.
* 使用set*和get*时，易于增加日志和错误控制
* Inheriting this class, you can override default functionality.
* 继承当前类时，可以复写默认的方法功能
* You can lazy load your object's properties, let's say getting it from a
server.
* 当对象属性是从其他服务获取时，get*，set*易于使用延迟加载

Additionally, this is part of Open/Closed principle, from object-oriented 
design principles.
此外，这样的方式也符合OOP开发中的开闭原则

**糟糕:**

```php
class BankAccount
{
    public $balance = 1000;
}

$bankAccount = new BankAccount();

// Buy shoes...
$bankAccount->balance -= 100;
```

**好:**

```php
class BankAccount
{
    private $balance;

    public function __construct($balance = 1000)
    {
      $this->balance = $balance;
    }

    public function withdrawBalance($amount)
    {
        if ($amount > $this->balance) {
            throw new \Exception('Amount greater than available balance.');
        }

        $this->balance -= $amount;
    }

    public function depositBalance($amount)
    {
        $this->balance += $amount;
    }

    public function getBalance()
    {
        return $this->balance;
    }
}

$bankAccount = new BankAccount();

// Buy shoes...
$bankAccount->withdrawBalance($shoesPrice);

// Get balance
$balance = $bankAccount->getBalance();
```

**[⬆ 返回顶部](#目录)**

### Make objects have private/protected members
对象属性多使用private/protected 限定

**糟糕:**

```php
class Employee
{
    public $name;

    public function __construct($name)
    {
        $this->name = $name;
    }
}

$employee = new Employee('John Doe');
echo 'Employee name: '.$employee->name; // Employee name: John Doe
```

**好:**

```php
class Employee
{
    private $name;

    public function __construct($name)
    {
        $this->name = $name;
    }

    public function getName()
    {
        return $this->name;
    }
}

$employee = new Employee('John Doe');
echo 'Employee name: '.$employee->getName(); // Employee name: John Doe
```

**[⬆ 返回顶部](#目录)**

## 类

### 使用方法链

这是一种非常有用的，并且在其他类库中（PHPUnit 和 Doctrine）常用的模式
它使你的代码更有表达力，减少冗余
因为这个原因，来看看如何使用方法链来使你的代码变得清爽：在你的类的每一个`set`方法的最后简单的使用 `return $this`，然后进一步将类方法链起来

**糟糕的:**

```php
class Car 
{
    private $make = 'Honda';
    private $model = 'Accord';
    private $color = 'white';

    public function setMake($make)
    {
        $this->make = $make;
    }

    public function setModel($model)
    {
        $this->model = $model;
    }

    public function setColor($color)
    {
        $this->color = $color;
    }

    public function dump()
    {
        var_dump($this->make, $this->model, $this->color);
    }
}

$car = new Car();
$car->setColor('pink');
$car->setMake('Ford');
$car->setModel('F-150');
$car->dump();
```

**好的:**

```php
class Car 
{
    private $make = 'Honda';
    private $model = 'Accord';
    private $color = 'white';

    public function setMake($make)
    {
        $this->make = $make;

        // NOTE: Returning this for chaining
        return $this;
    }

    public function setModel($model)
    {
        $this->model = $model;

        // NOTE: Returning this for chaining
        return $this;
    }

    public function setColor($color)
    {
        $this->color = $color;

        // NOTE: Returning this for chaining
        return $this;
    }

    public function dump()
    {
        var_dump($this->make, $this->model, $this->color);
    }
}

$car = (new Car())
  ->setColor('pink')
  ->setMake('Ford')
  ->setModel('F-150')
  ->dump();
```

**[⬆ back to top](#table-of-contents)**

### 组合优于继承

正如之前所说[*Design Patterns*](https://en.wikipedia.org/wiki/Design_Patterns)  the Gang of Four 所著，
我们应该尽量优先选择组合而不是继承的方式。使用继承和组合都有很多好处。
这个准则的主要意义在于当你本能的使用继承时，试着思考一下`组合`是否能更好对你的需求建模。
在一些情况下，是这样的。

接下来你或许会想，“那我应该在什么时候使用继承？” 答案依赖于你的问题，当然下面有一些何时继承比组合更好的说明：
1. 你的继承表达了“是一个”而不是“有一个”的关系（人类-》动物，用户-》用户详情）
2. 你可以复用基类的代码（人类可以像动物一样移动）
3. 你想通过修改基类对所有派生类做全局的修改（当动物移动时，修改她们的能量消耗）

**糟糕的:**

```php
class Employee 
{
    private $name;
    private $email;

    public function __construct($name, $email)
    {
        $this->name = $name;
        $this->email = $email;
    }

    // ...
}


// Employees "有" taxdata，EmployeeTaxData不是一种Employee，使用集成很糟糕


class EmployeeTaxData extends Employee
{
    private $ssn;
    private $salary;
    
    public function __construct($name, $email, $ssn, $salary)
    {
        parent::__construct($name, $email);

        $this->ssn = $ssn;
        $this->salary = $salary;
    }

    // ...
}
```

**Good:**

```php
class EmployeeTaxData 
{
    private $ssn;
    private $salary;

    public function __construct($ssn, $salary)
    {
        $this->ssn = $ssn;
        $this->salary = $salary;
    }

    // ...
}

class Employee 
{
    private $name;
    private $email;
    private $taxData;

    public function __construct($name, $email)
    {
        $this->name = $name;
        $this->email = $email;
    }

    public function setTaxData($ssn, $salary)
    {
        $this->taxData = new EmployeeTaxData($ssn, $salary);
    }

    // ...
}
```

**[⬆ back to top](#table-of-contents)**

## SOLID

**SOLID** 是Michael Feathers推荐的便于记忆的首字母简写，它代表了Robert Martin命名的最重要的五个面对对象编码设计原则
 * [S: 指责单一原则 (SRP)](#single-responsibility-principle-srp)
 * [O: 开闭原则原则 (OCP)](#openclosed-principle-ocp)
 * [L: 里氏替换原则 (LSP)](#liskov-substitution-principle-lsp)
 * [I: 接口隔离原则 (ISP)](#interface-segregation-principle-isp)
 * [D: 依赖反转原则 (DIP)](#dependency-inversion-principle-dip)


### Single Responsibility Principle (SRP)

正如在Clean Code所述，"应该只为一个理由去修改类"。人们总是易于用一堆方法塞满一个类，如同我们只能在飞机上只能携带一个行李箱（把所有的东西都塞到箱子里）。这样做的问题是：从概念上这样的类不是高内聚的，并且留下了很多理由去修改它。将你需要修改类的次数降低到最小很重要。
这是因为，当有很多方法在类中时，修改其中一处，你很难知晓在代码库中哪些依赖的模块会被影响到

**坏:**

```php
class UserSettings
{
    private $user;

    public function __construct($user)
    {
        $this->user = $user;
    }

    public function changeSettings($settings)
    {
        if ($this->verifyCredentials()) {
            // ...
        }
    }

    private function verifyCredentials()
    {
        // ...
    }
}
```

**好:**

```php
class UserAuth
{
    private $user;

    public function __construct($user)
    {
        $this->user = $user;
    }

    public function verifyCredentials()
    {
        // ...
    }
}

class UserSettings
{
    private $user;
    private $auth;

    public function __construct($user)
    {
        $this->user = $user;
        $this->auth = new UserAuth($user);
    }

    public function changeSettings($settings)
    {
        if ($this->auth->verifyCredentials()) {
            // ...
        }
    }
}
```

**[⬆ 返回顶部](#目录)**

### 开闭原则 (OCP)
正如Bertrand Meyer所述，"软件的工件（classes, modules, functions,等），
应该对扩展开放，对修改关闭" 然而这句话意味着什么呢？这个原则大体上表示你应该允许在不改变已有代码的情况下增加新的功能

**坏:**

```php
abstract class Adapter
{
    protected $name;

    public function getName()
    {
        return $this->name;
    }
}

class AjaxAdapter extends Adapter
{
    public function __construct()
    {
        parent::__construct();

        $this->name = 'ajaxAdapter';
    }
}

class NodeAdapter extends Adapter
{
    public function __construct()
    {
        parent::__construct();

        $this->name = 'nodeAdapter';
    }
}

class HttpRequester
{
    private $adapter;

    public function __construct($adapter)
    {
        $this->adapter = $adapter;
    }

    public function fetch($url)
    {
        $adapterName = $this->adapter->getName();

        if ($adapterName === 'ajaxAdapter') {
            return $this->makeAjaxCall($url);
        } elseif ($adapterName === 'httpNodeAdapter') {
            return $this->makeHttpCall($url);
        }
    }

    private function makeAjaxCall($url)
    {
        // request and return promise
    }

    private function makeHttpCall($url)
    {
        // request and return promise
    }
}
```

**好:**

```php
interface Adapter
{
    public function request($url);
}

class AjaxAdapter implements Adapter
{
    public function request($url)
    {
        // request and return promise
    }
}

class NodeAdapter implements Adapter
{
    public function request($url)
    {
        // request and return promise
    }
}

class HttpRequester
{
    private $adapter;

    public function __construct(Adapter $adapter)
    {
        $this->adapter = $adapter;
    }

    public function fetch($url)
    {
        return $this->adapter->request($url);
    }
}
```

**[⬆ 返回顶部](#目录)**

### 里氏替换原则 (LSP)
对一个简单的概念来说这是一个让人望而却步的术语。它的正式定义是"如果S是T的子类，在不改变程序原有既定属性的前提下，任何T的对象都可以使用S的对象替代（例如，使用S的对象可以替代T的对象）"这貌似是更吓人的阐述

对这个概念最好的解释是：如果你有一个父类和一个子类，在不改变原有结果正确性的前提下父类和子类可以互换。这个听起来依旧让人有些迷惑，所以让我们来看一个经典的正方形-长方形的例子。从数学上讲，正方形是一种长方形，但是当你的模型通过继承使用了"is-a"的关系时，你将发现你遇到了麻烦

**坏:**

```php
class Rectangle
{
    protected $width = 0;
    protected $height = 0;

    public function render($area)
    {
        // ...
    }

    public function setWidth($width)
    {
        $this->width = $width;
    }

    public function setHeight($height)
    {
        $this->height = $height;
    }

    public function getArea()
    {
        return $this->width * $this->height;
    }
}

class Square extends Rectangle
{
    public function setWidth($width)
    {
        $this->width = $this->height = $width;
    }

    public function setHeight(height)
    {
        $this->width = $this->height = $height;
    }
}

function renderLargeRectangles($rectangles)
{
    foreach ($rectangles as $rectangle) {
        $rectangle->setWidth(4);
        $rectangle->setHeight(5);
        $area = $rectangle->getArea(); // BAD: Will return 25 for Square. Should be 20.
        $rectangle->render($area);
    }
}

$rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles($rectangles);
```

**好:**

```php
abstract class Shape
{
    protected $width = 0;
    protected $height = 0;

    abstract public function getArea();

    public function render($area)
    {
        // ...
    }
}

class Rectangle extends Shape
{
    public function setWidth($width)
    {
        $this->width = $width;
    }

    public function setHeight($height)
    {
        $this->height = $height;
    }

    public function getArea()
    {
        return $this->width * $this->height;
    }
}

class Square extends Shape
{
    private $length = 0;

    public function setLength($length)
    {
        $this->length = $length;
    }

    public function getArea()
    {
        return pow($this->length, 2);
    }
}

function renderLargeRectangles($rectangles)
{
    foreach ($rectangles as $rectangle) {
        if ($rectangle instanceof Square) {
            $rectangle->setLength(5);
        } elseif ($rectangle instanceof Rectangle) {
            $rectangle->setWidth(4);
            $rectangle->setHeight(5);
        }

        $area = $rectangle->getArea();
        $rectangle->render($area);
    }
}

$shapes = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles($shapes);
```

**[⬆ 返回顶部](#目录)**

### 接口隔离原则 (ISP)

接口隔离原则表示："委托方不应该被强制依赖于他不需要的接口"

有一个清晰的例子来说明示范这条原则。当一个类需要一个大量的设置项，为了方便不会要求委托方去设置大量的选项，因为在大部分时间里他们不需要所有的设置项。使设置项可选有助于我们避免产生"胖接口"

**坏:**

```php
interface Employee
{
    public function work();

    public function eat();
}

class Human implements Employee
{
    public function work()
    {
        // ....working
    }

    public function eat()
    {
        // ...... eating in lunch break
    }
}

class Robot implements Employee
{
    public function work()
    {
        //.... working much more
    }

    public function eat()
    {
        //.... robot can't eat, but it must implement this method
    }
}
```

**好:**

不是每一个工人都是雇员，但是每一个雇员都是一个工人

```php
interface Workable
{
    public function work();
}

interface Feedable
{
    public function eat();
}

interface Employee extends Feedable, Workable
{
}

class Human implements Employee
{
    public function work()
    {
        // ....working
    }

    public function eat()
    {
        //.... eating in lunch break
    }
}

// robot can only work
class Robot implements Workable
{
    public function work()
    {
        // ....working
    }
}
```

**[⬆ 返回顶部](#目录)**

### 依赖倒置原则 (DIP)

这条原则说明两个基本的要点：
1.高阶的模块不应该依赖低阶的模块，它们都应该依赖于抽象
2.抽象不应该依赖于实现，实现应该依赖于抽象

这条起初看起来有点晦涩难懂，但是如果你使用过php框架（例如 Symfony），你应该见过依赖注入（DI）对这个概念的实现。虽然它们不是完全相通的概念，依赖倒置原则使高阶模块与低阶模块的实现细节和创建分离。可以使用依赖注入（DI）这种方式来实现它。更多的好处是它使模块之间解耦。耦合会导致你难于重构，它是一种非常糟糕的的开发模式

**坏:**

```php
class Employee
{
    public function work()
    {
        // ....working
    }
}

class Robot extends Employee
{
    public function work()
    {
        //.... working much more
    }
}

class Manager
{
    private $employee;

    public function __construct(Employee $employee)
    {
        $this->employee = $employee;
    }

    public function manage()
    {
        $this->employee->work();
    }
}
```

**好:**

```php
interface Employee
{
    public function work();
}

class Human implements Employee
{
    public function work()
    {
        // ....working
    }
}

class Robot implements Employee
{
    public function work()
    {
        //.... working much more
    }
}

class Manager
{
    private $employee;

    public function __construct(Employee $employee)
    {
        $this->employee = $employee;
    }

    public function manage()
    {
        $this->employee->work();
    }
}
```

**[⬆ 返回顶部](#目录)**

## 别写重复代码 (DRY)

试着去遵循[DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) 原则.

尽你最大的努力去避免复制代码，它是一种非常糟糕的行为，复制代码通常意味着当你需要变更一些逻辑时，你需要修改不止一处

试想一下，如果你在经营一家餐厅并且你在记录你仓库的进销记录：所有的土豆，洋葱，大蒜，辣椒等。
如果你有多个列表来管理进销记录，当你用其中一些土豆做菜时你需要更新所有的列表。如果你只有一个列表，只有一个地方需要更新

通常情况下你复制代码是应该有两个或者多个略微不同的逻辑，它们大多数都是一样的，但是由于它们的区别致使你必须有两个或者多个隔离的但大部分相同的方法，移除重复的代码意味着用一个function/module/class创建一个能处理差异的抽象

正确的抽象是非常关键的，这正是为什么你必须学习遵守在[Classes](#classes)章节展开的SOLID原则，不合理的抽象比复制代码更糟糕，所有务必谨慎！说到这么多，如果你能设计一个合理的抽象，实现它！不要重复，否则你会发现任何时候当你想修改一个逻辑时你必须修改多个地方

**Bad:**

```php
function showDeveloperList($developers)
{
    foreach ($developers as $developer) {
        $expectedSalary = $developer->calculateExpectedSalary();
        $experience = $developer->getExperience();
        $githubLink = $developer->getGithubLink();
        $data = [
            $expectedSalary,
            $experience,
            $githubLink
        ];

        render($data);
    }
}

function showManagerList($managers)
{
    foreach ($managers as $manager) {
        $expectedSalary = $manager->calculateExpectedSalary();
        $experience = $manager->getExperience();
        $githubLink = $manager->getGithubLink();
        $data = [
            $expectedSalary,
            $experience,
            $githubLink
        ];

        render($data);
    }
}
```

**Good:**

```php
function showList($employees)
{
    foreach ($employees as $employee) {
        $expectedSalary = $employee->calculateExpectedSalary();
        $experience = $employee->getExperience();
        $githubLink = $employee->getGithubLink();
        $data = [
            $expectedSalary,
            $experience,
            $githubLink
        ];

        render($data);
    }
}
```

**Very good:**

It is better to use a compact version of the code.

```php
function showList($employees)
{
    foreach ($employees as $employee) {
        render([
            $employee->calculateExpectedSalary(),
            $employee->getExperience(),
            $employee->getGithubLink()
        ]);
    }
}
```

**[⬆ 返回顶部](#目录)**

## Translations

This is also available in other languages:

 * ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Chinese:**
   * [yangweijie/clean-code-php](https://github.com/yangweijie/clean-code-php)
   * [php-cpm/clean-code-php](https://github.com/php-cpm/clean-code-php)

**[⬆ back to top](#table-of-contents)**
