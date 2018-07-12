<h1 align="center">Lumen 5.5 Lanjutan - Route dan Manual CORS Middleware</h1>

<p align="center">
    Kulgram Lumen kedua dengan versi 5.5, mengenai penjelasan tentang route, API Versioning, dan membuat CORS Middleware secara manual (tanpa package).
</p>


# Daftar Isi

- [Why Lumen?](#why-lumen)
- [Instalasi](#instalasi)
- [Config dasar](#config-dasar)
- [Composer : Laravel vs Lumen](#composer--laravel-vs-lumen)
- [Penjelasan Route di Lumen](#penjelasan-route-di-lumen)
- [Penjelasan API Versioning di Lumen](#api-versioning-di-lumen)
- [Manual CORS Middleware](#cors-middleware-optional)

<hr>

Assalamu'alaikum warahmatullahi wabarakatuh,

Sebelum memulai kulgram, perkenalkan saya Nusendra Hanggarawan. Sebagai catatan disini saya mau share saja, karena saya juga masih sangat awal dalam bahasa ini. Selain itu, saya tidak pintar dalam pemilihan bahasa, kekayaan bahasa saya dapet nilai C wkwk. Jadi nanti kalau ada salah kata atau kekeliruan, mohon dimaklumi. 😊

Bagi yang udah pinter, tolong saya jangan di bully ya hehehe. Semoga materi yang saya share berguna buat temen-temen semua. Aamiiinn.

Oke, bahasan pertama saya akan bahas sekilas tentang Lumen. Apa sih Lumen? [Lumen](https://lumen.laravel.com) adalah microframework yang khusus menangani API service, sama seperti Slim dan Silex.

Lumen ini juga bisa dikatakan sebagai adik kecil nya [Laravel](https://laravel.com), jadi kalian insya Allah pasti langsung paham dengan struktur dan cara mengimplementasikan Lumen.

### Why Lumen?

Sebelum menentukan framework PHP yang akan kita pakai, kita sebaiknya tentukan dulu teknologi dan sistem seperti apa yang akan kita bangun.

1. Jika web sederhana yang tanpa banyak melibatkan Front-End framework JS misalnya, kita gunakan saja Laravel. Pakai Laravel dengan dipadukan Front-End JS juga bisa, misalnya saja [Vue.js](https://vuejs.org) yang udah include di fresh instalasi nya Laravel.
2. Jika web yang membedakan repository project, misalkan untuk Back-End pakai PHP, sedangkan Front-End pakai full JS maka kita perlu yang namanya API. Bisa juga sih API kita pakai Laravel, tapi sangat tidak disarankan untuk memakai Laravel kalau hanya untuk kebutuhan API saja karena Laravel mempunyai banyak fitur, yang mana nantinya fitur fitur ini tidak terpakai karena kita hanya memanfaatkan API nya saja. So, kita perlu framework yang khusus untuk API (spesialis).

Nah kenapa harus Lumen? Kenapa bukan yang lain misalnya Silex/Slim/Phalcon? Sah sah saja kalau mau pakai selain Lumen, tapi berdasarkan benchmark, Lumen lebih mantap daripada Slim. (Benchmark ya, bukan vote). Ya paling tidak Lumen vs Slim 11-12 lah. Hampir sama. #CMIIW

![](./compare-lumen.jpg)

<sub>https://www.gajotres.net/best-available-php-restful-micro-frameworks/<sub>

Karena disini yang di rekomendasikan adalah Slim, Lumen, Silex, Phalcon.<br>
Silakan baca benchmark nya: https://symfony.fi/entry/symfony-benchmarks-microkernel-silex-lumen-and-slim

![](./compare-lumen2.jpg)

OK, insya Allah kita ga salah pilih kalo pake Lumen. 😊

### Instalasi

Untuk instalasi, gak perlu saya jelaskan panjang lebar. Hampir sama dengan instalasi Laravel/Lumen yang udah di bahas di kulgram sebelumnya.

### Config Dasar

Pada Lumen fresh install, dia bener bener ringan. Tapi jika kita pengen pake kekuatan yang ada di Laravel (seperti eloquent, facades, middleware, dll) untuk dipakai di Lumen, kita harus mengaktifkan nya di file `bootstrap/app.php`, karena secara default fitur fitur tersebut di nonaktifkan.

Nah, caranya kita tinggal uncomment baris kode yang ada di `bootstrap/app.php`

Uncomment
`$app->withFacades();`
<br>Untuk mengaktifkan fitur facade (sangat disarankan).

Uncomment
`$app->withEloquent();`
<br>Untuk mengaktifkan fitur eloquent (Optional).

Uncomment:
```php
$app->routeMiddleware([
    'auth' => App\Http\Middleware\Authenticate::class,
]);
```
<br>Untuk mengaktifkan Middleware di route (untuk Auth).

Uncomment 2 baris ini:
```php
$app->register(App\Providers\AppServiceProvider::class);
$app->register(App\Providers\AuthServiceProvider::class);
```
Config done...

### Composer : Laravel vs Lumen

Ketika temen-temen mengetik `php artisan` di Terminal, pada Laravel akan muncul banyak opsi, sebaliknya di Lumen kalian hanya akan menemukan sedikit. Karena Lumen ini lebih banyak menggunakan teknik manual daripada otomatisasi seperti di Laravel. Jika di Laravel kita bisa bikin model dan controller lewat `artisan`, di Lumen kita ga bisa melakukan hal tersebut. So, di Lumen kita harus create file nya secara manual.

### Penjelasan Route di Lumen

Di Laravel, kita bisa mengetikkan route seperti ini:

```php
Route::get('post','PostController@index');
```

atau dengan resource:

```php
Route::resource('post','PostController');
```

Tapi di Lumen 5.5 berbeda, seperti dibawah ini:

```php
$router->get('post','PostController@index');
```

dan satu lagi, di Lumen kita ga bisa pakai fitur resource.

### API Versioning di Lumen

Saya dulu gak paham dengan API versioning ini, sampe tanya mas Amirul(Kawan Koding) juga masih belum solved. Kebingungan ini merasuk ke dalam jiwa #lol ketika mencari perbedaan antara URI `localhost/api/v1/post` dengan `localhost/post`. Karena keduanya sama aja di response nya, gak ada perbedaan sama sekali. Lantas apa gunanya API version? Akhirnya googling sendiri dan nemu artikel ini: https://dzone.com/articles/rest-api-versioning-is-there-a-right-answer

Jadi ada 2 kondisi :
- Jika API yang dibangun hanya untuk kebutuhan internal, maka kita tidak perlu API versioning.
- Jika API kita merupakan public API yang mana kita gak bisa mengontroll di sisi client, maka kita perlu melakukan API versioning. Jadi misalkan di API v1 kita memberi response dengan struktur json tertentu, kemudian di sisi Back-End kita ada perubahan struktur json, maka kita harus bikin API v2 dan menginformasikan ke client bahwa kita update API ke versi 2. Sehingga client bisa menyesuaikan app nya dengan struktur response json yang baru milik kita.

Sampai sini pengenalan awal nya sudah beres. Insya Allah paham lah ya. Dasar banget ini soalnya. Gak jauh beda sama Laravel. Bedanya kita lebih banyak bermain di manual.

Sebelum menginjak ke point akhir, silakan kalau temen-temen ada yang mau nambahin/bertanya.

<blockquote><b>Lock On Stratos, [31.12.17 19:49]</b><br>
Jadi kalo buat public API v1, v2 termasuknya wajib ya bang?</blockquote>

Iya. Jika kita merasa kedepannya bakal ada perubahan struktur json untuk response kita, maka perlu dibuat API version. Tapi kalau misalnya response kita tidak ingin ada perubahan, dan selama nya tetap seperti itu struktur nya, maka tanpa dibuat versi juga tidak masalah.

Dan jika API kita hanya untuk kebutuhan internal, tanpa versioning API juga ga masalah. Karena di sisi client/Front-End kita sendiri yang pegang. Jika ada perubahan json, langsung kita ubah disisi Front-End nya.

<blockquote><b>Noor Adiana, [31.12.17 19:51]</b><br>
Secara default component Laravel apa aja yang dibawa oleh Lumen?</blockquote>

![](./lumen-vendor.jpg)

<blockquote><b>Lock On Stratos, [31.12.17 19:53]</b><br>
Kalo udah diupdate jadi v2, untuk yang v1 nya tetep bisa di gunakan ya bang?</blockquote>

Ini tergantung. Jika kita pengen mempertahankan v1 ya boleh saja. Tinggal kita atur di route dan controller nya. Jika pengen menutup v1 juga boleh. Jadi nanti client dipaksa untuk pakai v2.

### CORS Middleware (Optional)

Kalau kita pake Front-End Vue.js, kita perlu setting CORS di Lumen agar request dari HTTP client seperti `axios`/`vue-resource`/`superagent` bisa diterima oleh Lumen. Jika kita belum setting CORS di Lumen, nanti ketika ada request dari client (Vue.js) akan muncul error seperti dibawah ini.

![](./cors-error.jpg)

Untuk setting CORS di Middleware ada banyak cara. Bisa pakai package yang bertebaran di GitHub. Tapi saya lebih seneng bikin sendiri.

Pertama kita bikin CORS Middleware nya dulu. Buat `CorsMiddleware.php` di dalam `app/Http/Middleware`

Terus copas ini:

```php
<?php

namespace App\Http\Middleware;

use Closure;

class CorsMiddleware
{
    public function handle($request, Closure $next)
    {
    if($request->isMethod('OPTIONS')) {
      $response = response('', 200);
    } else {
      $response = $next($request);
    }

    $response->header('Access-Control-Allow-Methods', 'HEAD, GET, POST, PUT, PATCH, DELETE');
    $response->header('Access-Control-Allow-Headers', $request->header('Access-Control-Request-Headers'));
    $response->header('Access-Control-Allow-Origin', '*');

    return $response;
  }
}

```
Terus di `bootstrap/app.php` edit settingan/uncomment dan tambahkan Middleware nya jadi begini:

```php
$app->middleware([
   App\Http\Middleware\CorsMiddleware::class
]);
```

![](./cors-sukses.jpg)

Login setelah CORS middleware setelah selesai disetting di Back-End. API Key akan distore ke localstorage dan vuex. Next insya Allah bahas otentikasi ini. 😊

Sekian kulgram pembahasan Lumen ini. Maaf ya, judul nya Lumen Vue, tapi belum ada Vue nya. Ini masih awal pengenalan, insya Allah next pakai Vue di sisi client/Front-End.

Terima kasih mas Ammar Faizi, Slamet Sugandi, Amirul, Noor Adiana, Lock On Stratos, dan Undefined Zen yang udah berpartisipasi di kulgram kali ini. Mohon maaf kalau ada salah kata, karena saya gak pandai menyusun kata-kata.

Wassalamu'alaikum warahmatullahi wabarakatuh.

_**Penulis:** [Nusendra Hanggarawan](https://github.com/nusendra)_
