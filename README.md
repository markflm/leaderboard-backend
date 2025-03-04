# leaderboard-backend

An open-source community-driven leaderboard backend for the upcoming leaderboards.gg.
This repo is a proof-of-concept for switching to a C# with ASP.NET Core stack. The original backend, written in Go, can be found [here](https://github.com/leaderboardsgg/leaderboard-backend-go).

## Links
- Website: https://leaderboards.gg
- Other Repos: https://github.com/leaderboardsgg
- Discord: https://discord.gg/TZvfau25Vb

## Tech-Stack Information

* JSON REST API intended for the leaderboards.gg site
* C# with ASP.NET Core
* Docker containers for PostgreSQL hosting and management run via [Docker Compose](https://docs.docker.com/compose/install/)

## Editor/IDE

There are a couple options available for you to choose, depending on your OS.

### Visual Studio (for Windows)

If you are on Windows/are a beginner, this will likely be the easiest to use.

* Download [Visual Studio 2022](https://visualstudio.microsoft.com/vs/) (Community edition is free) or modify your existing install
  * You must choose this version as we use the .NET 6 SDK which older versions do not support
* In the section where you choose your Workloads, select at least "ASP.NET and Web Development"

Once VS has set itself up, you'll have to explicitly tell it to _not_ override our .editorconfig settings*. Go to Tools -> Customise -> Commands, pick "File" in the "Menu bar" item, then in the Add Command window, choose File -> Advanced Save Options, and finally, set that to "Current Setting". Screenshot below for a little extra clarity:

![Screenshot of VS settings GUI showing location to add a command to NOT make VS override our .editorconfig settings. Why do we have to do this..](./readme-images/vs2022-prevent-editorconfig-override.png)

> \* VS saves files with `CRLF`, which isn't good for everyone else on Mac/Linux. We've already set explicit line endings in our .editorconfig, but VS overrides it by default.

That should be it! Any other requirements to set up and run the application should be a directed process through the IDE.

### Visual Studio Code/Other Editors

A few cross-platform editor choices would be:

* [Monodevelop (IDE)](https://www.monodevelop.com)
* [Visual Studio Code (Code Editor)](https://code.visualstudio.com/Download)
* Other editors with [Omnisharp integrations](http://www.omnisharp.net/#integrations)

After installing a code editor:

* Download the [.NET 6 SDK](https://dotnet.microsoft.com/en-us/download/dotnet/6.0) for your platform
* After cloning this repo, run the command `dotnet restore` to install all required dependencies
* You will likely want to set up [Omnisharp](http://www.omnisharp.net/) for easier development with your editor
	* In Visual Studio Code, you can simply install the [C# extenstion](https://github.com/OmniSharp/omnisharp-vscode) (use this link or the editor UI)
	* Other editors will need to follow instructions to install the Language Server on their system manually

## Running the Application

### Running the Database(s)

If you would like to forgo running a Postgres database, you can set `USE_IN_MEMORY_DB` in your `.env` file to `true`, or not make a `.env` file at all, and skip to the next section. If you would like to run the Postgres database, follow these instructions:

As mentioned above, we run Docker containers for the DB. After [installing Docker Compose](https://docs.docker.com/compose/install/), run these commands in the project root:

```bash
cp example.env .env
sudo docker-compose (or docker compose) up -d
```

This starts up:
- Adminer which is a GUI manager for the databases
- The main Postgres DB
- The test Postgres DB

Using the default values provided in `example.env`, input these values in Adminer for access:

| Field | Value |
| --- | --- |
| System | PostgreSQL |
| Server | db (for main) / db-test (for test) |
| Username | admin |
| Password | example |
| Database | leaderboardsmain / leaderboardstest |

### Visual Studio

#### First Time Setup

After opening the solution, right click the `LeaderboardBackend` project and click "Set as Startup Project".

#### Run the App

Press F5 or the green play button at the top of the IDE.
Note: The first time running the app, the IDE will prompt you to trust the HTTPS development certs.

#### Test the App

After expanding the `LeaderboardBackend.Test` project, you should be able to select `Test > Run All Tests` from the top of the top menu. Alternatively, you can use the Test Explorer by selecting `Test > Test Explorer`.

### `dotnet` CLI

#### First Time Setup

You will need to trust the HTTPS development certs.
On Windows/Mac, you can run the following command (from the [.NET docs](https://docs.microsoft.com/en-us/dotnet/core/additional-tools/self-signed-certificates-guide#create-a-self-signed-certificate)):

```bash
dotnet dev-certs https --trust
```

If you are on Linux, you will need to follow your distribution's documentation to trust a certificate.

* [This chapter](https://docs.microsoft.com/en-us/aspnet/core/security/enforcing-ssl?view=aspnetcore-6.0&tabs=visual-studio#trust-https-certificate-on-linux) in the .NET docs covers how to generate and then trust the dev cert for service-to-service (e.g. cURLing) and browser communications on Ubuntu.
  * Trusting certs on Fedora and other distros(??) are linked at the bottom of the chapter.

You can read [this chapter](https://docs.microsoft.com/en-us/dotnet/core/additional-tools/self-signed-certificates-guide#clean-up) if you'd like to clear all certs and start over.

Then run the following commands to run the DB migrations:

```bash
# Install project tools
dotnet tool restore
# Run migrations
dotnet ef database update
```

#### Run the App

To run the application from the CLI, run the following command from the root of the project:

```
dotnet run --project LeaderboardBackend --urls https://localhost:7128 // dotnet watch run ... for hotfixing
```

The value provided to `--urls` has to match what's listed under `applicationUrl` in `LeaderboardBackend/Properties/launchSettings.json`. As of this writing, it's `https://localhost:7128`.

#### Test the App

To run the tests, run the following commands from the root of the project:

```bash
cd LeaderboardBackend.Test
dotnet test
```

To debug with breakpoints, first call `export VSTEST_DEBUG=1` before running a test. Doing this allows you to attach a debug process to hit breakpoints with.
