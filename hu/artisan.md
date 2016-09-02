# Konzol Parancsok

- [Bevezetés](#bevezetes)
- [Parancsok írása](#parancsok-irasa)
    - [Parancsok generálása](#parancsok-generalasa)
    - [Parancs szerkeszet](#parancs-szerkezet)
    - [Closure parancsok](#closure-parancsok)
- [Defining Input Expectations](#defining-input-expectations)
    - [Arguments](#arguments)
    - [Opciók](#options)
    - [Input Arrays](#input-arrays)
    - [Beviteli leírások](#beviteli-leirasok)
- [Command I/O](#command-io)
    - [Retrieving Input](#retrieving-input)
    - [Prompting For Input](#prompting-for-input)
    - [Writing Output](#writing-output)
- [Registering Commands](#registering-commands)
- [Programatically Executing Commands](#programatically-executing-commands)
    - [Calling Commands From Other Commands](#calling-commands-from-other-commands)

<a name="bevezetes"></a>
## Bevezetés


Az Artisan a Laravel-hez mellékelt parancssori interfész. Rengeteg hasznos parancsot biztosít ami segít az alkalmazásod létrehozásában. Az elérhető parancsok listázásához futtasd a `list` parancsot:

```bash
php artisan list
```

Minden parancs tartalmaz egy segítség részt is ami leírja a parancs célját, az elvárt argumentumokat és opciókat. A segítség megjelenítéséhez csak egyszerűen írd a `help` kifejezést a parancs elé:

```bash
php artisan help migrate
```

<a name="parancsok-irasa"></a>
## Parancsok írása

Az eredetileg az Artisan-al jövő parancsok mellé létrehozhatod a sajátjaidat is. A parancsok általában az `app/Console/Commands` mappában találhatóak, de ott tárolhatod a parancsokat ahol szeretnéd, csak a `composer` találja meg.

<a name="parancsok-generalasa"></a>
### Parancsok generálása

Egy új parancs létrehozásához használd a `make:command` Artisan parancsot. Ez létrehoz egy új parancs osztályt a `app/Console/Commands` mappában. Ne aggódj, ha nem létezik ez a mappa, akkor létre fog jönni amikor első alkalommal a `make:command` Artisan parancsot futtatod. A generált parancs egy alap tulajdonság és metódus szerkezetet tartalmaz amire minden Artisan parancs épül.

```bash
php artisan make:command SendEmails
```
<a name="parancs-szerkezet"></a>
### Parancs szerkeszet

A parancs generálása után, ajánlatos kitölteni a parancs osztály `signature` és `description` tulajdonságait, ezek kerülnek megjelenítésre amikor a parancs listázásra került a `list` futtatásakor. A `handle` metódus kerül meghívásra amikor a parancs végrehajtásra kerül.

> {tipp} A jobb kód újrahasznosítás érdekében ajánlatos egyszerűnek tartani a konzol parancsokat és az alkalmazás szolgáltatásokra hagyni a feladatok elvégzésést. A lenti példában egy Service Class -t veszünk rá arra, hogy végezze el a munka nehezét.

Nézzük meg az alábbi példa parancsot. Érdemes megjegyezni, hogy minden szükséges függőséget a parancs konstruktorában adhatunk meg. A Laravel [service container](/docs/{{version}}/container) automatikusan kezeli a konstruktorban megadott függőségeket.

```php
<?php

namespace App\Console\Commands;

use App\User;
use App\DripEmailer;
use Illuminate\Console\Command;

class SendEmails extends Command
{
    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'email:send {user}';

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = 'Send drip e-mails to a user';

    /**
     * The drip e-mail service.
     *
     * @var DripEmailer
     */
    protected $drip;

    /**
     * Create a new command instance.
     *
     * @param  DripEmailer  $drip
     * @return void
     */
    public function __construct(DripEmailer $drip)
    {
        parent::__construct();

        $this->drip = $drip;
    }

    /**
     * Execute the console command.
     *
     * @return mixed
     */
    public function handle()
    {
        $this->drip->send(User::find($this->argument('user')));
    }
}
```

<a name="closure-parancsok"></a>
### Closure parancsok

A Closure alapú parancsok biztosítanak alternatívát az osztály alapú parancsoknak. Hasonlóképpen mint a route Closure -ok a controller-eknek. A `app/Console/Kernel.php` fájl `commands` metódusában a lerevel betölti a `routes/console.php` fájlt:

```php
/**
 * Register the Closure based commands for the application.
 *
 * @return void
 */
protected function commands()
{
    require base_path('routes/console.php');
}
```

Even though this file does not define HTTP routes, it defines console based entry points (routes) into your application. Within this file, you may define all of your Closure based routes using the `Artisan::command` method. The `command` method accepts two arguments: the [command signature](#defining-input-expectations) and a Closure which receives the commands arguments and options:

```php
Artisan::command('build {project}', function ($project) {
    $this->info("Building {$project}!");
});
```

The Closure is bound to the underlying command instance, so you have full access to all of the helper methods you would typically be able to access on a full command class.

#### Type-Hinting Dependencies

In addition to receiving your command's arguments and options, command Closures may also type-hint additional dependencies that you would like resolved out of the [service container](/docs/{{version}}/container):

```php
use App\User;
use App\DripEmailer;

Artisan::command('email:send {user}', function (DripEmailer $drip, $user) {
    $drip->send(User::find($user));
});
```

#### Closure Command Descriptions

When defining a Closure based command, you may use the `describe` method to add a description to the command. This description will be displayed when you run the `php artisan list` or `php artisan help` commands:

```php
Artisan::command('build {project}', function ($project) {
    $this->info("Building {$project}!");
})->describe('Build the project');
```

<a name="defining-input-expectations"></a>
## Defining Input Expectations

When writing console commands, it is common to gather input from the user through arguments or options. Laravel makes it very convenient to define the input you expect from the user using the `signature` property on your commands. The `signature` property allows you to define the name, arguments, and options for the command in a single, expressive, route-like syntax.

<a name="arguments"></a>
### Arguments

All user supplied arguments and options are wrapped in curly braces. In the following example, the command defines one **required** argument: `user`:

```php
/**
 * The name and signature of the console command.
 *
 * @var string
 */
protected $signature = 'email:send {user}';

You may also make arguments optional and define default values for arguments:

// Optional argument...
email:send {user?}

// Optional argument with default value...
email:send {user=foo}
```

<a name="options"></a>
### Options

Options, like arguments, are another form of user input. Options are prefixed by two hyphens (`--`) when they are specified on the command line. There are two types of options: those that receive a value and those that don't. Options that don't receive a value serve as a boolean "switch". Let's take a look at an example of this type of option:

```php
/**
 * The name and signature of the console command.
 *
 * @var string
 */
protected $signature = 'email:send {user} {--queue}';
```

In this example, the `--queue` switch may be specified when calling the Artisan command. If the `--queue` switch is passed, the value of the option will be `true`. Otherwise, the value will be `false`:

```bash
php artisan email:send 1 --queue
```

<a name="options-with-values"></a>
#### Options With Values

Next, let's take a look at an option that expects a value. If the user must specify a value for an option, suffix the option name with a `=` sign:
```php
/**
 * The name and signature of the console command.
 *
 * @var string
 */
protected $signature = 'email:send {user} {--queue=}';
```

In this example, the user may pass a value for the option like so:
```bash
php artisan email:send 1 --queue=default
```

You may assign default values to options by specifying the default value after the option name. If no option value is passed by the user, the default value will be used:

```php
email:send {user} {--queue=default}
```

<a name="option-shortcuts"></a>
#### Option Shortcuts

To assign a shortcut when defining an option, you may specify it before the option name and use a | delimiter to separate the shortcut from the full option name:

```php
email:send {user} {--Q|queue}
```

<a name="input-arrays"></a>
### Input Arrays

If you would like to define arguments or options to expect array inputs, you may use the `*` character. First, let's take a look at an example that specifies an array argument:

```php
email:send {user*}
```

When calling this method, the `user` arguments may be passed in order to the command line. For example, the following command will set the value of `user` to `['foo', 'bar']`:
```bash
php artisan email:send foo bar
```
When defining an option that expects an array input, each option value passed to the command should be prefixed with the option name:

```php
email:send {user} {--id=*}
```
```bash
php artisan email:send --id=1 --id=2
```
<a name="input-descriptions"></a>
### Input Descriptions

You may assign descriptions to input arguments and options by separating the parameter from the description using a colon. If you need a little extra room to define your command, feel free to spread the definition across multiple lines:
```php
/**
 * The name and signature of the console command.
 *
 * @var string
 */
protected $signature = 'email:send
                        {user : The ID of the user}
                        {--queue= : Whether the job should be queued}';
```
<a name="command-io"></a>
## Command I/O

<a name="retrieving-input"></a>
### Retrieving Input

While your command is executing, you will obviously need to access the values for the arguments and options accepted by your command. To do so, you may use the `argument` and `option` methods:
```php
/**
 * Execute the console command.
 *
 * @return mixed
 */
public function handle()
{
    $userId = $this->argument('user');

    //
}
```
If you need to retrieve all of the arguments as an `array`, call the `arguments` method:
```php
$arguments = $this->arguments();
```
Options may be retrieved just as easily as arguments using the `option` method. To retrieve all of the options as an array, call the `options` method:
```php
// Retrieve a specific option...
$queueName = $this->option('queue');

// Retrieve all options...
$options = $this->options();
```
If the argument or option does not exist, `null` will be returned.

<a name="prompting-for-input"></a>
### Prompting For Input

In addition to displaying output, you may also ask the user to provide input during the execution of your command. The `ask` method will prompt the user with the given question, accept their input, and then return the user's input back to your command:
```php
/**
 * Execute the console command.
 *
 * @return mixed
 */
public function handle()
{
    $name = $this->ask('What is your name?');
}
```
The `secret` method is similar to `ask`, but the user's input will not be visible to them as they type in the console. This method is useful when asking for sensitive information such as a password:
```php
$password = $this->secret('What is the password?');
```
#### Asking For Confirmation

If you need to ask the user for a simple confirmation, you may use the `confirm` method. By default, this method will return `false`. However, if the user enters `y` in response to the prompt, the method will return `true`.
```php
if ($this->confirm('Do you wish to continue? [y|N]')) {
    //
}
```
#### Auto-Completion

The `anticipate` method can be used to provide auto-completion for possible choices. The user can still choose any answer, regardless of the auto-completion hints:
```php
$name = $this->anticipate('What is your name?', ['Taylor', 'Dayle']);
```
#### Multiple Choice Questions

If you need to give the user a predefined set of choices, you may use the `choice` method. You may set the default value to be returned if no option is chosen:
```php
$name = $this->choice('What is your name?', ['Taylor', 'Dayle'], $default);
```
<a name="writing-output"></a>
### Writing Output

To send output to the console, use the `line`, `info`, `comment`, `question` and `error` methods. Each of these methods will use appropriate ANSI colors for their purpose. For example, let's display some general information to the user. Typically, the `info` method will display in the console as green text:
```php
/**
 * Execute the console command.
 *
 * @return mixed
 */
public function handle()
{
    $this->info('Display this on the screen');
}
```
To display an error message, use the `error` method. Error message text is typically displayed in red:
```php
$this->error('Something went wrong!');
```
If you would like to display plain, uncolored console output, use the `line` method:
```php
$this->line('Display this on the screen');
```
#### Table Layouts

The `table` method makes it easy to correctly format multiple rows / columns of data. Just pass in the headers and rows to the method. The width and height will be dynamically calculated based on the given data:
```php
$headers = ['Name', 'Email'];

$users = App\User::all(['name', 'email'])->toArray();

$this->table($headers, $users);
```
#### Progress Bars

For long running tasks, it could be helpful to show a progress indicator. Using the output object, we can start, advance and stop the Progress Bar. First, define the total number of steps the process will iterate through. Then, advance the Progress Bar after processing each item:
```php
$users = App\User::all();

$bar = $this->output->createProgressBar(count($users));

foreach ($users as $user) {
    $this->performTask($user);

    $bar->advance();
}

$bar->finish();
```
For more advanced options, check out the [Symfony Progress Bar component documentation](http://symfony.com/doc/2.7/components/console/helpers/progressbar.html).

<a name="registering-commands"></a>
## Registering Commands

Once your command is finished, you need to register it with Artisan. All commands are registered in the `app/Console/Kernel.php` file. Within this file, you will find a list of commands in the `commands` property. To register your command, simply add the command's class name to the list. When Artisan boots, all the commands listed in this property will be resolved by the [service container](/docs/{{version}}/container) and registered with Artisan:
```php
protected $commands = [
    Commands\SendEmails::class
];
```
<a name="programatically-executing-commands"></a>
## Programatically Executing Commands

Sometimes you may wish to execute an Artisan command outside of the CLI. For example, you may wish to fire an Artisan command from a route or controller. You may use the `call` method on the `Artisan` facade to accomplish this. The `call` method accepts the name of the command as the first argument, and an array of command parameters as the second argument. The exit code will be returned:
```php
Route::get('/foo', function () {
    $exitCode = Artisan::call('email:send', [
        'user' => 1, '--queue' => 'default'
    ]);

    //
});
```
Using the `queue` method on the `Artisan` facade, you may even queue Artisan commands so they are processed in the background by your [queue workers](/docs/{{version}}/queues). Before using this method, make sure you have configured your queue and are running a queue listener:
```php
Route::get('/foo', function () {
    Artisan::queue('email:send', [
        'user' => 1, '--queue' => 'default'
    ]);

    //
});
```
If you need to specify the value of an option that does not accept string values, such as the `--force` flag on the `migrate:refresh` command, you may pass `true` or `false`:
```php
$exitCode = Artisan::call('migrate:refresh', [
    '--force' => true,
]);
```
<a name="calling-commands-from-other-commands"></a>
### Calling Commands From Other Commands

Sometimes you may wish to call other commands from an existing Artisan command. You may do so using the `call` method. This `call` method accepts the command name and an array of command parameters:
```php
/**
 * Execute the console command.
 *
 * @return mixed
 */
public function handle()
{
    $this->call('email:send', [
        'user' => 1, '--queue' => 'default'
    ]);

    //
}
```
If you would like to call another console command and suppress all of its output, you may use the `callSilent` method. The `callSilent` method has the same signature as the `call` method:
```php
$this->callSilent('email:send', [
    'user' => 1, '--queue' => 'default'
]);
```