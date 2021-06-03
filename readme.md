 # Sales-API-REST
 
- Diagrama relacional
<img src="https://trello-attachments.s3.amazonaws.com/5b014dcaf4507eacfc1b4540/5b014de4bc1b8dcc70d83031/6a3a5051307f57b023a2cd7de15dd2ca/image.png" height="300" width="500">

- Todos los atributos que se pueden configurar en un modelo
```
<project>\vendor\laravel\framework\src\Illuminate\Database\Eloquent\Model.php
+-------------------------------+-------------------------------------+---------------------------------------+
| protected $connection;        | protected $with = [];               | protected static $resolver;           |
| protected $table;             | protected $withCount = [];          | protected static $dispatcher;         |
| protected $primaryKey = 'id'; | protected $perPage = 15;            | protected static $booted = [];        |
| protected $keyType = 'int';   | public $exists = false;             | protected static $globalScopes = [];  |
| public $incrementing = true;  | public $wasRecentlyCreated = false; | protected static $ignoreOnTouch = []; |
+-------------------------------+-------------------------------------+---------------------------------------+
```
 [Controladores complejos que dependen de Seller](https://escuela.it/cursos/curso-de-desarrollo-de-api-restful-con-laravel/clase/controladores-complejos-que-dependen-de-seller)
- **comando:** `$ php artisan make:controller Seller/SellerTransactionController -p Seller -m Transaction`
- **postman:** `http://laravelapi:8000/sellers/15/transactions`
```php
//<project>/app/Http/Controllers/Seller/SellerTransactionController.php
    $oCollection = $seller->products()  //products.seller_id = s.id
            ->whereHas("transactions")  //products.id = transactions.product_id
            ->with("transactions")      //transactions.*
            ->get()
            ->pluck("transactions")     //quita el indice asociativo
            ->collapse()                //distinct transactions.*
    ;
    //dd($oCollection);
    return $this->showAll($oCollection);
```
```sql
-- las transacciones de un vendedor
SELECT DISTINCT t.*
,p.seller_id
FROM transactions t
INNER JOIN products p
ON t.product_id = p.id
WHERE 1=1
AND p.seller_id = 15
-- ORDER BY 7

+-----+---------------------+---------------------+----------+------------+----------+-----------+
| id  |     created_at      |     updated_at      | quantity | product_id | buyer_id | seller_id |
+-----+---------------------+---------------------+----------+------------+----------+-----------+
| 370 | 2018-08-23 22:52:01 | 2018-08-23 22:52:01 |        2 |        190 |      241 |        15 |
| 806 | 2018-08-23 22:52:06 | 2018-08-23 22:52:06 |        1 |        190 |     1222 |        15 |
| 853 | 2018-08-23 22:52:06 | 2018-08-23 22:52:06 |        1 |        190 |      894 |        15 |
+-----+---------------------+---------------------+----------+------------+----------+-----------+

{"data":[
    {"id":370,"created_at":"2018-08-23 22:52:01","updated_at":"2018-08-23 22:52:01","quantity":"2"
    ,"product_id":"190","buyer_id":"241"},{"id":806,"created_at":"2018-08-23 22:52:06"
    ,"updated_at":"2018-08-23 22:52:06","quantity":"1","product_id":"190","buyer_id":"1222"}
    ,{"id":853,"created_at":"2018-08-23 22:52:06","updated_at":"2018-08-23 22:52:06","quantity":"1"
    ,"product_id":"190","buyer_id":"894"}]}
```

## DESPLIEGUE EN PRODUCCIÓN
- Incluir archivo `usererrorhandler.php` si fuera necesario
- [Instalar laravel en 1n1 uf4no ingles](http://www.uf4no.com/articles/guide-to-deploy-laravel-5-app-to-shared-hosting-1and1-9)
- [Instalar laravel en 1n1 laracasts ingles](https://laracasts.com/discuss/channels/servers/install-laravel-in-1and1-servers)
    - **comando: `$mkdir -p hello/goodbye`** **-p** indica que si no existe la carpeta padre la crea
    - **comando: `ls -s`** **-s** crea un link **s**imbolico al archivo [`ls`](http://manpages.ubuntu.com/manpages/xenial/man1/ln.1.html)
    - **comando: `source ~/.profile`** Ejecuta el archivo `.profile` que es parte de un bash
    - **comando: `curl -sS https://getcomposer.org/installer | php`** El comando curl hace una transferencia de archivos `-s`: silent, `S`: show error `| php`: ???ni idea
    - **comando: `php composer.phar install`**
- Crear archivo: **.env**
    - Retocar el dominio
- Al ejecutar da **error**: `No application encryption key has been specified`
- Ejecutar **comando:** `php artisan key:generate`
    - Escribe en `.env`, `APP_KEY=base64:aNZ+S0Rq3xNuqHOemgYdh3jfEnXEQkox6IIID5VFbqs=`
```ssh
$ php artisan key:generate
Application key [base64:aNZ+S0Rq3xNuqHOemgYdh3jfEnXEQkox6IIID5VFbqs=] set successfully.
```
```ssh
# ejecutar 
source ~/.profile
php artisan tinker
```
