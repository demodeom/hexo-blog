---
title: PHP - 面向对象
date: 2021-08-12 07:18:12
tags:
    - 面向对象
categories:
    - PHP
---

PHP - 面向对象

<!-- more -->

## 类

类由变量 和 函数组成; 类中变量又叫做 `属性` `字段` `特征`

类中的函数又叫做方法

### 定义类

```php
class Car{

}
```

### 方法

```php
class Car{
	public function run()
	{
		echo "run";
	}
}
```

类内部通过 `$this` 调用内部的其它方法

```php
class Car{
	public function run()
	{
		echo "run";
	}

	public function stop()
	{
		$this->run();
		echo "stop";
	}
}
```

### 属性

```php
class Car{
	public $car_name;
}
```

类内部通过 `$this` 调用内部的属性

```php
class Car
{
	public $car_name;

	public function run()
	{
		echo $this->car_name . "run";
	}

	public function stop()
	{
		$this->run();
		echo "stop";
	}
}
```

### 常量

```php
class Car
{
	public $car_name;

	const PRICE = 999;
}
```

类内部通过 `self` 调用常量

```php
class Car
{
	public $car_name;

	const PRICE = 999;

	public function run()
	{
		echo $this->car_name . self::PRICE ."run";
	}
}
```

## 对象

类实例化之后， 得到对象

### 创建对象

```php
$my_car = new Car();
```

### 属性

对象调用属性

```php
echo $my_car->car_name;
```

对象对属性进行赋值

```php
$my_car->car_name = "TSLA";
```

对象调用常量

```php
echo $my_car::PRICE;
// 或者
echo Car::PRICE
```
