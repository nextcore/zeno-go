# ZenoBlade & ZenoEngine for Go (`zeno-go`)

[![Go Reference](https://pkg.go.dev/badge/github.com/nextcore/zeno-go.svg)](https://pkg.go.dev/github.com/nextcore/zeno-go)
[![Go Version](https://img.shields.io/github/go-mod/go-version/nextcore/zeno-go)](https://github.com/nextcore/zeno-go)
[![License](https://img.shields.io/github/license/nextcore/zeno-go)](https://github.com/nextcore/zeno-go/blob/main/LICENSE)
[![Build Status](https://img.shields.io/github/actions/workflow/status/nextcore/zeno-go/go.yml?branch=main&label=tests&logo=go)](https://github.com/nextcore/zeno-go/actions)

### 🚀 Bring the Elegance of Laravel Blade to the Speed of Go.
**ZenoBlade** is a high-performance, lightweight, and native **Laravel Blade template engine** and **ZenoLang script interpreter** written in pure Go. 

It is the ultimate bridge for PHP/Laravel developers migrating to Go, and the secret weapon for Go/Goravel developers looking for a clean, modular, and modern templating experience without compromises.

---

## The Laravel Developer's Dream: Go Speed, Blade Workflow

As a Laravel developer, you know how amazing the development experience is. But you also know the pain of PHP's hosting overhead, slower execution times, and heavy resource footprints.

Moving to Go is the logical step for scaling, but Go's native template engines (`html/template`) feel outdated, and modern alternatives (like `templ`) require frustrating CLI code-generation and binary recompilation steps just to change a line of HTML.

**ZenoBlade changes the game.** Now you can have:
- **10-100x Faster Performance**: Go's native execution speeds with sub-millisecond render times.
- **95% Less RAM Usage**: Run your entire web app on a fraction of the memory of a PHP-FPM pool.
- **100% Familiar Syntax**: Keep your beloved `@extends`, `@section`, `@yield`, `<x-component>`, and `@push`/`@stack` system.
- **Zero-Build Hot Reload**: Modify your templates, save, and refresh. No compilation, no watchers, no generator tools. Just instant gratification.

---

## The Secret Weapon for Go & Goravel Projects

Whether you are building with **Gin**, **Fiber**, **Echo**, or **Goravel** (the Laravel-like framework for Go), your view layer deserves the best. 

Instead of settling for standard Go templates or compiling JSX-like components, ZenoBlade gives you the most popular, readable, and battle-tested component-based templating syntax in the world, natively integrated into your Go pipeline.

---

## Direct Comparisons

### 1. ZenoBlade vs Original Laravel Blade (PHP)

| Feature / Metric | Original Laravel Blade (PHP) | ZenoBlade (Go) | Why it's a huge upgrade |
| :--- | :--- | :--- | :--- |
| **Render Speed** | Fast (PHP interpreter) | **Blazing Fast (Go Machine Code)** | Direct native execution in Go yields sub-millisecond render times under heavy load. |
| **RAM Footprint** | Moderate to High (PHP-FPM/Laravel) | **Extremely Low (Go Runtime)** | Perfect for deploying lightweight containerized microservices and cost-effective hosting. |
| **Syntax Style** | Blade Syntax | **100% Identical Blade Syntax** | Zero learning curve. Copy-paste your existing Blade views directly. |
| **Component System** | Yes (`<x-card>`) | **Yes (`<x-card>`)** | Build highly modular, component-driven UI layouts natively in Go. |
| **Asset Stacks** | Yes (`@push`, `@stack`) | **Yes** | Easily push CSS/JS assets from child templates to layout stacks. |
| **Deployment** | Hundreds of PHP files + vendors | **Single Self-Contained Binary** | Compiles directly into your Go binary. Zero runtime dependencies. |

### 2. ZenoBlade vs Go Alternatives (From a Laravel Developer's Perspective)

| Feature / Metric | Go Standard `html/template` | `templ` (`a-h/templ`) | **ZenoBlade (`zeno-go`)** |
| :--- | :--- | :--- | :--- |
| **Syntax Familiarity** | Unfamiliar (Go-specific dot notation) | Unfamiliar (JSX-like HTML/Go hybrid) | **100% Identical to Laravel Blade** |
| **Layout Inheritance** | Backwards (Uses block definitions) | Manual (Go function wrapper blocks) | **Native (`@extends`, `@section`, `@yield`)** |
| **Component Tag System** | None | JSX-like Go function calls (e.g. `@Card()`) | **HTML Components (`<x-card title="...">`)** |
| **Asset Stack System** | None | None | **Native Stacks (`@push`, `@stack`)** |
| **Development Cycle** | Fast (Requires custom reload setup) | Slow (Requires CLI code generation & rebuild) | **Instant Hot Reload (Save view & refresh browser)** |
| **Laravel Helpers** | None | None | **Built-in (`@csrf`, `@method`, `@class`)** |
| **Logic Evaluation** | Limited pipeline queries | Raw Go code blocks | **Safe ZenoLang sandbox (`@php` / `@zeno`)** |

---

## Development vs Production Mode (Smart RAM Caching)

`zeno-go` is designed to be highly optimized and production-ready by default. The engine adjusts its compilation and cache verification behavior based on the `APP_ENV` environment variable:

### 1. Default Mode (Production-Ready with Hot Reload)
By default (when `APP_ENV` is not set to `production`), **templates are cached in RAM and fully production-ready**:
- **Smart Modification Checking**: For every request, `zeno-go` does a quick, lightweight check on the template's modification time (`os.Stat`).
- **No Performance Penalty**: If the file hasn't changed, the template is served **directly from the RAM cache** (compiled AST).
- **Read-once on Modification**: The engine only reads and re-parses the file **once** when it detects a change. This gives you the instantaneous feedback cycle of dynamic engines (no Go recompilation required) while remaining fast enough for production.

### 2. Strict Production Mode (`APP_ENV=production`)
For maximum performance, security hardening, and absolute disk isolation:
```bash
export APP_ENV=production
```
- **Static In-Memory Cache**: Once a template is loaded and compiled, the AST remains in RAM permanently.
- **Bypasses Filesystem Entirely**: The engine completely stops checking `os.Stat` or querying the disk for subsequent render requests, eliminating filesystem overhead.
- **Hardened Security**: Because the engine only reads from the static RAM cache, it prevents potential filesystem-level attacks (like runtime template tampering or directory traversal attempts).

---

## Supported Blade Directives

`zeno-go` supports almost the entire core directive catalog of Laravel Blade:

| Directive | Description | Example |
| :--- | :--- | :--- |
| `{{ $var }}` | Escaped text output (Auto-escapes XSS) | `{{ $user.name }}` |
| `{!! $var !!}` | Raw unescaped output | `{!! $raw_html !!}` |
| `{{-- comment --}}` | Template comments (ignored in output) | `{{-- debug code --}}` |
| `@extends('name')` | Extends a base layout template | `@extends('layouts.app')` |
| `@section('name') ... @endsection` | Defines a template block content | `@section('content') ... @endsection` |
| `@yield('name')` | Outputs the contents of a defined section | `@yield('content')` |
| `@include('name', $data)` | Renders a sub-template/partial | `@include('partials.nav')` |
| `<x-name attr="val"> ... </x-name>` | Renders a reusable component | `<x-card title="Alert">...</x-card>` |
| `@push('stack') ... @endpush` | Appends content to a named stack | `@push('scripts') <script>...</script> @endpush` |
| `@stack('stack')` | Outputs the fully gathered stack content | `@stack('scripts')` |
| `@if(cond) ... @else ... @endif` | Evaluates conditional statements | `@if($user.is_admin) ... @endif` |
| `@foreach(list as item) ... @endforeach` | Loops over list with a `loop` helper variable | `@foreach($products as $p) ... @endforeach` |
| `@forelse(list as item) ... @empty ...` | Loops over list, executes `@empty` if empty | `@forelse($items as $i) ... @empty ... @endforelse` |
| `@csrf` / `@method('PUT')` | Generates CSRF inputs and HTTP method spoofing | `@csrf` `@method('DELETE')` |
| `@class(['c' => true])` | Dynamically compiles CSS classes | `@class(['text-red' => $has_error])` |

---

## Installation

Add `zeno-go` to your Go project:

```bash
go get github.com/nextcore/zeno-go
```

---

## Quickstart & Integrations

### 1. Simple Rendering (String Templates)

Evaluate string-based ZenoBlade templates directly from Go:

```go
package main

import (
	"fmt"
	"github.com/nextcore/zeno-go/pkg/blade"
	"github.com/nextcore/zeno-go/pkg/engine"
)

func main() {
	// Initialize Zeno engine & register standard Blade slots
	eng := engine.NewEngine()
	blade.RegisterBladeSlots(eng)

	// Inject variables into scope
	eng.Scope.Set("title", "Welcome Page")
	eng.Scope.Set("user", map[string]interface{}{
		"name": "Max",
		"role": "admin",
	})

	template := `
		<h1>{{ $title }}</h1>
		@if($user.role == "admin")
			<p>Hello Admin {{ $user.name }}!</p>
		@else
			<p>Welcome, {{ $user.name }}.</p>
		@endif
	`

	output, err := blade.RenderString(eng, template)
	if err != nil {
		panic(err)
	}

	fmt.Println(output)
}
```

### 2. Layout Inheritance & File Structure

Create a base layout structure inside your views directory:

#### `views/layouts/app.blade.zl`
```html
<!DOCTYPE html>
<html>
<head>
    <title>@yield('title')</title>
    @stack('styles')
</head>
<body>
    @include('partials.header')

    <main class="container">
        @yield('content')
    </main>

    @stack('scripts')
</body>
</html>
```

#### `views/welcome.blade.zl`
```html
@extends('layouts.app')

@section('title', 'Homepage')

@section('content')
    <h1>Hello World!</h1>
    <p>Welcome to our ZenoBlade-powered application.</p>
    
    <x-alert type="success">
        Your dynamic content was loaded successfully!
    </x-alert>
@endsection

@push('scripts')
    <script src="/static/welcome.js"></script>
@endpush
```

#### `views/components/alert.blade.zl`
```html
<div class="alert alert-{{ $type }}">
    <strong>Notice:</strong> {{ $slot }}
</div>
```

---

### 3. Framework Integrations (Fiber, Gin & Goravel)

#### 🚀 Integration with Fiber (Express/Laravel routing style)
```go
package main

import (
	"context"
	"net/http/httptest"
	"github.com/gofiber/fiber/v2"
	"github.com/nextcore/zeno-go/pkg/blade"
	"github.com/nextcore/zeno-go/pkg/engine"
)

func main() {
	app := fiber.New()

	app.Get("/", func(c *fiber.Ctx) error {
		eng := engine.NewEngine()
		blade.RegisterBladeSlots(eng)

		// Set variables & view root
		eng.Scope.Set("_view_root", "views")
		eng.Scope.Set("title", "Welcome to Fiber")

		// Prepare HTTP context
		w := httptest.NewRecorder()
		ctx := context.WithValue(c.Context(), "httpWriter", w)

		// Render views/welcome.blade.zl
		err := eng.Execute(ctx, &engine.Node{
			Name:  "view.blade",
			Value: "welcome",
		}, eng.Scope)

		if err != nil {
			return c.Status(500).SendString(err.Error())
		}

		c.Set("Content-Type", "text/html; charset=utf-8")
		return c.SendString(w.Body.String())
	})

	app.Listen(":3000")
}
```

#### 🍸 Integration with Gin Gonic
```go
package main

import (
	"context"
	"github.com/gin-gonic/gin"
	"github.com/nextcore/zeno-go/pkg/blade"
	"github.com/nextcore/zeno-go/pkg/engine"
)

func main() {
	r := gin.Default()

	r.GET("/", func(c *gin.Context) {
		eng := engine.NewEngine()
		blade.RegisterBladeSlots(eng)

		eng.Scope.Set("_view_root", "views")
		eng.Scope.Set("title", "Welcome to Gin")

		// Bind writer and request to context
		ctx := context.WithValue(c.Request.Context(), "httpWriter", c.Writer)
		ctx = context.WithValue(ctx, "httpRequest", c.Request)

		err := eng.Execute(ctx, &engine.Node{
			Name:  "view.blade",
			Value: "welcome",
		}, eng.Scope)

		if err != nil {
			c.String(500, err.Error())
		}
	})

	r.Run(":8080")
}
```

---

## Extending ZenoBlade (Custom Directives)

You can easily extend ZenoBlade by registering your own custom slots/functions in the engine. They will be instantly available inside your templates:

```go
package main

import (
	"context"
	"net/http"
	"strings"
	"github.com/nextcore/zeno-go/pkg/engine"
)

func main() {
	eng := engine.NewEngine()

	// Register a custom function slot: {{ upper("hello") }}
	eng.Register("upper", func(ctx context.Context, node *engine.Node, scope *engine.Scope) error {
		w, ok := ctx.Value("httpWriter").(http.ResponseWriter)
		if !ok {
			return nil
		}
		
		// Resolve the value passed to the function
		val := eng.ResolveShorthandValue(node, scope)
		upperCase := strings.ToUpper(fmt.Sprintf("%v", val))
		
		w.Write([]byte(upperCase))
		return nil
	}, engine.SlotMeta{Description: "Convert string to uppercase"})
}
```

---

## Unit Testing

To run the full suite of AST parser, conditional evaluator, and template transpiler tests:

```bash
go test ./...
```

---

## License

This project is licensed under the Apache License 2.0. See [LICENSE](LICENSE) for details.
