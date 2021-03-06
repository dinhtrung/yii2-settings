Yii2 Settings
=============
Yii2 Database settings

Installation
------------

The preferred way to install this extension is through [composer](http://getcomposer.org/download/).

Either run

```
php composer.phar require --prefer-dist pheme/yii2-settings "*"
```

or add

```
"pheme/yii2-settings": "*"
```

to the require section of your `composer.json` file.

Subsequently, run

```php
./yii migrate/up --migrationPath=@vendor/pheme/yii2-settings/migrations
```

in order to create the settings table in your database.


Usage
-----

There are 2 parts to this extension. A module and a component.
The module provides a simple GUI to edit your settings.
The component provides a way to retrieve and save settings programmatically.

Add this to your main configuration's modules array

```php
	'modules' => [
        'settings' => [
            'class' => 'pheme\settings\Module',
            'sourceLanguage' => 'en'
        ],
        ...
	],
```

Add this to your main configuration's components array

```php
	'components' => [
		'settings' => [
        	'class' => 'pheme\settings\components\Settings'
        ],
        ...
	]
```

Typical component usage

```php

$settings = Yii::$app->settings;

$value = $settings->get('section.key');

$value = $settings->get('key', 'section');

$settings->set('section.key', 'value');

$settings->set('section.key', 'value', null, 'string');

$settings->set('key', 'value', 'section', 'integer');

// Automatically called on set();
$settings->clearCache();

```

SettingsAction
-----

To develope custom settings form, we can use `SettingsAction`.

1. Create a `yii\base\Model` with normal validation rules.
2. Create an associated view with `ActiveForm` contain all the settings we need.
3. Add `pheme\settings\SettingsAction` to the controller.

The settings will be stored in section taken from form name, with key is the field name.

__Model__:

```
class Site extends Model {
	public $siteName, $siteDescription;
	function rules(){
		return [
				[['siteName', 'siteDescription'], 'string'],
		];
	}
}
```
__Views__:
```
<?php $form = ActiveForm::begin(['id' => 'site-settings-form']); ?>
<?= $form->field($model, 'siteName') ?>
<?= $form->field($model, 'siteDescription') ?>
```
__Controller__:
```
function actions(){
   return [
   		....
				'site-settings' => [
        				'class' => 'pheme\settings\SettingsAction',
        				'modelClass' => 'app\models\Site',
        				//'scenario' => 'site',	// Change if you want to re-use the model for multiple setting form.
        				'viewName' => 'site-settings'	// The form we need to render
        		],
        ....
    ];
}
```

