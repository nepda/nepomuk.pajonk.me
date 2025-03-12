---
layout: post
title: "TIL: OpenAPI - Value expected to be 'array', but 'array' given."
tags:
  - openapi
  - php
  - software
  - til
---

# OpenAPI - Value expected to be 'array', but 'array' given.

Today I learned, with [`league/openapi-psr7-validator`](https://github.com/thephpleague/openapi-psr7-validator) 
array is not array - as in PHP.

This is a real array:
```php
$array = [
    0 => [ 'id' => 'zero' ],
    1 => [ 'id' => 'one' ],
    2 => [ 'id' => 'two' ]
];
```

This is a list/associative array:
```php
$array = [
    1 => [ 'id' => 'one' ],
    2 => [ 'id' => 'two' ]
];
```

This means, if you define your property like this:
```php
#[OA\Property(items: new OA\Items(ref: YourData::class))]
public array $payload
```

And assign an associative array, you will get a validation error:

```text
Value expected to be 'array', 'array' given.
```

Because `league/openapi-psr7-validator` checks this like so:

```php
// vendor/league/openapi-psr7-validator/src/Schema/Keywords/Type.php:62
if (! is_array($data) || ArrayHelper::isAssoc($data)) {
    break;
}
```

One solution, depending on your situation and actual code could be:
```php
$data = array_values($data);
```
