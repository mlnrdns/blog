Title: ASP.NET Core 2 biztonság OAuth2 és OpenId használatával 3. rész SQL Server migration
Published: 2019-02-26 8:14
Author: molnardenes
Tags: [oauth2, openid, security, identityserver4, sql server, entity framework core]
---

Ebben a bejegyzésben megismerjük, hogy az előzőekben bemutatott in-memory tárolók tartalmát hogyan tudjuk SQL Serverre mozgatni Entity Framework Core használatával. Első lépésben ehhez szükségünk lesz néhány NuGetre, amit adjunk hozzá a Choam.IDP projekthez:

```bash
dotnet add Choam.IDP package IdentityServer4.EntityFramework
dotnet add Choam.IDP package Microsoft.EntityFrameworkCore.Tools
dotnet add Choam.IDP package Microsoft.EntityFrameworkCore.SqlServer
```

Ha ezzel megvagyunk, akkor a Choam.IDP projekt *appsettings.json* állományában állítsuk be a connectionStringet. A Choam.ERP.API appsettinsgéből átmásolhatjuk nyugodtan, de módosítsuk egy kicsit és ne ugyanazt az adatbázist használjuk, hanem egy másikat, mondjuk ChoamProjects.IDP-t:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },  
  "ConnectionStrings": {
    "projectDbConnectionString": "Server=(localdb)\\mssqllocaldb;Database=ChoamProjects.IDP;Trusted_Connection=True;"
  }
}
```

Térjünk át a Startup.cs-re, ahol szükséges lesz megfelelően beállítani az alkalmazást a Configuration segítségével. Ezt is másoljuk ki nyugodtan az Choam.ERP.API-ból:

```csharp
public class Startup
{
    public IConfiguration Configuration { get; }

    public Startup(IConfiguration configuration)
    {
        Configuration = configuration;
    }

    ...
}
```

Ha ez megvan, akkor a ConfigureServices metódusban az in-memory services-t át kell irányítanunk az SQL Serverre. Minden in-memory bejegyzést távolítsunk el, és használjuk az SQL Servert helyette. Első körben adjunk hozzá egy úgynevezett **ConfigurationStore**-t és egy **OperationalStore**-t. Előfször építsük fel a db contextet, adjuk meg a connectionStringet és azt az assembly-t, amelyiken a migration-ök találhatók. Az előbbi store a konfigurációs, míg az utóbbi az operational információkat fogja tartalmazni:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var assemblyThatContainsMigrations = typeof(Startup).GetTypeInfo().Assembly.GetName().Name;
    services.AddMvc();
    services.AddIdentityServer()
        .AddDeveloperSigningCredential()
        .AddTestUsers(Config.GetUsers())
        .AddConfigurationStore(options =>
        {
            options.ConfigureDbContext = builder =>
                builder.UseSqlServer(Configuration.GetConnectionString("IdpDbConnectionString"),
                    sql => sql.MigrationsAssembly(assemblyThatContainsMigrations));
        })
        .AddOperationalStore(options =>
        {
            options.ConfigureDbContext = builder =>
                builder.UseSqlServer(Configuration.GetConnectionString("IdpDbConnectionString"),
                    sql => sql.MigrationsAssembly(assemblyThatContainsMigrations));
        });
        
}
```

Ha ezzel megvagyunk, indítsuk el a **migration**t parancssorból. A **PersistedGrantDbContext** és a **** az IdentityServer4.EntityFramework package-ben találhatók. A *-c* megadja, hogy melyik DbContextet szeretnénk használni, az *-o* pedig, hogy melyik mappába várjuk az outputot.

```bash
dotnet ef migrations add InitialPersistedGrantMigration -c PersistedGrantDbContext -o Data/Migrations/IdentityServer/PersistedGrantDb
dotnet ef migrations add InitialConfigurationMigration -c ConfigurationDbContext -o Data/Migrations/IdentityServer/ConfigurationDb
```

Miután rendben lefutottak a migrationök, készen áll az alkalmazásunk az SQL Serverrel történő használatra. Jelenleg viszont üres ez az adatbázis, úgyhogy célszerű lenne feltölteni az in-memory adatokkal. Hozzunk létre a Startup.cs-ben egy új metódust, amiben átmigráljuk az adatokat memóriából az SQL Serverre. Ha paraméterben átadjuk az IApplicationBuildert, akkor elérjük a regisztrált service-eket. A **CreateScope** metódus egy **IServiceScope**-ot ad vissza, ami lehetővé teszi a függőségek feloldását ebben a scope-ban. 

Ezután biztosítjuk, hogy migration biztosan végbement, ezután pedig a Config.cs-ben létrehozott metódusok segítségével végimegyünk az in-memory objektumokon és hozzáadjuk őket az adatbázishoz: 

```csharp
private void MigrateFromInMemoryToSqlServer(IApplicationBuilder app)
{
    using (var scope = app.ApplicationServices.GetService<IServiceScopeFactory>().CreateScope())
    {
        scope.ServiceProvider.GetRequiredService<PersistedGrantDbContext>().Database.Migrate();

        var context = scope.ServiceProvider.GetRequiredService<ConfigurationDbContext>();
        context.Database.Migrate();

        if (!context.Clients.Any())
        {
            foreach (var client in Config.GetClients())
            {
                context.Clients.Add(client.ToEntity());
            }

            context.SaveChanges();
        }

        if (!context.IdentityResources.Any())
        {
            foreach (var identityResource in Config.GetIdentityResources())
            {
                context.IdentityResources.Add(identityResource.ToEntity());
            }

            context.SaveChanges();
        }

        if (!context.ApiResources.Any())
        {
            foreach (var apiResource in Config.GetApiResources())
            {
                context.ApiResources.Add(apiResource.ToEntity());
            }

            context.SaveChanges();
        }

    }
}
```

Ezután nincs más dolgunk, mint meghívni a fenti metódust a **Configure**-ban:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    MigrateFromInMemoryToSqlServer(app);
    
    ...
}
```

Ha lefutott a migration, célszerű a kódot eltávolítani, ugyanis a későbbiekben erre már nem lesz szükségünk. Ugyanígy a ConfigureServices metódusban törölhetjük az AddInMemory...() hívásokat, ugyanis ezek már az adatbázisból fognak történni.

Az IDP-nk már össze van kötve az adatbázissal, viszont a felhasználók továbbra is kódból kerülnek hozzáadásra. Egy következő bejegyzésben megnézzük, hogy hogyan lehet in-memory user store helyett az ASP.NET Core Identity-t használni a felhasználókezelésre.
