# CSRF Védelem

- [Bevezetés](#csrf-introduction)
- [URI -k kizárása](#csrf-excluding-uris)
- [X-CSRF-Token](#csrf-x-csrf-token)
- [X-XSRF-Token](#csrf-x-xsrf-token)

<a name="csrf-introduction"></a>
## Bevezetés

A Laravel egyszerűvé teszi, hogy megvédd az alkalmazásod a [cross-site request forgery](http://en.wikipedia.org/wiki/Cross-site_request_forgery) (CSRF) támadásokkal szemben. A Cross-site request forgeries egy veszélyes exploit, melynek célja a bejelentkezett felhasználó nevében parancs futtatása megfelelő jogosultságok nélkül.

A Laravel automatikusan CSRF "token" -t generál minden aktív felhasználói sessionhöz. Ez a token azonosítja az authorizált felhasználót, hogy valóban ő indította a request -et az alkalmazás felé.

Akárhányszor definiálsz egy HTML formot, csatolnod kell bele egy rejtett CSRF token mezőt, hogy a CSRF védelemért felelős middleware validálhassa a request -et. Erre használhatod a `csrf_field` helpert:

    <form method="POST" action="/profile">
        {{ csrf_field() }}
        ...
    </form>

A `VerifyCsrfToken` [middleware](/docs/{{version}}/middleware), ami része a  `web` middleware groupnak automatikusan ellenőrzi, hogy a request -el érkező token megegyezik-e a session -höz tárolttal.

<a name="csrf-excluding-uris"></a>
## URI -k kizárása a CSRF védelemből

Néha szükség lehet rá, hogy bizonyos linkeket kizárj a CSRF védelemből. Például, ha a [Stripe](https://stripe.com) -t használod a fizetéshez egy webhook rendszerrel, akkor kivételre kell tenned azt az url-t amit a webhook meghív, hiszen a Stripe nem mellékelné a CSRF tokent.

Ezeket az URI -kat általában a `web` middleware -n kívülre helyezzük, azonban lehetőség van rá, hogy magában a `VerifyCsrfToken` middlewareben adjuk hozzá az $except tömbbe a kivételekhez:

    <?php

    namespace App\Http\Middleware;

    use Illuminate\Foundation\Http\Middleware\VerifyCsrfToken as BaseVerifier;

    class VerifyCsrfToken extends BaseVerifier
    {
        /**
         * The URIs that should be excluded from CSRF verification.
         *
         * @var array
         */
        protected $except = [
            'stripe/*',
        ];
    }

<a name="csrf-x-csrf-token"></a>
## X-CSRF-TOKEN

Amellett, hogy a POST paramétereket elenőrzi a `VerifyCsrfToken` middleware, ellenőrzi a `X-CSRF-TOKEN` headert a request -ben. Tárolhatod a CSRF tokent például egy meta teg  -ben:

    <meta name="csrf-token" content="{{ csrf_token() }}">

Ekkora csak egyszer kell beleraknod a `meta` tag -be, majd felhasználhatod különböző library -kban, például a jQuery -ben, hogy automatikusan adja hozzá az összes lekérdezéshez. Ezzel egyszerűen és kényelmesen védheted az AJAX alapú alkalmazásokat:

    $.ajaxSetup({
        headers: {
            'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
        }
    });

<a name="csrf-x-xsrf-token"></a>
## X-XSRF-TOKEN

A Laravel eltárolja az aktuális CSRF token -t a `XSRF-TOKEN` cookie -ban is, amit minden response -al elküld a keretrendszer. Ezt felhasználhatod arra, hogy beállíthasd az `X-XSRF-TOKEN` -t a fejlécben.

Ez a cookie főként azért létezik, mert néhány Javascript keretrendszer, mint az Angular, automatikusa képes átheyezni az értékét a `X-XSRF-TOKEN` headerbe.
