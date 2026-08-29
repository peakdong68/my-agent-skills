# Laravel Specialist -- Code Reference

Code examples referenced from [SKILL.md](./SKILL.md).

## Scopes

```php
// Local scope -- reusable query constraint
public function scopeActive(Builder $query): Builder
{
    return $query->where('status', 'active');
}

// Usage: User::active()->where('role', 'admin')->get();

// Global scope -- applied to all queries on the model
protected static function booted(): void
{
    static::addGlobalScope('published', function (Builder $builder) {
        $builder->whereNotNull('published_at');
    });
}
```

## Accessors, Mutators, and Casts

```php
// Attribute accessor/mutator (Laravel 11+ syntax)
protected function fullName(): Attribute
{
    return Attribute::make(
        get: fn () => "{$this->first_name} {$this->last_name}",
    );
}

// Custom cast
protected function casts(): array
{
    return [
        'options'    => AsCollection::class,
        'address'    => AddressCast::class,
        'status'     => OrderStatus::class,  // Backed enum
        'metadata'   => 'array',
        'is_active'  => 'boolean',
        'amount'     => MoneyCast::class,
    ];
}
```

## Eager Loading

```php
// BAD -- N+1 problem: 1 query for posts + N queries for authors
$posts = Post::all();
foreach ($posts as $post) {
    echo $post->author->name;  // Triggers lazy load each iteration
}

// GOOD -- Eager load: 2 queries total
$posts = Post::with('author')->get();

// Nested eager loading
$posts = Post::with(['author', 'comments.user'])->get();

// Constrained eager loading
$posts = Post::with(['comments' => function ($query) {
    $query->where('approved', true)->latest()->limit(5);
}])->get();

// Prevent lazy loading in development
Model::preventLazyLoading(! app()->isProduction());
```

## Livewire Patterns

```php
// Full-page Livewire component (Livewire 3+)
#[Layout('layouts.app')]
#[Title('Dashboard')]
class Dashboard extends Component
{
    public string $search = '';

    #[Computed]
    public function users(): LengthAwarePaginator
    {
        return User::where('name', 'like', "%{$this->search}%")->paginate(15);
    }

    public function render(): View
    {
        return view('livewire.dashboard');
    }
}
```

## Job Design

```php
class ProcessInvoice implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    public int $tries = 3;
    public int $backoff = 60;
    public int $timeout = 120;
    public string $queue = 'invoices';

    public function __construct(public readonly Invoice $invoice) {}

    public function handle(PdfGenerator $generator): void
    {
        $generator->generate($this->invoice);
    }

    public function failed(Throwable $exception): void
    {
        // Notify admin, log to error tracker
    }
}
```

## Event / Listener Pattern

```php
// Dispatch event
OrderPlaced::dispatch($order);

// Listener (queued)
class SendOrderConfirmation implements ShouldQueue
{
    public function handle(OrderPlaced $event): void
    {
        Mail::to($event->order->user)->send(new OrderConfirmationMail($event->order));
    }
}
```

## Middleware Stack

```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->web(append: [
        HandleInertiaRequests::class,  // After session, before response
    ]);

    $middleware->api(prepend: [
        EnsureFrontendRequestsAreStateful::class,  // Sanctum SPA auth
    ]);

    $middleware->alias([
        'role'     => EnsureUserHasRole::class,
        'verified' => EnsureEmailIsVerified::class,
    ]);
})
```

## Pest Tests

### Unit Test

```php
test('order total calculates tax correctly', function () {
    $order = Order::factory()->make(['subtotal' => 10000, 'tax_rate' => 0.08]);

    expect($order->total)->toBe(10800);
});
```

### Feature Test

```php
test('authenticated user can create a post', function () {
    $user = User::factory()->create();

    $response = $this->actingAs($user)
        ->postJson('/api/posts', [
            'title' => 'My Post',
            'body'  => 'Content here.',
        ]);

    $response->assertCreated()
        ->assertJsonPath('data.title', 'My Post');

    $this->assertDatabaseHas('posts', [
        'user_id' => $user->id,
        'title'   => 'My Post',
    ]);
});
```

### Queue and Event Fakes

```php
test('placing an order dispatches confirmation job', function () {
    Queue::fake();

    $order = Order::factory()->create();
    PlaceOrder::dispatch($order);

    Queue::assertPushed(SendOrderConfirmation::class, function ($job) use ($order) {
        return $job->order->id === $order->id;
    });
});
```

### Browser Test (Dusk)

```php
test('user can complete checkout flow', function () {
    $this->browse(function (Browser $browser) {
        $browser->loginAs(User::factory()->create())
            ->visit('/cart')
            ->press('Checkout')
            ->waitForText('Order Confirmed')
            ->assertSee('Thank you');
    });
});
```

## Migration Conventions

```php
// Always include down() for rollback capability
public function up(): void
{
    Schema::create('invoices', function (Blueprint $table) {
        $table->id();
        $table->foreignId('user_id')->constrained()->cascadeOnDelete();
        $table->string('number')->unique();
        $table->integer('amount');          // Store money as cents
        $table->string('currency', 3);
        $table->string('status')->default('draft');
        $table->timestamp('due_at')->nullable();
        $table->timestamps();
        $table->softDeletes();

        $table->index(['user_id', 'status']);
    });
}
```

## Factory Patterns

```php
class InvoiceFactory extends Factory
{
    public function definition(): array
    {
        return [
            'user_id'  => User::factory(),
            'number'   => $this->faker->unique()->numerify('INV-####'),
            'amount'   => $this->faker->numberBetween(1000, 100000),
            'currency' => 'USD',
            'status'   => 'draft',
            'due_at'   => now()->addDays(30),
        ];
    }

    public function paid(): static
    {
        return $this->state(fn () => ['status' => 'paid']);
    }

    public function overdue(): static
    {
        return $this->state(fn () => [
            'status' => 'sent',
            'due_at' => now()->subDays(7),
        ]);
    }
}
```

## Directory Structure

```
app/
├── Actions/              # Single-purpose action classes
├── Casts/                # Custom Eloquent casts
├── Console/Commands/     # Artisan commands
├── Enums/                # PHP backed enums
├── Events/               # Event classes
├── Exceptions/           # Custom exception classes
├── Http/
│   ├── Controllers/      # Resourceful or single-action controllers
│   ├── Middleware/        # Request/response middleware
│   └── Requests/         # Form Request validation
├── Jobs/                 # Queued job classes
├── Listeners/            # Event listener classes
├── Mail/                 # Mailable classes
├── Models/               # Eloquent models
├── Notifications/        # Notification classes
├── Observers/            # Model observers
├── Policies/             # Authorization policies
├── Providers/            # Service providers
├── Rules/                # Custom validation rules
├── Services/             # Domain service classes
└── View/Components/      # Blade view components
database/
├── factories/            # Model factories
├── migrations/           # Schema migrations (timestamped)
└── seeders/              # Database seeders
resources/views/
├── components/           # Blade components
├── layouts/              # Layout templates
├── livewire/             # Livewire component views
└── mail/                 # Email templates
routes/
├── api.php               # API routes
├── channels.php          # Broadcast channels
├── console.php           # Artisan closures
└── web.php               # Web routes
tests/
├── Feature/              # Feature (integration) tests
├── Unit/                 # Unit tests
└── Browser/              # Dusk browser tests
```
