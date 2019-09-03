# **Instructions:**

This part will be just a simple support guide to register everything that was done to create this app. (Bootcamp 8 GoStack students)

## **Let's start!**

Since i'm using <a href="https://voidlinux.org/">Void Linux</a> (:green_heart:) as my main OS, some commands may not work in your machine, but you will be able to make this program in all other linux, mac and windows too.

First you need to make you _"master"_ folder, mine is named `desafio02` and inside of it you will make an separed folder for the app, `mkdir meetapp`. We will use this in the future.

Then `yarn init -y` to create a `package.json`. The `package.json` file is core to the NodeJS ecosystem and is a basic part of understanding and working with Node, he is used as a register to a manifest about applications, modules, packages and more.

Now open your terminal and install `Express`:

`$ yarn add express`

You will get `node_modules` folder and `yarn.lock`, remember to make a `.gitignore` with `node_modules/` inside it if you want to send for you repository.

Now make a `src` folder. This will be the main folder that will have all the code of the application. Next create three files inside the src folder.

`app.js` - Express Server. Application estructure with middlewares and routes.

`routes.js` - All our routes stay in here.

`server.js` - This file import `app.js` and start the server with the port of your choice, in this case `3333`.

Now you gotta install sucrase and nodemon in dev mode:

`$ yarn add sucrase nodemon -D`

You will be able to change

```js
const express = require("express");
-
module.exports = new App().server;
```

for

```js
import express from "express";
-
export default new App().server;
```

After your changes, you can now use nodemoon with `yarn sucrase-node src/server.js` or you can configure a script to automate the process. So inside the file `package.json` you will create a script:

```js
"scripts": {
    "dev": "nodemon src/server.js"
  },
```

And then run in the terminal the command `yarn dev` and, you will got a error! Why? Because you gotta run with `sucrase`, thats why. So now create a file called `nodemon.json`. With that you will be able to run with `sucrase-node`.

### **Docker Time!**

After you <a href="https://hub.docker.com/_/postgres">install and configure the docker image postgresql</a>, its time to create our database, open your terminal:

`$ docker run --name database -e POSTGRES_PASSWORD=docker -p 5432:5432 -d postgres`

- `docker run`, run the command. ~~lol~~
- `--name database`, name our DB as database.
- `-e POSTGRES_PASSWORD=docker`, our password: docker.
- `-p 5432:5432`, the ports, 0.0.0.0:5432->5432/tcp.
- `-d postgres`, download our DB image, postgresql.

To see if our DB is working, just use `docker ps` and you will get how many database is on, etc.

To see how many ports are open on your pc:

`$ ss -nltu`

To kill the port:

`$ fuser -k 5432/tcp`

Its possible to manage your DB with docker cli but its better to use a GUI in this case since we will be working with tables n stuff. Thats when `Postbird` enter here. If you having trouble to install it I recommend you to use:

```
git clone https://github.com/Paxa/postbird
cd postbird
yarn
yarn rebuild_ext
yarn start
```

If you shutdown your machine, to restart your DB simple use the command `docker restart CONTAINER_ID`.
You can use `docker logs database` if you want to see more details.

### **ESLint + Prettier + EditorConfig**

Install in dev mode, this will format and make you write a more clean code.

`$ yarn add prettier eslint-config-prettier eslint-plugin-prettier eslint -D`

Now its time to configure eslint: `yarn eslint --init`. Since eslint use `npm` to install `eslint-config-airbnb-base@latest`, remove the `package-lock.json` and use simple `yarn` in the folder.

You will need to install `ESlint` and `EditorConfig` inside your VSCode too as a extension. Then you will need configure `.eslintrc.js` and to create a `.prettierrc`.

If you want to fix all your codes at once you can use `yarn eslint --fix src --ext .js`, this will fix all your files javascript in your src folder.

EditorConfig helps maintain consistent coding styles for multiple developers working (VSCode, Atom, SublimeText, etc).

### **Sequelize & Migration**

**Sequelize**: Inside the `src` folder:

- make the folder `config` with a `database.js` file (credentials to access database).

- now make a `database` folder with `migrations` folder inside of database. This folder will have everything relate to database/connections.

- now make `app` folder, this folder will have the logic of network, code, everything related to the app itself, like `controllers` and `models` folder to make sequelize start the work.

Then install Sequelize:

`$ yarn add sequelize` and `$ yard add sequelize-cli -D`.

The `sequelize-cli` will help us to execute/create migrations, models, etc.

