# PHP Specialist — Code Reference

> This file contains code examples extracted from [SKILL.md](./SKILL.md). Refer back to the main skill file for decision tables, process phases, and design guidance.

## Enums (PHP 8.1+)

```php
// Backed enum with methods — replaces class constants and magic strings
enum OrderStatus: string
{
    case Draft     = 'draft';
    case Pending   = 'pending';
    case Confirmed = 'confirmed';
    case Shipped   = 'shipped';
    case Delivered = 'delivered';
    case Cancelled = 'cancelled';

    public function label(): string
    {
        return match ($this) {
            self::Draft     => 'Draft',
            self::Pending   => 'Pending Review',
            self::Confirmed => 'Confirmed',
            self::Shipped   => 'Shipped',
            self::Delivered => 'Delivered',
            self::Cancelled => 'Cancelled',
        };
    }

    public function isFinal(): bool
    {
        return in_array($this, [self::Delivered, self::Cancelled], true);
    }

    /** @return list<self> */
    public static function active(): array
    {
        return array_filter(self::cases(), fn (self $s) => ! $s->isFinal());
    }
}
```

## Readonly Properties and Classes (PHP 8.1 / 8.2)

```php
// Readonly class — all properties are implicitly readonly
readonly class Money
{
    public function __construct(
        public int    $amount,
        public string $currency,
    ) {}

    public function add(self $other): self
    {
        if ($this->currency !== $other->currency) {
            throw new CurrencyMismatchException($this->currency, $other->currency);
        }

        return new self($this->amount + $other->amount, $this->currency);
    }

    public function isPositive(): bool
    {
        return $this->amount > 0;
    }
}
```

## Constructor Promotion

```php
class CreateUserAction
{
    public function __construct(
        private readonly UserRepository $users,
        private readonly Hasher         $hasher,
        private readonly EventDispatcher $events,
    ) {}

    public function execute(CreateUserData $data): User
    {
        $user = $this->users->create([
            'name'     => $data->name,
            'email'    => $data->email,
            'password' => $this->hasher->make($data->password),
        ]);

        $this->events->dispatch(new UserCreated($user));

        return $user;
    }
}
```

## Named Arguments

```php
// Improves readability for functions with many parameters or boolean flags
$user = User::create(
    name: $request->name,
    email: $request->email,
    isAdmin: false,
    sendWelcomeEmail: true,
);

// Particularly valuable with optional parameters
$response = Http::timeout(seconds: 30)
    ->retry(times: 3, sleepMilliseconds: 500, throw: true)
    ->get($url);
```

## Match Expressions

```php
// match is strict (===), exhaustive, and returns a value
$discount = match (true) {
    $total >= 10000 => 0.15,
    $total >= 5000  => 0.10,
    $total >= 1000  => 0.05,
    default         => 0.00,
};

// Replaces switch with no fall-through risk
$handler = match ($event::class) {
    OrderPlaced::class   => new HandleOrderPlaced(),
    PaymentFailed::class => new HandlePaymentFailed(),
    default              => throw new UnhandledEventException($event),
};
```

## Union and Intersection Types

```php
// Union type — accepts either type
function findUser(int|string $identifier): User
{
    return is_int($identifier)
        ? User::findOrFail($identifier)
        : User::where('email', $identifier)->firstOrFail();
}

// Intersection type — must satisfy all interfaces
function processLoggableEntity(Loggable&Serializable $entity): void
{
    $entity->log();
    $data = $entity->serialize();
}

// DNF types (PHP 8.2) — combine union and intersection
function handle((Renderable&Countable)|string $content): string
{
    if (is_string($content)) {
        return $content;
    }

    return $content->render();
}
```

## First-Class Callable Syntax (PHP 8.1+)

```php
// Create closures from named functions
$slugify = Str::slug(...);
$titles  = array_map($slugify, $names);

// Method references
$validator = Validator::make(...);

// Useful for pipeline / collection patterns
$activeUsers = collect($users)
    ->filter(UserPolicy::isActive(...))
    ->map(UserTransformer::toArray(...))
    ->values();
```

## Fibers (PHP 8.1+)

```php
// Fibers enable cooperative multitasking — foundation for async frameworks
$fiber = new Fiber(function (): void {
    $value = Fiber::suspend('paused');
    echo "Resumed with: {$value}";
});

$result = $fiber->start();        // Returns 'paused'
$fiber->resume('hello world');    // Prints: "Resumed with: hello world"

// Practical use: async HTTP client internals, event loops (Revolt, ReactPHP)
// Application developers rarely use Fiber directly — frameworks abstract it
```

