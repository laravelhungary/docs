# Hashelés

- [Bevezetés](#introduction)
- [Használat](#basic-usage)

<a name="introduction"></a>
## Bevezetés

A Laravel `Hash` [facade](/docs/{{version}}/facades) lehetőséget ad a biztonságos Bcrypt hashelésre a felhasználói jelszavak tárolásához. Ha a beépített `LoginController` és a `RegisterController` classokat használod a Laravel alkalmazásodhoz, ezek automatikusan használják a Bcrypt -et a regisztrációhoz és az authentikációhoz.

> {tip} A Bcrypt remek választás a jelszavak hashelésére, hiszen a nehézségi szintje "work factor" beállítható, ami azt jelenti, hogy növelhető az előállításához szükséges hardveridő.

<a name="basic-usage"></a>
## Használat

A `Hash` facade `make` metódusával generálhatsz hash -t a jelszóból:

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Hash;
    use App\Http\Controllers\Controller;

    class UpdatePasswordController extends Controller
    {
        /**
         * Update the password for the user.
         *
         * @param  Request  $request
         * @return Response
         */
        public function update(Request $request)
        {
            // Validate the new password length...

            $request->user()->fill([
                'password' => Hash::make($request->newPassword)
            ])->save();
        }
    }

#### Ellenőrizd, hogy A Jelszó Megfelelő-e

A `check` metódus lehetővé teszi, hogy egy egyszerű stringről eldöntsd, hogy a megadott hash előállítható-e belőle. Ha a `LoginController` [included with Laravel](/docs/{{version}}/authentication) -t használod, akkor ezzel nem kell törődnöd, mert a vezérlő ezt automatikusan hívja meg helyetted:

    if (Hash::check('plain-text', $hashedPassword)) {
        // The passwords match...
    }

#### Ellenőrizd, hogy A Jelszót Újra Kell-e Hashelni


A `needsRehash` függvény lehetővé teszi számodra, hogy eldöntsd, hogy a letárolt hash -t szükséges-e a hasher lecserélése miatt újrakódolni:

    if (Hash::needsRehash($hashed)) {
        $hashed = Hash::make('plain-text');
    }
