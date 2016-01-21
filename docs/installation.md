# Installation

Installing Particle\Filter is very easy, if you're using [composer](http://getcomposer.com). 
If you haven't done so, install composer, and use **composer require** to install Particle\Filter.

```bash
curl -sS https://getcomposer.org/installer | php
php composer.phar require particle/filter
```

## First usage

Make sure you include `vendor/autoload.php` in your application, and then create your first filter 
instance:

```php
use Particle\Filter\Filter;

$filter = new Filter;

// filter rules
$filter->all()->trim();
$filter->value('first_name')->upperFirst();

$result = $filter->filter([
    // data to filter
]);
```
