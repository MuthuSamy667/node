# Pillar Offers Exchaange


## Why


## Features



# Table of Contents

- [Getting Started](#getting-started)
- [Scripts and Tasks](#scripts-and-tasks)
- [Debugger in VSCode](#debugger-in-vscode)
- [API Routes](#api-routes)
- [Project Structure](#project-structure)
- [Logging](#logging)
- [Event Dispatching](#event-dispatching)
- [Seeding](#seeding)
- [Further Documentations](#further-documentation)

## Getting Started

### Step 1:  Set up the Development Environment

You need to set up your development environment before you can do anything.

Install [Node.js and NPM](https://nodejs.org/en/download/)

- on OSX use [homebrew](http://brew.sh) `brew install node`
- on Windows use [chocolatey](https://chocolatey.org/) `choco install nodejs`

Install yarn globally

```bash
npm install yarn -g
```

Install Mongodb database.

> If you work with a mac, we recommend to use homebrew for the installation.

### Step 2: Create new Project

Fork or download this project. Configure your package.json for your new project.

Then copy the `.env.example` file and rename it to `.env`. In this file you have to add your database connection information.

Create a new database with the name you have in your `.env`-file.

Then setup your application environment.

```bash
npm run setup
```

> This installs all dependencies with yarn. After that it migrates the database and seeds some test data into it. So after that your development environment is ready to use.

### Step 3: Serve your App

Go to the project dir and start your app with this npm script.

```bash
npm start serve
```

> This starts a local server using `nodemon`, which will watch for any file changes and will restart the sever according to these changes.
> The server address will be displayed to you as `http://0.0.0.0:3000`.

## Scripts and Tasks

All script are defined in the package.json file, but the most important ones are listed here.

### Install

- Install all dependencies with `yarn install`

### Linting

- Run code quality analysis using `npm start lint`. This runs tslint.
- There is also a vscode task for this called `lint`.

### Tests

- Run the unit tests using `npm start test` (There is also a vscode task for this called `test`).
- Run the integration tests using `npm start test:integration`.
- Run the e2e tests using `npm start test:e2e`.

### Running in dev mode

- Run `npm start serve` to start nodemon with ts-node, to serve the app.
- The server address will be displayed to you as `http://0.0.0.0:3000`

### Building the project and run it

- Run `npm start build` to generated all JavaScript files from the TypeScript sources (There is also a vscode task for this called `build`).
- To start the builded app located in `dist` use `npm start`.

### Database Migration

- Run `typeorm migrations:create -n <migration-file-name>` to create a new migration file.
- Try `typeorm -h` to see more useful cli commands like generating migration out of your models.
- To migrate your database run `npm start db.migrate`.
- To revert your latest migration run `npm start db.revert`.
- Drops the complete database schema `npm start db.drop`.

### Database Seeding

- Run `npm start db.seed` to seed your seeds into the database.

## Debugger in VSCode

To debug your code run `npm start build` or hit `cmd + b` to build your app.
Then, just set a breakpoint and hit `F5` in your Visual Studio Code.

## API Routes

The route prefix is `/api` by default, but you can change this in the .env file.
The swagger and the monitor route can be altered in the `.env` file.

| Route          | Description |
| -------------- | ----------- |
| **/api**       | Shows us the name, description and the version of the package.json |
| **/graphql**   | Route to the graphql editor or your query/mutations requests |
| **/swagger**   | This is the Swagger UI with our API documentation |
| **/monitor**   | Shows a small monitor page for the server |
| **/api/users** | Example entity endpoint |
| **/api/pets**  | Example entity endpoint |

## Project Structure

| Name                              | Description |
| --------------------------------- | ----------- |
| **.vscode/**                      | VSCode tasks, launch configuration and some other settings |
| **dist/**                         | Compiled source files will be placed here |
| **src/**                          | Source files |
| **src/api/controllers/**          | REST API Controllers |
| **src/api/controllers/requests**  | Request classes with validation rules if the body is not equal with a model |
| **src/api/controllers/responses** | Response classes or interfaces to type json response bodies  |
| **src/api/errors/**               | Custom HttpErrors like 404 NotFound |
| **src/api/interceptors/**         | Interceptors are used to change or replace the data returned to the client. |
| **src/api/middlewares/**          | Express Middlewares like helmet security features |
| **src/api/models/**               | Bookshelf Models |
| **src/api/repositories/**         | Repository / DB layer |
| **src/api/services/**             | Service layer |
| **src/api/subscribers/**          | Event subscribers |
| **src/api/validators/**           | Custom validators, which can be used in the request classes |
| **src/api/queries/**              | GraphQL queries |
| **src/api/mutations/**            | GraphQL mutations |
| **src/api/types/**                | GraphQL types |
| **src/api/** swagger.json         | Swagger documentation |
| **src/auth/**                     | Authentication checkers and services |
| **src/core/**                     | The core features like logger and env variables |
| **src/database/factories**        | Factory the generate fake entities |
| **src/database/migrations**       | Database migration scripts |
| **src/database/seeds**            | Seeds to create some data in the database |
| **src/decorators/**               | Custom decorators like @Logger & @EventDispatch |
| **src/loaders/**                  | Loader is a place where you can configure your app |
| **src/public/**                   | Static assets (fonts, css, js, img). |
| **src/types/** *.d.ts             | Custom type definitions and files that aren't on DefinitelyTyped |
| **test**                          | Tests |
| **test/e2e/** *.test.ts           | End-2-End tests (like e2e) |
| **test/integration/** *.test.ts   | Integration test with SQLite3 |
| **test/unit/** *.test.ts          | Unit tests |
| .env.example                      | Environment configurations |
| .env.test                         | Test environment configurations |
| ormconfig.json                    | TypeORM configuration for the database. Used by seeds and the migration. (generated file) |
| mydb.sql                          | SQLite database for integration tests. Ignored by git and only available after integration tests |

## Logging

Our logger is [winston](https://github.com/winstonjs/winston). To log http request we use the express middleware [morgan](https://github.com/expressjs/morgan).
We created a simple annotation to inject the logger in your service (see example below).

```typescript
import { Logger, LoggerInterface } from '../../decorators/Logger';

@Service()
export class UserService {

    constructor(
        @Logger(__filename) private log: LoggerInterface
    ) { }

    ...
```

## Event Dispatching

Our we use this awesome repository [event-dispatch](https://github.com/pleerock/event-dispatch) for event dispatching.
We created a simple annotation to inject the EventDispatcher in your service (see example below). All events are listed in the `events.ts` file.

```typescript
import { events } from '../subscribers/events';
import { EventDispatcher, EventDispatcherInterface } from '../../decorators/EventDispatcher';

@Service()
export class UserService {

    constructor(
        @EventDispatcher() private eventDispatcher: EventDispatcherInterface
    ) { }

    public async create(user: User): Promise<User> {
        ...
        this.eventDispatcher.dispatch(events.user.created, newUser);
        ...
    }
```

## Seeding

Isn't exhausting to create some sample data into your fresh migrated database, well this time is over!
How does it work? Just, create a factory for your entities and a seeds script.

### 1. Create a factory for your entity

For all the entities we want to seed, we need to define a factory. To do so we give you the awesome [faker](https://github.com/marak/Faker.js/) library as a parameter into your factory. Then create your "fake" entity as you would normally do and return it. Those factory files should be in the `src/database/factories` folder and suffixed with `Factory`. Example `src/database/factories/UserFactory.ts`.

```typescript
factory.define(User, (faker: typeof Faker) => {
    const gender = faker.random.number(1);
    const firstName = faker.name.firstName(gender);
    const lastName = faker.name.lastName(gender);
    const email = faker.internet.email(firstName, lastName);

    const user = new User();
    user.firstName = firstName;
    user.lastName = lastName;
    user.email = email;
    return user;
});
```

This can be used to pass some dynamic value into the factory.

```typescript
factory.define(Pet, (faker: typeof Faker, args: any[]) => {
    const type = args[0];
    return {
        name: faker.name.firstName(),
        type: type || 'dog'
    };
});
```

To deal with relations you can use the entity manager like this.

```typescript
import { SeedsInterface, FactoryInterface, times } from '../../lib/seeds';
import { Pet } from '../../../src/api/models/Pet';
import { User } from '../../../src/api/models/User';

export class CreatePets implements SeedsInterface {

    public async seed(factory: FactoryInterface): Promise<any> {
        const connection = await factory.getConnection();
        const em = connection.createEntityManager();

        await times(10, async (n) => {
            // This creates a pet in the database
            const pet = await factory.get(Pet).create();
            // This only returns a entity with fake data
            const user = await factory.get(User).make();
            user.pets = [pet];
            await em.save(user);
        });
    }

}
```

### 2. Create a seed file

The seeds files define how much and how the data are connected with each other. The files will be executed alphabetically.

```typescript
export class CreateUsers implements SeedsInterface {

    public async seed(factory: FactoryInterface): Promise<any> {
        await factory
            .get(User)
            .createMany(10);
    }

}
```

With the second parameter in the `.get(<Entity>, <args>)` you are able to create different variations of entities.

```typescript
export class CreateUsers implements SeedsInterface {

    public async seed(factory: FactoryInterface): Promise<any> {
        await factory
            .get(User, 'admin')
            .create();
    }

}
```

Here an example with nested factories.

```typescript
...
await factory.get(User)
    .each(async (user: User) => {

        const pets: Pet[] = await factory.get(Pet)
            .createMany(2);

        const petIds = pets.map((pet: Pet) => pet.Id);
        await user.pets().attach(petIds);

    })
    .create(5);
...
```

### 3. Run the seeder

The last step is the easiest, just hit the following command in your terminal, but be sure you are in the projects root folder.

```bash
npm start db.seed
```

## Run in Docker container

### Install Docker

Before you start, make sure you have a recent version of [Docker](https://docs.docker.com/engine/installation/) installed

### Build Docker image

```shell
docker build -t <your-image-name> .
```

### Run Docker image in container and map port

The port which runs your application inside Docker container is either configured as `PORT` property in your `.env` configuration file or passed to Docker container via environment variable `PORT`. Default port is `3000`.

#### Run image in detached mode

```shell
docker run -d -p <port-on-host>:<port-inside-docker-container> <your-image-name>
```

#### Run image in foreground mode

```shell
docker run -i -t -p <port-on-host>:<port-inside-docker-container> <your-image-name>
```

### Stop Docker container

#### Detached mode

```shell
docker stop <container-id>
```

You can get a list of all running Docker container and its ids by following command

```shell
docker images
```

#### Foreground mode

Go to console and press <CTRL> + C at any time.

### Docker environment variables

There are several options to configure your app inside a Docker container

#### project .env file

You can use `.env` file in project root folder which will be copied inside Docker image. If you want to change a property inside `.env` you have to rebuild your Docker image.

#### run options

You can also change app configuration by passing environment variables via `docker run` option `-e` or `--env`.

```shell
docker run --env DB_HOST=localhost -e DB_PORT=3306
```

#### environment file

Last but not least you can pass a config file to `docker run`.

```shell
docker run --env-file ./env.list
```

`env.list` example:

```
# this is a comment
DB_TYPE=mongodb
DB_HOST=localhost
DB_PORT=27017
```# node
