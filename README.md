CakePHP HybridAuth Plugin
=========================

A CakePHP plugin which allows using the [HybridAuth](http://hybridauth.sourceforge.net/)
social sign on library.

Requirements
------------

* CakePHP 3.0+

Installation
------------

Run: `composer require admad/cakephp-hybridauth` or add `admad/cakephp-hybridauth`
to `require` in your application's `composer.json`

Setup
-----

Load the plugin by adding following to your app's boostrap:
`CakePlugin::load('ADmad/HybridAuth', ['bootstrap' => true, 'routes' => true]);`

Configuration
-------------

Make a config file `src/Config/hybridauth.php`
Eg.

	<?php
	use Cake\Core\Configure;

	$config['HybridAuth'] = [
		'providers' => [
			'OpenID' => [
				'enabled' => true
			]
		],
		'debug_mode' => Configure::read('debug'),
		'debug_file' => LOGS . 'hybridauth.log',
	);

For more information about the hybridauth configuration array check
http://hybridauth.sourceforge.net/userguide/Configuration.html

The plugin also expects that your users table used for authentication contains
fields `provider` and `provider_uid`. The fields are configurable through the
`HybridAuthAuthenticate` authenticator.

Usage
-----
Check the CakePHP manual on how to configure and use the `AuthComponent` with
required authenticator. You would have something like this in your `AppController`.

	<?php
	public $components = [
		'Auth' => [
			'authenticate' => [
				'ADmad/HybridAuth.HybridAuth'
			]
		]
	];

Your controller's login action should be similar to this:

	<?php
	public function login() {
		if ($this->request->is('post')) {
			$user = $this->Auth->identify();
			if ($user) {
				$this->Auth->setUser($user);
				return $this->redirect($this->Auth->redirectUrl());
			}
			$this->Flash->error(__('Invalid username or password, try again'));
		}
	}

An eg. element `Template/Elements/login.ctp` showing how to setup the login page
form is provided. Checkout the various
[examples](http://hybridauth.sourceforge.net/userguide/Examples_and_Demos.html)
in hybridauth documentation to see various ways to setup your login page.

Once a user is authenticated through the provider the authenticator gets the user
profile from the identity provider and using that tries to find the corresponding
user record in your app's users table. If no user is found and `registrationCallback`
option is specified the specified method from the `User` model is called. You
can use the callback to save user record to database.

If no callback is specified the profile returned by identity provider itself is
returned by the authenticator.

Copyright
---------

Copyright 2014 ADmad

License
-------

[The MIT License](http://opensource.org/licenses/mit-license.php)
