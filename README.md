# API STARTER PACKAGE


This is a laravel package that helps you start of building a laravel api. It contains useful command generators to generate various files for your api and is completely configurable. The generators include:
* `starter:init`
* `starter:model <name> --archive --migration --table[=TABLE] --schema[=SCHEMA] --relationships[=RELATIONSHIPS]`
* `starter:migration <name> --archive --schema[=SCHEMA] --model[=MODEL]`
* `starter:controller <name> --plain` 
* `starter:repository <name> --model`
* `starter:transformer <name> --fields[=FIELDS] --includes[=INCLUDES]`
 

##Usage

###Step 1: Install the composer packages

Add
```json
{
   "minimum-stability" : "dev",
   "prefer-stable" : true
}
```
to your composer.json file.

Run in terminal
 
    composer require ralphowino/restful-api-helper 1.0.x-dev.

###Step 2: Add the Service Provider

Add the service provider in `config/app.php`:  

    Ralphowino\ApiStarter\ApiStarterServiceProvider::class

###Step 3: Publish the packages configuration files

Publish the package's assets by running 

    php artisan vendor:publish --provider="Ralphowino\ApiStarter\ApiStarterServiceProvider"

###Step 4: Initialize the starter

Initialize the project by running 

    php artisan starter:init

###Step 5: Generate a new jwt token

Generate a new jwt token for the application by running 

    php artisan jwt:generate

###Commands

####1. starter:init
#####Description
This command is used to initialize the package directory structure on the application and setup the API document structures as should 
be. It also sets up authentication for the API straight out of the box.
#####Arguments
*No arguments for this command*
#####Options
*This command has no options for it's execution*

####2. starter:model
#####Description
This command allows you to create a model for the api it will automatically extend the Base Model which is completely
configurable from the starter.php config file and saves the model to the set out path in the starter.php config file.
#####Arguments
* `name` - The name of the model class (*Preferred to be singular*)

#####Options
* `--archive` - This option selects whether the model being created uses soft-delete
* `--migration` - This option generates a migration along with the model being created
* `--schema` - This option sets the fields for the model and if a migration is to be created adds the fields to the migration
* `--table` - This option is used to explicitly set the table name for the model
* `--relationships` - This option is used to set the relationships for the model

#####Example

    php artisan starter:model Task --schema="title:string priority:string:nullable" --archive --relationships="user:belongsTo, goals:hasMany"

The appropriate format for the schema is:

`COLUMN_NAME:COLUMN_VALUE`

..the model created will be:

```php
<?php

namespace App\Data\Models;

use Illuminate\Database\Eloquent\SoftDeletes;


class Task extends BaseModel
{
    use SoftDeletes;
    /**
     * The model's table
     *
     */
    protected $table = 'tasks';

    /**
     * This are the mass assignable fields for the model
     *
     * @var array
     */
    protected $fillable = [];
    
    /**
     * Links to it's user
     *
     * @return \Illuminate\Database\Eloquent\Relations\BelongsTo
     */
     public function user()
     {
        return $this->belongsTo(User::class);
     }

    /**
     * Links to it's goals
     *
     * @return \Illuminate\Database\Eloquent\Relations\HasMany
     */
     public function goals()
     {
        return $this->hasMany(Goal::class);
     }
}
```

####3. starter:migration
This command is used to create a migration 

#####Arguments
* `name` -  The name of the migration

#####Options
* `--schema`  - This option adds the fields for the migration
* `--model`   - This option creates a model along with the migration
* `--archive` - This option adds soft deletes to the migration

#####Example

    php artisan starter:migration create_tasks_table --schema="name:string:unique, slug:string:nullable" --archive

The schema format remains the same as that of the model command

...the file generated with the command:

```php
<?php

use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateTasksTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('tasks', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name')->unique();
            $table->string('slug')->nullable();
            $table->softDeletes();
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
        Schema::drop('tasks');
    }
}
```

####4. starter:controller
This command creates a controller for the application and extends the BaseController automatically and is saved in the specified
folder in the config file although all this is fully configurable in the config file. The controller uses the resourceful trait
straight of the box.

