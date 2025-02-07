# Getting Started

### Tech stack and platforms:

###### Frontend
- [Next.js](https://nextjs.org/)
- [Material UI](https://mui.com/)

###### Backend
- [Express](https://expressjs.com/)
- [Prisma with Postgresql](https://www.prisma.io/) 

###### Testing
- [Jest](https://jestjs.io/)
- [Cypress](https://www.cypress.io/)
- [Faker.js](https://fakerjs.dev/)

###### Deployment
- [Nginx](https://www.nginx.com/)
- [Pm2](https://pm2.keymetrics.io/)

### Repositories:
- [Skylab Frontend](https://github.com/orbital-skylab/skylab-frontend)
- [Skylab Backend](https://github.com/orbital-skylab/skylab-backend)

### Important Reads:
- [Git Workflow](https://github.com/orbital-skylab/skylab-frontend/wiki/Git-Workflow)
- [Issue Tracking](https://github.com/orbital-skylab/skylab-frontend/wiki/Issue-Tracking)
- [Style Guide](https://github.com/orbital-skylab/skylab-frontend/wiki/Style-Guide)
- [Code Review Rubrics](https://github.com/orbital-skylab/skylab-frontend/wiki/Code-Review-Rubrics)
- [Contribution Guide](https://github.com/orbital-skylab/.github/blob/main/CONTRIBUTING.md)

## Prerequisites
1. Install [Node.js (version LTS Fermium)](https://nodejs.org/download/)
2. Install [Postgresql (version 14)](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads)

## Initial Setup for Local Development

### Frontend

#### 1. Clone Git Repository

Open your terminal and clone the frontend repository into a directory of your choice.

```
cd <path to directory of choice>
git clone https://github.com/orbital-skylab/skylab-frontend
```

#### 2. Install Dependencies

Ensure that you have installed and are using the **LTS Fermium** version of Node.js. If you have a different version of Node.js installed, you can make use of nvm for [windows](https://github.com/coreybutler/nvm-windows) or [macOS/unix/WSL](https://github.com/nvm-sh/nvm) to change it to LTS Fermium.

```
cd skylab-frontend
npm install
```

#### 3. Set up .env.local File

We have a `.env.local` file in the root of the `skylab frontend` repository to contain project secrets. If you wish to add more secrets, ensure to prefix the name of the secret variable with `NEXT_PUBLIC_` for it to be exposed to the browser as described [here](https://nextjs.org/docs/basic-features/environment-variables).

```
touch .env.local
echo NEXT_PUBLIC_BASE_DEV_API_URL="http://localhost:4000/api" >> .env.local
```

### Postgres Database

#### 1. Connect to psql

Ensure that you have version 14.X of [Postgresql](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads) installed.

Postgresql comes with a default super user named `postgres`. We will be connecting to the psql interactive terminal using this super user.

**macOS**:

_macOS does not require password authentication for the default `postgres` super user_.
```
sudo -u postgres psql // sudo -u <username> psql
```

**Windows**: 

_You will be prompted to enter the password for `postgres` which you would have set up during installation_.
```
psql -U postgres // psql -U <username>
```

#### 2. Create Development Databases

We will be creating and using a new database for development. By default we set the name of the database to be `skylab`, but you can name it anything that you want.

If you do rename the database, ensure to update the environment variables for the backend application (which we will be setting up later on)  accordingly.

```
CREATE DATABASE skylab;
```

#### 3. (Optional) Create New Super User

You can create your own super user to use in place of the default `postgres` super user.

```
CREATE USER <your_super_user_username> WITH SUPERUSER PASSWORD '<your_super_user_password>';
```

#### 4. Exit psql

If there is nothing else for you to do within psql, you can shut down the psql interactive terminal.

````
\q
````

### Backend

#### 1. Clone the Repository

Open your terminal and clone the backend repository into a directory of your choice.

```
cd <path to directory of choice>
git clone https://github.com/orbital-skylab/skylab-backend
```

#### 2. Install Dependencies

Ensure that you have installed and are using the **LTS Fermium** version of Node.js. If you have a different version of Node.js installed, you can make use of nvm for [windows](https://github.com/coreybutler/nvm-windows) or [macOS/unix/WSL](https://github.com/nvm-sh/nvm) to change it to LTS Fermium.

```
cd skylab-backend
npm install
```

#### 3. Set up .env File

We have a `.env` file in the root of the `skylab backend` repository to contain project secrets. Ensure to replace all the values encapsulated in angle brackets: `<replace_these_values>`.

```
touch .env
echo DATABASE_URL="postgresql://postgres:<your_postgres_super_user_password>@localhost:5432/skylab" >> .env 
echo JWT_SECRET="<any_value>" >> .env
```

The format of the `DATABASE_URL` is `postgresql://<username>:<password>@<domain>:<port>/<database_name>`. 

You can make use of another postgres user if you choose to do so in place of the `postgres` super user. The default port used by psql is `5432`. If you chose a different name instead of `skylab` for your development database, ensure to update the database name in `DATABASE_URL`.

If your postgres super user account is not password protected, use `...postgres@localhost...` instead of `...postgres:<password>@localhost...`.

`JWT_SECRET` is used for user authentication and authorization via JSON web tokens. These tokens are cryptographically signed and contain verifiable user information in a standard format which we use to verify the identity of an entity making a request to the server. Unauthenticated and unauthorized users will be rejected with HTTP error `401` or `403` respectively.

#### 4. Run Migration

Run a migration to create the necessary tables in the development databases.

```
npx prisma generate
npx prisma migrate dev
```

## Booting up the Development Environment

Open two different terminals to start up the frontend and backend applications.

```
cd <path_to_skylab_frontend>
npm run dev

cd <path_to_skylab_backend>
npm run dev
```

The frontend application can be accessed at `localhost:3000` while the backend application can be accessed at `localhost:4000`.


## Seeding Mock Data

We are currently trying to resolve some issues with the Prisma seeding function. 

In the mean time, mock data can be seeded into the development databases by making a `POST` request using [Postman Desktop Application](https://www.postman.com/) to `localhost:4000/api/seed` while the backend application is running. No request body is required, and the seeding will take a few minutes to complete.

## Using Admin Account

After seeding the mock data, an admin account with the following credentials will be available for you to use.

```
email: admin@skylab.com
password: Password123
```

If you wish to create another admin account, you will need to make a `POST` request while the backend application is running as outlined [here](https://github.com/orbital-skylab/skylab-backend/wiki/Administrators-Endpoints#create-a-new-account-with-administrator-role).

## Directly Interacting with Database

After migration is compelted, there are two ways to directly view and interact with data in the skylab database.

### PSQL
1. Connect to psql as the postgres super user via the command `psql -U postgres`**(Windows)** or `sudo -u postgres psql`**(macOS)** and key in your postgres super user password if applicable.
2. Connect to the development database via the command `\c skylab`
3. Shut down psql after you are done via `\q`

### Prisma Studio
1. Boot up prisma studio via the command `npx prisma studio`
2. By default, prisma studio can then be accessed at `localhost:5555`

## Deployment on SoC VM

The front and back end of Skylab V2 are deployed and running on SoC VM. When changes are made to either repository, the corresponding applications on the VM will need to be restarted.

Please contact prof for the hostname and password of the VM, then ssh into it via `ssh <hostname>` and key in the password.

Both applications are hosted via [pm2](https://pm2.keymetrics.io/). The frontend application is named `skylab-frontend` and the backend application is named `skylab-frontend`.

`skylab-frontend` is a standard [Next.js](https://nextjs.org/) application.

`skylab-backend` is an [Express.js](https://expressjs.com/) server supported by [Prisma ORM](https://www.prisma.io/).
- A local instance of psql running on port 5432 is being used to host the database for Prisma.
- The database can be managed directly by connecting to psql by simply using the `sudo -u postgres psql`, then, connecting to the production database via the command `\c skylab`.
    - You can also connect to psql with our skylab super user, more information can be found in the `.env` file in the `skylab-backend` repository.
- There is also a development database and shadow development database called `skykab_dev` and `skylab_dev_shadow` respectively, which can be used if necessary. The development database should contain mock data seeded via [faker.js](https://fakerjs.dev/).

### Frontend

1. `cd Desktop/skylab-frontend` to move to the frontend repository
2. `git pull` to update the repository with the latest changes
3. `npm ci` to update dependencies with lockfile frozen
4. `npm run build` to rebuild the static files for production
5. `pm2 restart skylab-frontend` to restart the frontend on production

### Backend

1. `cd Desktop/skylab-backend` to move to the backend repository
2. `git pull` to update the repository with the latest changes
3. `npm ci` to update dependencies with lockfile frozen
4. `npm run build` to rebuild the static files for production
5. `pm2 restart skylab-backend` to restart the backend on production

### Other useful information

- The frontend and backend applications were started using the following steps
    - Frontend
        - ssh into VM
        - `cd Desktop/skylab-frontend`
        - `npm run build`
        - `pm2 start npm --name skylab-frontend -- run start`
    - Backend
        - ssh into VM
        - `cd Desktop/skylab-backend`
        - `npm run build`
        - `pm2 start npm --name skylab-backend -- run start`
- The applications can be stopped/reloaded or even deleted via `pm2 <stop/reload/delete> <skylab-frontend/skylab-backend>`
    - eg; `pm2 reload skylab-backend` to reload the backend application
- The status of both applications can be monitored via `pm2 monit`

## Glossary

|Term|What It Refers To|
|-|-|
|Cohort|The Orbital program runs once per academic year, during the summmer break from May to August. Each run is referred to as a cohort.|
|User|Someone who is able to sign in to the Skylab platform with their email and password.|
|Role|Student, Adviser, Mentor or Administrator. The role a user has during a particular cohort will determine what pages they can access and what actions they can carry out in that time period. For example, a particular user could be a student in the 2020 cohort then an adviser in the 2021 cohort.|
|Team|Groups of students (typically 2 students per team).|
|Project|Refers to the software project undertaken by a team for Orbital|
|Milestone|A checkpoint for students to submit their preliminary work and documentation. Evaluations also happen after each milestone. There are typically 5 milestones per Orbital cohort (Liftoff, Milestone 1, Milestone 2, Milestone 3 and Splashdown).|
|Deadline|A general term for set of questions that requires a submission by a specified due date.|
|Submission|A general term for a set of answers to a deadline.|
|Evaluation|A specific type of deadline where teams and advisers review other teams' projects. **Note:** evaluations are team-level submissions, not individual, so only 1 student from each team is required to submit each evaluation.|
|Evaluation Relationship|A unidirectional relationship which determines who evaluates what. For example, there could be an evaluation relationship whereby Team A evaluates Team B's project, but it is not necessary for there to also be an evaluation relationship whereby Team B evaluates Team A's project. Typically, Artemis teams will be assigned more projects to review.|
