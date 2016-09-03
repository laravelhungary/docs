# Tesztelés

- [Bevezetés](#introduction)
- [Futtató környezet](#environment)
- [Tesztek készítése és futtatása](#creating-and-running-tests)

<a name="introduction"></a>
## Bevezetés

A Laravel a tesztelés szem előtt tartásával készült. Ez azt jelenti, hogy a PHPUnit támogatására fel van készítve az alkalmazásod és egy beállított `phpunit.xml` fájlt tartalmaz. A keretrendszer megfelelő segéd függvényekkel együtt került telepítésre, amelyekkel tesztelheted az alkalmazásodat.

Egy `ExampleTest.php` nevű fájl található a `tests` könyvtárban. Laravel alkalmazás installálása után, egyszerűen futtasd a tesztjeidet a `phpunit` paranccsal.

<a name="environment"></a>
## Futtató környezet

Tesztek futatásakor, a Laravel automatikusan a `testing` konfigurációs környezetet állítja be. A Laravel automatikusan beállítja a munkamenetet és gyorstárat az `array` meghajtóra a tesztek futása alatt, ami azt jelenti, hogy sem a munkamenetben sem a gyorstárban nem maradnak meg az adatok tesztfuttatás közben.

Szabadon definiálhatsz más teszkörnyezet konfigurációs változókat amennyiben szükséges. A `testing` környezeti változókat a `phpunit.xml` fájlban állíthatod be, de figyelj arra, hogy a konfigurációs gyorstárad legyen kiürítve a `config:clear` Artisan paranccsal, mielőtt futtatod a tesztjeidet!

<a name="creating-and-running-tests"></a>
## Tesztek készítése és futtatása

Új teszteset készítéséhez használd a `make:test` Artisan parancsot:

    php artisan make:test UserTest

Ez a parancs egy új `UserTest` nevü osztályt fog létrehozni a `tests` könyvtárban. Ezek után ugyan úgy definiálhatod a teszt függvényeket, ahogy a PHPUnit használata során általában szoktad. A tesztjeid futtatásához, egyszerűen hajtsd végre a `phpunit` parancsot a parancssorodban:

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