## PSR-4 Autoloading

```json
{
    "autoload": {
        "psr-4": {
            "App\\": "app/",
            "Domain\\": "src/Domain/"
        }
    },
    "autoload-dev": {
        "psr-4": {
            "Tests\\": "tests/"
        }
    }
}
```

Rule: namespace segment maps 1:1 to directory. `App\Http\Controllers\UserController` lives at `app/Http/Controllers/UserController.php`.

## PHPStan Configuration

```neon
# phpstan.neon
parameters:
    level: 9
    paths:
        - app
        - src
    excludePaths:
        - app/Console/Kernel.php
    ignoreErrors: []
    checkMissingIterableValueType: true
    checkGenericClassInNonGenericObjectType: true

includes:
    - vendor/larastan/larastan/extension.neon  # Laravel-specific rules
```

## PHP CS Fixer / Pint

```php
// .php-cs-fixer.php — for non-Laravel projects
return (new PhpCsFixer\Config())
    ->setRules([
        '@PER-CS'            => true,
        'strict_types'       => true,
        'declare_strict_types' => true,
        'ordered_imports'    => ['sort_algorithm' => 'alpha'],
        'no_unused_imports'  => true,
        'trailing_comma_in_multiline' => true,
    ])
    ->setFinder(
        PhpCsFixer\Finder::create()->in([__DIR__ . '/src', __DIR__ . '/tests'])
    );
```

## Dependency Inversion Example

```php
// Contract (abstraction)
interface PaymentGateway
{
    public function charge(Money $amount, PaymentMethod $method): PaymentResult;
}

// Implementation (concretion) — can be swapped without changing consumers
final class StripeGateway implements PaymentGateway
{
    public function __construct(private readonly StripeClient $client) {}

    public function charge(Money $amount, PaymentMethod $method): PaymentResult
    {
        // Stripe-specific logic
    }
}

// Consumer depends on abstraction only
final class ProcessPaymentAction
{
    public function __construct(private readonly PaymentGateway $gateway) {}

    public function execute(Order $order): PaymentResult
    {
        return $this->gateway->charge($order->total, $order->paymentMethod);
    }
}
```

## Custom Exception Hierarchy

```php
// Base domain exception
abstract class DomainException extends \RuntimeException {}

// Specific exceptions with factory methods
final class InsufficientFundsException extends DomainException
{
    public static function forAccount(Account $account, Money $required): self
    {
        return new self(sprintf(
            'Account %s has %d %s but %d %s is required.',
            $account->id,
            $account->balance->amount,
            $account->balance->currency,
            $required->amount,
            $required->currency,
        ));
    }
}
```

## Result Pattern (Error as Value)

```php
/** @template T */
readonly class Result
{
    /** @param T|null $value */
    private function __construct(
        public bool    $ok,
        public mixed   $value = null,
        public ?string $error = null,
    ) {}

    /** @param T $value */
    public static function success(mixed $value): self
    {
        return new self(ok: true, value: $value);
    }

    public static function failure(string $error): self
    {
        return new self(ok: false, error: $error);
    }
}

// Usage — caller must handle both paths
$result = $action->execute($data);
if (! $result->ok) {
    return response()->json(['error' => $result->error], 422);
}
```

## Branded / Opaque Types via Readonly Classes

```php
// Prevent accidental mixing of IDs from different entities
readonly class UserId
{
    public function __construct(public int $value) {}

    public function equals(self $other): bool
    {
        return $this->value === $other->value;
    }
}

readonly class OrderId
{
    public function __construct(public int $value) {}
}

// Compiler prevents: processOrder(new UserId(1)) when OrderId is expected
function processOrder(OrderId $orderId): void { /* ... */ }
```

## Generic Collections via PHPStan Annotations

```php
/**
 * @template T
 * @implements \IteratorAggregate<int, T>
 */
final class TypedCollection implements \IteratorAggregate, \Countable
{
    /** @param list<T> $items */
    public function __construct(private array $items = []) {}

    /** @param T $item */
    public function add(mixed $item): void
    {
        $this->items[] = $item;
    }

    /** @return \ArrayIterator<int, T> */
    public function getIterator(): \ArrayIterator
    {
        return new \ArrayIterator($this->items);
    }

    public function count(): int
    {
        return count($this->items);
    }
}
