# blade-templates-autodetect

## Setup Guide

1. Create the following file `app/View/AutoDetectBlade.php`:

```php
<?php

namespace App\View;

use Illuminate\View\Component;

class AutoDetectBlade extends Component
{
    /**
     * Create a new component instance.
     */
    public function __construct()
    {
    }

    /**
     * Get the view / contents that represent the component.
     *
     * @return \Illuminate\View\View|string
     */
    public function render()
    {
        return view('components.' . $this->componentName);
    }
}
``` 

2. Modify the following file `app/Providers/AppServiceProvider.php`:

```php
use Illuminate\Support\Facades\Blade;

/**
 * Bootstrap any application services.
 */
public function boot()
{
     $this->autoDetectBlade();
}

/**
 * Auto-detect Blade Templates.
 */
protected function autoDetectBlade()
{
    $initialized = [];

    foreach (array_diff(scandir(base_path() . '/resources/views/components'), ['.', '..']) as $full_name) {
        $component_name = explode('.', $full_name)[0];
        if (! in_array($component_name, $initialized)) {
             $initialized[] = $component_name;
             Blade::component($component_name, 'App\View\AutoDetectBlade');
        }
    }
}

```

## Template Examples

1. Button example `resources/views/components/button.blade.php`:

```blade
@php

$class = 'inline-flex justify-center w-full rounded-md border px-4 py-2 text-base leading-6 font-medium shadow-sm focus:outline-none transition ease-in-out duration-150 sm:text-sm sm:leading-5';

if (($attributes['kind'] ?? 'primary') === 'primary') {
    $class .= "border-transparent bg-{$attributes['color']}-600 hover:bg-{$attributes['color']}-500 text-white focus:border-{$attributes['color']}-700 focus:shadow-outline-{$attributes['color']}";
} else {
    $class .= 'border-gray-300 hover:text-gray-500 bg-white text-gray-700 focus:border-blue-300 focus:shadow-outline-blue';
}

@endphp

<span class="inline-flex rounded-md-shadow-sm">
    @if(($attributes['as'] ?? 'button') === 'button')
        <button type="button" {{ $attributes->merge(['class' => $class]) }}>
            {{ $slot }}
        </button>
    @elseif($attributes['as'] === 'a')
        <a {{ $attributes->merge(['class' => $class]) }}>
            {{ $slot }}
        </a>
    @endif
</span>
```
