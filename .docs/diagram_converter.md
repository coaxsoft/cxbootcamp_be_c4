# Diagram Converter

Converter that transforms C4 PUML text
into Python code should be implemented. It'll be done as its own service.

## Containers

Below you may see the container diagram of the converter.

```mermaid
flowchart TD;

s3[("
    S3

    Static files storage
")]

mlflow("
    MlFlow

    ML models storage
")

subgraph diagram-converter;
    serve["
        Ray.serve

        Serve ML model
    "]
    fastapi["
        FastAPI

        RestAPI proxy
    "]
    fastapi --Request model--> serve;
end

subgraph "API";
   client["
        Client

       FE client of MC
   "]
   api["
      Django BE

      BE application
   "]
   db[("
      PostgreSQL

      Relational Database
   ")]
   client --> api
   api --Manage contracts--> db
end

api --Vectorize / search  contracts--> fastapi
mlflow --Download models--> s3

serve --Download models--> mlflow
```

We use [ray.serve](https://docs.ray.io/en/latest/serve/index.html) to run the models and
experiments in production.

The models and experiments are stored in [mlflow](https://www.mlflow.org).

## Entity Relation

Below is the entity relation diagram of DB tables required for the task solution.

```mermaid
erDiagram

User{
    INT id PK "Primary key"
    VARCHAR email
    VARCHAR password
    VARCHAR is_active
}

Diagram{
    INT id PK "Primary key"
    VARCHAR name
    VARCHAR file
    INT user_id FK "User O2M" 
}

Code{
    INT id PK "Primary key"
    TEXT code
    INT diagram_id FK "Diagram O2O"
}

Diagram }o--|| User: Relates
Code ||--|| Diagram: "Optional O2O"
```

## Sequence

In the sequence diagram below you may see how user interacts with the ML converter.

```mermaid
sequenceDiagram

actor User
participant client as Client
participant api as Django BE
participant generate_code_task as generate_code_task
participant fastapi as FastAPI
participant ray as Ray.Serve
participant db as PostgreSQL

User ->>+ client: Upload diagram
client ->>+ api: Send request

api -->> generate_code_task: Run background task to generate code
api ->>- client: 200, Diagram is being processed
client ->>- User: Display success message

generate_code_task ->>+ fastapi: Send diagram
fastapi ->> fastapi: Preprocess the diagram
fastapi ->>+ ray: Generate code with the ML model

ray ->>- fastapi: Code is generated
fastapi ->>- generate_code_task: Return generated code

generate_code_task ->>+ db: Save generated code
db ->>- generate_code_task: Code is saved
```

## Problems & Troubleshooting

### Database Storage

As we know the generated from C4 diagram project can be "Big", e.g. contain a lot of files with
millions of rows. Keeping it all in the database is effective only if we generate one-file project.

Later, we should research a way to store the generated project in S3 and keep only links on the files
in the database.

### Code is generated slowly

There are several tactics to overcome slow generation.

First of all, we must (of course) generate the code in the background. We can notify users about
generation status using websockets.

We should monitor the execution of models. If we don't have enough entrypoints, we should open
more `ray.serve` nodes (it works with kubernetes). Otherwise, we should use more powerful
server instances with GPU support.
