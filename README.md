# Smart Code

Practical meterial to the first lecture of COAX Software Python Bootcamp.

## Project description

Github recently announced Copilot app. We want to build similar app but that builds code from C4 architecture.
The app is named SmartCode.

User can:

* Sign up / Sign in (2FA with Authenticator app);
* Manage profile;
* Add payment cards. Subscribe to plans;
* Get project structure suggestions;
* Generate project;
* Search projects.

Admin can:

* Manage data via django admin panel

## How to use this repo

This repository contains examples of all levels of [C4 model](https://c4model.com/) using PlantUML. The built architecture is build based on the project description above.

You can navigate by the files in the repo.

### System. The First Level

PUML diagram available by this [link](system.wsd).

![img](images/system.png)

### Containers. The Second Level

PUML diagram available by this [link](containers.wsd).

![img](images/containers.png)

### Components. The Third Level

The components are stored in the `components/` directory.

#### Authentication

PUML diagram available by this [link](components/authentication.wsd).

![img](images/components/authentication/png/authentication_components.png)

### Code. The Fourth Level

The code diagrams are stored in the `code/` directory.

#### Sign In Objects

This is the diagram of packages, classes that should be used in the application.

PUML diagram available by this [link](code/authentication/sign_in/sign_in_objects.wsd)

![img](images/code/sign_in_objects/sign_in_objects.png)

#### Sign In Sequence

This is the diagram of user flow to use the objects.

PUML diagram is available by this [link](code/authentication/sign_in/sign_in_sequence.wsd)

![img](images/code/sign_in_sequence/sign_in_sequence.png)