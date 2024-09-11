# .NET-Commands
common .NET commands needed to run solutions in VSCode

### FROM IATC

> `dotnet new webapi -lang "c#" -n "S5L2-Sandbox" -f "net7.0" -o S5L2-Sandbox -d -v diag`

- Note here, `.\` was not used when sepcifying output directory, usually, just -n is enough,
  it will automatically create directory, use -o if you want folder name to be different,
  or when grouping projects under specific folders.

> `dotnet sln add S5L2-Sandbox/S5L2-Sandbox.csproj`

- again, see how `.\` was omitted as we are in the same directory, this is different in windows.

> `dotnet run --project S5L2-Sandbox --launch-profile "https"`

- the command already looks for .csproj file, so just passing the directory here is fine.

### FROM RC/AsyncAwait

`dotnet new gitignore`

`dotnet new sln`

`dotnet new console -lang "c#" -n "One" -f "net8.0" -o src/First/one -d -v diag`

`dotnet new console -lang "c#" -n "Twi" -f "net8.0" -o src/First/two -d -v diag`

`dotnet new web -lang "c#" -n "Second" -f "net8.0" -o src/Second -d -v diag`

`dotnet sln add src/First/one`

`dotnet sln add src/First/two`

`dotnet sln add src/Second`

`dotnet run --project src/Second`


### .NET Commands

- For commands to run for project setup, see `OT-ProjectSetup.sh`
- Run startup project `dotnet run --project API`
- `dotnet add /workspaces/NETReact-Server/Persistence/Persistence.csproj package Microsoft.EntityFrameworkCore.Sqlite -v 8.0.1 -s https://api.nuget.org/v3/index.json `
  - adds reference in Persistence .csproj
- to list available dotnet tools -> `dotnet tool list -g`
- for global tool installs, https://www.nuget.org/packages/dotnet-ef
  - `dotnet tool install --global dotnet-ef --version 8.0.1`
  - probably have to match runtime version of .NET, but not sure about EF core version for projects
  - you can check available dotnet-ef commands in the nuget page as well.
  - update by doing `dotnet tool update` instead of `install`
- for initial migration `dotnet ef migrations add InitialCreate -s API -p Persistence`
- `dotnet add /workspaces/NETReact-Server/API/API.csproj package Microsoft.EntityFrameworkCore.Design -v 8.0.1 -s https://api.nuget.org/v3/index.json`
  - adds reference in API .csproj
- `dotnet watch --project API`
- Had issue with ConString, use `dotnet watch --project API run --environment "Development"` instead.
- Hot reload still have some issues, Author complaisn about .NET 7, I have issues in .NET 8.
  - So add the `--no-hot-reload` flag to watch command
- updated file structure so use
  - `dotnet watch --no-hot-reload --project .NET-Server/src/API run --environment "Development"` instead
- `dotnet add /workspaces/.NET-React/.NET-Server/src/Application/Application.csproj package MediatR -v 12.2.0 -s https://api.nuget.org/v3/index.json`
- use `dotnet build` in project level to update inter-project changes, as references dlls.
  - keep dotnet watch running during development so inter project changes will be available via dlls.
