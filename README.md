# Loopback PostgreSQL Prototype

[![Build Status](https://travis-ci.org/danielmihai/LoopbackPrototype.svg?branch=master)](https://travis-ci.org/danielmihai/LoopbackPrototype)

The project is generated by [LoopBack](http://loopback.io).

## LOOPBACK-POSTGRESQL Useful Tips

Loopback models' database schema is not created in the datasource by default.
In order to persist the models into the database, you have to create a boot script that loads all the models using your datasource (PostgreSQL, in this case), check if they're the same with the model definitions on the server (_common/models/*_ and `app.datasources.PostgreSQL`) with `app.datasources.PostgreSQL.isActual` and use `autoupdate` (or `automigrate`, which drops the model table and recreates it, instead of updating the structure). In this project this operation is handled by the _01-create-dbschema.js_ script.

After creating the tables, you have to create your roles, users and user-role mappings, relations and first data (like the default admin users). The script for creating default user data is handled by the _02-create-users.js_ boot script.

`OBS:` I noticed that, although the [1] Loopback Boot Script Order Documentation says that the boot script load order can be specified (forced) by adding an order number to the beginning of the file name, running the application on _Node v6.9.1_ and _Ubuntu 16.10_, does not load and execute the scripts in that order. I had to start the application twice to create the db schema and then the users. I ran the application with `DEBUG=loopback:boot,datasource:* npm start` to see the boot script load order. In case this does not show the desired output, you could run the app with `DEBUG=loopback:boot:* npm start` or `DEBUG=loopback:boot:* node .`.
Applying order numbers to all scripts seems to solve the problem on local machine. I used to have numbers only on _create-dbschema.js_ and _create-users.js_; _authentication.js_, _root.js_ and _debug.js_ did not (considered these can be loaded at the end.)

## POSTGRESQL Useful Commands

To access PGSQL Prompt without switching accounts:

	sudo -u postgres psql

To exit PGSQL Prompt session:

	postgres=# \q

To Create a new Role:

	sudo -u postgres createuser --interactive

To Create a new Database:

	sudo -u postgres createdb loopback

To Create a new Table:

	CREATE TABLE [table-name] {
		col_name1 col_type (field-length) col_constraints,
		col_name2 col_type (field-length),
		col_name3 col_type (field-length)
	}

Example:

	CREATE TABLE user {
		id serial PRIMARY KEY,
		email varchar (50) NOT NULL,
		password varchar (50) NOT NULL,
		created date
	}

[1] https://docs.strongloop.com/display/public/LB/Defining+boot+scripts#Definingbootscripts-Bootscriptloadingorder
