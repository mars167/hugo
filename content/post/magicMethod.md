---
title: "PHP魔术方法总结"
date: 2019-04-18T22:01:48+08:00
lastmod: 2019-04-18T22:01:48+08:00
draft: false
keywords: ['php','魔术方法']
description: ""
tags: ['php',]
categories: ['php']
author: "mars167"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: false
autoCollapseToc: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
---

> 参考《PHP7内核刨析》，以及php官方文档

**PHP魔术方法包括**

`__construct()`， `__destruct()`， `__call()`， `__callStatic()`， `__get()`， `__set()`， `__isset()`， `__unset()`， `__sleep()`， `__wakeup()`， `__toString()`， `__invoke()`， `__set_state()`， `__clone()` 和 `__debugInfo()` 

> PHP 将所有以 `__`（两个下划线）开头的类方法保留为魔术方法。所以在定义类方法时，除了上述魔术方法，建议不要以 `__` 为前缀。
<!--more-->
#### [**重载**](https://www.php.net/manual/zh/language.oop5.overloading.php#language.oop5.overloading)

PHP所提供的重载（overloading）是指动态地创建类属性和方法。我们是通过魔术方法（magic methods）来实现的。

当调用当前环境下未定义或不[可见](https://www.php.net/manual/zh/language.oop5.visibility.php)的类属性或方法时，重载方法会被调用。本节后面将使用不可访问属性（inaccessible properties）和不可访问方法（inaccessible methods）来称呼这些未定义或不可见的类属性或方法。

所有的重载方法都必须被声明为 *public*。

> **Note**:
>
> 这些魔术方法的参数都不能[通过引用传递](https://www.php.net/manual/zh/functions.arguments.php#functions.arguments.by-reference)。

> **Note**:
>
> PHP中的重载与其它绝大多数面向对象语言不同。传统的重载是用于提供多个同名的类方法，但各方法的参数类型和个数不同。




### 1. `__get()`,`__set()`，`__isset()` `__unset()`方法（属性重载）


```php
public __set ( string $name , mixed $value ) : void
public __get ( string $name ) : mixed
public __isset ( string $name ) : bool
public __unset ( string $name ) : void
```

在给不可访问属性赋值时，[__set()](https://www.php.net/manual/zh/language.oop5.overloading.php#object.set) 会被调用。

读取不可访问属性的值时，[__get()](https://www.php.net/manual/zh/language.oop5.overloading.php#object.get) 会被调用。

当对不可访问属性调用 [isset()](https://www.php.net/manual/zh/function.isset.php) 或 [empty()](https://www.php.net/manual/zh/function.empty.php) 时，[__isset()](https://www.php.net/manual/zh/language.oop5.overloading.php#object.isset) 会被调用。

当对不可访问属性调用 [unset()](https://www.php.net/manual/zh/function.unset.php) 时，[__unset()](https://www.php.net/manual/zh/language.oop5.overloading.php#object.unset) 会被调用。

参数 `$name` 是指要操作的变量名称。[`__set()`](https://www.php.net/manual/zh/language.oop5.overloading.php#object.set) 方法的 `$value` 参数指定了 `$name` 变量的值。

属性重载只能在对象中进行。在静态方法中，这些魔术方法将不会被调用。所以这些方法都不能被 声明为 [static](https://www.php.net/manual/zh/language.oop5.static.php)。从 PHP 5.3.0 起, 将这些魔术方法定义为 *static* 会产生一个警告。

> **Note**:
>
> 因为 PHP 处理赋值运算的方式，[__set()](https://www.php.net/manual/zh/language.oop5.overloading.php#object.set) 的返回值将被忽略。类似的, 在下面这样的链式赋值中，[__get()](https://www.php.net/manual/zh/language.oop5.overloading.php#object.get) 不会被调用：
>
 ```
 $a = $obj->b = 8; 
 ```
> 在除 [isset()](https://www.php.net/manual/zh/function.isset.php) 外的其它语言结构中无法使用重载的属性，这意味着当对一个重载的属性使用 [empty()](https://www.php.net/manual/zh/function.empty.php) 时，重载魔术方法将不会被调用。
>
> 为避开此限制，必须将重载属性赋值到本地变量再使用 [empty()](https://www.php.net/manual/zh/function.empty.php)。

**Example #1 使用 __get()，__set()，__isset() 和 __unset() 进行属性重载**

```php
<?php
  class PropertyTest {     /**  被重载的数据保存在此  */  
  private $data = array();      /**  重载不能被用在已经定义的属性  */  
  public $declared = 1;     /**  只有从类外部访问这个属性时，重载才会发生 */   
  private $hidden = 2;   
  public function __set($name, $value)    
  {        
    echo "Setting '$name' to '$value'\n";     
    $this->data[$name] = $value;    
  }   
  public function __get($name) 
  {       
    echo "Getting '$name'\n";    
    if (array_key_exists($name, $this->data)) {      
      return $this->data[$name];      
    }        
    $trace = debug_backtrace();    
    trigger_error(          
      'Undefined property via __get(): ' . $name .     
      ' in ' . $trace[0]['file'] .        
      ' on line ' . $trace[0]['line'],   
      E_USER_NOTICE);      
    return null;   
  }    /**  PHP 5.1.0之后版本 */   
  public function __isset($name)  
  {        echo "Is '$name' set?\n";   
   return isset($this->data[$name]);   
  }    /**  PHP 5.1.0之后版本 */   
  public function __unset($name)    
  {        echo "Unsetting '$name'\n";  
   unset($this->data[$name]);   
  }    /**  非魔术方法  */  
  public function getHidden()    
  {      
    return $this->hidden;
  }
}
echo "<pre>\n";
$obj = new PropertyTest;
$obj->a = 1;echo $obj->a . "\n\n";
var_dump(isset($obj->a));
unset($obj->a);
var_dump(isset($obj->a));
echo "\n";echo $obj->declared . "\n\n";
echo "Let's experiment with the private property named 'hidden':\n";
echo "Privates are visible inside the class, so __get() not used...\n";
echo $obj->getHidden() . "\n";
echo "Privates not visible outside of class, so __get() is used...\n";
echo $obj->hidden . "\n";?>
```

以上例程会输出：

```
Setting 'a' to '1'
Getting 'a'
1

Is 'a' set?
bool(true)
Unsetting 'a'
Is 'a' set?
bool(false)

1

Let's experiment with the private property named 'hidden':
Privates are visible inside the class, so __get() not used...
2
Privates not visible outside of class, so __get() is used...
Getting 'hidden'


Notice:  Undefined property via __get(): hidden in <file> on line 70 in <file> on line 29
```

### 2. `__call()`,`__callStatic`([方法重载](https://www.php.net/manual/zh/language.oop5.overloading.php#language.oop5.overloading.methods))

public __call ( string `$name` , array `$arguments` ) : [mixed](https://www.php.net/manual/zh/language.pseudo-types.php#language.types.mixed)

public static __callStatic ( string `$name` , array `$arguments` ) : [mixed](https://www.php.net/manual/zh/language.pseudo-types.php#language.types.mixed)

在对象中调用一个不可访问方法时，[__call()](https://www.php.net/manual/zh/language.oop5.overloading.php#object.call) 会被调用。

在静态上下文中调用一个不可访问方法时，[__callStatic()](https://www.php.net/manual/zh/language.oop5.overloading.php#object.callstatic) 会被调用。

$name 参数是要调用的方法名称。$arguments 参数是一个枚举数组，包含着要传递给方法 $name 的参数。

**Example #2 使用 __call() 和 __callStatic() 对方法重载**

```php
<?php
  class MethodTest 
	{   
  	public function __call($name, $arguments)  
    {        // 注意: $name 的值区分大小写   
      echo "Calling object method '$name' "    
        . implode(', ', $arguments). "\n";   
    	}    /**  PHP 5.3.0之后版本  */ 
  		public static function __callStatic($name, $arguments)    
      {        // 注意: $name 的值区分大小写   
        echo "Calling static method '$name' "          
          . implode(', ', $arguments). "\n";  
      }
	}
$obj = new MethodTest;$obj->runTest('in object context');
MethodTest::runTest('in static context');  // PHP 5.3.0之后版本
?>
```

以上例程会输出：

```
Calling object method 'runTest' in object context
Calling static method 'runTest' in static context
```

### 3. [__sleep()](https://www.php.net/manual/zh/language.oop5.magic.php#object.sleep) 和 [__wakeup()](https://www.php.net/manual/zh/language.oop5.magic.php#object.wakeup) [¶](https://www.php.net/manual/zh/language.oop5.magic.php#language.oop5.magic.sleep)

```php
public __sleep ( void ) : array

__wakeup ( void ) : void
```

[serialize()](https://www.php.net/manual/zh/function.serialize.php) 函数会检查类中是否存在一个魔术方法 [__sleep()](https://www.php.net/manual/zh/language.oop5.magic.php#object.sleep)。如果存在，该方法会先被调用，然后才执行序列化操作。此功能可以用于清理对象，并返回一个包含对象中所有应被序列化的变量名称的数组。如果该方法未返回任何内容，则 **NULL** 被序列化，并产生一个 **E_NOTICE** 级别的错误。

> **Note**:
>
> [__sleep()](https://www.php.net/manual/zh/language.oop5.magic.php#object.sleep) 不能返回父类的私有成员的名字。这样做会产生一个 **E_NOTICE** 级别的错误。可以用 **Serializable** 接口来替代。

[__sleep()](https://www.php.net/manual/zh/language.oop5.magic.php#object.sleep) 方法常用于提交未提交的数据，或类似的清理操作。同时，如果有一些很大的对象，但不需要全部保存，这个功能就很好用。

与之相反，[unserialize()](https://www.php.net/manual/zh/function.unserialize.php) 会检查是否存在一个 [__wakeup()](https://www.php.net/manual/zh/language.oop5.magic.php#object.wakeup) 方法。如果存在，则会先调用 *__wakeup* 方法，预先准备对象需要的资源。

[__wakeup()](https://www.php.net/manual/zh/language.oop5.magic.php#object.wakeup) 经常用在反序列化操作中，例如重新建立数据库连接，或执行其它初始化操作。

**Example #1 Sleep 和 wakeup**

```php
<?php 
class Connection 
{    
	protected $link;   
  private $server, $username, $password, $db;    
  public function __construct($server, $username, $password, $db)  
  {       
    $this->server = $server;   
    $this->username = $username;    
    $this->password = $password;       
    $this->db = $db;       
    $this->connect();  
  }       
  private function connect()   
  {     
    $this->link = mysql_connect($this->server, $this->username, $this->password);        		    	
    mysql_select_db($this->db, $this->link);   
  }       
  public function __sleep()  
  {       
    return array('server', 'username', 'password', 'db');   
  }       
  public function __wakeup()   
  {        
    $this->connect();    
  }
}
?>
```

### 4. [__toString()](https://www.php.net/manual/zh/language.oop5.magic.php#object.tostring)[ ¶](https://www.php.net/manual/zh/language.oop5.magic.php#language.oop5.magic.tostring)

public __toString ( void ) : string

[__toString()](https://www.php.net/manual/zh/language.oop5.magic.php#object.tostring) 方法用于一个类被当成字符串时应怎样回应。例如 *echo $obj;* 应该显示些什么。此方法必须返回一个字符串，否则将发出一条 **E_RECOVERABLE_ERROR** 级别的致命错误。

Warning

不能在 [__toString()](https://www.php.net/manual/zh/language.oop5.magic.php#object.tostring) 方法中抛出异常。这么做会导致致命错误。

**Example #2 简单示例**

```
<?php// Declare a simple classclass TestClass{    public $foo;    public function __construct($foo)     {        $this->foo = $foo;    }    public function __toString() {        return $this->foo;    }}$class = new TestClass('Hello');echo $class;?>
```

以上例程会输出：

```
Hello
```

需要指出的是在 PHP 5.2.0 之前，[__toString()](https://www.php.net/manual/zh/language.oop5.magic.php#object.tostring) 方法只有在直接使用于 [echo](https://www.php.net/manual/zh/function.echo.php) 或 [print](https://www.php.net/manual/zh/function.print.php) 时才能生效。PHP 5.2.0 之后，则可以在任何字符串环境生效（例如通过 [printf()](https://www.php.net/manual/zh/function.printf.php)，使用 *%s* 修饰符），但不能用于非字符串环境（如使用 *%d* 修饰符）。自 PHP 5.2.0 起，如果将一个未定义 [__toString()](https://www.php.net/manual/zh/language.oop5.magic.php#object.tostring) 方法的对象转换为字符串，会产生 **E_RECOVERABLE_ERROR** 级别的错误。

### 5. [__invoke()](https://www.php.net/manual/zh/language.oop5.magic.php#object.invoke)[ ¶](https://www.php.net/manual/zh/language.oop5.magic.php#language.oop5.magic.invoke)

`__invoke ([ $... ] ) : mixed`

当尝试以调用函数的方式调用一个对象时，[__invoke()](https://www.php.net/manual/zh/language.oop5.magic.php#object.invoke) 方法会被自动调用。

> **Note**:
>
> 本特性只在 PHP 5.3.0 及以上版本有效。

**Example #3 使用 __invoke()**

```php
<?php
   class CallableClass {   
    function __invoke($x)
    {       
      var_dump($x);
    }
  }
  $obj = new CallableClass;$obj(5);
  var_dump(is_callable($obj));
?>
```

以上例程会输出：

```
int(5)
bool(true)
```

### 6. [__set_state()](https://www.php.net/manual/zh/language.oop5.magic.php#object.set-state)[ ¶](https://www.php.net/manual/zh/language.oop5.magic.php#language.oop5.magic.set-state)

static __set_state ( array `$properties` ) : object

自 PHP 5.1.0 起当调用 [var_export()](https://www.php.net/manual/zh/function.var-export.php) 导出类时，此[静态](https://www.php.net/manual/zh/language.oop5.static.php) 方法会被调用。

本方法的唯一参数是一个数组，其中包含按 *array('property' => value, ...)* 格式排列的类属性。

**Example #4 使用 __set_state()>（PHP 5.1.0 起）**

```php
<?php 
  class A{   
  public $var1;   
  public $var2;    
  public static function __set_state($an_array) // As of PHP 5.1.0   
  {        
    $obj = new A;       
    $obj->var1 = $an_array['var1'];       
    $obj->var2 = $an_array['var2'];        
    return $obj;   
  }
}
$a = new A;
$a->var1 = 5;
$a->var2 = 'foo';
eval('$b = ' . var_export($a, true) . ';');	// $b = A::__set_state(array(                                            																					 	 //    'var1' => 5,                                   
																						//    'var2' => 'foo',                                            
																						// ));
var_dump($b);?>
```

以上例程会输出：

```
object(A)#2 (2) {
  ["var1"]=>
  int(5)
  ["var2"]=>
  string(3) "foo"
}
```

### 7. [__debugInfo()](https://www.php.net/manual/zh/language.oop5.magic.php#object.debuginfo)[ ¶](https://www.php.net/manual/zh/language.oop5.magic.php#language.oop5.magic.debuginfo)

`__debugInfo ( void ) : array`

当 `var_dump()`被使用时，如果此方法被定义了就会调用此方法。如果这个方法没有被定义`var_dump()`会显示类的全部信息。

This feature was added in PHP 5.6.0.

**Example #5 Using __debugInfo()**

```php
<?php 
  class C {  
    private $prop;   
    public function __construct($val)
    {  
      $this->prop = $val;   
    }    
    public function __debugInfo()
    {       
      return [ 
        'propSquared' => $this->prop ** 2,    //幂运算 
      ];   
 	 	}
	}
	var_dump(new C(42));
?>
```

以上例程会输出：

```
object(C)#1 (1) {
  ["propSquared"]=>
  int(1764)
}
```