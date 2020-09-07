# blade-templates-autodetect

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
             Blade::component($component_name, 'App\View\AutoDetectBlade');
        }
    }
}

```
