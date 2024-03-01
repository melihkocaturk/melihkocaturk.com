---
layout: post
title: Eager Loading ile Laravel Eloquent Sorgularını Optimize Edin
---

Object Relational Mapping (ORM), veritabanlarıyla çalışmayı şaşırtıcı derecede basit hale getirir. Veritabanı ilişkilerini nesne yönelimli bir şekilde tanımlamak, ilgili model verilerini sorgulamayı kolaylaştırırken, geliştiriciler temeldeki veritabanı çağrılarına dikkat etmeyebilir.

## Eager Loading Nedir?

Özünde eager loading, Eloquent'e, ihtiyacınız olan verileri alırken daha performanslı bir sorgu üretmesi için belirli ilişkilere sahip bir modeli yakalamak istediğinizi söylemektir. Eager loading ile birçok sorguyu yalnızca bir veya ikiye indirebilirsiniz.

Bu yazıda, bazı örnek ilişkiler kuracağız ve ardından eager loading ile ve eager loading olmadan sorguların nasıl değiştiğini inceleyeceğiz. Doğrudan koda el atmayı ve bir şeyler denemeyi seviyorum, sorgularınızı nasıl optimize edeceğinizi anlamanıza yardımcı olacak bazı örneklerle eager loading’in nasıl çalıştığını göstermeyi umuyorum.

## Giriş

Temel düzeyde, ORM'ler model verilerini “lazy” yükler. Ne de olsa, ORM niyetinizi nasıl bilecek? Belki de modeli sorguladıktan sonra ilgili modelin verilerini aslında hiçbir zaman kullanmayacaksınız. Sorgunun optimize edilmemesi "N+1" sorunu olarak bilinir. Sorguları temsil etmek için nesneleri kullandığınızda, farkında bile olmadan sorgular yapıyor olabilirsiniz.

```
$posts = Post::published()->get(); // one query
 
$authors = array_map(function($post) {
    // Produces a query on the author model
    return $post->author->name;
}, $posts);
```

Modele tüm yazarlara ihtiyacımız olduğunu söylemiyoruz, bu nedenle yazarın adını Post model örneklerinden her aldığımızda ayrı bir sorgu gerçekleşir.

## Eager Loading

Bahsettiğim gibi, ORM'ler tembeldir. İlişkili model verilerini kullanmayı düşünüyorsanız, eager loading kullanarak toplam 101 sorguyu 2 sorguya indirebilirsiniz. Modele, yüklemek için neye ihtiyacınız olduğunu söylemeniz yeterlidir.

İşte eager loading kullanımı hakkında Rails Active Record kılavuzundan bir örnek. Gördüğünüz gibi konsept, Laravel'in eager loading konseptine oldukça benziyor.

```
# Rails
posts = Post.includes(:author).limit(100)
 
# Laravel
$posts = Post::with('author')->limit(100)->get();
```

Fikirleri daha geniş bir perspektiften keşfederek daha iyi anladığımı görüyorum. Active Record dökümantasyonu, fikrin yankı bulmasına yardımcı olabilecek bazı örnekleri içerir.

## Laravel Eloquent ORM

Laravel'in Eloquent adlı ORM'i, eager loading modellerini ve hatta iç içe geçmiş ilişkileri yüklemeyi önemsiz hale getirir. Post model örneğini geliştirelim ve bir Laravel projesinde eager loading ile nasıl çalışılacağını öğrenelim.

Proje kurulumu üzerinde çalışacağız ve ardından toparlamak için bazı eager loading örneklerini daha derinlemesine inceleyeceğiz.

### Kurulum

Eager loading’i denemek için bazı migration, model ve seeder’lar ayarlayalım. Devam etmek istiyorsanız, bir veritabanına erişiminiz olduğunu ve temel Laravel kurulumundan geçebileceğinizi varsayıyorum.

Laravel installer kullanarak projeyi oluşturalım:

```
laravel new blog-example
```

.env değerlerinizi veritabanınıza veya seçiminize uyacak şekilde düzenleyin.

Daha sonra, eager loading iç içe ilişkileri deneyebilmek için üç model oluşturacağız. Örnek basit, böylece eager loading’e odaklanabiliriz. Index ve foreign key kısıtlamaları gibi kullanabileceğiniz şeyleri atladım.

```
php artisan make:model -m Post
php artisan make:model -m Author
php artisan make:model -m Profile
```

-m flag’i, tablo şemasını oluşturmak için kullanacağınız modele uygun bir migration oluşturur.

Veri modelleri aşağıdaki ilişkilelere sahip olacaktır:

```
Post -> belongsTo -> Author
Author -> hasMany -> Post
Author -> hasOne -> Profile
```

### Migration

Her tablo için basit bir şema oluşturalım; Yalnızca up() metodunu oluşturdum çünkü Laravel, yeni tablolar için down() metodunu otomatik olarak oluşturacaktır. Migration dosyaları database/migrations/ klasöründedir:

```
<?php
 
use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;
 
class CreatePostsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->increments('id');
            $table->unsignedInteger('author_id');
            $table->string('title');
            $table->text('body');
            $table->timestamps();
        });
    }
 
    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('posts');
    }
}
```

