# Helperek

- [Bevezetés](#introduction)
- [Elérhető Helperek](#available-methods)

<a name="introduction"></a>
## Bevezetés

A Laravel különböző globális "helper" (segéd) függvényeket tartalmaz. Ezek közül sokat a keretrendszer használ, de te is felhasználhatod őket az alkalmazásodhoz.

<a name="available-methods"></a>
## Elérhető Helperek

<style>
    .collection-method-list > p {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        column-gap: 2em; -moz-column-gap: 2em; -webkit-column-gap: 2em;
    }

    .collection-method-list a {
        display: block;
    }
</style>

### Tömbök

<div class="collection-method-list" markdown="1">

[array_add](#method-array-add)
[array_collapse](#method-array-collapse)
[array_divide](#method-array-divide)
[array_dot](#method-array-dot)
[array_except](#method-array-except)
[array_first](#method-array-first)
[array_flatten](#method-array-flatten)
[array_forget](#method-array-forget)
[array_get](#method-array-get)
[array_has](#method-array-has)
[array_last](#method-array-last)
[array_only](#method-array-only)
[array_pluck](#method-array-pluck)
[array_prepend](#method-array-prepend)
[array_pull](#method-array-pull)
[array_set](#method-array-set)
[array_sort](#method-array-sort)
[array_sort_recursive](#method-array-sort-recursive)
[array_where](#method-array-where)
[head](#method-head)
[last](#method-last)
</div>

### Elérési Utak

<div class="collection-method-list" markdown="1">

[app_path](#method-app-path)
[base_path](#method-base-path)
[config_path](#method-config-path)
[database_path](#method-database-path)
[elixir](#method-elixir)
[public_path](#method-public-path)
[resource_path](#method-resource-path)
[storage_path](#method-storage-path)

</div>

### Stringek

<div class="collection-method-list" markdown="1">

[camel_case](#method-camel-case)
[class_basename](#method-class-basename)
[e](#method-e)
[ends_with](#method-ends-with)
[snake_case](#method-snake-case)
[str_limit](#method-str-limit)
[starts_with](#method-starts-with)
[str_contains](#method-str-contains)
[str_finish](#method-str-finish)
[str_is](#method-str-is)
[str_plural](#method-str-plural)
[str_random](#method-str-random)
[str_singular](#method-str-singular)
[str_slug](#method-str-slug)
[studly_case](#method-studly-case)
[title_case](#method-title-case)
[trans](#method-trans)
[trans_choice](#method-trans-choice)

</div>

### URL-ek

<div class="collection-method-list" markdown="1">

[action](#method-action)
[asset](#method-asset)
[secure_asset](#method-secure-asset)
[route](#method-route)
[url](#method-url)

</div>

### Vegyes

<div class="collection-method-list" markdown="1">

[abort](#method-abort)
[abort_if](#method-abort-if)
[abort_unless](#method-abort-unless)
[auth](#method-auth)
[back](#method-back)
[bcrypt](#method-bcrypt)
[collect](#method-collect)
[config](#method-config)
[csrf_field](#method-csrf-field)
[csrf_token](#method-csrf-token)
[dd](#method-dd)
[dispatch](#method-dispatch)
[env](#method-env)
[event](#method-event)
[factory](#method-factory)
[method_field](#method-method-field)
[old](#method-old)
[redirect](#method-redirect)
[request](#method-request)
[response](#method-response)
[session](#method-session)
[value](#method-value)
[view](#method-view)

</div>

<a name="method-listing"></a>
## Lista

<style>
    #collection-method code {
        font-size: 14px;
    }

    #collection-method:not(.first-collection-method) {
        margin-top: 50px;
    }
</style>

<a name="arrays"></a>
## Tömbök

<a name="method-array-add"></a>
#### `array_add()` {#collection-method .first-collection-method}

Az `array_add` függvény hozzáad egy kulcs / érték párt a tömbhöz, ha a megadott kulcs még nem létezik:

    $array = array_add(['name' => 'Desk'], 'price', 100);

    // ['name' => 'Desk', 'price' => 100]

<a name="method-array-collapse"></a>
#### `array_collapse()` {#collection-method}

Az `array_collapse` függvény tömböket egyesít egy tömbbe:

    $array = array_collapse([[1, 2, 3], [4, 5, 6], [7, 8, 9]]);

    // [1, 2, 3, 4, 5, 6, 7, 8, 9]

<a name="method-array-divide"></a>
#### `array_divide()` {#collection-method}

Az `array_divide` függvény két tömbbel tér vissza, az egyik a paraméterül kapott tömb kulcsait, a másik pedig az értékeit tartalmazza:

    list($keys, $values) = array_divide(['name' => 'Desk']);

    // $keys: ['name']

    // $values: ['Desk']

<a name="method-array-dot"></a>
#### `array_dot()` {#collection-method}

Az `array_dot` függvény egy többdimenziós tömböt valósít meg egy egyszintű tömbben úgy, hogy a különböző szinteket a kulcsban ponttal ("dot") választja el:

    $array = array_dot(['foo' => ['bar' => 'baz']]);

    // ['foo.bar' => 'baz'];

<a name="method-array-except"></a>
#### `array_except()` {#collection-method}

Az `array_except` függvény eltávolítja a megadott kulcs / érték párt a megadott tömbből:

    $array = ['name' => 'Desk', 'price' => 100];

    $array = array_except($array, ['price']);

    // ['name' => 'Desk']

<a name="method-array-first"></a>
#### `array_first()` {#collection-method}

Az `array_first` függvény visszaadja az első elemet a tömbből, ami átmegy a megadott teszten:

    $array = [100, 200, 300];

    $value = array_first($array, function ($value, $key) {
        return $value >= 150;
    });

    // 200

A default paraméterben megadható egy érték, ami akkor kerül visszaadásra ha nincs olyan elem, ami átmenne a teszten:

    $value = array_first($array, $callback, $default);

<a name="method-array-flatten"></a>
#### `array_flatten()` {#collection-method}

Az `array_flatten` függvény egy többdimenziós tömböt alakít át egydimenziósra:

    $array = ['name' => 'Joe', 'languages' => ['PHP', 'Ruby']];

    $array = array_flatten($array);

    // ['Joe', 'PHP', 'Ruby'];

<a name="method-array-forget"></a>
#### `array_forget()` {#collection-method}

Az `array_forget` függvény eltávolít egy kulcs / érték párt egy többdimenziós tömbből. A többdimenziós kulcsot ponttal ("dot") elválasztva adjuk meg:

    $array = ['products' => ['desk' => ['price' => 100]]];

    array_forget($array, 'products.desk');

    // ['products' => []]

<a name="method-array-get"></a>
#### `array_get()` {#collection-method}

Az `array_get` függvény visszaad egy értéket egy akár többdimenziós tömbből a kulcsot ponttal ("dot") megadva:

    $array = ['products' => ['desk' => ['price' => 100]]];

    $value = array_get($array, 'products.desk');

    // ['price' => 100]

Az `array_get` függvény tartalmaz továbbá egy harmadik "default" értéket, amit akkor ad vissza, ha nem találta meg a kért kulcsot a tömbben:

    $value = array_get($array, 'names.john', 'default');

<a name="method-array-has"></a>
#### `array_has()` {#collection-method}

Az `array_has` függvény megnézi, hogy a megadott ponttal ("dot") elválasztott kulcs létezik-e a tömbben:

    $array = ['products' => ['desk' => ['price' => 100]]];

    $hasDesk = array_has($array, 'products.desk');

    // true

<a name="method-array-last"></a>
#### `array_last()` {#collection-method}

Az `array_last` függvény visszaadja az utolsó elemet a tömbből, ami átmegy a megadott teszten:

    $array = [100, 200, 300, 110];

    $value = array_last($array, function ($value, $key) {
        return $value >= 150;
    });

    // 300

<a name="method-array-only"></a>
#### `array_only()` {#collection-method}

Az `array_only` function csak a megadott kulccsal / kulcsokkal rendelkező elemeket adja vissza a tömbből:

    $array = ['name' => 'Desk', 'price' => 100, 'orders' => 10];

    $array = array_only($array, ['name', 'price']);

    // ['name' => 'Desk', 'price' => 100]

<a name="method-array-pluck"></a>
#### `array_pluck()` {#collection-method}

Az `array_pluck` függvény "kiszakít" a tömbből egy adott kulcs / érték listát:

    $array = [
        ['developer' => ['id' => 1, 'name' => 'Taylor']],
        ['developer' => ['id' => 2, 'name' => 'Abigail']],
    ];

    $array = array_pluck($array, 'developer.name');

    // ['Taylor', 'Abigail'];

Beállíthatod, hogy a visszaadott tömb kulcsai mi alapján jöjjenek létre:

    $array = array_pluck($array, 'developer.name', 'developer.id');

    // [1 => 'Taylor', 2 => 'Abigail'];

<a name="method-array-prepend"></a>
#### `array_prepend()` {#collection-method}

Az `array_prepend` függvény egy értéket ad hozzá a tömb elejéhez:

    $array = ['one', 'two', 'three', 'four'];

    $array = array_prepend($array, 'zero');

    // $array: ['zero', 'one', 'two', 'three', 'four']

<a name="method-array-pull"></a>
#### `array_pull()` {#collection-method}

Az `array_pull` függvény visszaad eltávolít egy kulcs / érték párt a tömbből:

    $array = ['name' => 'Desk', 'price' => 100];

    $name = array_pull($array, 'name');

    // $name: Desk

    // $array: ['price' => 100]

<a name="method-array-set"></a>
#### `array_set()` {#collection-method}

Az `array_set` függvény beállít egy értéket a tömbben úgy, hogy a kulcs nevét ponttal ("dot") elválasztva adtad meg:

    $array = ['products' => ['desk' => ['price' => 100]]];

    array_set($array, 'products.desk.price', 200);

    // ['products' => ['desk' => ['price' => 200]]]

<a name="method-array-sort"></a>
#### `array_sort()` {#collection-method}

Az `array_sort` függvény egy tömböt rendez sorba a megadott Closure alapján:

    $array = [
        ['name' => 'Desk'],
        ['name' => 'Chair'],
    ];

    $array = array_values(array_sort($array, function ($value) {
        return $value['name'];
    }));

    /*
        [
            ['name' => 'Chair'],
            ['name' => 'Desk'],
        ]
    */

<a name="method-array-sort-recursive"></a>
#### `array_sort_recursive()` {#collection-method}

Az `array_sort_recursive` függvény rekurzívan rendez egy tömböt a `sort` függvényt használva:

    $array = [
        [
            'Roman',
            'Taylor',
            'Li',
        ],
        [
            'PHP',
            'Ruby',
            'JavaScript',
        ],
    ];

    $array = array_sort_recursive($array);

    /*
        [
            [
                'Li',
                'Roman',
                'Taylor',
            ],
            [
                'JavaScript',
                'PHP',
                'Ruby',
            ]
        ];
    */

<a name="method-array-where"></a>
#### `array_where()` {#collection-method}

Az `array_where` függvény megszűri a tömb értékeit a megadott Closure alapján:

    $array = [100, '200', 300, '400', 500];

    $array = array_where($array, function ($value, $key) {
        return is_string($value);
    });

    // [1 => 200, 3 => 400]

<a name="method-head"></a>
#### `head()` {#collection-method}

A `head` függvény egyszerűen visszaadja a tömb első értékét:

    $array = [100, 200, 300];

    $first = head($array);

    // 100

<a name="method-last"></a>
#### `last()` {#collection-method}

A `last` függvény visszaadja a tömb utolsó elemét:

    $array = [100, 200, 300];

    $last = last($array);

    // 300

<a name="paths"></a>
## Elérési Utak

<a name="method-app-path"></a>
#### `app_path()` {#collection-method}

Az `app_path` függvény visszaadja a teljes elérési utat az `app` könyvtárhoz. Felhasználhatod arra is az `app_path` függvényt, hogy az app könyvtáron belül teljes útvonalat kapj meg relatív útvonalak helyett:

    $path = app_path();

    $path = app_path('Http/Controllers/Controller.php');

<a name="method-base-path"></a>
#### `base_path()` {#collection-method}

Az `base_path` függvény visszaadja a teljes elérési utat a projekt gyökérkönyvtárához. Felhasználhatod arra is a `base_path` függvényt, hogy a projekten belül teljes útvonalat kapj meg a relatív útvonalak helyett:

    $path = base_path();

    $path = base_path('vendor/bin');

<a name="method-config-path"></a>
#### `config_path()` {#collection-method}

A `config_path` függvény visszaadja a teljes elérési utat a config könyvtárhoz:

    $path = config_path();

<a name="method-database-path"></a>
#### `database_path()` {#collection-method}

A `database_path` függvény visszaadja a teljes elérési utat a database könyvtárhoz:

    $path = database_path();

<a name="method-elixir"></a>
#### `elixir()` {#collection-method}

Az `elixir` függvény visszaadja az elérési útját az [Elixir fájl-nak](/docs/{{version}}/elixir):

    elixir($file);

<a name="method-public-path"></a>
#### `public_path()` {#collection-method}

A `public_path` függvény visszaadja a teljes elérési útját a `public` könyvtárnak:

    $path = public_path();

<a name="method-resource-path"></a>
#### `resource_path()` {#collection-method}

A `resource_path` függvény visszaadja a teljes elérési útját a `resources` könyvtárnak. Felhasználhatod arra is a `resource_path` függvényt, hogy a projekten belül teljes útvonalat kapj meg a relatív útvonalak helyett: 

    $path = resource_path();

    $path = resource_path('assets/sass/app.scss');

<a name="method-storage-path"></a>
#### `storage_path()` {#collection-method}

A `storage_path` function visszaadja a teljes elérési útját a `storage` könyvtárnak. Felhasználhatod arra is a `storage_path` függvényt, hogy a projekten belül teljes útvonalat kapj meg a relatív útvonalak helyett: 

    $path = storage_path();

    $path = storage_path('app/file.txt');

<a name="strings"></a>
## Stringek

<a name="method-camel-case"></a>
#### `camel_case()` {#collection-method}

A `camel_case` függvény átalakítja a kapott szöveget `camelCase`-re:

    $camel = camel_case('foo_bar');

    // fooBar

<a name="method-class-basename"></a>
#### `class_basename()` {#collection-method}

A `class_basename` függvény visszaadja egy adott osztály nevét a namespace-ek (névterek) nélkül:

    $class = class_basename('Foo\Bar\Baz');

    // Baz

<a name="method-e"></a>
#### `e()` {#collection-method}

Az `e` függvény lefuttatja a `htmlentities` függvényt a kapott stringen:

    echo e('<html>foo</html>');

    // &lt;html&gt;foo&lt;/html&gt;

<a name="method-ends-with"></a>
#### `ends_with()` {#collection-method}

Az `ends_with` függvény visszaadja, hogy a kapott string vége a megadott stringgel végződik-e:

    $value = ends_with('This is my name', 'name');

    // true

<a name="method-snake-case"></a>
#### `snake_case()` {#collection-method}

A `snake_case` függvény átalakítja a kapott szöveget `snake_case`-re:

    $snake = snake_case('fooBar');

    // foo_bar

<a name="method-str-limit"></a>
#### `str_limit()` {#collection-method}

Az `str_limit` függvény limitálja a megadott string hosszát. Első paraméterben megadható meg a string, a második paraméter pedig megadja, hogy maximum hány karakternél legyen elvágva:

    $value = str_limit('The PHP framework for web artisans.', 7);

    // The PHP...

<a name="method-starts-with"></a>
#### `starts_with()` {#collection-method}

A `starts_with` függvény visszaadja, hogy a kapott string eleje a megadott stringgel kezdődik-e:

    $value = starts_with('This is my name', 'This');

    // true

<a name="method-str-contains"></a>
#### `str_contains()` {#collection-method}

Az `str_contains` függvény visszaadja, hogy a megadott szöveg tartalmazza-e a paraméterül kapott stringet:

    $value = str_contains('This is my name', 'my');

    // true

<a name="method-str-finish"></a>
#### `str_finish()` {#collection-method}

A `str_finish` függvény a szöveg végéhez fűzi a kapott stringet:

    $string = str_finish('this/string', '/');

    // this/string/

<a name="method-str-is"></a>
#### `str_is()` {#collection-method}

Az `str_is` függvény visszaadja, hogy a kapott minta illesztkedik-e a stringre. Csillag wildcard is használható:

    $value = str_is('foo*', 'foobar');

    // true

    $value = str_is('baz*', 'foobar');

    // false

<a name="method-str-plural"></a>
#### `str_plural()` {#collection-method}

A `str_plural` függvény átalakítja a megkapott stringet annak a többes számú alakjává. Ez a függvény jelenleg csak angol nyelven működik:

    $plural = str_plural('car');

    // cars

    $plural = str_plural('child');

    // children

Második paraméternek megadható egy integer, és az `str_plural` ennek a számnak megfelelően adja vissza többes vagy egyes számban a megadott szót:

    $plural = str_plural('child', 2);

    // children

    $plural = str_plural('child', 1);

    // child

<a name="method-str-random"></a>
#### `str_random()` {#collection-method}

Az `str_random` függvény egy megadott hosszúságú véletlen stringet generál. Ez a függvény a PHP `random_bytes` függvényét használja:

    $string = str_random(40);

<a name="method-str-singular"></a>
#### `str_singular()` {#collection-method}

Az `str_singular` függvény visszaadja a megadott stringet az egyes számú alakjában. Ez a függvény jelenleg csak az angol nyelvű szavakkal működik:

    $singular = str_singular('cars');

    // car

<a name="method-str-slug"></a>
#### `str_slug()` {#collection-method}

Az `str_slug` függvény a megadott stringből egy URL barát "slug" formát ad vissza:

    $title = str_slug('Laravel 5 Framework', '-');

    // laravel-5-framework

<a name="method-studly-case"></a>
#### `studly_case()` {#collection-method}

Az `studly_case` átalakítja function a megadott stringet `StudlyCase`-re:

    $value = studly_case('foo_bar');

    // FooBar

<a name="method-title-case"></a>
#### `title_case()` {#collection-method}

A `title_case` függvény átalakítja a megadott stringet  `Title Case`-re:

    $title = title_case('a nice title uses the correct case');

    // A Nice Title Uses The Correct Case

<a name="method-trans"></a>
#### `trans()` {#collection-method}

A `trans` függvény lefordítja a kapott stringet a [lokalizációs fájl](/docs/{{version}}/localization) segítségével:

    echo trans('validation.required'):

<a name="method-trans-choice"></a>
#### `trans_choice()` {#collection-method}

A `trans_choice` függvény lefordítja a kapott nyelvi kulcsot a nyelvi fájl segítségével, egyeztetve az egyes / többes számot:

    $value = trans_choice('foo.bar', $count);

<a name="urls"></a>
## URL-ek

<a name="method-action"></a>
#### `action()` {#collection-method}


Az `action` függvény visszaadja az URL-t a kapott controller@action-höz. Nem kell a teljes namespace-t átadnod a controllerhez, helyette használd a relatív namespace-t a `App\Http\Controllers`-hez képest:

    $url = action('HomeController@getIndex');

Ha a route paramétereket is kezelm ezeket a második paraméterben adhatod át:

    $url = action('UserController@profile', ['id' => 1]);

<a name="method-asset"></a>
#### `asset()` {#collection-method}

Egy asset fájlhoz generál teljes url-t figyelembe véve, hogy HTTP vagy HTTPS-e a kérés:

	$url = asset('img/photo.jpg');

<a name="method-secure-asset"></a>
#### `secure_asset()` {#collection-method}

URL-t generál egy asset fájlhoz HTTPS-t használva

	echo secure_asset('foo/bar.zip', $title, $attributes = []);

<a name="method-route"></a>
#### `route()` {#collection-method}

A `route` függvény egy URL-t ad vissza a megadott nevű route-hoz:

    $url = route('routeName');

Ha a route paramétereket is kezel, akkor ezeket a második paraméterben adhatod át:

    $url = route('routeName', ['id' => 1]);

<a name="method-url"></a>
#### `url()` {#collection-method}

Az `url` függvény abszolút elérési utat generál a megadott relatív linkre:

    echo url('user/profile');

    echo url('user/profile', [1]);

Ha nincs elérési út megadva, akkor `Illuminate\Routing\UrlGenerator` példányt ad vissza:

    echo url()->current();
    echo url()->full();
    echo url()->previous();

<a name="miscellaneous"></a>
## Vegyes

<a name="method-abort"></a>
#### `abort()` {#collection-method}
 
Az `abort` függvény HTTP exception-t dob, amit az exception handler kap el:

    abort(401);

Második paraméterben megadható az exception üzenete:

    abort(401, 'Unauthorized.');

<a name="method-abort-if"></a>
#### `abort_if()` {#collection-method}

Az `abort_if` függvény HTTP exceptiont dob, ha az első paramétere `true`:

    abort_if(! Auth::user()->isAdmin(), 403);

<a name="method-abort-unless"></a>
#### `abort_unless()` {#collection-method}

Az `abort_unless` függvény HTTP exceptiont dob, ha az első paramétere `false`:

    abort_unless(Auth::user()->isAdmin(), 403);

<a name="method-auth"></a>
#### `auth()` {#collection-method}

Az `auth` függvény visszaad egy authentikált user példányt. Az `Auth` facade helyett használhatod:

    $user = auth()->user();

<a name="method-back"></a>
#### `back()` {#collection-method}

A `back()` function egy átirányítást generál a felhasználó előző oldalára (referer):

    return back();

<a name="method-bcrypt"></a>
#### `bcrypt()` {#collection-method}

A `bcrypt` függvény egy Bcrypt hash-t ad vissza. Felhasználhatod a `Hash` facade helyett:

    $password = bcrypt('my-secret-password');

<a name="method-collect"></a>
#### `collect()` {#collection-method}

A `collect` függvény egy [collection](/docs/{{version}}/collections) példányt ad vissza a megadott tömb helyett:

    $collection = collect(['taylor', 'abigail']);

<a name="method-config"></a>
#### `config()` {#collection-method}

A `config` function visszaad egy config értéket. A config értékeket pottal ("dot") elválasztva kell megadni, ahol az első pont előtti rész a config fájl neve. A default értéket akkor adja vissza, a fájlban a megadott érték nem található.

    $value = config('app.timezone');

    $value = config('app.timezone', $default);

A `config` helper felhasználható arra, hogy a konfigurációs értékeket futásidőben megváltoztassuk. Ehhez egy kulcs / érték párt kell átadni a helpernek:

    config(['app.debug' => true]);

<a name="method-csrf-field"></a>
#### `csrf_field()` {#collection-method}

A `csrf_field` függvény egy HTML `hidden` input mezőt generál, aminek az értéke az érvényes CSRF token. Felhasználható például [Blade szintaxisban](/docs/{{version}}/blade) így:

    {{ csrf_field() }}

<a name="method-csrf-token"></a>
#### `csrf_token()` {#collection-method}

A `csrf_token` függvény visszaadja az aktuálisan érvényes CSRF token értékét:

    $token = csrf_token();

<a name="method-dd"></a>
#### `dd()` {#collection-method}

A `dd` függvény dump-olja a kapott változót, majd leállítja az alkalmazás futását:

    dd($value);

    dd($value1, $value2, $value3, ...);

Ha nem akarod leállítani az alkalmazást, akkor használd `dd` helyett a `dump` functiont:

    dump($value);

<a name="method-dispatch"></a>
#### `dispatch()` {#collection-method}

A `dispatch` függvény egy új job-ot ad hozzá a Laravel [job queue](/docs/{{version}}/queues)-jához:

    dispatch(new App\Jobs\SendEmails);

<a name="method-env"></a>
#### `env()` {#collection-method}

Az `env` függvény visszaadja egy környezeti változó értékét:

    $env = env('APP_ENV');

    // Return a default value if the variable doesn't exist...
    $env = env('APP_ENV', 'production');

<a name="method-event"></a>
#### `event()` {#collection-method}

Az `event` függvény kivált egy [event](/docs/{{version}}/events)-et:

    event(new UserRegistered($user));

<a name="method-factory"></a>
#### `factory()` {#collection-method}

A `factory` függvény egy model factoryt ad vissza a megadott modelhez. Megadható az osztály neve és a létrehozandó példányok száma is. Felhasználható [teszteléshez](/docs/{{version}}/database-testing#writing-factories) vagy [seedeléshez](/docs/{{version}}/seeding#using-model-factories):

    $user = factory(App\User::class)->make();

<a name="method-method-field"></a>
#### `method_field()` {#collection-method}

A `method_field` függvény egy HTML `hidden` input mezőt ad viszsa, ami tartalmazza a form HTTP verb-et. Például [Blade-del használva](/docs/{{version}}/blade):

    <form method="POST">
        {{ method_field('DELETE') }}
    </form>

<a name="method-old"></a>
#### `old()` {#collection-method}

Az `old` függvény [visszaadja](/docs/{{version}}/requests#retrieving-input) az inputok régi értékeit, melyek session flash-ben lettek tárolva:

    $value = old('value');

    $value = old('value', 'default');

<a name="method-redirect"></a>
#### `redirect()` {#collection-method}

A `redirect` függvény visszaad egy redirect HTTP response-t, vagy visszaad egy redirector példányt, ha nem kapott paramétert:

    return redirect('/home');

    return redirect()->route('route.name');

<a name="method-request"></a>
#### `request()` {#collection-method}

A `request` függvény visszaadja az aktuális [request](/docs/{{version}}/requests) példányt:

    $request = request();

    $value = request('key', $default = null)

<a name="method-response"></a>
#### `response()` {#collection-method}

A `response` függvény létrehoz egy [response](/docs/{{version}}/responses) példányt, vagy ha nem kapott értéket, akkor visszaad egy üres response objektumot:

    return response('Hello World', 200, $headers);

    return response()->json(['foo' => 'bar'], 200, $headers);

<a name="method-session"></a>
#### `session()` {#collection-method}

A `session` függvény lekér egy értéket a session-ből:

    $value = session('key');

Ha paraméterben kulcs/érték pár(oka)t kap, akkor beállítja azt.

    session(['chairs' => 7, 'instruments' => 3]);

Ha nem kap semmilyen értéket, akkor visszaadja a session store objektumot:

    $value = session()->get('key');

    session()->put('key', $value);

<a name="method-value"></a>
#### `value()` {#collection-method}

A `value` függvény visszaadja a kapott closure értékét. A megadott Closure automatikusan lefut a value használata esetén:

    $value = value(function() { return 'bar'; });

<a name="method-view"></a>
#### `view()` {#collection-method}

A `view` függvény visszaad egy [view](/docs/{{version}}/views) példányt:

    return view('auth.login');
