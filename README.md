# ZenoBlade & ZenoEngine for Go (`zeno-go`)

[![Go Reference](https://pkg.go.dev/badge/github.com/nextcore/zeno-go.svg)](https://pkg.go.dev/github.com/nextcore/zeno-go)
[![Go Version](https://img.shields.io/github/go-mod/go-version/nextcore/zeno-go)](https://github.com/nextcore/zeno-go)
[![License](https://img.shields.io/github/license/nextcore/zeno-go)](https://github.com/nextcore/zeno-go/blob/main/LICENSE)
[![Build Status](https://img.shields.io/github/actions/workflow/status/nextcore/zeno-go/go.yml?branch=main&label=tests&logo=go)](https://github.com/nextcore/zeno-go/actions)

`zeno-go` is a high-performance, lightweight, and native **Laravel Blade template engine** and **ZenoLang script interpreter** written in pure Go. It is designed to bring the highly elegant, developer-friendly templating syntax of Laravel Blade directly into the Go ecosystem.

If you are a Go developer who misses Laravel's layout inheritance, stacks, section-yields, and reusable HTML components, `zeno-go` is the solution you've been looking for.

---

## Why ZenoBlade? (Comparison with Laravel Blade & Go Alternatives)

Go's standard `html/template` is safe but syntax-wise verbose, context-unaware, and lacks layout inheritance or reusable component systems. `zeno-go` solves this.

Here is how `zeno-go` compares to original Laravel Blade (PHP) and why it's a game-changer for Go developers:

### 1. ZenoBlade vs Original Laravel Blade (PHP)

| Feature / Metric | Original Laravel Blade (PHP) | ZenoBlade (Go) | Why it matters |
| :--- | :--- | :--- | :--- |
| **Execution Performance** | Fast (PHP interpreter) | **Blazing Fast (Go Machine Code)** | Direct native execution in Go yields sub-millisecond render times. |
| **Memory Footprint** | Moderate to High (PHP-FPM/Laravel) | **Extremely Low (Go Runtime)** | Perfect for microservices, edge computing, and high-concurrency systems. |
| **Layout Inheritance** | Yes (`@extends`, `@yield`, `@section`) | **Yes** | Write clean, modular web pages with zero boilerplate. |
| **Component Tags** | Yes (`<x-alert>`) | **Yes** | Extract UI elements into clean, isolated component blocks natively. |
| **Stacks System** | Yes (`@push`, `@stack`) | **Yes** | Easily inject assets (JS/CSS) from sub-views into your layout header/footer. |
| **Control Flow & Loops** | Yes (`@if`, `@foreach`, `@forelse`) | **Yes** | Standard Blade conditional and loop structures work out-of-the-box. |
| **Security (XSS)** | Manual/Auto escape via `{{ }}` | **Auto-escaping (HTML Safe)** | All variables inside `{{ }}` are auto-escaped by default to prevent XSS. |
| **Strict Type Validation** | No (Dynamic PHP typing) | **Yes (Strict validation rules)** | Enforce and validate parameter types (`decimal`, `int`, `string`) at template level. |
| **Inline Custom Logic** | Yes (`@php` block evaluates PHP) | **Yes (`@php` / `@zeno` evaluates ZenoLang)** | Run custom code safely inside the template sandbox without crashes. |
| **Deployment** | Multi-file PHP codebase | **Single Self-Contained Binary** | Compiles directly into your Go binary. Zero external runtime dependencies. |

### 2. ZenoBlade vs Other Go Templating Alternatives

| Feature / Metric | `html/template` (StdLib) | `Pongo2` (Django/Jinja) | `Quicktemplate` | **ZenoBlade (`zeno-go`)** |
| :--- | :--- | :--- | :--- | :--- |
| **Syntax Style** | Go-specific `{{ .Data }}` | Django/Twig `{% block %}` | Custom Go-like | **Laravel Blade (`@extends`, `<x-comp>`)** |
| **Layout Inheritance** | Basic (via `{{ template }}`) | Yes (`extends`, `block`) | Yes (via Go interfaces) | **Yes (`@extends`, `@section`, `@yield`)** |
| **Component Tags** | No | No | No | **Yes (HTML-style tags `<x-card>`)** |
| **Asset Stacks** | No | No | No | **Yes (`@push`, `@stack`)** |
| **Hot Reload** | Manual setup required | Yes | No (requires Go compile) | **Yes (Built-in for dev mode)** |
| **Custom Extension** | Via `FuncMap` | Tag/Filter registration | Via Go methods | **Direct slot registration (`engine.Register`)** |
| **Strict Validation** | No | No | Compile-time Go typing | **Yes (Runtime validation)** |

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

## Key Features

- **Laravel Blade Directives (`pkg/blade`)**: Support for layout inheritance (`@extends`, `@yield`, `@section`), partials (`@include`), slots, and reusable component tags (`<x-card>`).
- **Dynamic Logic Interpreter (`pkg/engine`)**: A secure, fast, and thread-safe AST evaluator powered by **ZenoLang** for rendering template expressions.
- **Rich Control Flow (`pkg/slots`)**: Complete set of standard control loops (`@for`, `@foreach`, `@forelse`), conditionals (`@if`, `@unless`, `@switch`), error handling (`@try`/`@catch`), and auth state helpers (`@auth`/`@guest`).
- **Security First**: Native auto-escaping for all template outputs to prevent XSS (Cross-Site Scripting), with support for unescaped output (`{!! $html !!}`) when explicit.
- **Embedded & Pure Go**: Zero CGO dependencies, extremely lightweight, and runs flawlessly inside any standard Go project (Gin, Fiber, Echo, or standard `net/http`).
- **Hot Reload**: Automatically detects and parses template modifications in development mode, while caching compiled templates in production for maximum performance.

---

## Installation

Add `zeno-go` to your Go module:

```bash
go get github.com/nextcore/zeno-go
```

---

## Supported Blade Directives

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

## Quickstart

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

### 2. Layout Inheritance (Filesystem Templates)

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
@endsection

@push('scripts')
    <script src="/static/welcome.js"></script>
@endpush
```

#### Go Code to Render Filesystem Views
```go
package main

import (
	"context"
	"net/http"
	"net/http/httptest"
	"github.com/nextcore/zeno-go/pkg/blade"
	"github.com/nextcore/zeno-go/pkg/engine"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		eng := engine.NewEngine()
		blade.RegisterBladeSlots(eng)

		// Set directory containing the .blade.zl templates
		eng.Scope.Set("_view_root", "views")

		// Pass HTTP Request context (optional, enables CSRF/Cookies features)
		ctx := context.WithValue(r.Context(), "httpRequest", r)
		ctx = context.WithValue(ctx, "httpWriter", w)

		// Render views/welcome.blade.zl
		err := eng.Execute(ctx, &engine.Node{
			Name:  "view.blade",
			Value: "welcome",
		}, eng.Scope)

		if err != nil {
			http.Error(w, err.Error(), http.StatusInternalServerError)
		}
	})

	http.ListenAndServe(":8080", nil)
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