```
<?php
 
use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;
 
class CreateAuthorsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('authors', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->text('bio');
            $table->timestamps();
        });
    }
 
    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('authors');
    }
}
```

```
<?php
 
use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;
 
class CreateProfilesTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('profiles', function (Blueprint $table) {
            $table->increments('id');
            $table->unsignedInteger('author_id');
            $table->date('birthday');
            $table->string('city');
            $table->string('state');
            $table->string('website');
            $table->timestamps();
        });
    }
 
    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('profiles');
    }
}
```

### Model

Eager loading ile daha fazla deneme yapmak için model ilişkilendirmeleri tanımlamanız gerekir. php artisan make:model komutunu çalıştırdığınızda, sizin için model dosyalarını oluşturmuştur.

İlk model app/Post.php'dir:

```
<?php
 
namespace App;
 
use Illuminate\Database\Eloquent\Model;
 
class Post extends Model
{
    public function author()
    {
        return $this->belongsTo(Author::class);
    }
}
```

Ardından, app\Author.php iki ilişkilendirmeye sahiptir:

```
<?php
 
namespace App;
 
use Illuminate\Database\Eloquent\Model;
 
class Author extends Model
{
    public function profile()
    {
        return $this->hasOne(Profile::class);
    }
 
    public function posts()
    {
        return $this->hasMany(Post::class);
    }
}
```

Model ve migration’lar hazır olduğunda, migration’ları çalıştırabilir ve ardından seed (tohum) verileriyle eager loading denemelerine geçebilirsiniz.

```
php artisan migrate
Migration table created successfully.
Migrating: 2014_10_12_000000_create_users_table
Migrated:  2014_10_12_000000_create_users_table
Migrating: 2014_10_12_100000_create_password_resets_table
Migrated:  2014_10_12_100000_create_password_resets_table
Migrating: 2017_08_04_042509_create_posts_table
Migrated:  2017_08_04_042509_create_posts_table
Migrating: 2017_08_04_042516_create_authors_table
Migrated:  2017_08_04_042516_create_authors_table
Migrating: 2017_08_04_044554_create_profiles_table
Migrated:  2017_08_04_044554_create_profiles_table
```

Veritabanını kontrol ettiğinizde, oluşturulan tüm tabloları görmelisiniz!

### Model Factory

Sorgu çalıştırabileceğimiz fake veriler oluşturmak için, veritabanını test verileriyle tohumlayacak birkaç model factory ekleyelim.

database/factories/ModelFactory.php dosyasını açın ve aşağıdaki üç factory’i mevcut User factory’nin altına ekleyin:

```
/** @var \Illuminate\Database\Eloquent\Factory $factory */
$factory->define(App\Post::class, function (Faker\Generator $faker) {
    return [
        'title' => $faker->sentence,
        'author_id' => function () {
            return factory(App\Author::class)->create()->id;
        },
        'body' => $faker->paragraphs(rand(3,10), true),
    ];
});
 
/** @var \Illuminate\Database\Eloquent\Factory $factory */
$factory->define(App\Author::class, function (Faker\Generator $faker) {
    return [
        'name' => $faker->name,
        'bio' => $faker->paragraph,
    ];
});
 
$factory->define(App\Profile::class, function (Faker\Generator $faker) {
    return [
        'birthday' => $faker->dateTimeBetween('-100 years', '-18 years'),
        'author_id' => function () {
            return factory(App\Author::class)->create()->id;
        },
        'city' => $faker->city,
        'state' => $faker->state,
        'website' => $faker->domainName,
    ];
});
```

Factory’ler, sorgulayabileceğimiz bir dizi gönderiyi oluşturmayı kolaylaştıracak; bunları veritabanı tohumlaması ile ilişkili model verileri oluşturmak için kullanabiliriz.

database/seeds/DatabaseSeeder.php dosyasını açın ve DatabaseSeeder::run() metoduna şunu ekleyin:

```
public function run()
{
    $authors = factory(App\Author::class, 5)->create();
    $authors->each(function ($author) {
        $author
            ->profile()
            ->save(factory(App\Profile::class)->make());
        $author
            ->posts()
            ->saveMany(
                factory(App\Post::class, rand(20,30))->make()
            );
    });
}
```

Beş yazar oluşturdunuz ve ardından yazarlar arasında dolaşıp ilişkili bir profili ve birçok gönderiyi (yazar başına 20 ila 30 gönderi) kaydettiniz.

Migration, model, model factory ve seeder oluşturmayı bitirdik. Hepsini birleştirebilir ve tekrarlanabilir bir şekilde yeniden çalıştırabiliriz:

```
php artisan migrate:refresh
php artisan db:seed
```

Artık bir sonraki bölümde oynayabileceğiniz verilere sahip olmalısınız. Laravel 5.5'in, migration’ları geri alıp yeniden uygulamak yerine tabloları silen migration:fresh komutu içerdiğini unutmayın.

## Eager Loading’i Denemek

