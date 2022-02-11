# Vehicle database for Laravel
[![StyleCI](https://styleci.io/repos/74467821/shield?branch=master)](https://styleci.io/repos/74467821)

This package allows you to conveniently use the car database in your projects, implement search hints, link a car to your models, etc.

The package includes only an example of the base structure, the car base itself is not included in the package, but is purchased separately on the site https://auto.basebuy.ru/.

At the moment, the package **does not support** vehicle characteristics and the Basebuy.ru vehicle database REST API

## Content
* Installation
    * Database import
* Usage

## Installation
You can install this package with composer:

```
composer require bigperson/auto-base-buy
```

### Vehicle base import
First you need to create the necessary tables in the database, to do this, import the migration files from the package using artisan:

```
 php artisan vendor:publish --tag=migrations --provider="Bigperson\AutoBaseBuy\AutoBaseBuyServiceProvider"
```
Then you need to apply the migrations:
```
php artisan migrate
```

Next, you need to import seeds:

```
 php artisan vendor:publish --tag=seeds --provider="Bigperson\AutoBaseBuy\AutoBaseBuyServiceProvider"
```

И regenerate autoload.php: `composer dump-autoload`

Import csv files will be created in database/csv/*. They will need to be replaced with original ones after purchase at https://auto.basebuy.ru/.

Next, you need to apply seeds:
```
php artisan db:seed --class=AutoBusyBuySeeder
```

## Usage

Using the package is quite simple. You can call models in controllers:
```php
namespace App\Http\Controllers;

use Bigperson\AutoBaseBuy\Models\CarMark;

class Controller
{
    protected function show($id){

        $mark = CarMark::findOrFail($id);
        
    }
}
```

Link your models with cars by brand, model, series, etc., of course, first you need to decide on the type of connection and create the necessary tables or columns in the tables of your models:

```php
namespace App;

use Bigperson\AutoBaseBuy\Models\CarModification;

class User extends Model
{
     public function car()
     {
         return $this->belongsTo(CarModification::class, 'id_car_modification');
     }
}
```

You can also override models and extend them, for example by adding an accessor:
```php
namespace App;

use Bigperson\AutoBaseBuy\Models\CarModification as BaseCarModification;

class CarModification extends BaseCarModification
{
    /**
     * Get the full name of the car, including make, model, years of manufacture, series
     * @return string
     */
    public function getFullNameAttribute()
    {
        $string = $this->carModel->carMark->name;
        $string .= ' '.$this->carModel->name;
        $string .= ' '.$this->carSerie->name;
        $string .= ' '.$this->carSerie->carGeneration->name;
        $string .= ' ('.$this->carSerie->carGeneration->year_begin.'-'.$this->carSerie->carGeneration->year_end.')';
        $string .= ' '.$this->name;

        return $string;
    }
}
```


##License

This package (not including the database) is open source under the [MIT license](https://opensource.org/licenses/MIT).
