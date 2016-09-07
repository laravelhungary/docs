# Tesztelés

- [Bevezetés](#introduction)
- [Futtató környezet](#environment)
- [Tesztek készítése és futtatása](#creating-and-running-tests)

<a name="introduction"></a>
## Bevezetés

A Laravel a tesztelés szem előtt tartásával készült. Ez azt jelenti, hogy az alkalmazásod fel van készítve a PHPUnit támogatására, és tartalmaz egy előre beállított `phpunit.xml` fájlt. A keretrendszer megfelelő segéd függvényekkel együtt került telepítésre, amelyekkel tesztelheted az alkalmazásodat.

Egy `ExampleTest.php` nevű fájl található a `tests` könyvtárban. A Laravel alkalmazás installálása után egyszerűen futtathatod a teszteket a `phpunit` paranccsal.

<a name="environment"></a>
## Futtató környezet

Tesztek futatásakor a Laravel automatikusan a `testing` konfigurációs környezetet állítja be. Ilyenkor automatikusan beállítja a session-t és cache-t az `array` driverre, ami azt jelenti, hogy sem a munkamenetben, sem a gyorstárban nem maradnak meg az adatok teszt futtatása közben.

Szabadon definiálhatsz más teszkörnyezet-konfigurációs változókat amennyiben szükséges. A `testing` környezeti változókat a `phpunit.xml` fájlban állíthatod be, de figyelj arra, hogy a konfigurációs cache legyen kiürítve a `config:clear` Artisan paranccsal a tesztek futtatása előtt!

<a name="creating-and-running-tests"></a>
## Tesztek készítése és futtatása

Új tesztek készítéséhez használd a `make:test` Artisan parancsot:

    php artisan make:test UserTest

Ez a parancs egy új `UserTest` nevü osztályt fog létrehozni a `tests` könyvtárban. Ezek után ugyanúgy definiálhatod a teszt függvényeket, ahogy a PHPUnit használata során általában szoktad. A tesztjeid futtatásához, egyszerűen hajtsd végre a `phpunit` parancsot a parancssorodban:

    <?php

    use Illuminate\Foundation\Testing\WithoutMiddleware;
    use Illuminate\Foundation\Testing\DatabaseMigrations;
    use Illuminate\Foundation\Testing\DatabaseTransactions;

    class UserTest extends TestCase
    {
        /**
         * A basic test example.
         *
         * @return void
         */
        public function testExample()
        {
            $this->assertTrue(true);
        }
    }

> {note} Ha a teszt osztályodban definiálsz saját `setUp` metódust, akkor figyelj oda, hogy meghvásra kerüljön a `parent::setUp` is.
