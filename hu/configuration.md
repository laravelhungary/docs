# Beállítás

- [Bevezetés](#introduction)
- [Hozzáférés A Konfigurációs Értékekhez](#accessing-configuration-values)
- [Környezeti Beállítások](#environment-configuration)
    - [Aktuális Futtatókörnyezet Detektálása](#determining-the-current-environment)
- [Konfigurációs Értékek Cachelése](#configuration-caching)
- [Karbantartási Mód](#maintenance-mode)

<a name="introduction"></a>
## Bevezetés

Minden a Laravel által használt konfigurációs fájl a `config` könyvtárban található. Minden opció dokumentálva van, így olvasd végig a fájlokat, hogy megismerhesd az összes rendelkezésre álló lehetőséget.

<a name="accessing-configuration-values"></a>
## Hozzáférés A Konfigurációs Értékekhez

A különböző konfigurációs értékekhez egyszerűen hozzáférhetsz az alkalmazáson belül a `config` helper segítségével. Az értékek lekérdezéséhez használd a "dot" szintaxist, ahol megadod a fájlt és a konfigurációs kulcs nevét a hozzáféréshez. A default paraméterben megadhatod, hogy ha nem található az érték akkor mit adjon vissza a függvény:

    $value = config('app.timezone');

Az érték futásidejű megváltoztatásához adj paraméterül egy tömböt a `config` helpernek:

    config(['app.timezone' => 'America/Chicago']);

<a name="environment-configuration"></a>
## Környezeti Beállítások

Gyakran hasznos lehet, ha különböző konfigurációs értékei vannak a rendszernek a futtatókörnyezettől függően. Például más cache drivert használhatsz fejlesztés közben és az éles környezetben.

Azért, hogy ez gyerekjáték legyen a Laravel a [DotEnv](https://github.com/vlucas/phpdotenv) PHP library -t (Készítette: Vance Lucas) használja. A friss Laravel telepítés után a rendszer gyökérkönyvtárában található egy `.env.example` file. Ha composer -el telepítetted a Laravel -t akkor automatikusan átneveződik `.env` -re. Ellenkező esetben neked kell átnevezned.

#### Aktuális Környezeti Változók

Minden felsorolt változót az `$_ENV` PHP super-globalális változó foglalja össze, azonban használhatod az `env` helpert, hogy átadd az env értékeket a konfigurációs fájloknak. Sőt, ha megnézed a Laravel konfigurációs fájlait, akkor észreveheted, hogy több helyen már használjuk ezt a helpert:

    'debug' => env('APP_DEBUG', false),

A második érték átadva az `env` függvénynek az "alapértelmezett érték". Ez az érték lesz használva akkor, ha nincs a megadott néven környezeti változó.	

Az `.env` fájlt nem kell commitolnod az alkalmazásoddal a verziókezelő rendszerbe, hogy minden fejlesztő / szerver a saját maga környezeti beállításait alkalmazhassa.

Ha csapatban fejlesztesz akkor érdemes lehet az értékeket felvezetni az `.env.example` fájlba, hogy mindenki láthassa melyek azok a környezeti változók melyeket be kell állítani az alkalmazás futtatásához.

<a name="determining-the-current-environment"></a>
### Aktuális Futtatókörnyezet Detektálása

Az aktuális futtatókörnyezetet az `APP_ENV` határozza meg a `.env` fájlodban. Ehhez hozzáférhetsz az `environment` metódus segítségével az  `App` [facade](/docs/{{version}}/facades) segítségével:

    $environment = App::environment();

Ha paramétert adsz az `environment` metódusnak, akkor leellenőrzi, hogy az aktuális környezet a megadott-e. A metódus `true` -val tér vissza ha egyezést talált:

    if (App::environment('local')) {
        // The environment is local
    }

    if (App::environment('local', 'staging')) {
        // The environment is either local OR staging...
    }

<a name="configuration-caching"></a>
## Konfigurációs Értékek Cachelése

Az alkalmazás gyorsításához az összes konfigurációs értéket egyetlen fájlba cachelheted a `config:cache` Artisan parancs segítségével. Ez az összes az alkalmazásod által használt konfigurációs értéket és fájlt egyetlen fájlba fogja összegyűjteni, így a keretrendszernek csak egyetlen fájlt kell betöltenie.

Általában a `php artisan config:cache` parancsot csak az éles környezetben kell futtatni az élesítési procedúra részeként. Ezt fejlesztés közben nem kell használnod, hiszen ilyenkor még sűrűn változhatnak a konfigurációs értékek.

<a name="maintenance-mode"></a>
## Karbantartási Mód

Ha az alkalmazásod karbantartási módban van, akkor egy egyedi view jelenik meg minden request -re válaszul. Ez egyszerűvé teszi az alkalmazás "leállítását"  a frissítés vagy módosítás végrehajtásának idejére. A karbantartási mód middleware -je alapból benne van az alkalmazás stackjában. Ha az alkalmazás karbantartási módban van akkor `MaintenanceModeException` -t és 503 -as HTTP státusz kódot fog dobni.

Karbantartási módba váltáshoz használd az Artisan `down` parancsát:

    php artisan down

Megadhatsz egy üzenetet `message` és egy visszatérő `retry` paramétert is a `down` parancsnak. A `message` értéke felhasználható kiíratásra, vagy a logban, míg a `retry` értéke elküldésre kerül a `Retry-After` HTTP header -ben:

    php artisan down --message='Upgrading Database' --retry=60

A karbantartási mód befejezéséhez az `up` parancs használható:

    php artisan up

#### Karbantartási Mód Válasz Template

A karbantartási mód alapértelmezett view fájla a `resources/views/errors/503.blade.php`. Szabadon személyre szabhatod és módosíthatod, hogy illesztkedjen az alkalmazásodhoz.

#### Karbantartási mód & Queue-k

Amíg az alkalmazásod karbantartási módban van nem futnak a [queued jobs](/docs/{{version}}/queues) -ok. A jobok feldolgozása normálisan folytatódik amint véget ért a karbantartás.

#### Alternatívák A Karbantartási Módra

A Karbantartási üzemmódban az alkalmazásnak szüksége van több másodpercnyi leállásra, így alternatívaként használható például az [Envoyer](https://envoyer.io), hogy kiesés nélküli élesítés `zero-downtime deployment` legyen elérhető Laravellel.