Then inside `desafio02` folder, create a file named `.sequelizerc`. This is a config file that will export the directions/folders to the DB (database, migrations, models, etc).

Inside the `config/database.js` file, you will create the migration. This file will be used by the app and sequelize-cli.

You will need `pg` and `pg-hstore` to use the `dialect: 'postgres'`<a href="https://sequelize.org/master/manual/dialects.html#postgresql">\*</a>,so simple install `yarn add pg pg-hstore`.

Remember that if you already have a postgres image in port:5432, simple create another table in Postbird, and change the `config/database.js` to the name of the table, ex: `database: 'meetapp',`.

**Migration**: After creating the credentials in our DB, now its time to config/create our migration to create the `User Table`. With sequelize-cli we can create our users table:

`$ yarn sequelize migration:create --name=create-users`

After setting up the file, you can test your migration with:

`$ yarn sequelize db:migrate`

Then inside of Postbird you will see `SequelizeMeta` (will store all migrations that the DB recieve) and `users` table.

If you want to change something or did a mistake, you can simple run `yarn sequelize db:migrate:undo` to undo the last migration or `undo:all` to undo all migrations.

### **Models & Loaders**

Now we gonna make our model `User.js` inside `src/app/models`. With this you will be able to create, update, delete, etc, your user in DB.

Now we gonna create a file that will create the conection with Database, this file will load all the models of the app too.

Create a file inside `src/database` named `index.js`. (This is the file that will conect to the DB and load the models)

After all the config's/updates, if you did everything right, after running `yarn dev` and opening the site (localhost:3333) in your browser you will be able to see your first user creation.

### **User's registration**

After we created the `User.js` in `model`, its time to create our first controller, `src/controllers/UserController.js`!

The model is an ORM structure that we create for our migration to tell the DB what we want to send, in this case our user registration with the fields like name, email, password, etc. The controller otherwise is the responsable for receiving this command from the routes, sending this info for the DB through the models, then going back to the controller and finishing in the view.

Now that we did the `UserController.js` configuration, we need to import to the `routes.js`,

```js
routes.post('/users', UserController.store);
```

and finally we can use Insomonia to test it out!

### **Hash Password**

To make our users password secure in our DB, we gonna use Bcryptjs.

Bcryptjs uses salt, a salt is random data that is used as an additional input to a one-way function that "hashes" data, a password or passphrase. Salts are used to safeguard passwords in storage. So install bcrypt:

`$ yarn add bcryptjs`

And then import in the `User.js` model:

```js
import bcrypt from 'bcryptjs';
```

Finally configure your `User.js` model.

### **JWT**

JWT is a Json Web Token and it's used to autenticate our session with a token in our API. When the user login in the app (email and password), if correct, the DB will generate a encrypted Token JWT to validate the user session in the app.

To do it we need to create another controller file in our app folder, `src/app/controllers/SessionController.js`.

And then install jsonwebtoken: `yarn add jsonwebtoken`.

Since we're using linux, for the JWT validation method payload, we can use md5 in the terminal really easy by this command:

`$ echo -n "thisisamd5phrasegostackmeetapp" | md5sum`

We also need a validation time, dont make your token ∞! Define a expiration date: `expiresIn: '7d',`.

Now define a new route for the login in our `routes.js`:

```js
routes.post('/sessions', SessionController.store);
```

After you finish it, you can create and config a file in `src/config/auth.js` <-

### **Middlewares**

We will use middleware to block the user from using the content if he is not logged in. A middleware is a function that has acess to the request(req) and response(res) object, this make a queue of process that can help us work better with req-res of the app.

So create `src/app/middlewares/auth.js` and finish the config.

### **Yup - Validation Input Data**

To wrap it up everything we will finish with validation of the input data in our back-end. We need to validate every object data to tell the user if something is required, like email or password as example.

Yup is a scheema validation lib, so first we need to install it:

`$ yarn add yup`

Config and its done.

# **Final**

This was the first steps for the feature in the app: Login, create and update. At the end of the bootcamp gostack 8 in 2019 we will have a full back-end, front-end and mobile app. (And a lot other stuff!)

:rocket:

<br>
<p>

## **Useful links**

- [Github Help - Basic writing and formatting syntaxBasic writing and formatting syntax. [EN]](https://help.github.com/en/articles/basic-writing-and-formatting-syntax)
- [Rocketseat Starter - 100% free online courses to get you right into the most desired technologies on the market. [PT-BR]](https://rocketseat.com.br/starter)
