# ZenoEngine (Go Implementation) - `zeno-go`

`zeno-go` adalah implementasi bahasa pemrograman dinamis **ZenoLang** dan sistem templating **ZenoBlade** berbasis Go. Engine ini dirancang khusus untuk performa tinggi, konsumsi memori rendah, serta kemudahan integrasi ke dalam aplikasi web Go modern.

---

## Fitur Utama

* **ZenoLang Core Execution**: Evaluasi ekspresi berbasis Abstract Syntax Tree (AST) yang sangat cepat, aman, dan modular.
* **ZenoBlade Template Engine**: Engine template berbasis Blade dengan dukungan pewarisan layout (`@extends`), blok partial (`@include`), dynamic section (`@section`/`@yield`), push-stack assets (`@push`/`@stack`), dan proteksi XSS otomatis.
* **Unified Database Layer**: Akses data terstandarisasi untuk MariaDB/MySQL, PostgreSQL, Microsoft SQL Server, dan SQLite.
* **Native Storage Abstraction**: Manajemen file terabstraksi mendukung penyimpanan lokal (filesystem) dan AWS S3-compatible cloud storage (MinIO, Cloudflare R2, DigitalOcean Spaces, dll.) dengan fallback lokal otomatis.
* **SMTP Mail Engine**: Pengiriman email bawaan dengan mode Mock otomatis untuk mempermudah lingkungan pengembangan (development).
* **Automatic OpenAPI Docs**: Registrasi routing API yang langsung menerjemahkan endpoint menjadi skema OpenAPI 3.0 dan menyajikan visualisasi Swagger UI secara instan.
* **CLI Runtime Tooling**: Program CLI `zeno` yang siap pakai untuk eksekusi skrip (`run`), pengujian (`test`), validasi sintaks (`check`), migrasi database (`migrate`), dan generator kunci keamanan (`key:generate`).

---

## Cara Instalasi

Pasang pustaka `zeno-go` ke proyek Go Anda melalui Go Modules:

```bash
go get github.com/nextcore/zeno-go
```

---

## Panduan Memulai Cepat (Quickstart)

### 1. Inisialisasi Engine & Eksekusi ZenoLang

Berikut contoh dasar mengevaluasi skrip ZenoLang dari Go:

```go
package main

import (
	"fmt"
	"github.com/nextcore/zeno-go/pkg/engine"
)

func main() {
	// 1. Buat instance engine baru
	eng := engine.NewEngine()

	// 2. Tentukan variabel di dalam global scope
	eng.Scope.Set("$name", "ZenoEngine")

	// 3. Evaluasi ekspresi logika/script
	code := `
		var: $greeting { val: "Halo, " + $name + "!" }
		return: $greeting
	`
	result, err := eng.EvaluateString(code)
	if err != nil {
		panic(err)
	}

	fmt.Println(result) // Output: Halo, ZenoEngine!
}
```

### 2. Rendering Template ZenoBlade

Gunakan `pkg/blade` untuk merender template Blade HTML secara interaktif:

```go
package main

import (
	"fmt"
	"github.com/nextcore/zeno-go/pkg/blade"
	"github.com/nextcore/zeno-go/pkg/engine"
)

func main() {
	eng := engine.NewEngine()
	
	// Registrasi slot logic standar
	blade.RegisterBladeSlots(eng)

	// Inisialisasi template context
	eng.Scope.Set("$title", "Beranda")
	eng.Scope.Set("$user", "Max")

	templateCode := `
		<h1>{{ $title }}</h1>
		<p>Selamat datang kembali, {{ $user }}!</p>
	`
	
	output, err := blade.RenderString(eng, templateCode)
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

## Menggunakan CLI Runtime `zeno`

Kompilasi CLI tool `zeno` langsung dari repositori Anda untuk keperluan pengujian dan otomasi server:

```bash
# Kompilasi CLI
go build -o zeno cmd/zeno/zeno.go

# Mengecek sintaks berkas ZenoLang
./zeno check src/main.zl

# Menjalankan script secara langsung
./zeno run src/main.zl

# Menjalankan pengujian (unit testing)
./zeno test
```

---

## Pengujian Unit (Unit Testing)

Jalankan perintah pengujian Go untuk memastikan semua subsystem (compiler, parser, slots) lulus uji:

```bash
go test ./...
```

---

## Lisensi

Didistribusikan di bawah lisensi MIT. Silakan baca berkas `LICENSE` untuk detail selengkapnya.
