# ZenoEngine & ZenoBlade Core (Go) - `zeno-go`

`zeno-go` adalah pustaka inti (*core library*) bahasa pemrograman dinamis **ZenoLang** dan engine template **ZenoBlade** berbasis Go. Pustaka ini sangat ringan, memiliki dependensi minimal, dan dirancang khusus untuk disematkan (*embedded*) pada proyek aplikasi Go mana saja.

---

## Fitur Utama

* **ZenoLang Core (`pkg/engine`)**: Evaluasi ekspresi dinamis berbasis Abstract Syntax Tree (AST) yang sangat cepat, aman, thread-safe, dan hemat memori.
* **ZenoBlade Template (`pkg/blade`)**: Engine parser template bergaya Laravel Blade dengan dukungan pewarisan layout (`@extends`), blok partial (`@include`), dynamic section (`@section`/`@yield`), push-stack assets (`@push`/`@stack`), hot-reload di mode development, dan XSS auto-escaping otomatis demi keamanan data.
* **Standard Control Slots (`pkg/slots`)**: Menyediakan fungsi logika bawaan standar seperti perulangan (`for`/`foreach`), kondisi (`if`, `switch`, `isset`, `empty`, `unless`), penanganan error (`try`/`catch`), dan pengecekan otentikasi sederhana (`auth`/`guest`).

---

## Cara Instalasi

Pasang pustaka `zeno-go` ke proyek Go Anda melalui Go Modules:

```bash
go get github.com/nextcore/zeno-go
```

---

## Panduan Memulai Cepat (Quickstart)

### 1. Eksekusi Script ZenoLang (Zeno Core)

Evaluasi ekspresi logika atau variabel ZenoLang langsung di Go:

```go
package main

import (
	"fmt"
	"github.com/nextcore/zeno-go/pkg/engine"
)

func main() {
	// 1. Buat instance engine dan scope baru
	eng := engine.NewEngine()

	// 2. Masukkan data ke dalam scope variabel
	eng.Scope.Set("$name", "ZenoEngine")

	// 3. Jalankan script
	scriptCode := `
		var: $greeting { val: "Halo, " + $name + "!" }
		return: $greeting
	`
	result, err := eng.EvaluateString(scriptCode)
	if err != nil {
		panic(err)
	}

	fmt.Println(result) // Output: Halo, ZenoEngine!
}
```

### 2. Rendering Template ZenoBlade

Render string/file template HTML menggunakan ZenoBlade parser:

```go
package main

import (
	"fmt"
	"github.com/nextcore/zeno-go/pkg/blade"
	"github.com/nextcore/zeno-go/pkg/engine"
)

func main() {
	eng := engine.NewEngine()
	
	// Registrasi slot control flow dasar (if, for, dll.) ke engine
	blade.RegisterBladeSlots(eng)

	// Set data yang akan ditampilkan di template
	eng.Scope.Set("$title", "Beranda")
	eng.Scope.Set("$user", "Max")

	templateHTML := `
		<h1>{{ $title }}</h1>
		<p>Selamat datang kembali, {{ $user }}!</p>
	`
	
	output, err := blade.RenderString(eng, templateHTML)
	if err != nil {
		panic(err)
	}

	fmt.Println(output)
	// Output:
	// <h1>Beranda</h1>
	// <p>Selamat datang kembali, Max!</p>
}
```

---

## Pengujian Unit (Unit Testing)

Untuk memastikan seluruh parser AST, transpiler template, dan slots kontrol flow berjalan sempurna di lokal Anda, jalankan:

```bash
go test ./...
```

---

## Lisensi

Didistribusikan di bawah lisensi Apache 2.0. Silahkan baca berkas `LICENSE` untuk detail selengkapnya.
