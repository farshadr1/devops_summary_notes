# twelve-Factor App

**https://www.12factor.net**

**https://cobra.dev/docs/tutorials/12-factor-app**

Here's the Twelve-Factor App that is easy to review and memorize.

|      # | Factor                  | Memory Tip                                    | Example                                                             |
| -----: | ----------------------- | --------------------------------------------- | ------------------------------------------------------------------- |
|  **1** | **Codebase**            | One codebase, many deployments                | One Git repository for Dev, Test, and Production                    |
|  **2** | **Dependencies**        | Declare and isolate dependencies              | `go.mod`, `package.json`, `requirements.txt`                        |
|  **3** | **Config**              | Keep configuration outside the code           | Environment variables like `DB_HOST`, `API_KEY`                     |
|  **4** | **Backing Services**    | Treat external services as attached resources | PostgreSQL, Redis, RabbitMQ can be replaced without changing code   |
|  **5** | **Build, Release, Run** | Separate build, release, and execution        | Build Docker image → Create release → Run container                 |
|  **6** | **Processes**           | Keep processes stateless                      | Store sessions in Redis instead of application memory               |
|  **7** | **Port Binding**        | Export services via a port                    | Web server listens on `:8080`                                       |
|  **8** | **Concurrency**         | Scale out by running more processes           | Multiple replicas in Kubernetes                                     |
|  **9** | **Disposability**       | Fast startup, graceful shutdown               | Handle `SIGTERM` correctly and start quickly                        |
| **10** | **Dev/Prod Parity**     | Keep development and production similar       | Same PostgreSQL version and Docker image in all environments        |
| **11** | **Logs**                | Treat logs as event streams                   | Write logs to `stdout`/`stderr`; Docker or Kubernetes collects them |
| **12** | **Admin Processes**     | Run admin tasks as one-off processes          | Database migrations, backups, restore commands                      |
