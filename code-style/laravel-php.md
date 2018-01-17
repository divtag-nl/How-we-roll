[← Terug](/)

# Laravel en PHP

Alle richtlijnen die hier beschreven staan zijn precies dat, richtlijnen. Het is de bedoeling dat iedereen zich zo goed mogelijk aan deze richtlijnen houd, maar er kan van worden afgeweken indien hier een goed reden voor is.

- [Artisan commands](#artisan-commands)
- [Form request validation](#form-request-validation)
- [Ternary operators](#ternary-operators)

## PHP

Alle PHP code die we schrijven moet voldoen aan de [PSR-1](http://www.php-fig.org/psr/psr-1/) en [PSR-2](http://www.php-fig.org/psr/psr-2/) standaarden. Hiernaast moet iedere applicatie die wij ontwikkelen zo goed mogelijk voldoen aan de punten beschreven op [12factor.net](https://12factor.net/). Wij volgen deze standaarden om het ontwikkelen van een applicatie met een team van meerdere personen te stroomlijnen, applicaties duurzaam en onderhoudbaar te maken. En om ieder project dat wij opleveren met zo min mogelijk frictie te kunnen hosten in een moderne, schaalbare cloud omgeving. 

### Artisan commands

De naam van ieder artisan command zou kebab-case moeten zijn. 

```php
// Good
php artisan delete-old-records

// Bad
php artisan deleteOldRecords
```

Een command zou altijd feedback moeten geven om te laten zien dat het succesvol uitgevoerd is.

```php
class Foo extends Command
{
    public function handle()
    {
        // Do something

        $this->comment('Done!');
    }
}
```

Wanneer een artisan command in de scheduler gebruikt wordt zou hier altijd de `::class` constant voor gebruikt moeten worden in plaats van de signature.

```php
class Kernel extends ConsoleKernel
{
    protected function schedule(Schedule $schedule)
    {
        // Good
        $schedule->command(Foo::class, ['--force'])->daily();
        
        // Bad
        $schedule->command('foo --force')->daily();
    }
}
```

### Form request validation

Gebruiker wanneer er meerdere validatie regels op een veld zijn altijd een array, hierdoor blijft de validatie een stuk beter leesbaar en kan er zonder problemen van meerdere regels code gebruikt worden gemaakt wanneer er veel validatie regels nodig zijn.

```php
public function rules()
{
    // Good
    return [
        'title' => ['required', 'min:10', 'max:255'],
    ];
    
    // Bad
    return [
        'title' => 'required|minx:10|max:255',
    ];
}
```

Zet binnen een request consistent de regels in strings of arrays. Anders worden de regels snel onoverzichtelijk.

```php
public function rules()
{
    // Good
    return [
        'title'       => ['required'],
        'description' => ['required', 'max:255'],
    ];
    
    // Bad
    return [
        'title'       => 'required',
        'description' => ['required', 'max:255'],
    ];
}
```

Gebruik de methodes van de `Rule` class wanneer deze beschikbaar zijn. Hierdoor kunnen de regels makkelijker leesbaar geformat worden en is er syntax highlighting beschikbaar. Dit maakt de regels een stuk beter leesbaar, zeker wanner er complexe regels nodig zijn zoals bij `unique` of `exists` regels vaak het geval is.

```php
public function rules()
{
    // Good
    return [
        'email' => [
            Rule::unique('users', 'email_address'),
        ],
    ];
    
    // Bad
    return [
        'email' => [
            'unique:users,email_address',
        ],
    ];
}
```

### Ternary operators

Ieder gedeelte van een ternary if statement zou op een eigen regel moeten staat, behalve als het een heel korte statement is.

```php
// Good
$result = $foo instanceof Model
    ? $foo->name
    : 'A default value';

$result = $foo ? 'foo' : 'bar';

// Bad
$result = $foo instanceof Model ?
    $foo->name : 
   'A default value';
```
