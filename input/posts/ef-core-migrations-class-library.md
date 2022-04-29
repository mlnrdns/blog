Title: Entity Framework Core migration futtatása NET Core/NET standard class library-ből
Published: 2019-02-22 13:11
Author: molnardenes
Tags: [ORM, entity framework core]
---

A napokban egy érdekes problémába futottam bele: egy NET standard class library-ben hoztam létre egy persistence réteget, és innen szerettem volna migrationt csinálni. Amikor megpróbáltam lefuttatni, a következő hibaüzenet fogadott:

```shell
Startup project 'Persistence.csproj' targets framework '.NETStandard'. There is no runtime associated with this framework, and projects targeting it cannot be executed directly. To use the Entity Framework Core .NET Command-line Tools with this project, add an executable project targeting .NET Core or .NET Framework that references this project, and set it as the startup project using --startup-project; or, update this project to cross-target .NET Core or .NET Framework. For more information on using the EF Core Tools with .NET Standard projects, see https://go.microsoft.com/fwlink/?linkid=2034781
```

Egy lehetséges megoldás ilyenkor, ha létrehozunk egy dummy NET Core console alkalmazást, hozzáadjuk a Microsoft.EntityFrameworkCore.Design package-t és beállítjuk startup projektnek. Van azonban egy ennél elegánsabb megoldás is:

A kérdéses projektet multi-targetté alakítjuk úgy. hogy a NET Standard mellett behivatkozzuk a NET Core-t is a projektfájlban:

```csharp
<PropertyGroup> 
    <TargetFrameworks>netcoreapp2.0;netstandard2.0</TargetFrameworks>
</PropertyGroup>

<PropertyGroup>
    <GenerateRuntimeConfigurationFiles>true</GenerateRuntimeConfigurationFiles>
</PropertyGroup>
```

Amire itt figyelni kell, hogy a **TargetFrameworks** többes számba kerüljön, a frameworkök pedig pontosvesszővel legyenek elválasztva egymástól.

Ha ezután beállítjuk startup projektnek a class library-nkat, működni fognak a migrationök.

