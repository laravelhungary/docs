# View

- [Viewk létrehozása](#creating-views)
- [Adat átadások Viewknak](#passing-data-to-views)
    - [Adatok megosztása az össze Viewal](#sharing-data-with-all-views)
- [View Composerek](#view-composers)

<a name="creating-views"></a>
## Viewk létrehozása

A Viewk HTML kódot tartalmaznak amit az alkalmazásod prezetál és külön választja a controllerektől / alkalmazás logikai felépítésétől. A Viewk a `resources/views` könyvtárban találhatók.  Egy sima View valahogy igy néz ki:

    <!-- View stored in resources/views/greeting.php -->

    <html>
        <body>
            <h1>Hello, {{ $name }}</h1>
        </body>
    </html>

Mivel a view itt van tárolva `resources/views/greeting.php`, így vissza tudunk térni vele a globális `view` helperrel:

    Route::get('/', function () {
        return view('greeting', ['name' => 'James']);
    });

Mint ahogy látod, az első átadott paraméter a `view` helpernek a fájl neve amit a `resources/views` könyvtárban tároltunk. A második paraméter egy tömb(array) és így elérhető a view számára. Ebben az esetben a `name` változót adtuk át és a  [Blade syntax](/docs/{{version}}/blade) segítségével jelentítettük meg.

Természetesen a viewkat is lehet szeparálni alkönyvtárakba a `resources/views` mappán belül. "Dot" azaz pontokkal fűzve tudunk az alkönyvtárakban megtalálható viewkra hivatkozni. Például ha a viewd `resources/views/admin/profile.php` van tárolva, így tudsz hivatkozni rá:

    return view('admin.profile', $data);

#### Derítsük ki ,hogy egy View létezik

Ha le kell ellenőrizned,hogy egy view létezik-e vagy sem akkor `View` facadeot érdemes használni. Az `exists` methódusa `true` értékkel tér vissza ha igaz:

    use Illuminate\Support\Facades\View;

    if (View::exists('emails.customer')) {
        //
    }

<a name="passing-data-to-views"></a>
## Adatok átadása Viewknak

Mint ahogy láttad az előző példában így tudsz átadni egy adat tömböt a viewnak:

    return view('greetings', ['name' => 'Victoria']);

Amikor adatot adsz át ilyen módon a `$data` egy tömb lesz (key/value) kulcs/érték párokkal. A viewdban minden értéket elérsz a hozzá tartozó kulcs használatával, pédául `<?php echo $key; ?>`. Alternatívaként egy tömb helyett egyes elemeket is át lehet adni a `view` helpernek, a `with` methódus használatásal:

    return view('greeting')->with('name', 'Victoria');

<a name="sharing-data-with-all-views"></a>
#### Adatok megosztása az össze Viewal

Előfordulhat, hogy alkalmanként az összes view számára elérhetővé kell tenni az adatok egy részét . Amit meg a view facade's `share` methódusával tudsz elérni. Általánosságban a `share`-t egy (Service Provider) Szolgáltatás kiszolgáló `boot` methódusában helyezzük el. Nyugodtan használható az `AppServiceProvider` vagy készítesz egy különálló service provider-t ahol tárolni akarod:

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

View composerek akkor hívódnak meg ha egy callback vagy class(osztály) methódusai hivatkozik rájuk. Ha egy bizonyos adatot egy viewhoz szeretnél kötni ami minden alkalommal lefut a view renderelésekor, akkor a view composer segítségűl hívva könnyedén elszeparálhatod a logikai feladatokat egy helyre.

Ebben a példában létrehozunk egy view composer-t a  [service provider](/docs/{{version}}/providers)-en belül. Ehhez a `View` facade-ot fogjuk használni amit `Illuminate\Contracts\View\Factory` contract implementáció tartalmaz. Ne feletjsük el, a Laravelnek nincs előre megadott könyvtára ahol tároljuk a view composereket. Úgy helyezd el ahogy neked szimpatikus. Például, létrehozhatsz neki egy `App\Http\ViewComposers` könyvtárat:

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

> {jegyzet} Ha létrehozol egy saját service provider-t ami tartalmazza majd a view composereket, akkor a `config/app.php` configurációs fájlba fel kell venni a `providers` tömbbe a betöltendő service providerek közé.

Most ,hogy regisztráltunk egy saját compsert, a `ProfileComposer@compose` methódus fog végrehajtódni amikor a `profile` view renderelésre kerül. Szóval hozzunk létre egy composer (class)osztályt:

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

Mielött a view renderelésre kerülne a composer `compose` methódusa meghívásra kerül `Illuminate\View\View` példánya által. A `with` methódus által hozzá kapcsoljuk az adatunkat a viewhoz.

> {tipp} Mivel az összes view compsoser a [service container](/docs/{{version}}/container) által használunk, érdemes a szükséges függőségeket már a construktorban átadni.

#### Composer hozzárendelése több Viewhoz

Szükséged lehet rá,hogy egy view composer több viewnak is átadjon adatot. A `composer` methódusának tömböt átadva első paramétereként lehetőségünk van rá:

    View::composer(
        ['profile', 'dashboard'],
        'App\Http\ViewComposers\MyViewComposer'
    );

A `composer` methódus elfogadja a `*` karatert, ezáltal minden viewhoz hozzárendelődik:

    View::composer('*', function ($view) {
        //
    });

#### View Creators

A View **creators** nagyon hasonlóak a view composerhez. Azonban ezek azonnal végrehajtódnak mikor a view példányosításra kerül, ahelyett hogy megvárná a renderelés végét. A view creator használatához, használd a `creator` methódust:

    View::creator('profile', 'App\Http\ViewCreators\ProfileCreator');
