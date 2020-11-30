<h1>Projeto Inútil</h1>

Tecnologias Utilizadas:
- Laravel 8
- Laravel UI
- MariaDB


## Sobre o projeto
Esse projeto busca implementar um sistema de controle simples de acesso. Usando o vídeo [Live-coding: Laravel Admin Seed, Tenants CRUD and Email Invitations](https://www.youtube.com/watch?v=rgOlkcTncv8&t=1093s)

#### Criando um projeto no laravel 8
> laravel new project

Rodando o projeto
> php artisan serve

Criando o primeiro model que será responsável pelas regras de acesso.
> php artisan make:model Role -ms

Criamos apenas uma string name.

Inserimos o `name` na variável *$fillable* do Model criado

Vamos criar a seed com as Role permitidas para o sistema - RoleSeeder
```php
public function run()
    {
        Role::create(['name'=>'Administrator']);
        Role::create(['name'=>'Company']);
        Role::create(['name'=>'User']);
    }
```
vamos criar o user: administrator:secret.
Para isso vamos duplicar "RoleSeeder" e feita as devidas adaptações colocamos:

```php
    User::create([
        'name' => 'Administrator',
        'email' => 'admin@admin.com',
        'password' => bcrypt('secret'),
        'role' => 1
    ]);
```
Vamos configurar o "DatabaseSeeder" para executar nossas seeds

```php
    public function run()
    {
        $this->call(RoleSeeder::class);
        $this->call(AdminUserSeeder::class);
    }
```

No entanto, precisamos criar a chave estrangeira entre as duas tabelas.
Precisamos criar um nova migration.
> php artisan make:migration add_role_id_to_users_table

```php
    Schema::table('users', function (Blueprint $table) {
        $table->foreignId('role')->constrained();
    });
```
Adicionamos no `$fillable` "user model" o campo "role".

```php
    protected $fillable = [
        'name',
        'email',
        'password',
        'role'
    ];
```
Vamos testar agora
> php artisan migrate:fresh --seed

 - Lembre-se de configurar o banco de dados... Eu havia esquecido até aqui. 😂️

Tudo ✌ até aqui

## Trabalhando com a parte de Login usando Laravel UI
Vamos instalar a library
> composer require laravel/ui

Acessando a documentação: [Laravel UI - Github](https://github.com/laravel/ui)
No nosso caso vamos trabalhar com o bootstrap, seguindo a documentação vamos usar o comando:
> php artisan ui bootstrap --auth
Em seguida vamos executar o npm
> npm install && npm run dev

Vamos acessar a página inicial do projeto. Na página inicial teremos o duas opções "Login" e "Register".

Vamos desabilitar o registro e mais algumas opções no arquivo de rotas `web.php`. Vamos desabilitar.
```php
Auth::routes([
    'register' => false, // Registration Routes...
    'reset' => false, // Password Reset Routes...
    'verify' => false, // Email Verification Routes...
]);
```
