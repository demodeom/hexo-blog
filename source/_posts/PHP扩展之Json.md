---
title: PHP扩展之Json
date: 2021-08-15 20:06:25
tags:
    - Json
categories:
    - PHP
---

PHP扩展之Json

<!-- more -->

## 函数

- json_decode — 对 JSON 格式的字符串进行解码
- json_encode — 对变量进行 JSON 编码
- json_last_error_msg — Returns the error string of the last json_encode() or json_decode() call
- json_last_error — 返回最后发生的错误

### json_decode



**说明**

`json_decode(string $json,bool $assoc = false,int $depth = 512,int $options = 0): mixed`

接受一个 JSON 编码的字符串并且把它转换为 PHP 变量

**参数**


- json 待解码的 json string 格式的字符串。 这个函数仅能处理 UTF-8 编码的数据。
    - 注意: PHP 实现了 JSON 的一个超集，参考 » RFC 7159.
- assoc 当该参数为 true 时，将返回 array 而非 object 。
- depth 指定递归深度。
- options 
    - JSON_BIGINT_AS_STRING, 
    - JSON_INVALID_UTF8_IGNORE, 
    - JSON_INVALID_UTF8_SUBSTITUTE, 
    - JSON_OBJECT_AS_ARRAY, 
    - JSON_THROW_ON_ERROR

这些常量的行为在JSON constants页面有进一步描述。


**返回值**


通过恰当的 PHP 类型返回在 json 中编码的数据。值true, false 和 null 会相应地返回 true, false 和 null。 如果 json 无法被解码， 或者编码数据深度超过了递归限制的话，将会返回null 。

### json_encode


**说明**

`json_encode(mixed $value, int $options = 0, int $depth = 512): string|false`

返回字符串，包含了 value 值 JSON 形式的表示。

编码受传入的 options 参数影响，此外浮点值的编码依赖于 serialize_precision。

**参数**


- value 待编码的 value ，除了 资源(resource) 类型之外，可以为任何数据类型。
- options 由以下常量组成的二进制掩码： 
    - JSON_FORCE_OBJECT
    - JSON_HEX_QUOT
    - JSON_HEX_TAG
    - JSON_HEX_AMP
    - JSON_HEX_APOS
    - JSON_INVALID_UTF8_IGNORE
    - JSON_INVALID_UTF8_SUBSTITUTE
    - JSON_NUMERIC_CHECK
    - JSON_PARTIAL_OUTPUT_ON_ERROR
    - JSON_PRESERVE_ZERO_FRACTION
    - JSON_PRETTY_PRINT
    - JSON_UNESCAPED_LINE_TERMINATORS
    - JSON_UNESCAPED_SLASHES
    - JSON_UNESCAPED_UNICODE
    - JSON_THROW_ON_ERROR。 
    - 关于 JSON 常量详情参考 JSON 常量页面。
- depth 设置最大深度。 必须大于0。


**返回值**


成功则返回 JSON 编码的 string 或者在失败时返回 false 。


#### json_last_error_msg



**说明**

`json_last_error_msg(): string`

Returns the error string of the last json_encode() or json_decode() call, which did not specify JSON_THROW_ON_ERROR.

**参数**

此函数没有参数。

**返回值**


Returns the error message on success, or "No error" if no error has occurred.


#### json_last_error



**说明**

`json_last_error(): int`

如果有，返回 JSON 编码解码时最后发生的错误。

**参数**

此函数没有参数。

**返回值**


返回一个整型（integer），这个值会是以下的常量之一：



JSON 错误码 

|常量     |含义  |可用性|
|:---     |:---  |:---|
|JSON_ERROR_NONE|     没有错误发生  | |
|JSON_ERROR_DEPTH|    到达了最大堆栈深度   | |
|JSON_ERROR_STATE_MISMATCH|   无效或异常的 JSON   |   |
|JSON_ERROR_CTRL_CHAR|    控制字符错误，可能是编码不对 |  |
|JSON_ERROR_SYNTAX|   语法错误   |  |
|JSON_ERROR_UTF8|     异常的 UTF-8 字符，也许是因为不正确的编码。   |PHP 5.3.3|
|JSON_ERROR_RECURSION|    One or more recursive references in the value to be encoded    | PHP 5.5.0|
|JSON_ERROR_INF_OR_NAN|   One or more NAN or INF values in the value to be encoded    |PHP 5.5.0|
|JSON_ERROR_UNSUPPORTED_TYPE|     指定的类型，值无法编码。    |PHP 5.5.0|
|JSON_ERROR_INVALID_PROPERTY_NAME|    指定的属性名无法编码。    | PHP 7.0.0|
|JSON_ERROR_UTF16|    畸形的 UTF-16 字符，可能因为字符编码不正确。  |PHP 7.0.0|