# CSRF Védelem

- [Bevezetés](#csrf-introduction)
- [URI -k kizárása](#csrf-excluding-uris)
- [X-CSRF-Token](#csrf-x-csrf-token)
- [X-XSRF-Token](#csrf-x-xsrf-token)

<a name="csrf-introduction"></a>
## Bevezetés

A Laravellel egyszerűen megvédheted az alkalmazásod a [cross-site request forgery](http://en.wikipedia.org/wiki/Cross-site_request_forgery) (CSRF) támadásokkal szemben. A Cross-site request forgery egy veszélyes exploit, melynek célja valamilyen parancs futtatása a bejelentkezett felhasználó nevében annak tudta nélkül.

A Laravel minden felhasználói session-höz automatikusan egy  CSRF "token"-t generál. Ez a token azonosítja a felhasználót, és biztosítja, hogy valóban ő indította a request-et az alkalmazás felé.

Amikor egy HTML formot írsz, csatolnod kell hozzá egy rejtett CSRF token mezőt, hogy a CSRF védelemért felelős middleware validálhassa a request-et. Erre használhatod a `csrf_field` helpert:

    <form method="POST" action="/profile">
        {{ csrf_field() }}
        ...
    </form>

A `VerifyCsrfToken` [middleware](/docs/{{version}}/middleware), ami része a  `web` middleware groupnak, automatikusan ellenőrzi, hogy a request-tel érkező token megegyezik-e a session-ben tárolttal.

<a name="csrf-excluding-uris"></a>
## URI -k kizárása a CSRF védelemből

Néha szükség lehet rá, hogy bizonyos linkeket kizárj a CSRF védelemből. Például, ha a [Stripe](https://stripe.com)-ot használod a fizetéshez egy webhook rendszerrel, akkor kivételre kell tenned azt az url-t, amit a webhook meghív, hiszen a Stripe nem mellékelné a CSRF tokent.

Ezeket az URI-kat általában a `web` middleware -n kívülre helyezzük, azonban lehetőség van rá, hogy magában a `VerifyCsrfToken` middlewareben adjuk hozzá az $except tömbbe a kivételekhez:

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

Amellett, hogy a POST paramétereket elenőrzi, a `VerifyCsrfToken` middleware ellenőrzi a `X-CSRF-TOKEN` headert is a request -ben. Tárolhatod a CSRF tokent például egy meta tag-ben:

    <meta name="csrf-token" content="{{ csrf_token() }}">

Így csak egyszer kell beleírnod a tokent a `meta` tag-be, majd felhasználhatod különböző library-kban, például a jQuery-ben, hogy automatikusan adja hozzá az összes lekérdezéshez. Ezzel egyszerűen és kényelmesen védheted az AJAX alapú alkalmazásokat:

    $.ajaxSetup({
        headers: {
            'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
        }
    });

<a name="csrf-x-xsrf-token"></a>
## X-XSRF-TOKEN

A Laravel az aktuális CSRF token-t a `XSRF-TOKEN` cookie-ban is eltárolja, amit minden response-zal elküld. Ezt felhasználhatod arra, hogy beállítsd az `X-XSRF-TOKEN` -t a fejlécben.

Ez a cookie főként azért létezik, mert néhány Javascript keretrendszer, mint az Angular, automatikusa képes átheyezni az értékét a `X-XSRF-TOKEN` headerbe.