Sonunda eager loading’i çalışırken görmeye hazırız. Bence, eager loading’i görselleştirmenin en iyi yolu, storage/logs/laravel.log dosyasına sorguları kaydetmektir.

Veritabanı sorgularını log’a kaydetmek için MySQL log’unu etkinleştirebilir veya Eloquent'ten gelen veritabanı çağrılarını dinleyebilirsiniz. Eloquent aracılığıyla sorguları log’a kaydetmek için, app/Providers/AppServiceProvider.php boot() metoduna aşağıdaki kodu ekleyin:

```
namespace App\Providers;
 
use DB;
use Log;
use Illuminate\Support\ServiceProvider;
 
class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        DB::listen(function($query) {
            Log::info(
                $query->sql,
                $query->bindings,
                $query->time
            );
        });
    }
 
    // ...
}
```

Bu bilgiyi Laravel Debugbar'dan da alabilirsiniz.

Bakalım model ilişkilerinde eager loading kullanmadığımızda ne olacak. storage/log/laravel.log dosyanızı temizleyin ve “tinker” komutunu çalıştırın.

```
php artisan tinker
 
>>> $posts = App\Post::all();
>>> $posts->map(function ($post) {
...     return $post->author;
... });
>>> ...
```

laravel.log dosyanızı kontrol ederseniz, ilişkili yazarı almak için bir dizi sorgu görmelisiniz:

```
[2017-08-04 06:21:58] local.INFO: select * from `posts`
[2017-08-04 06:22:06] local.INFO: select * from `authors` where `authors`.`id` = ? limit 1 [1]
[2017-08-04 06:22:06] local.INFO: select * from `authors` where `authors`.`id` = ? limit 1 [1]
[2017-08-04 06:22:06] local.INFO: select * from `authors` where `authors`.`id` = ? limit 1 [1]
....
```

laravel.log dosyanızı tekrar boşaltın ve bu sefer yazar kayıtlarını eager loading ile yüklemek için with() kullanın:

```
php artisan tinker
 
>>> $posts = App\Post::with('author')->get();
>>> $posts->map(function ($post) {
...     return $post->author;
... });
...
```

Bu sefer log dosyasında yalnızca iki sorgu görmelisiniz. Tüm gönderiler için ilk sorgu ve ilişkili tüm yazarlar için ikinci sorgu.

```
[2017-08-04 07:18:02] local.INFO: select * from `posts`
[2017-08-04 07:18:02] local.INFO: select * from `authors` where `authors`.`id` in (?, ?, ?, ?, ?) [1,2,3,4,5]
```

Birden fazla ilgili ilişkilendirmeniz varsa, bunları bir dizi ile isteyerek yükleyebilirsiniz:

```
$posts = App\Post::with(['author', 'comments'])->get();
```

## Eloquent'te İç İçe Eager Loading

İç içe eager loading aynı şekilde çalışır. Örneğimizde Author modelinin bir profili vardır. Böylece her profil için bir sorgu yürütülecektir.

laravel.log dosyasını boşaltın ve deneyin:

```
php artisan tinker
 
>>> $posts = App\Post::with('author')->get();
>>> $posts->map(function ($post) {
...     return $post->author->profile;
... });
...
```

Şimdi yedi sorgunuz olacak. İlk ikisi eager yüklenir ve ardından her yeni yazar profili için bir sorgu gerekir.

Eager loading ile iç içe ilişkilerdeki fazladan sorgulardan kaçınabiliriz. laravel.log dosyanızı son bir kez temizleyin ve aşağıdakileri çalıştırın:

```
>>> $posts = App\Post::with('author.profile')->get();
>>> $posts->map(function ($post) {
...     return $post->author->profile;
... });
```

Şimdi, toplamda yalnızca 3 sorgunuz olmalıdır:

```
[2017-08-04 07:27:27] local.INFO: select * from `posts`
[2017-08-04 07:27:27] local.INFO: select * from `authors` where `authors`.`id` in (?, ?, ?, ?, ?) [1,2,3,4,5]
[2017-08-04 07:27:27] local.INFO: select * from `profiles` where `profiles`.`author_id` in (?, ?, ?, ?, ?) [1,2,3,4,5]
```

## Lazy Eager Loading

İlişkili modelleri yalnızca bir koşula dayalı olarak toplamanız gerekebilir. Bu durumda, ilgili veriler için tembelce ek sorgular başlatabilirsiniz:

```
php artisan tinker
 
>>> $posts = App\Post::all();
...
>>> $posts->load('author.profile');
>>> $posts->first()->author->profile;
...
```

Toplamda üç sorgu görmelisiniz, ancak yalnızca $posts->load() çağrılırsa.

## Sonuç

Umarız eager loading hakkında biraz daha fazla şey öğrenmiş ve daha derin bir düzeyde nasıl çalıştığını anlamışsınızdır. Eager loading belgeleri kapsamlıdır ve ek olarak uygulamalı alıştırmanın, sorgularınızı optimize etme konusunda kendinize daha fazla güvenmenize yardımcı olacağını umuyorum.

**Kaynak:** https://laravel-news.com/eloquent-eager-loading
