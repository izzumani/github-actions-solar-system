# Solar System NodeJS Application

A simple HTML+MongoDB+NodeJS project to display Solar System and it's planets.

---
## Requirements

For development, you will only need Node.js and NPM installed in your environement.

### Node
- #### Node installation on Windows

  Just go on [official Node.js website](https://nodejs.org/) and download the installer.
Also, be sure to have `git` available in your PATH, `npm` might need it (You can find git [here](https://git-scm.com/)).

- #### Node installation on Ubuntu

  You can install nodejs and npm easily with apt install, just run the following commands.

      $ sudo apt install nodejs
      $ sudo apt install npm

- #### Other Operating Systems
  You can find more information about the installation on the [official Node.js website](https://nodejs.org/) and the [official NPM website](https://npmjs.org/).

If the installation was successful, you should be able to run the following command.

    $ node --version
    v8.11.3

    $ npm --version
    6.1.0

---
## Install Dependencies from `package.json`
    $ npm install

## Run Unit Testing

Tests use **sinon** to mock the MongoDB layer, so no running database is required.

    $ npm test

## Run Code Coverage
    $ npm run coverage

## Run Application
    $ npm start

## Access Application on Browser
    http://localhost:3000/

---
## Docker

### Prerequisites
- Docker and Docker Compose installed

### Environment Setup

Create a `.env` file in the project root:

```env
MONGO_USERNAME=admin
MONGO_PASSWORD=P@ssw0rd
MONGO_URI=mongodb://localhost:27017/solarSystemDB?authSource=admin
```

> **Note:** The `authSource=admin` parameter is required because MongoDB root credentials are stored in the `admin` database.

### Run with Docker Compose

Start the application and MongoDB together:

    $ docker compose up -d --build

This provisions two containers:
- **mongo** — MongoDB 6 instance with authentication enabled, using credentials from `.env`
- **solar-system** — The Node.js application, connected to the Mongo container

### Verify the Application

Check that containers are running:

    $ docker container ls

Check application logs:

    $ docker logs -f <solar-system-container-id>

Test the live endpoint:

    $ wget -q -O - 127.0.0.1:3000/live | grep live

### Stop and Clean Up

Stop containers:

    $ docker compose down

Stop and remove data volume (resets MongoDB):

    $ docker compose down -v

---
## CI/CD — GitHub Actions Workflow

The project includes a GitHub Actions workflow (`.github/workflows/solar-system.yml`) that runs on pushes to `main` and `feature/*` branches. It contains three jobs:

### 1. Unit Testing
Runs `npm test` across a matrix of Node.js versions (18, 19, 20, 24) and archives test results as artifacts.

### 2. Code Coverage
Runs `npm run coverage` and archives the coverage report with a 5-day retention.

### 3. Docker (Containerization)
Depends on the unit-testing and code-coverage jobs. Performs the following steps:
- Checks out the repository
- Logs in to Docker Hub using repository secrets
- Builds the Docker image (without pushing) tagged as `<DOCKERHUB_USERNAME>/solar-system:<commit-sha>`
- Runs the container locally with MongoDB environment variables and verifies the `/live` endpoint responds correctly

---
## Publish Package via GitHub Release

The package publish workflow (`.github/workflows/publish-package.yml`) is triggered when a release is **published** on GitHub.

### Prerequisites
- Ensure your latest changes are pushed to the branch you want to release (typically `main`)
- Ensure `package.json` has the correct package name and the version you want to publish

### Steps to Initiate a Release in GitHub
1. Open your repository on GitHub.
2. Go to **Releases**.
3. Click **Draft a new release**.
4. Create/select a new tag (recommended format: `v6.7.7`) and choose the target branch.
5. Add release title and notes.
6. Click **Publish release**.

### Verify Publish Pipeline
1. Go to **Actions** tab in GitHub.
2. Open the workflow run for **Publish Package to GitHub NPM Registry**.
3. Confirm all steps pass, especially **Publish to GitHub Registry**.
4. Verify the package appears under your repository/account **Packages** section.

