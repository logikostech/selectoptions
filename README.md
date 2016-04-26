# Logikos\Forms\SelectOptions
Phalcon plugin to query selectbox options.  Works well as a backend to select2

## Installation

### Installing via Composer

Install Composer in a common location or in your project:

```bash
curl -s http://getcomposer.org/installer | php
```

create or edit the `composer.json` file as follows:

```json
{
    "repositories": [
        {
            "type": "git",
            "url": "https://github.com/logikostech/selectoptions"
        }
    ],
    "require": {
        "logikostech/selectoptions": "dev-master"
    }
}
```

Run the composer installer:

```bash
$ php composer.phar install
```

### Installing via GitHub

Just clone the repository in a common location or inside your project:

```bash
git clone https://github.com/logikostech/selectoptions.git
```

## Autoloading

Add or register the following namespace strategy to your `Phalcon\Loader`:

```php
$loader = new Phalcon\Loader();

$loader->registerNamespaces([
    'Logikos\Forms' => '/path/to/this/repo/src/'
]);

$loader->register();
```

## Usage

### Add to Dependency Injector
```php
$di = new Phalcon\Di();

$di->set('selectOptions',function($modelname,$options=null) {
  $e = new EventsManager;
  $s = new SelectOptions($modelname,$options);
  $s->setEventsManager($e);
  return $s;
});

Phalcon\Di::setDefault($di);
```

### Optional method in BaseModel for easy access
```php
class BaseModel extends Phalcon\Mvc\Model {
  public static function getSelectOptions($options=null) {
    
    $selectoptions = Phalcon\Di::getDefault()->get(
        'selectOptions',
        [static::class,$options]
    );
    
    if (defined(static::class.'::ID_COLUMN'))
      $selectoptions->setIdColumn(static::ID_COLUMN);
    
    if (defined(static::class.'::TEXT_COLUMN'))
      $selectoptions->setTextColumn(static::TEXT_COLUMN);
    
    return $selectoptions;
  }
}
```

### Use BaseModel Method from controller or anywhere
```php
public function robotOptionsAction() {
  /* @var $selectoptions \Logikos\Forms\SelectOptions */
  $request = new Phalcon\Http\Request();
  $selectoptions = Robots::getSelectOptions();
  
  $selectoptions
    ->options($request->get())   // good for select2 serverside, ex: ?search=Terminator
    ->setIdColumn('id')
    ->setTextColumn('name')
    ->addSelect([
      'price',
      'year'
    ])
    ->addConditions(
        "type = :type:",
        [
            'type' => 'mechanical',
        ]
    );
  $result = $selectoptions->result();
  
  $response = new Phalcon\Http\Response();
  $response->setJsonContent($result);
  $response->send();
}
```
