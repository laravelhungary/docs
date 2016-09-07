# View

- [View-k létrehozása](#creating-views)
- [Adat átadása View-knak](#passing-data-to-views)
    - [Adatok megosztása az össze View-val](#sharing-data-with-all-views)
- [View Composerek](#view-composers)

<a name="creating-views"></a>
## View-k létrehozása

A View-k HTML kódot tartalmaznak, amik így külön vannak választva a controllerektől és alkalmazás logikai felépítésétől. A View-k a `resources/views` könyvtárban találhatók. Egy egyszerű View valahogy igy néz ki:

    <!-- View stored in resources/views/greeting.php -->

    <html>
        <body>
            <h1>Hello, {{ $name }}</h1>
        </body>
    </html>

Mivel a view `resources/views/greeting.php` néven van tárolva, a controllerben vissza tudjuk adni azt a globális `view` helperrel:

    Route::get('/', function () {
        return view('greeting', ['name' => 'James']);
    });

Mint ahogy látod, a `view` helpernek átadott első paraméter a fájl neve, amit a `resources/views` könyvtárban tároltunk. A második paraméter egy tömb (array), és az ebben található változók érhetők el a view számára. Ebben az esetben a `name` változót adtuk át és a [Blade syntax](/docs/{{version}}/blade) segítségével jelentítettük meg.

Természetesen a view-k alkönyvtárakba is szeparálhatók a `resources/views` mappán belül. Az alkönyvtárakban található view-k hivatkozása a "dot syntax"-szal, azaz pontokkal összefűzve lehetséges. Például ha a view-d a `resources/views/admin/profile.php` helyen van tárolva, így tudsz hivatkozni rá:

    return view('admin.profile', $data);

#### Egy View ellenőrzése

Ha le kell ellenőrizned, hogy egy view létezik-e vagy sem, akkor a `View` facade-ot érdemes használni. Az `exists` metódus `true` értékkel tér vissza, ha a view létezik:

    use Illuminate\Support\Facades\View;

    if (View::exists('emails.customer')) {
        //
    }

<a name="passing-data-to-views"></a>
## Adatok átadása a View-knak

Mint ahogy láttad az előző példában, így tudsz átadni egy adat tömböt a view-nak:

    return view('greetings', ['name' => 'Victoria']);

Amikor adatot adsz át ilyen módon, a `$data` paraméternek (a második paraméter) egy tömbnek kell lennie kulcs/érték párokkal. A view-dban minden ily módon átadott változó elérhető a hozzá tartozó kulcs használatával, pédául `<?php echo $key; ?>`. Alternatívaként egy tömb helyett egyes elemeket is át lehet adni a `view` helpernek. Erre a `with` methódus használható:

    return view('greeting')->with('name', 'Victoria');

<a name="sharing-data-with-all-views"></a>
#### Adatok megosztása az össze View-val

Előfordulhat, hogy alkalmanként az összes view számára elérhetővé kell tenni bizonyos adatokat. Ezt meg a View facade `share` metódusával teheted meg. Általánosságban a `share`-t egy Service Provider `boot` methódusában helyezzük el. Nyugodtan használd az `AppServiceProvider`-t, vagy akár egy különálló service provider-t is készíthetsz:

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\View;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            View::share('key', 'value');
        }

        /**
         * Register the service provider.
         *
         * @return void
         */
        public function register()
        {
            //
        }
    }

<a name="view-composers"></a>
## View Composerek

A View composerek olyan callback-ek vagy osztálymetódusok, amelyek egy-egy view renderelése során kerülnek meghívásra. Ha egy bizonyos adatot egy view minden renderelésekor elérhetővé szeretnél tenni, akkor a view composer-t segítségűl hívva könnyedén elszeparálhatod ezeket a logikai feladatokat egy helyre.

Ebben a példában létrehozunk egy view composer-t a [service provider](/docs/{{version}}/providers)-en belül. Ehhez a `View` facade-ot fogjuk használni, ami a `Illuminate\Contracts\View\Factory` interface által definiált szolgáltatásokat nyújtja. Ne feletjsük el, a Laravelnek nincs előre megadott könyvtára a view composerek tárolására, úgy helyezed el őket, ahogy neked szimpatikus. Például létrehozhatsz nekik egy `App\Http\ViewComposers` könyvtárat:

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\View;
    use Illuminate\Support\ServiceProvider;

    class ComposerServiceProvider extends ServiceProvider
    {
        /**
         * Register bindings in the container.
         *
         * @return void
         */
        public function boot()
        {
            // Using class based composers...
            View::composer(
                'profile', 'App\Http\ViewComposers\ProfileComposer'
            );

            // Using Closure based composers...
            View::composer('dashboard', function ($view) {
                //
            });
        }

        /**
         * Register the service provider.
         *
         * @return void
         */
        public function register()
        {
            //
        }
    }

> {jegyzet} Ha létrehozol egy saját service provider-t, ami a view composereket tartalmazza, akkor azt a `config/app.php` konfigurációs fájl   `providers` tömbjében fel kell tüntetni.

Most, hogy regisztráltunk egy saját compsert, a `ProfileComposer@compose` methódus fog végrehajtódni minden alkalommal, amikor a `profile` view renderelésre kerül. Hozzunk tehát létre egy view composer osztályt:

    <?php

    namespace App\Http\ViewComposers;

    use Illuminate\View\View;
    use App\Repositories\UserRepository;

    class ProfileComposer
    {
        /**
         * The user repository implementation.
         *
         * @var UserRepository
         */
        protected $users;

        /**
         * Create a new profile composer.
         *
         * @param  UserRepository  $users
         * @return void
         */
        public function __construct(UserRepository $users)
        {
            // Dependencies automatically resolved by service container...
            $this->users = $users;
        }

        /**
         * Bind data to the view.
         *
         * @param  View  $view
         * @return void
         */
        public function compose(View $view)
        {
            $view->with('count', $this->users->count());
        }
    }

Mielött a view renderelésre kerülne, a composer `compose` methódusa meghívásra kerül, átadva a `Illuminate\View\View` példányát paraméterként. A `with` methódus által így hozzákapcsolhatjuk az adatunkat a viewhoz.

> {tipp} Mivel az összes view composer a [service container](/docs/{{version}}/container) által kerül meghívásra, a szükséges függőségeket akár a konstruktorban type hynt-elheted.

#### Composer hozzárendelése több View-hoz

Szükség lehet rá, hogy egy view composer több view-nak is átadjon adatot. Ezt megtehetjük a `composer` metódus első paramétereként tömböt átadva:

    View::composer(
        ['profile', 'dashboard'],
        'App\Http\ViewComposers\MyViewComposer'
    );

A `composer` metódus elfogadja a `*` karatert is, amivel a beállított view composer minden view-hoz hozzárendelődik:

    View::composer('*', function ($view) {
        //
    });

#### View Creators

A View **creator**-ok nagyon hasonlóak a view composerhez, azzal a különbséggel, hogy ezek azonnal meghívásra kerülnek, amikor egy view példányosításra kerül, nem várnak a view renderelésének kezdetére. A view creator használatához, használd a `creator` methódust:

    View::creator('profile', 'App\Http\ViewCreators\ProfileCreator');
