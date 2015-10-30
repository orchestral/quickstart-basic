# Basic Task List

- [Introduction](#introduction)
- [Installation](#installation)
- [Prepping The Database](#prepping-the-database)
	- [Database Migrations](#database-migrations)
	- [Eloquent Models](#eloquent-models)
- [Routing](#routing)
	- [Stubbing The Routes](#stubbing-the-routes)
	- [Displaying A View](#displaying-a-view)
- [Building Layouts & Views](#building-layouts-and-views)
	- [Defining The Layout](#defining-the-layout)
	- [Defining The Child View](#defining-the-child-view)
- [Adding Tasks](#adding-tasks)
	- [Validation](#validation)
	- [Creating The Task](#creating-the-task)
	- [Displaying Existing Tasks](#displaying-existing-tasks)
- [Deleting Tasks](#deleting-tasks)
	- [Adding The Delete Button](#adding-the-delete-button)
	- [Deleting The Task](#deleting-the-task)

<a name="introduction"></a>
## Introduction

This quickstart guide provides a basic introduction to the Orchestra Platform and includes content on database migrations, the Eloquent ORM, routing, validation, views, and Blade templates. This is a great starting point if you are brand new to the Orchestra Platform, Laravel framework or PHP frameworks in general. If you have already used Laravel or other PHP frameworks, you may wish to consult one of our more advanced quickstarts.

To sample a basic selection of Laravel features, we will build a simple task list we can use to track all of the tasks we want to accomplish (the typical "to-do list" example). The complete, finished source code for this project is [available on GitHub](http://github.com/orchestral/quickstart-basic).

<a name="installation"></a>
## Installation

Of course, first you will need a fresh installation of the Orchestra Platform. You may use the [Homestead virtual machine](http://laravel.com/docs/5.1/homestead) or the local PHP environment of your choice to run the framework. Once your local environment is ready, you may install the Laravel framework using Composer:

	composer create-project orchestra/platform quickstart --prefer-dist

You're free to just read along for the remainder of this quickstart; however, if you would like to download the source code for this quickstart and run it on your local machine, you may clone its Git repository and install its dependencies:

	git clone https://github.com/orchestral/quickstart-basic quickstart
	cd quickstart
	composer run-script post-root-package-install
	composer install
	php artisan key:generate

For more complete documentation on building a local Orchestra Platform development environment, check out the full [Homestead](http://laravel.com/docs/5.1/homestead) and [installation](http://orchestraplatform.com/docs/latest/installation) documentation.

<a name="prepping-the-database"></a>
## Prepping The Database

<a name="database-migrations"></a>
### Database Migrations

First, let's use a migration to define a database table to hold all of our tasks. Laravel's database migrations provide an easy way to define your database table structure and modifications using fluent, expressive PHP code. Instead of telling your team members to manually add columns to their local copy of the database, your teammates can simply run the migrations you push into source control.

So, let's build a database table that will hold all of our tasks. The [Artisan CLI](http://laravel.com/docs/5.1/artisan) can be used to generate a variety of classes and will save you a lot of typing as you build your Laravel projects. In this case, let's use the `make:migration` command to generate a new database migration for our `tasks` table:

	php artisan make:migration create_tasks_table --create=tasks

The migration will be placed in the `resources/database/migrations` directory of your project. As you may have noticed, the `make:migration` command already added an auto-incrementing ID and timestamps to the migration file. Let's edit this file and add an additional `string` column for the name of our tasks:

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
	            $table->string('name');
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

To run our migration, we will use the `migrate` Artisan command. If you are using Homestead, you should run this command from within your virtual machine, since your host machine will not have direct access to the database:

	php artisan migrate

This command will create all of our database tables. If you inspect the database tables using the database client of your choice, you should see a new `tasks` table which contains the columns defined in our migration. Next, we're ready to define an Eloquent ORM model for our tasks!
