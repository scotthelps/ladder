<p>
    <a href="https://github.com/eneadm/ladder/actions">
        <img src="https://github.com/eneadm/ladder/workflows/tests/badge.svg" alt="Build Status">
    </a>
    <a href="https://packagist.org/packages/eneadm/ladder">
        <img src="https://img.shields.io/packagist/dt/eneadm/ladder" alt="Total Downloads">
    </a>
    <a href="https://packagist.org/packages/eneadm/ladder">
        <img src="https://img.shields.io/packagist/v/eneadm/ladder" alt="Latest Stable Version">
    </a>
    <a href="https://packagist.org/packages/eneadm/ladder">
        <img src="https://img.shields.io/github/license/eneadm/ladder" alt="License">
    </a>
</p>

# Ladder 🪜
Ladder simplifies role and permission management for your Laravel project by avoiding storing everything in the database. 
Inspired by [Laravel Jetstream](https://jetstream.laravel.com/features/teams.html#roles-permissions), 
it offers a static approach, reducing queries and ensuring immutability for easy modifications.

## Install
> This package requires Laravel 10 and above.
```bash
composer require eneadm/ladder
```

Once Ladder is installed, create a new LadderServiceProvider to manage roles and permissions. 
You can do so effortlessly with this command:

```bash
php artisan ladder:install
```

Lastly, execute the `migration` command to create a single pivot `user_role` table, assigning roles to users.

```bash
php artisan migrate
```

## Use

Before using Ladder add the `HasRoles` trait to your `App\Models\User` model. 
By doing so this trait will provide the necessary methods to manage roles and permissions.

```php
use Ladder\HasRoles;
 
class User extends Authenticatable
{
    use HasRoles;
}
```

### `HasRoles` trait in detail

```php
// Access all of user's roles...
$user->roles : Illuminate\Database\Eloquent\Collection

// Determine if the user has the given role... 
$user->hasRole(string $role) : bool

// Access all permissions for a given role belonging to the user...
$user->rolePermissions(string $role) : ?array

// Determine if the user role has a given permission...
$user->hasRolePermission(string $role, string $permission) : bool

// Determine if the user has a given permission...
$user->hasPermission(string $permission) : bool
```

### Roles & Permissions
Users can receive roles with permissions defined in `App\Providers\LadderServiceProvider` using `Ladder::role` method. This involves specifying a role's slug, name, permissions, and description. For instance, in a blog app, role definitions could be:
```php
Ladder::role('admin', 'Administrator', [
    'create',
    'read',
    'update',
    'delete',
])->description('Administrator users can perform any action.');

Ladder::role('editor', 'Editor', [
    'post:read',
    'post:create',
    'post:update',
])->description('Editor users have the ability to read, create, and update posts.');
```

### Assign Roles
You may assign roles to the user using the `roles` relationship that is provided by the `Ladder\HasRoles` trait:
```php
use App\Models\User;

post:$user = User::find(1);post:post:

$user->roles()->updateOrCreate(['role' => 'admin']);
```

### Authorization
For request authorization, utilize the `Ladder\HasRoles` trait's hasPermission method to check user's role permissions. Generally, verifying granular permissions is more important than roles. Roles group permissions and are mainly for presentation. Use the `hasPermission` method within authorization policies.
```php
/**
 * Determine whether the user can update a post.
 */
public function update(User $user, Post $post): bool
{
    return $user->hasPermission('post:update');
}
```

## License
Ladder is free software distributed under the terms of the [MIT license](https://github.com/eneadm/ladder/blob/main/LICENSE.md).