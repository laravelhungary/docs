# Hashelés

- [Bevezetés](#introduction)
- [Használat](#basic-usage)

<a name="introduction"></a>
## Bevezetés

A Laravel `Hash` [facade](/docs/{{version}}/facades) lehetőséget ad a biztonságos Bcrypt hashelésre a felhasználói jelszavak tárolásához. Ha a beépített `LoginController` és a `RegisterController` osztályokat használod a Laravel alkalmazásodhoz, ezek automatikusan a Bcrypt hashelést alkalmazzák a regisztrációhoz és az authentikációhoz.

> {tip} A Bcrypt remek választás a jelszavak hashelésére, hiszen beállítható a nehézségi szintje ("work factor"), ami azt jelenti, hogy ahogy a hardverek erősödnek, a hash előállításához szükgéges idő is növelhető.

<a name="basic-usage"></a>
## Használat

A `Hash` facade `make` metódusával generálhatsz hash-t a jelszóból:

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

#### A helyes jelszó ellenőrzése

A `check` metódus lehetővé teszi, hogy egy egyszerű stringről eldöntsd, a megadott hash előállítható-e belőle. Ha a [Laravel által biztosított](/docs/{{version}}/authentication) `LoginController`-t  használod, akkor ezzel nem kell törődnöd, mert a controller automatikusan meghívja helyetted:

    if (Hash::check('plain-text', $hashedPassword)) {
        // The passwords match...
    }

#### A jelszó újrahashelése

A `needsRehash` függvény lehetővé teszi számodra, hogy eldöntsd, szükséges-e a letárolt hash-t a hash metódus megváltozása miatt újrahashelni:

    if (Hash::needsRehash($hashed)) {
        $hashed = Hash::make('plain-text');
    }
