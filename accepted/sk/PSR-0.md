Štandart Autonačítavania (Autoloading)
====================

> **Zastaran** - Od 2014-10-21 PSR-0 je označený ako zastaralý. [PSR-4] je odporúčanou
alternatívou.

[PSR-4]: http://www.php-fig.org/psr/psr-4/

Nasledujúci dokument obsahuje povinné požiadavky štandartov kódu, ktoré
je nutné dodržať pre jednoduchosť prevádzkovania autoloaderu.

Povinné
---------

* Plne obsiahnutý namespace a triedy musia mať nasledujúcu štruktúru
  `\<Meno Autora>\(<Namespace>\)*<Meno triedy>`
* Každý namespace musí mať nad sebou v hierarchii ("Meno Autora").
* Každý namespace môže mať ľubovolný počet sub-namespacov-ov.
* Každý namespace-ový oddeľovač je zmenení na `DIRECTORY_SEPARATOR`, keď
  sa načítava z súborového systému.
* Každý znak `_` v mene Triedy je zmenení na
  `DIRECTORY_SEPARATOR`. Znak `_` nemá nejaký hlbší zmysel v
  namespace.
* The fully-qualified namespace and class is suffixed with `.php` when
  loading from the file system.
* Alphabetic characters in vendor names, namespaces, and class names may
  be of any combination of lower case and upper case.

Examples
--------

* `\Doctrine\Common\IsolatedClassLoader` => `/path/to/project/lib/vendor/Doctrine/Common/IsolatedClassLoader.php`
* `\Symfony\Core\Request` => `/path/to/project/lib/vendor/Symfony/Core/Request.php`
* `\Zend\Acl` => `/path/to/project/lib/vendor/Zend/Acl.php`
* `\Zend\Mail\Message` => `/path/to/project/lib/vendor/Zend/Mail/Message.php`

Underscores in Namespaces and Class Names
-----------------------------------------

* `\namespace\package\Class_Name` => `/path/to/project/lib/vendor/namespace/package/Class/Name.php`
* `\namespace\package_name\Class_Name` => `/path/to/project/lib/vendor/namespace/package_name/Class/Name.php`

The standards we set here should be the lowest common denominator for
painless autoloader interoperability. You can test that you are
following these standards by utilizing this sample SplClassLoader
implementation which is able to load PHP 5.3 classes.

Example Implementation
----------------------

Below is an example function to simply demonstrate how the above
proposed standards are autoloaded.

```php
<?php

function autoload($className)
{
    $className = ltrim($className, '\\');
    $fileName  = '';
    $namespace = '';
    if ($lastNsPos = strrpos($className, '\\')) {
        $namespace = substr($className, 0, $lastNsPos);
        $className = substr($className, $lastNsPos + 1);
        $fileName  = str_replace('\\', DIRECTORY_SEPARATOR, $namespace) . DIRECTORY_SEPARATOR;
    }
    $fileName .= str_replace('_', DIRECTORY_SEPARATOR, $className) . '.php';

    require $fileName;
}
spl_autoload_register('autoload');
```

SplClassLoader Implementation
-----------------------------

The following gist is a sample SplClassLoader implementation that can
load your classes if you follow the autoloader interoperability
standards proposed above. It is the current recommended way to load PHP
5.3 classes that follow these standards.

* [http://gist.github.com/221634](http://gist.github.com/221634)
