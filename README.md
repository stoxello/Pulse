# Pulse

Pulse is a lightweight infrastructure monitoring platform from Stoxello. It gives teams a central web dashboard for watching servers, services, applications, Windows updates, ICMP checks, and system health across Windows and Linux environments.

Pulse is built as a .NET 9 solution with a Blazor web UI, ASP.NET Core API, EF Core data layer, optional host agents, and licensing support through Stoxello LicenseWeb.

## Features

- Central dashboard for monitored nodes and health status
- Agent-based Windows and Linux monitoring
- Agentless ICMP monitoring
- CPU, memory, disk, services, installed applications, and Windows Update visibility
- Node inventory and detail pages
- License status, activation, and node entitlement enforcement
- MySQL/MariaDB and SQL Server support
- Docker Compose deployment for quick evaluation



## Requirements

- .NET 9 SDK or Runtime
- MySQL/MariaDB or SQL Server
- Windows or Linux host for the web/API services
- Network access between agents, `pulse.Api`, and the database
- A Pulse license for production deployments

## Quick Start With Docker

The included Compose file starts MySQL, the Pulse API, and the Pulse web dashboard.

```powershell
docker compose up -d
```

Open the web dashboard:

```text
http://localhost:4080/Account/Login
```

Default local bootstrap credentials from `docker-compose.yml`:

```text
Username: admin
Password: ChangeMe123!
```

Change these credentials before deploying anywhere outside a local test environment.

The Docker stack exposes:

| Service | URL |
| --- | --- |
| Pulse Web | `http://localhost:4080` |
| Pulse API | `http://localhost:5080` |
| MySQL | private Compose network only |

The containers run EF Core migrations before startup. By default, the Compose deployment uses MySQL with the `pulse` database.

## Local Development Install

Restore and build the solution:

```powershell
dotnet restore
dotnet build .\Pulse.sln
```

Configure the database provider with `PULSE_DB_PROVIDER`:

```powershell
$env:PULSE_DB_PROVIDER = "mysql"
```

For SQL Server:

```powershell
$env:PULSE_DB_PROVIDER = "mssql"
```

Update connection strings in:

- `src/pulse.Api/appsettings.json`
- `src/pulse.Web/appsettings.json`

Run database migrations:

```powershell
dotnet run --project src/pulse.Api -- --migrate --db-provider mysql
dotnet run --project src/pulse.Web -- --migrate-auth --db-provider mysql
```

Start the API:

```powershell
dotnet run --project src/pulse.Api -- --urls http://localhost:5080
```

Start the web app in another terminal:

```powershell
dotnet run --project src/pulse.Web -- --urls http://localhost:4080 --api-url http://localhost:5080
```

Then open:

```text
http://localhost:4080
```

If no admin user exists, Pulse redirects to `/setup` so the first administrator can be created.

## Configuration

Important configuration keys:

| Key | Description |
| --- | --- |
| `PULSE_DB_PROVIDER` | Database provider. Use `mysql`, `mssql`, or `sqlserver`. |
| `ConnectionStrings:PulseDbMySql` | MySQL/MariaDB connection string. |
| `ConnectionStrings:PulseDbSqlServer` | SQL Server connection string. |
| `PulseApi:BaseUrl` | Web app setting that points to the API. |
| `Pulse:AdminUser` | Optional bootstrap admin username. |
| `Pulse:AdminPass` | Optional bootstrap admin password. |
| `Licensing:LicenseWebBaseUrl` | LicenseWeb endpoint. Defaults to `https://license.stoxello.com`. |
| `Licensing:ProductCode` | Pulse product code. Defaults to `PULSE`. |

Command-line URL options are also supported:

```powershell
--urls "http://*:4080;https://*:4443"
--http-url "http://*:4080"
--https-url "https://*:4443"
```

## Agent Installation

Agents are optional. Use them when a node should report detailed local metrics to Pulse.

Windows agent assets are published under:

```text
src/pulse.Web/wwwroot/agents/windows/
```

Linux agent assets are published under:

```text
src/pulse.Web/wwwroot/agents/linux/
```

After the web application is running, agent installers and setup instructions are available from the Pulse dashboard.

## Licensing

Pulse runs in trial mode when no license is installed. Trial mode allows up to 2 monitored nodes.

Production licenses are issued by Stoxello LicenseWeb and use the Pulse product code:

```text
PULSE
```

License keys use this format:

```text
PULSE-XXXX-YYYY-LIVE
```

Activate a license from the Pulse web dashboard under the Licenses page by pasting the key or uploading a `.lic`, `.dat`, or `.txt` license file.

Keep live license keys private. Do not commit customer or production license keys to GitHub; store them in deployment secrets, private configuration, or enter them directly in the Pulse UI.

The LicenseWeb entitlement count controls the maximum number of monitored nodes allowed by the deployment. If a deployment is over its licensed node count, Pulse limits which nodes are allowed to report.

## Commercial License

Pulse is proprietary software owned by Stoxello. Use, copying, modification, redistribution, hosting, and production deployment require a valid license or written permission from Stoxello.

Third-party dependencies remain governed by their own licenses.

For licensing, purchasing, and product information, visit:

[https://stoxello.com](https://stoxello.com)

## Support

For Pulse support, licensing help, or product questions, contact Stoxello through:

[https://stoxello.com](https://stoxello.com)
