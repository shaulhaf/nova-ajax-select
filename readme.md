# Nova Ajax Select

[![Latest Version on Github](https://img.shields.io/github/release/dillingham/nova-ajax-select.svg?style=flat-square)](https://packagist.org/packages/dillingham/nova-ajax-select)
[![Total Downloads](https://img.shields.io/packagist/dt/dillingham/nova-ajax-select.svg?style=flat-square)](https://packagist.org/packages/dillingham/nova-ajax-select)

Nova package to fill a select via ajax & based on other field values

### Install
```
composer require dillingham/nova-ajax-select
```

### Usage

Specify a request url & optionally the `parent($attribute)` to watch & trigger the ajax select:

```php
use NovaAjaxSelect\AjaxSelect;
```
```php
BelongsTo::make('Company'),

AjaxSelect::make('User')
    ->get('/api/company/{company}/users')
    ->parent('company'),
```
Add the field for index & detail views display. AjaxSelect is for forms only
```php
BelongsTo::make('User')->exceptOnForms(),
```

### Request Url:

In the above example, we say `company` is the parent.

The `{company}` url parameter will equal the selected `Company` field value.

### Response Format:

The select field expects a "key" & "display". Map your results like so:

```php
Route::get('api/company/{company}/users', function($company_id) {

    $company = \App\Company::findOrFail($company_id);

    return $company->users->map(function($user) {
        return [
            'key' => $user->id,
            'display' => $user->name
        ];
    });
});
```

### Make children depend on other children

`City` makes a request based on `State`, which makes a request based on `Country`:

```php
Select::make('Country')->options([]),
AjaxSelect::make('State')->parent('country'),
AjaxSelect::make('City')->parent('state'),
```
### Supports multiple children per parent

`File` & `Comment` will both make a request based on `Project`

```php
BelongsTo::make('Project'),
AjaxSelect::make('File')->get('/{project}/files')->parent('project'),
AjaxSelect::make('Comment')->get('/{project}/comments')->parent('project'),
```