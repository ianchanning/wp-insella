# WP in-sella

Based on [WordPress Skeleton](https://github.com/markjaquith/WordPress-Skeleton) but replacing Git Submodules with [Composer](https://getcomposer.org/).

In sella is Italian for 'in the saddle' and was initially used for <http://insella.co.uk>.

This tries to make the benefits of WordPress Skeleton available for a wider audience:

1. This readme should get you fully started with WordPress installed and uploads working
1. Composer in my view is easier to understand than learning about Git Submodules
1. Composer should be able to install on hosts that don't have Git
1. I agree with the idea that [Composer is a more appropriate tool to use than Git Submodules](https://roots.io/using-composer-with-wordpress/)
1. The directory structure remains compatible with WordPress Skeleton, so you should be able to mix in the extra WordPress Skeleton goodies when you get to them
1. There's a separate `wp-config-skeleton.php` file so that you don't have to fork this repo which makes it easier to get WordPress updates
1. This is a [Composer Package](https://packagist.org/packages/ianchanning/wp-insella) so instead of cloning you can call `composer create-project ianchanning/wp-insella .` in your httpdocs directory
1. It still encourages local development with the `local-config.php` file and allows all the other great stuff you can do with WordPress-Skeleton

**This setup relies on having SSH access and running the PHP commands to [install composer](https://getcomposer.org/doc/00-intro.md#installation-linux-unix-osx) on your webserver.**

If you don't have Git on your webserver, follow the [No git and no composer setup](#no-git-and-no-composer-setup) section below. There's a [Media Temple installation guide](#media-temple-grid-service-installation) at the bottom as that happens to be what I'm using.

## Quick start
[Skip](#no-git-and-no-composer-setup) this section if you don't have git or composer installed.

```shell
cd your/httpdocs/dir
git clone https://github.com/ianchanning/wp-insella .
composer install
cp wp-config-skeleton.php wp-config.php
```

Create your database and edit `wp-config.php` with your database config and salts.

### Configuration

Then go to [http://www.example.com/wp/wp-admin/](http://www.example.com/wp/wp-admin/) - beware of going directly to [www.example.com](http://www.example.com) as it will try going to [http://www.example.com/wp-admin](http://www.example.com/wp-admin) which misses out the `wp` directory and ends up in an infinite redirect.

Once WordPress is installed, go to *Settings | General* and change the **Site Address (URL)** to [http://www.example.com](http://www.example.com)

![Site Address (URL) example](https://dl.dropboxusercontent.com/u/7765571/github/wp-insella/site_address.png)

### Uploads

WordPress Skeleton suggests that you should only use the 'shared' directory for uploads locally. Given that we're not using capistrano we'll keep the directory structure the same but just use the shared directory in production.

```shell
cd your/httpdocs/dir
mkdir -p shared/content/uploads
```

Make sure uploads directory is writable by your webserver.

### Future updates

1. I'll update, commit and push the `composer.json` file to https://github.com/ianchanning/wp-insella
2. On your site:

```shell
cd your/httpdocs/dir
git pull
composer update
```

## No git and no composer setup

This is assuming that you have the most basic web host that doesn't allow anything except SSH / PHP / MySQL.

### Database

Using your hosting admin:

1. Create the MySQL database
2. Create a database user
3. Give the database read and write access on the database
4. Have the details handy

### Composer

From the main [composer installation guide](https://getcomposer.org/doc/00-intro.md#installation-linux-unix-osx), I'm going to assume that you probably don't have the rights to install it globally so we'll install it locally. N.B. the `-d allow_url_fopen=On` which is to modify one of the php.ini settings which may be turned off by your host (e.g. Media Temple)

Log in via SSH. Install composer in the `httpdocs` **parent** directory and `httpdocs` must be empty.

```shell
cd your/httpdocs/dir
cd ..
php -r "readfile('https://getcomposer.org/installer');" | php -d allow_url_fopen=On
```

### WordPress

```shell
php -d allow_url_fopen=On composer.phar create-project ianchanning/wp-insella [httpdocs]
cp wp-config-skeleton.php wp-config.php
```

This should set up the entire WordPress Skeleton installation and the `wp` directory with all the WordPress files.

Edit `wp-config.php` with your database config and [salts](https://api.wordpress.org/secret-key/1.1/salt/).

Follow the above [WordPress database installation / configuration](#wordpress-database-installation--configuration) and [Create the shared uploads directory](#create-the-shared-uploads-directory) sections above.

### Future updates

1. I'll update, commit and push the `composer.json` file to <https://github.com/ianchanning/wp-insella>
1. Download <https://raw.githubusercontent.com/ianchanning/wp-insella/master/composer.json>
1. FTP it to your httpdocs directory and overwrite the `composer.json`
1. Run `php -d allow_url_fopen=On ../composer.phar update`

## Media Temple (Grid Service) installation

### Database

In [media temple admin](https://ac.mediatemple.net/home.mt) go to:

1. Create a database
2. Create a database user
3. Give the user permission on the database

### Composer

Composer can't be installed globally so it has to be installed locally. Based off [this guide](http://www.neontsunami.com/posts/installing-composer-and-laravel-4-on-media-temple-%28gs%29):

Login via SSH, this assumes that your `html` directory is empty. You will get warnings when you install comopser, but it should still install correctly.

```shell
cd domains/example.com
curl -sS https://getcomposer.org/installer | php -d allow_url_fopen=On
```

### WordPress

```shell
cd html
git clone https://github.com/ianchanning/wp-insella .
php -d allow_url_fopen=On ../composer.phar install
cp wp-config-skeleton.php wp-config.php
```

This should set up the entire WordPress Skeleton installation and the `wp` directory with all the WordPress files.

Edit `wp-config.php` and change the database settings to (**N.B. the `DB_HOST` isn't localhost**):

```php
define( 'DB_NAME', 'dbXXXXXX_[your domain]' );
define( 'DB_USER', 'dbXXXXXX_[your domain]' );
define( 'DB_PASSWORD', 'xkcd.com/936' );
define( 'DB_HOST', $_ENV['DATABASE_SERVER'] );
```

Update the [salts](https://api.wordpress.org/secret-key/1.1/salt/).

Follow the above [WordPress database installation / configuration](#wordpress-database-installation--configuration) and [Create the shared uploads directory](#create-the-shared-uploads-directory) sections above.

### Future updates

1. I'll update, commit and push the `composer.json` file to https://github.com/ianchanning/wp-insella
2. On your site:

```shell
cd domains/example.com/html
git pull
php -d allow_url_fopen=On ../composer.phar update
```
