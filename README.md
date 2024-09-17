# .NET-Commands
Common .NET commands needed to run solutions in VSCode

### Running .NET in VSCode
- `ctrl + shift + p` -> .NET Generate Assets

### dotnet NEW
- for checking command usages, do `dotnet --help`
  - this is a nested command, you can also `dotnet build --help`
- you can list templates using `dotnet new list`
  - search local templates `dotnet new list <fuzzy_search_input>`
- you can also search online templates using `dotnet new search "Clean Architecture"`
  - you can search by author too by passing `--author mariyam` or `--author="mariyam"`
  - then install template via package name, like `dotnet new install Custom.Online.Template`
  - This will install locally, now just create template like usual `dotnet new custom-template`
- A very important argument that can be passed is `--dry-run` to well, do what it says.
  - usage, `dotnet new gitignore --dry-run -v diag`

### dotnet SLN
- you can recursively add all projects to sln using `dotnet sln add **/*.csproj`
- then do `dotnet sln list` to see all projects in solution.
- you can add references using `add`, eg: `dotnet add Playground reference Playground2`
  - adds `ItemGroup > ProjectReference#Include` in `.csproj` file
- list all references of a project, `dotnet list Playground/ reference`
- add package, `dotnet add Playground package Error  0r`
- you can also list packages, using `dotnet list Playground/ package`


### Nicities
- `dotnet new gitignore`
- `dotnet new globaljson`
- `dotnet new editorconfig`
  - to apply config, do `dotnet format` optionally with verbose `-v detailed`
- `dotnet tool list` or globably with  `--global` (obviously, lol)
- `dotnet tool install --global dotnetsay`
  - excute `dotnetsay`

#### Creating Own Tool
- create new console app `dotnet new console -o Playground`
- write it, run it.
- in `.csproj` of project, add `PackAsTool` as True, to `PropertyGroup`
  - set `ToolCommandName` in `PropertyGroup` as well, give some name.
- do `dotnet pack -c Release -o CompanyToolAssets` or just "assets", whatever.
  - should save as `.nupkg` in assets.
- can install globally now using `dotnet tool install --global --add-source assets/ MyTool` or whatever folder name.
- sayconf
- use `dotnet tool run` to run local commands etc, very handy. Underused functions.
- you can also search for tools `dotnet tool search dotnetsay` <fuzzy_input>
- there is also a runtime option you can pass `--fx-version 7.0.0` to target another version.


## Creating & Running (Common)

> `dotnet new sln --name "BigProject"`

- if `--name` is not specified, some convention is followed, just remove it using rm.

> `dotnet new webapi -lang "c#" -n "S5L2-Sandbox" -f "net7.0" -o S5L2-Sandbox -d -v diag`

- ^ Note here, `.\` was not used when sepcifying output directory, usually, just -n is enough,
  it will automatically create directory, use -o if you want folder name to be different,
  or when grouping projects under specific folders.

- simpler way of adding projects.
  - `dotnet new webapi -n API --use-controllers`
  - `dotnet new classlib -n Application`
  - `dotnet new console -o Playground`
    - difference between using `-o` and `-n` is that, -n will autoamtiaclly create directory as same name.
      There might be slight difference based on naming convention, no confirmed. But using -o Will specify output folder.
      if -n is not specified, then same as folder is given to program. Just try it out.

> `dotnet sln add S5L2-Sandbox/S5L2-Sandbox.csproj`

- ^ again, see how `.\` was omitted as we are in the same directory, this is different in windows.

> `dotnet run --project S5L2-Sandbox --launch-profile "https"`

- ^ the command already looks for .csproj file, so just passing the directory here is fine.

> `dotnet watch --no-hot-reload --project .NET-Server/src/API run --environment "Development"`

- ^ another way to run, with hot reload.

> `dotnet add API/API.csproj reference Application/Application.csproj`

- ^ adding reference.

> `dotnet sln list`
`dotnet restore`
`dotnet --list-sdks`
`dotnet --info`
`dotnet --list-runtimes`

- ^ common helpful commands.

## .NET Commands

- For commands to run for project setup, see `OT-ProjectSetup.sh`
- If you have 2 projects, just create sln and add projects to sln first.
- Run startup project `dotnet run --project API`
- You can run `.dll`'s directly, `dotnet MyProgram.dll`
  - You may pass arguments here too, refer to image at top.
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

```
# Make File Executeable Using `chmod +x OT-ProjectSetup.sh"
# Run File Using `./OT-ProjectSetup.sh`

