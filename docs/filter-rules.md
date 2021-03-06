# Included filter-rules

Particle\Filter tries to provide you the most common filters. An overview is listed below. If you want to add custom
filters, take a look at the callback filter-rule, or check out "Extending the Filter" in the menu.

* [alnum](#alnum)()
* [append](#append)($append)
* [bool](#bool)()
* [callback](#callback)($callable, $allowNotSet = false)
* [cut](#cut)($start, $length = null)
* [defaults](#defaults)($defaultValue)
* [each](#each)($callable)
* [encode](#encode)($toEncodingFormat = null, $fromEncodingFormat = null)
* [float](#float)()
* [int](#int)()
* [letters](#letters)()
* [lower](#lower)()
* [numberFormat](numberformat)($decimals, $decimalPoint, $thousandSeparator)
* [numbers](#numbers)()
* [prepend](#prepend)($prepend)
* [regexReplace](#regexreplace)($searchRegex, $replace)
* [remove](#remove)()
* [removeNull](#removenull)()
* [replace](#replace)($search, $replace)
* [string](#string)()
* [stripHtml](#striphtml)($excludeTags = null)
* [trim](#trim)($characters = null)
* [upper](#upper)()
* [upperFirst](#upperfirst)()

## Alnum

Filters everything but alphabetic numeric characters out of the value

```php
$f = new Filter;
$f->value('name')->alnum();
$result = $f->filter(['name' => '1!23-abc?']);
// array(1) { ["name"]=> string(6) "123abc" }
```

## Append

Appends a value to the end of the provided value.

```php
$f = new Filter;
$f->value('name')->append(' is your name.');
$result = $f->filter(['name' => 'John']);
// array(1) { ["name"]=> string(18) "John is your name." }
```

## Bool

Make sure the value is a boolean. Check [PHP Validate filters](http://php.net/manual/en/filter.filters.validate.php)
for the expected outcome.

```php
$f = new Filter;
$f->value('newsletter')->bool();
$result = $f->filter(['newsletter' => 'yes']);
// array(1) { ["newsletter"]=> bool(true) }
```

## Callback

Callback allows you to quickly add a custom filter, as you can provide a closure that manipulates the value.

The callable function will receive two parameters from the callback rule. First `$value`, holding the
currently filtered value for the current key. And second `$filterData`, holding all the data that is being
filtered by the filter.

```php
$f = new Filter;
$f->value('name')->callback(function($value, $filterData) {
    return '<strong>' . $value . '</strong>';
});
$result = $f->filter(['name' => 'John']);
// array(1) { ["name"]=> string(21) "<strong>John</strong>" }
```

Callback can also be used if a value is not set in the filter data. Make sure you set the second parameter
`$allowNotEmpty` to `true`, and you can always populate the value with a callback.

```php
$f = new Filter;
$f->value('year')->callback(function($value, $filterData) {
    return date('Y');
}, true);
$result = $f->filter([]); // note that no year is set
// array(1) { ["year"]=> string(4) "2016" }
```

## Cut

If you need to cut a string. Same usage as PHP substr.

```php
$f = new Filter;
$f->value('slug')->cut(0, 28);
$result = $f->filter(['slug' => 'my-very-super-extra-long-url-that-needs-to-be-cut]);
// array(1) { ["slug"]=> string(28) "my-very-super-extra-long-url" }
```

## Defaults

When there is no data present for a given value key, you can default to a given value.

```php
$f = new Filter;
$f->value('name')->defaults('Annonymous');
$result = $f->filter([]); // Note: no name is given
// array(1) { ["name"]=> string(10) "Annonymous" }
```

## Each

Filter nested repeated arrays, using a provided Filter instance in a closure.
Note that the values in the array must be arrays too, else the filter can't apply a new filter on each element.

```php
$f = new Filter;
$f->value('names')->each(function (Filter $filter) {
    $filter->value('name')->upperFirst();
});
$result = $f->filter([
    'names' => [
        ['name' => 'john'],
        ['name' => 'rick'],
    ],
]);
/**
 * array(1) {
 *     ["names"]=> array(2) {
 *         array(1) { ["name"]=> string(4) "John" }
 *         array(1) { ["name"]=> string(4) "Rick" }
 *     }
 * }
 */
```

## Encode

Makes sure that the given value is in a specific encoding format.

```php
$f = new Filter;
$f->value('text')->encode('Base64', 'UTF-8');
$result = $f->filter(['text' => 'hello']);
// array(1) { ["text"]=> string(8) "aGVsbG8=" }
```

if `$f->setEncodingFormat()` is set, you don't need to provide any parameters as the value encoding format will
be set to that.

## Float

Make sure the value is a float.

```php
$f = new Filter;
$f->value('value')->float();
$result = $f->filter(['value' => '123.123']);
// array(1) { ["value"]=> float(123.123) }
```

## Int

Make sure the value is a int.

```php
$f = new Filter;
$f->value('value')->int();
$result = $f->filter(['value' => '123.123']);
// array(1) { ["value"]=> int(123) }
```

## Letters

Filters everything but letters out of the value

```php
$f = new Filter;
$f->value('name')->letters();
$result = $f->filter(['name' => 'john99!']);
// array(1) { ["name"]=> string(4) "john" }
```

## Lower

Lowercase the full value.

```php
$f = new Filter;
$f->value('name')->lower();
$result = $f->filter(['name' => 'JOHN']);
// array(1) { ["name"]=> string(4) "john" }
```

## NumberFormat

Formats the numbers according to the configuration

```php
$f = new Filter;
$f->all()->numberFormat(2, '.', '');
$result = $f->filter([
    'price' => 5,
    'discount' => 2.92847,
]);
/**
 * array(2) {
 *     ["price"]=> string(4) "5.00"
 *     ["discount"]=> string(4) "2.93"
 * }
 */
```

## Numbers

Filters everything but numbers out of the value

```php
$f = new Filter;
$f->value('name')->numbers();
$result = $f->filter(['name' => '1a2s3']);
// array(1) { ["name"]=> string(3) "123" }
```

## Prepend

Appends a value to the end of the provided value.

```php
$f = new Filter;
$f->value('name')->prepend('Hello ');
$result = $f->filter(['name' => 'John']);
// array(1) { ["name"]=> string(10) "Hello John" }
```

## RegexReplace

Replace all matches with a replacement in the value.

```php
$f = new Filter;
$f->value('value')->regexReplace('/[^a-zA-Z0-9\-]/', '');
$result = $f->filter(['value' => '!!!l!#o?*l&&']);
// array(1) { ["value"]=> string(3) "lol" }
```

## Remove

This rule makes sure a key gets removed from your resulting array.

```php
$f = new Filter;
$f->value('value')->remove();
$result = $f->filter(['value' => 'hello']);
// array(0) {}
```

## RemoveNull

This rule makes sure a key gets removed from your resulting array when the value is null.

```php
$f = new Filter;
$f->values(['value1', 'value2'])->removeNull();
$result = $f->filter(['value1' => 'hello', 'value2' => null]);
// array(1) { ["value1"]=> string(5) "hello" }
```

## Replace

Replace a needle for a replacement in the value.

```php
$f = new Filter;
$f->value('value')->replace(' ', '-');
$result = $f->filter(['name' => 'hello im john']);
// array(1) { ["name"]=> string(13) "hello-im-john" }
```

## String

Make sure the value is a string.

```php
$f = new Filter;
$f->value('value')->string();
$result = $f->filter(['value' => 50]);
// array(1) { ["value"]=> string(2) "50" }
```

## StripHtml

Strip all tags from the value.

```php
$f = new Filter;
$f->value('name')->stripHtml();
$result = $f->filter(['name' => '<p><strong>John</strong></p>']);
// array(1) { ["name"]=> string(4) "John" }
```

Exclude some tags:

```php
$f = new Filter;
$f->value('name')->stripHtml('<strong>');
$result = $f->filter(['name' => '<p><strong>John</strong></p>']);
// array(1) { ["name"]=> string(21) "<strong>John</strong>" }
```

## Trim

Strip all 'white-space' characters from the beginning and end of the string.

```php
$f = new Filter;
$f->value('name')->trim();
$result = $f->filter(['name' => ' John ']);
// array(1) { ["name"]=> string(4) "John" }
```

You can also provide the specific characters that you want to strip.

```php
$f = new Filter;
$f->value('name')->trim("\s");
$result = $f->filter(['name' => ' John ']);
// array(1) { ["name"]=> string(4) "John" }
```

## Upper

Uppercase the full value.

```php
$f = new Filter;
$f->value('name')->upper();
$result = $f->filter(['name' => 'john']);
// array(1) { ["name"]=> string(4) "JOHN" }
```

## UpperFirst

Uppercase the first character of the value.

```php
$f = new Filter;
$f->value('name')->upperFirst();
$result = $f->filter(['name' => 'john']);
// array(1) { ["name"]=> string(4) "John" }
```
