# LaravelPlanetScale

[![Latest Version on Packagist][ico-version]][link-packagist]
[![Total Downloads][ico-downloads]][link-downloads]

This package adds a `php artisan pscale:migrate` command to your Laravel app which can be used instead of the normal `php artisan migrate` command when using a PlanetScale database.

## Installation

Via Composer

``` bash
composer require x7media/laravel-planetscale
```

## Configuration & Usage

1. Login to your PlanetScale account and get your Service Token and Service Token ID from the organaization settings. Also take a note of your organization name and production branch name as well for the next steps.

2. Add the following database level permissions to your Service Token for your app's database:

	- create_branch - Create a database branch
	- delete_branch - Delete a database branch
	- connect_branch - Connect to, or create passwords and certificates for a database branch
	- create_deploy_request - Create a database deploy request
	- read_deploy_request - Read database deploy requests

3. From the database settings screen on PlanetScale, click the checkmark to enable the "Automatically copy migration data" settings. Select "Laravel" from the migration framework dropdown and it should fill it "migrations" for the migration table name. Then save the database settings. This will allow migration status to be synced across PlanetScale database branches.

4. Setup the following enviroment variables in your app with the appropriate values:

`PLANETSCALE_ORGANIZATION=`

`PLANETSCALE_PRODUCTION_BRANCH=`

`PLANETSCALE_SERVICE_TOKEN_ID=`

`PLANETSCALE_SERVICE_TOKEN=`

Additonally yuou'll need to make sure your database name is set under:

`DB_DATABASE=`

**OR**

Optionally you can publish the config:

``` bash
php artisan vendor:publish --tag=laravel-planetscale-config
```

Then customize the values in the config. **NOTE:** If you take this approach we *STRONGLY RECOMMEND* that you still use enviroment varibles or some other secrets storage at least for your service token and service token ID for security.

5. Replase the `php artisan migrate` command in your deployment script or process with this:

``` bash
php artisan pscale:migrate
```

**NOTE:** The `pscale:migrate` command supports the same options are Laravel's built in migration command, and will pass those options along to it when it gets to that step in the process.

## FAQ's

### Why is this necessary?

PlanetScale has alot of advantages when using it as your application's production database. However it handles your database and schema migrations in a somewhat unusual way.

It uses branches for your database. A branch can be production or development. You'll want to use a production branch for your app in production because that afford you extra features like automatic backups, however you cannot perform schema changes directly against a production branch. Instead you should create a new development branch based on your production branch and perform your schema changes on that than merge that back into your production branch just like you would do with your code in Git.

This package uses [PlanetScale's Public API](https://api-docs.planetscale.com/) to automate the process of creating a new development branch, connecting your app to the development branch, running your Laravel migrations on the development branch, merging that back into your production branch, and deleting the development branch.

### Are there any notable limitations to PlanetScale's branching?

Yes, there is one **BIG** caveat. That is branching and merging is for *schema only*. So you will need to seperate your schema migrations from your data migrations. Use this to run your schema migrations and run your data migrations seperatly against your production branch.

An alternative method is to demote your production branch back to a development branch, then you can mix schema and data migrations. Then when that is finished promote the branch back to a production branch. But that is currently a manaual process. I have however made a request with the PlanetScale team to make a slight change to their API that would allow this demote-promote process to be automated, and if that change is made I will update this package. However I ultimately have no control over if or when that will become possible.

## Change log

Please see the [changelog](changelog.md) for more information on what has changed recently.

## Testing

``` bash
$ composer test
```

## Contributing

Please see the [contributing](contributing.md) guidelines.

## Security

If you discover any security related issues, please email info@x7media.com instead of using the issue tracker.

## Credits

- [Ryan](https://github.com/x7ryan)
- [All Contributors](../../contributors)

## License

MIT. Please see the [license file](license.md) for more information.

[ico-version]: https://img.shields.io/packagist/v/x7media/laravel-planetscale.svg?style=flat-square
[ico-downloads]: https://img.shields.io/packagist/dt/x7media/laravel-planetscale.svg?style=flat-square

[link-packagist]: https://packagist.org/packages/x7media/laravel-planetscale
[link-downloads]: https://packagist.org/packages/x7media/laravel-planetscale
[link-author]: https://github.com/x7media
[link-contributors]: ../../contributors
