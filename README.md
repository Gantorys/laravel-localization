# Laravel Localization

Easy i18n localization for Laravel 4, an useful tool to combine with Laravel localization classes.


## Table of Contents

- <a href="#installation">Installation</a>
    - <a href="#composer">Composer</a>
    - <a href="#manually">Manually</a>
    - <a href="#laravel-4">Laravel 4</a>
- <a href="#usage">Usage</a>
    - <a href="#filters">Filters</a>
- <a href="#helpers">Helpers</a>
- <a href="#config">Config</a>
- <a href="#changelog">Changelog</a>
- <a href="#license">License</a>

## Installation

### Composer

Add Laravel Localization to your `composer.json` file.

    "mcamara/laravel-localization": "dev-master"

Run `composer install` to get the latest version of the package.

### Manually

It's recommended that you use Composer, however you can download and install from this repository.

### Laravel 4

Laravel Localization comes with a service provider for Laravel 4. You'll need to add it to your `composer.json` as mentioned in the above steps, then register the service provider with your application.

Open `app/config/app.php` and find the `providers` key. Add `LaravelLocalizationServiceProvider` to the array.

```php
	...
	'Mcamara\LaravelLocalization\LaravelLocalizationServiceProvider'
	...
```

You can also add an alias to the list of class aliases in the same app.php

```php
	...
	'LaravelLocalization'	=> 'Mcamara\LaravelLocalization\Facades\LaravelLocalization'
	...
```

To finish, publish configuration file using the command `php artisan config:publish mcamara/laravel-localization` in your laravel root path. This will create a file in `app/config/packages/mcamara/laravel-localization/config.php`, containing the most common setting options.

## Usage

Laravel Localization uses the URL given for the request. In order to achieve this purpose, a group should be added into the routes.php file. It will filter all pages that must be localized.

```php
	// app/routes.php

	Route::group(array('prefix' => LaravelLocalization::setLanguage()), function()
	{
		/** ADD ALL LOCALIZED ROUTES INSIDE THIS GROUP **/
		Route::get('/', function()
		{
			return View::make('hello');
		});

		Route::get('test',function(){
			return View::make('test');
		});
	});

	/** OTHER PAGES THAT SHOULD NOT BE LOCALIZED **/

```

Once this group is added to the routes file, an user can access to all languages added into the 'languagesAllowed' ('en' and 'es' for default, look at the config section to change that option). For example, an user can now access to two different languages, using the following addresses:

```
	http://url-to-laravel/en
	http://url-to-laravel/es
	http://url-to-laravel
```

If language is not present in the url or it is not defined into the 'languagesAllowed' array, the system will take the application default language (by default) or the user's browser default language (if defined in config file).

Once the language is defined, the language variable will be stored in a session, so it is not necessary to write the /lang/ section in the url after defining it once, using the last known language for the user. If the user accesses to a different language this session value would be changed, translating any other page he visits with the last chosen language.

Templates files and all language files should follow the [Lang class](http://laravel.com/docs/localization).

### Filters

Moreover, this package includes a filter to redirect all "non-languaged" routes to a "languaged" one (thanks to Sangar82). 

So, if a user accesses to http://url-to-laravel/test and the system have this filter actived and 'en' as a current language for this user, it would redirect (301) him automatically to http://url-to-laravel/en/test. This is mainly used to avoid duplicate content and improve SEO performance.


```php
	// app/routes.php

	Route::group(
	array(
		'prefix' => LaravelLocalization::setLanguage(),
		'before' => 'LaravelLocalizationRedirectFilter' // LaravelLocalization filter
	), 
	function()
	{
		/** ADD ALL LOCALIZED ROUTES INSIDE THIS GROUP **/
		Route::get('/', function()
		{
			return View::make('hello');
		});

		Route::get('test',function(){
			return View::make('test');
		});
	});

	/** OTHER PAGES THAT SHOULD NOT BE LOCALIZED **/

```
In order to active it, you just have to attach this filter to the routes you want to be accessible localized.

## Helpers

This package comes with three useful functions:

### Get Language Bar

```php
	/**
		* Returns html with language selector
		* @param  boolean $abbr 	Should languages be abbreviate (2 characters) or full named?
		* @return String 			Returns an html view with a language bar
	*/
	public function getLanguageBar($abbr = false)

	//Should be called in a view like this:
	{{ LaravelLocalization::getLanguageBar(optional boolean $abbr) }}
```

It returns an html string with `<a>` links to the very same page into another allowed language. Having english, catalan and spanish allowed as languages, being in url-to-laravel/test and english as the current language, this function would return...

```html
	<ul class="laravel_language_chooser">
		<li class="active">EN</li>
		<li><a href="http://url-to-laravel/ca/test">CA</a></li>
		<li><a href="http://url-to-laravel/es/test">ES</a></li>
	</ul>
```
### Get Clean routes

```php
	/**
		 * It returns an URL without language (if it has it)
		 * @param  String $route URL to clean, if false, current url would be taken
		 * @return String        Clean URL
	 */
	public function getCleanRoute($route = false)

	//Should be called in a view like this:
	{{ LaravelLocalization::getCleanRoute(optional string $route) }}
```

It returns an string, giving url passed through the function clean of any language section. 

### Get URL for an specific language

```php
	/**
		* Returns an URL adapted to $language language
		* @param  String $language Language to adapt
		* @param  String $route    URL to adapt, if false, current url would be taken
		* @return String           URL translated
	*/
	public function getURLLanguage($language,$route = false)

	//Should be called in a view like this:
	{{ LaravelLocalization::getURLLanguage(string $lang, optional string $route) }}
```

It returns an string, without any trace of language in it.


## Config

For default only english and spanish are allowed but it can be changed using config.php file that exists in `app/config/packages/mcamara/laravel-localization/config.php` . If this file does not exist, use the following artisan command `php artisan config:publish mcamara/laravel-localization`  in order to create it.

This file have some interesting configuration settings (as the allowed languages or browser language detection, among others) feel free to play with it, all variables are self-explained.

## Changelog

### 0.3
- Added 'LaravelLocalizationRedirectFilter' filter
- To Be Done: Tests

### 0.2

- Added `getURLLanguage` method.
- Added `getLanguageBar` method.
- Added `getURLLanguage` method.
- Added config file
- Added `useBrowserLanguage` config value
- Added README

### 0.1

 - Initial release.

## License

Laravel Localization is an open-sourced laravel package licensed under the MIT license