#!/usr/bin/env bash
green="\033[1;32m"
reset="\033[m"

echo "Creating Main Project Directory"
echo -e "${green}Skipped Creating Directory, Building in Current Directory${reset}"
# mkdir Reactivities
# cd Reactivities

echo -e "${green}Adding Git Ignore File${reset}"
dotnet new gitignore

echo -e "${green}Creating Solution & Projects${reset}"
dotnet new sln
dotnet new webapi -n API --use-controllers
dotnet new classlib -n Application
dotnet new classlib -n Domain
dotnet new classlib -n Persistence

echo -e "${green}Adding Projects To The Solution${reset}"
dotnet sln add API/API.csproj
dotnet sln add Application/Application.csproj
dotnet sln add Domain/Domain.csproj
dotnet sln add Persistence/Persistence.csproj

echo -e "${green}Setting Up Project Dependancies${reset}"
dotnet add API/API.csproj reference Application/Application.csproj
dotnet add Application/Application.csproj reference Domain/Domain.csproj
dotnet add Application/Application.csproj reference Persistence/Persistence.csproj
dotnet add Persistence/Persistence.csproj reference Domain/Domain.csproj

echo -e "${green}Executing Restore${reset}"
dotnet restore

echo -e "${green}Finished!${reset}"
echo "Listing Solution Linked Projects.."
dotnet sln list
```

## From RC Async/Await
```
dotnet new gitignore
dotnet new sln
dotnet new console -lang "c#" -n "One" -f "net8.0" -o src/First/one -d -v diag
dotnet new console -lang "c#" -n "Twi" -f "net8.0" -o src/First/two -d -v diag
dotnet new web -lang "c#" -n "Second" -f "net8.0" -o src/Second -d -v diag
dotnet sln add src/First/one
dotnet sln add src/First/two
dotnet sln add src/Second
dotnet run --project src/Second
```

## Command Structure & Getting Started.
<img src="https://github.com/user-attachments/assets/a44f6ada-3ec4-4e22-8c42-dfe303708914" width="450">

<em>Amichai Mantinband, 2022, YouTube, https://www.youtube.com/watch?v=gPoUSBnrYFU</em>
<br><br><br>
<img src="https://github.com/user-attachments/assets/808487e8-4647-4685-8036-d0ecb1072fa0" width="450">

<em>Amichai Mantinband, 2022, YouTube, https://www.youtube.com/watch?v=gPoUSBnrYFU</em>
<br><br><br>
<img src="https://github.com/user-attachments/assets/da78a38a-6393-43c9-a0c9-e6bf811e47cd" width="450">
> you can also pass arguments here.

<em>Amichai Mantinband, 2022, YouTube, https://www.youtube.com/watch?v=gPoUSBnrYFU</em>
<br><br><br>
<img src="https://github.com/user-attachments/assets/2dd19adc-f627-4161-9f6b-f4b78332fc33" width="450">

<em>Amichai Mantinband, 2022, YouTube, https://www.youtube.com/watch?v=gPoUSBnrYFU</em>
<br><br><br>

<img src="https://github.com/user-attachments/assets/39da4512-d438-46fd-9f30-5f8634258a54" width="450">

<em>Amichai Mantinband, 2022, YouTube, https://www.youtube.com/watch?v=gPoUSBnrYFU</em>
<br><br><br>

<img src="https://github.com/user-attachments/assets/e1246ffa-58a5-45f6-8c6a-101fcae89339" width="450">

<em>Amichai Mantinband, 2022, YouTube, https://www.youtube.com/watch?v=gPoUSBnrYFU</em>
<br><br><br>

<img src="https://github.com/user-attachments/assets/7a4fc1ec-06ac-4ac6-bd6f-dfb36ed6542c" width="450">

<em>Amichai Mantinband, 2022, YouTube, https://www.youtube.com/watch?v=gPoUSBnrYFU</em>
<br><br><br>

<img src="https://github.com/user-attachments/assets/0677e8bf-4883-4aa9-808f-ef3f34bfc589" width="450">

<em>Amichai Mantinband, 2022, YouTube, https://www.youtube.com/watch?v=gPoUSBnrYFU</em>
<br><br><br>

<img src="https://github.com/user-attachments/assets/c0d75ad1-7346-481a-a210-d4ec4166b788" width="450">

<em>Amichai Mantinband, 2022, YouTube, https://www.youtube.com/watch?v=gPoUSBnrYFU</em>
