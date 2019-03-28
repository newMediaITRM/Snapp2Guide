Example MarkDown syntax below

# Versioning

For a model to be versionable it must have the VersionableTrait, there must be a table created to contain the 
versions which has the same structure, without any constraints that would stop duplicate rows, and with the extra
columns provided by the versionable() function in the SnappBlueprint class. The model for the version table must 
implement the VersionTrait and it must implement any relations that should also be versioned as well as listing 
them in an array named $versionRelations. The tests which exist for this will show you exactly how this needs to 
be done in TestModel and TestModelVersion.

## Creating a new version

```php
$model = Model::where('id', $id);
$model->setVersionStatus('draft');
$model->save();
```

## Finding the latest version

```php
$draft = $model->latestDraft();
```

## Finding a specific version

```php
$draft = $model->versionOfDraft(3);
```

## Publishing a version

A user must have the `publish` rights to be able to publish a model, otherwise it is added to a manual jobs 
queue and someone with the rights will have to do it later.

```php
$draft = $model->latestDraft();
$draft->publish();
```

## Pages

Pages are set up to be versionable, and have an API controller for versioning and publishing objects. When 
an attempt is made to publish a page by a user who does not have permission a Job is added to a queue for 
an authorised user to process later. This makes use of the Laravel queuing system but with the worker
being a user manually selecting jobs instead of an automated worker. These are stored in a seperate table
from the automated jobs so they can't accidentally be worked on automatically.