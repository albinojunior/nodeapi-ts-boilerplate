# CrudsAPI Node + TypeScript
- A Node API boilerplate for create CRUDS written in TypeScript

- [1.  Installing](#1-installing)
  - [Install dependencies](#install-dependencies)
  - [Installing pm2](#installing-pm2)
  - [Environments](#environments)
- [2. Database](#2-database)
  - [Install database driver](#install-database-driver)
  - [Database Config](#database-config)
  - [Migrations and Seeds](#migrations-and-seeds)
  - [Creating Migrations](#creating-migrations)
  - [Running Migrations](#running-migrations)
  - [Creating Seeds](#creating-seeds)
  - [Running Seeds](#running-seeds)
- [3.  Schedules](#3--schedules)
  - [Installing](#installing)
  - [Configuring](#configuring)
- [4. Running](#4-running)
- [5. Building](#5-building)
  - [DEV](#dev)
  - [PROD](#prod)
- [6. Docs](#6-docs)

## 1.  Installing

#### Install dependencies

```bash
$ npm i
```

#### Installing pm2
```bash
$ npm i -g pm2
```

#### Environments

Copy `.env.example` to `.env` and set environments

## 2. Database
#### Install database driver

- **PostgresSQL**
```bash
$ npm install --save pg pg-hstore
```

- **MySQL**
```bash
$ npm install --save mysql2
```

- **MariaDB**
```bash
$ npm install --save mariadb
```

- **SQLite**
```bash
$ npm install --save sqlite3
```

- **SQL Server**
```bash
$ npm install --save tedious
```

#### Database Config
Setting config database connection on config file:  `config/database.js`
***if you will not use migrations or seeds this config  can be created with ".ts" extension**
[more config details](http://docs.sequelizejs.com/manual/getting-started.html)

#### Migrations and Seeds
- For implements migrations or seeds install sequelize-cli module:

```bash
$ npm i -g sequelize-cli
```

- Create `database/seeds` and/or  `database/migrations` folder
*OBS: You can create seeds or migrations folders individually*

- Create a `.sequeilizerc` and paste follow code:

```
module.exports = {
  "config": "./config/database.js", //database config file reference
  "seeders-path": "./database/seeds", //remove if you don't use seeds
  "migrations-path": "./database/migrations" //remove if you don't use migrations
};
```

#### Creating Migrations

```bash
$ sequelize migration:create --name MIGRATION_NAME
```

#### Running Migrations

```bash
$ sequelize db:migrate
```

#### Creating Seeds

```bash
$ sequelize seed:create --name SEED_NAME
```

#### Running Seeds
```bash
$ sequelize db:seed:all
```


## 3.  Schedules
#### Installing
- For implements Schedules install node-schedule module:

```bash
$ npm i node-schedule --save
```

```bash
$ npm i @types/node-schedule --save-dev
```

#### Configuring

- Create `src/jobs` folder and view [example](https://github.com/albinojunior/nodeapi-typescript/blob/master/examples/job.example.ts) of job file.

- Create `src/schedule.ts` file ande paste follow code or copy from [example](https://github.com/albinojunior/nodeapi-typescript/blob/master/examples/schedule.example.ts)

```
/* src/schedule.ts */

import { resolve } from "path";
import { scheduleJob } from "node-schedule";
import * as fs from "fs";
import { Job } from "./common/interfaces/job";

export default class Schedule {
  public jobs: Job[] = [];

  public constructor() {
    this.getJobs();
  }

  public getJobs = (): void => {
    const path = resolve("src/jobs");
    let files = fs.readdirSync(path);
    this.jobs = files.map((file): Job => require(`./src/jobs/${file}`));
  };

  public startJobs = (): void => {
    this.jobs.map(
      (job: Job): void => {
        if (job.enabled)
          scheduleJob(job.date, async (): Promise<void> => await job.execute());
      }
    );
  };
}
```
- Import `src/schedule.ts` on app and call **startJobs()** method on *App* constructor


##  4. Running

```bash
$ npm run dev
```

## 5. Building

#### DEV

```bash
$ npm build:dev
```

#### PROD

```bash
$ npm build
```


## 6. Docs

- [Sequelize](http://docs.sequelizejs.com/)