#####Arguments
* `name` - The name of the controller

#####Options
* `--plain` - This option does not include the resourceful trait in the controller

#####Example

    php artisan starter:controller TasksController

...the controller generated by the above command:

```php
<?php

namespace App\Http\Controllers\Api;

use Illuminate\Http\Request;

use App\Http\Requests;
use App\Http\Controllers\Controller;

class TasksController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        //
    }
    
    /**
     * Store a newly created resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    public function store(Request $request)
    {
        //
    }
    
    /**
     * Display the specified resource.
     *
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function show($id)
    {
        //
    }
    
    /**
     * Update the specified resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function update(Request $request, $id)
    {
        //
    }
    
    /**
     * Remove the specified resource from storage.
     *
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function destroy($id)
    {
        //
    }
}
```

####5. starter:repository
This command creates a repository for the application in the configured path in the configuration file.

#####Arguments
* `name` - The name of the model

#####Options
* `--model` - This option creates the model linked to the repository as it creates the repository

#####Example

    php artisan starter:repository Task

...the repository generated is:

```php
<?php

namespace App\Data\Repositories;

use App\Data\Models\Task;
use App\Data\Repositories\Traits\ResourcefulTrait;

class TasksRepository extends BaseRepository
{
    use ResourcefulTrait;

    /**
     * This is the repository's model
     *
     * @var Task
     */
    protected $model;

    /**
     * TasksRepository constructor.
     * @param Task $task
     */
    function __construct(Task $task)
    {
        $this->model = $task;
    }
}
```

####6. starter:transformer
This command creates a transformer for the application, the transformer is saved in the specified path in the configuration file.

#####Arguments
* `name` - The name of the model linked to the transformer

#####Options
* `--fields` - The fields to be included in the transformer
* `--includes` - This are the includes for the transformer

#####Exampl

    php artisan starter:transformer Task --fields="name,slug" --includes="user:item, comments:collection, goal, elements"


For the fields format a comma seperated list is expected

`name,user,time`

For the includes the following format should be used

`INCLUDE_NAME:INCLUDE_TYPE`

if you do not include the `INCLUDE_TYPE` the generator will try to determine if the word is plural or singular and change the type according with that

...transformer generated by the command is:

```php
<?php

namespace App\Http\Transformers;

use App\Data\Models\Task;
use League\Fractal\TransformerAbstract;

class TaskTransformer extends TransformerAbstract
{
    /**
     * List of resources possible to include
     *
     * @var array
     */
    protected $availableIncludes = ['user','comments','goal','elements
'];

    /**
     * Turn this item object into a generic array
     *
     * @param Task $task
     * @return array
     */
    public function transform(Task $task)
    {
        return [
            'id' => $task->id,
            'name' => $task->name,
            'slug' => $task->slug,
            'created_at' => (String)$task->created_at,
            'updated_at' => (String)$task->updated_at
        ];
    }
    
    /**
     * Include User
     *
     * @param Task $task
     * @return \League\Fractal\Resource\item
     */
    public function includeUser(Task $task)
    {
        $user = $task->user;
        return $this->item($user, new UserTransformer());
    }

    /**
     * Include Comments
     *
     * @param Task $task
     * @return \League\Fractal\Resource\collection
     */
    public function includeComments(Task $task)
    {
        $comments = $task->comments;
        return $this->collection($comments, new CommentTransformer());
    }

    /**
     * Include Goal
     *
     * @param Task $task
     * @return \League\Fractal\Resource\item
     */
    public function includeGoal(Task $task)
    {
        $goal = $task->goal;
        return $this->item($goal, new GoalTransformer());
    }

    /**
     * Include Elements

     *
     * @param Task $task
     * @return \League\Fractal\Resource\collection
     */
    public function includeElements(Task $task)
    {
        $elements = $task->elements;
        return $this->collection($elements, new ElementTransformer());
    }
}
```

##Configuration
You can configure the destinations of the files generated by configuring the paths to the various files in the `config/starter.php` configuration file


> Written with [StackEdit](https://stackedit.io/).
