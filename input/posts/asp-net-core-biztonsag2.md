Title: ASP.NET Core 2 biztonság OAuth2 és OpenId használatával 2. rész API
Published: 2019-02-08 09:50
Author: molnardenes
Tags: [oauth2, openid, security, identityserver4]
---

## Hybrid flow visszatekintés

A folyamat első lépéseként a kliensalkalmazás egy authentication requestet küld az IDP felé az authorization endpointra, ahol a felhasználó azonosítja magát, majd az IDP hozzájárulást kér az adatok továbbítására. Ezután az IDP átiránytja az authorization code-ot és az identity_tokent a klienshez, ami validálja az identity_tokent. Sikeres validálás után szükségünk lesz az access_tokenre, amit a kliens a back channelen (ez nem látható a user számára) keresztül a token endpointtól tud megkérni. Ez a request tartalmazza az authorization code-ot, client_id-t és client_secretet. Ez a lépés tulajdonképpen azt jelenti, hogy a kliens azonosítja magát az IDP számára. Ezután válaszban visszakapjuk az identity_tokent és az access_tokent.

Az előző bejegyzésben láthattuk, hogy az access_tokent hozzá van rendelve az identity_tokenhez, mégpedig az **at_hash** értéken keresztül. Az identity_token validálásra kerül, és ezáltal az access_token hozzá kapcsolt része is. Az identity_tokenből megkapja a kliens a user azonosítóját, így be tud jelentkezni a kliensbe, ahonnan most már meg tudjuk hívni az API-nkat.

Az access_token eltárolásra kerül és minden kérésben elküldjük az API felé az **authorization header**ben mint **bearer token**. Az API ezután validálja az access_tokent.

## Hozzáférés biztonságossá tétele

A hozzáférés biztonságossá tételéhez először az IDP **Config.cs**-jét kell módosítani, mégpedig egy újabb scope hozzáadásával. Hozzunk létre egy új metódust a **GetApiResources**-t, ami egy IEnumerable<ApiResource>-t ad vissza. Az ApiResource egy resource scope:

```csharp
public static IEnumerable<ApiResource> ApiResources()
{
    return new List<ApiResource>
    {
        new ApiResource("choamprojectsapi", "Choam Projects API")
    };
}
```

Ezután a **GetClients** metódusban is fel kell vennünk ezt a scope-ot az **AllowedScope**-ok közé, hogy a kliensek tudják kérni:

```csharp
public static IEnumerable<Client> GetClients()
{
    return new List<Client>()
    {
        new Client
        {
            ClientName = "CHOAM Projects",
            ClientId = "choamprojectsclient",
            
            ...

            AllowedScopes =
            {
                IdentityServerConstants.StandardScopes.OpenId,
                IdentityServerConstants.StandardScopes.Profile,
                IdentityServerConstants.StandardScopes.Address,
                "choamprojectsapi"
            },
            
            ...
        }
    };
}
```

Az IDP Startup.cs-ben korábban hozzáadtuk a klienseket és az IdentityResource-okat, tegyük meg ezt most az ApiResource-okkal is:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddIdentityServer()
        .AddDeveloperSigningCredential()
        .AddTestUsers(Config.GetUsers())
        .AddInMemoryIdentityResources(Config.GetIdentityResources())
        .AddInMemoryApiResources(Config.GetApiResources())
        .AddInMemoryClients(Config.GetClients());
    
    ...        
}
```

Ha megvagyunk az IDP-vel, térjünk át a kliens Startup.cs állományához. Biztosítsuk, hogy az access_token tartalmazza a choamprojectsapi scope-ot és audience-t:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddAuthentication(options =>
    {
        options.DefaultScheme = "Cookies";
        options.DefaultChallengeScheme = "oidc";
    }).AddCookie("Cookies", (options) =>
    {
        options.AccessDeniedPath = "/Authorization/AccessDenied";
    }).AddOpenIdConnect("oidc", options =>
    {
        options.SignInScheme = "Cookies";
        options.Authority = "https://localhost:44345";
        options.ClientId = "choamprojectsclient";
        options.ResponseType = "code id_token";
        options.Scope.Add("openid");
        options.Scope.Add("profile");
        options.Scope.Add("address");
        options.Scope.Add("choamprojectsapi");
        options.SaveTokens = true;
        options.ClientSecret = "superSecretPassword";
        options.GetClaimsFromUserInfoEndpoint = true;
    });
}
```

Ezután az API-val szükséges egy kicsit foglalkozni. Most szükséges biztosítani, hogy az API a megfelelő audience-szel kéri meg az access_tokent. Ehhez szükségünk lesz az **IdentityServer4.AccessTokenValidation** package-re:

```bash
dotnet add Choam.ERP.API package IdentityServer4.AccessTokenValidation
```

Ezután az API Startup.cs ConfigureServices metódusában regisztráljuk a szükséges service-eket:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddAuthentication(IdentityServerAuthenticationDefaults.AuthenticationScheme)
        .AddIdentityServerAuthentication(options =>
        {
            options.Authority = "https://localhost:44345/";
            options.ApiName = "choamprojectsapi";
        });

    ...
}
```

Az IdentityServerAuthenticationDefaults.AuthenticationScheme egy konstans, amelynek értéke **"Bearer"**. Az AddIdentityServerAuthenticationben az Authority nem más, mint az IDP címe. A middleware innen szerzi a metadatat, így ismert lesz számára a publikus kulcs és az endpointok, mégpedig a *.well-known* dokumentumnak köszönhetően. A middleware emellett validálja az acces_tokent. Az ApiName-ben átadjuk a choamprojectsapi értéket, ezzel bebiztosítva, hogy az API-nk bekerül audience értékként a tokenbe.

Ezután az Authentication middleware-t a request pipeline-hoz is szükséges lesz hozzáadni. Fontos, hogy mindenképpen a UseMvc() elé kerüljön, ugyanis a middleware-nek ellenőriznie kell, hogy a hozzáférés engedélyezve van-e az API-hoz mielőtt elérünk az MVC-ig.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory, ProjectContext projectContext)
{
    ...

    app.UseAuthentication();

    app.UseStaticFiles();

    ...

    app.UseMvc();
}
```

Ha ezzel is készen vagyunk, akkor biztosítani kell, hogy a kliens által meghívott ProjectsController csak megfelelő autorizáció után érhető el:

```csharp
namespace Choam.ERP.API.Controllers
{
    [Route("api/projects")]
    [Authorize]
    public class ProjectsController : Controller
    {
        ...
    }
}
```

Ha most lefuttatjuk mindhárom projektet, akkor láthatjuk az API-oz való engedélykérést:

![Engedélykérés az API-hoz](/assets/images/askpermission.png)

Ha továbblépünk, akkor pedig a **401 Unauthorized** hibaüzenetet kell kapnunk, ugyanis az előzőkben éppen azt akarunk elérni, hogy API szinten blokkoljuk a hozzáférést az unauthorized felhasználók számára. És ez így is lesz:

![Unathorized access](/assets/images/unauthorized.png)

Akkor most hogyan tovább? Hát itt jön képbe az access_token, ugyanis annak a továbbküldésével fogunk tudni hozzáférni az API-hoz ismételten. Ezt a tokent minden egyes kérésben küldeni kell **bearer token**ként. A bearer token annyit jelent, hogy a hozzáférét a token tulajdonosának, vagyis a mi esetünkben a konkrét kérésnek adjuk.

A klienalkalmazásban a **ProjectHttpClient.cs** állomány konstruktorában át van adva a **httpContextAccessor**, ami hozzáférést biztosít az aktuális httpContexthez. A GetClient metódusban meghívjuk a GetTokenAsync (Microsoft.AspNetCore.Authentication namespace) metódust a httpContextre, amelynek paraméterként átadjuk, hogy az access_tokenre (Microsoft.IdentityModel.Protocols.OpenIdConnect namespace) van szükségünk most:

```csharp
public async Task<HttpClient> GetClient()
{
    string accessToken = string.Empty;
    var currentHttpContext = this.httpContextAccessor.HttpContext;

    accessToken = await currentHttpContext.GetTokenAsync(OpenIdConnectParameterNames.AccessToken);

    
     if(!string.IsNullOrWhiteSpace(accessToken))
    {
        this.httpClient.SetBearerToken(accessToken);
    }

    this.httpClient.BaseAddress = new Uri("https://localhost:44333/");
    this.httpClient.DefaultRequestHeaders.Accept.Clear();
    this.httpClient.DefaultRequestHeaders.Accept.Add(
        new MediaTypeWithQualityHeaderValue("application/json"));

    return this.httpClient;
}
```

Ha most lefuttatjuk, akkor azt tapasztaljuk, hogy újra lett hozzáférésünk az API-hoz. Nézzük meg, hogy hogyan is néz ki az access_tokenünk:

![Access_token](/assets/images/access_token.png)

Látható, hogy a kért scope-ok között ott található a choamprojectsapi, csakúgy, mint az audience között.

---

A következő lépés az lesz, hogy minden projekthez csak az férjen hozzá, aki az adott projektnek az ownere. Ezt API szinten kell kezelni, vagyis csak azokat a projekteket kell az API-nak visszaküldenie, amelynek a bejelentkezett user a tulajdonosa. Korábban volt róla szó, hogy a tokenben a sub claimben megtaláljuk a user azonosítóját, így könnyű dolgunk van, csak ki kell választanunk a bejelentkezett user claimjei közül a subot, kiolvasni az értékét és meg is kapjuk a bejelentkezett user id-ját. Az API ProjectControllerében módosítsuk ennek megfelelően a GetProjects metódust:

```csharp
[HttpGet()]
public IActionResult GetProjects()
{
    var ownerId = User.Claims.FirstOrDefault(c => c.Type == "sub").Value;
    // get from repo
    var projectsFromRepo = this.projectRepository.GetProjects(ownerId);

    // map to model
    var projectsToReturn = Mapper.Map<IEnumerable<Model.Project>>(projectsFromRepo);

    // return
    return Ok(projectsToReturn);
}
```

Módosítsuk a repository-t is mindenképpen, hogy a GetProjects függvény mindenképpen várjon egy paramétert, ugyanis nem akarjuk soha visszaadni az összes projektet, mindig csak egy adott ownerhez tartozó projekteket (az interface-ben is módosítsuk a szignatúrát)

**IProjectRepository.cs**

```csharp
IEnumerable<Project> GetProjects(string ownerId);
```

**ProjectRepository.cs**

```csharp
public IEnumerable<Project> GetProjects(string ownerId)
{
    return this.context.Projects
        .Where(p => p.OwnerId == ownerId)
        .OrderBy(p => p.Name).ToList();
}
```

Ha ezzel is megvagyunk és elindítjuk a projekteket, azt fogjuk tapasztalni, hogy minden esetben csak a bejelentkezett felhasználóhoz tartozó projektek kerülnek megjelenítésre. Ez tök jó, de mi van akkor, ha valaki ismeri a pontos URI-t egy adott projekt törléséhez vagy módosításához. Jelenleg egy érvényes token birtokában bárki képes ezeket meghívni.
Itt jönnek képbe az Authorization Policy-k.

## IAuthorizationRequirement és AuthorizationHandler

Minden resource biztonságossá tehető megfelelő policy-k használatával: ezeknek a policy-knek többféle követelménye lehet, amelyek közül mindnek egyszerre kell teljesülnie. Ezeknek a követelményeknek egy jelentős része built-in, de akár sajátokat is létre tudunk hozni. Ez az **IAuthorizationRequirement** interface megvalósításával tudjuk elérni. Minden requirementnek van egy saját handlere, ami az **AuthorizationHandler**-ből származik.

Az API Startup.cs ConfigureServices metódusában hozzáadunk egy új policy-t: MustOwnProject. Ehhez tartozik egy követelmény: a felhasználónak autentikáltnak kell lennie, emellett azt is szeretnénk, hogy a bejelentkezett felhasználó legyen a projekt ownere. Az előbb megtudtuk, hogy hozzá tudunk adni új követelményt és ehhez a követelményhez egy handlert.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddAuthorization(authorizationOptions =>
    {
        authorizationOptions.AddPolicy(
            "MustOwnProject",
            policyBuilder =>
            {
                policyBuilder.RequireAuthenticatedUser();
            });
    });

    services.AddAuthentication(IdentityServerAuthenticationDefaults.AuthenticationScheme)
        .AddIdentityServerAuthentication(options =>
        {
            ...
        });

    ...
}
```

Hozzunk létre egy Authorization mappát, abban pedig egy új classt: **MostOwnProjectRequirement.cs**, ami valósítsa meg az **IAuthorizationRequirement** interface-t (ez a Microsoft.AspNetCore.Authorization névtérben van)

```csharp
using Microsoft.AspNetCore.Authorization;

namespace Choam.ERP.API.Authorization
{
    public class MustOwnProjectRequirement : IAuthorizationRequirement
    {
        public MustOwnProjectRequirement()
        {

        }
    }
}
```

Ezután adjunk hozzá egy handlert is: **MostOwnProjectHandler.cs**, ami az **AuthorizatinHandler**-ből származik (Microsoft.AspNetCore.Authorization névtér) és át kell adnunk a requirement típusát is. Az AuthorizationHandler egy abstract class, implementáljuk a szükséges metódust. Ebben a HandlerRequirementAsync metódusban fogjuk tudni definiálni, hogy a követelménynek megfelelünk-e vagy sem. Amire ennek a megállapításához szükségünk van:

- a felhasználó sub értéke, vagyis az id-ja
- a route-ot is ismernünk kell, legalábbis egy részét biztosan, ami a project id-ja
- repository, hogy megnézzük, a user hozzáférhet-e a képhez

A porject id-ját megtaláljuk a context.Resource objektumban. A Resource castolható **AuthorizationFilterContext**té (Microsoft.AspNetCore.Mvc.Filters névtér). Ha ez a castolás elbukik, akkor nincs meg a requirement, aminek meg kell felelnünk. Ha sikerül, akkor egy csomó más dolog mellett hozzáférünk a **RouteData**hoz. A RouteData.Values dictionary tartalmazza a route adatait: action = action neve, controller = controller neve, id = id. És erre az utóbbira van szükségünk most: próbáljuk meg Guiddá castolni, ha nem sikerül, akkor ismételten nem felelünk meg a követelménynek. Szükségünk van a project owner id-jára is, amit - ahogyan ezt fentebb már láttuk - a context.User.Claims-ből megkapunk. Ezeken felül még a repository-ra is szükségünk van, amit dependency injection használatával konstruktorban tudunk átadni. Ezután megvizsgáljuk, hogy a repository IsProjectOwner metódusa mit ad vissza: ha false, akkor ismételten elbukott a requirement.

Ha viszont mindegyik checkpoint true volt, akkor a requirement teljesül:

```csharp
using Choam.ERP.API.Services;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc.Filters;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace Choam.ERP.API.Authorization
{
    public class MustOwnProjectHandler : AuthorizationHandler<MustOwnProjectRequirement>
    {
        private readonly IProjectRepository projectRepository;

        public MustOwnProjectHandler(IProjectRepository projectRepository)
        {
            this.projectRepository = projectRepository;
        }

        protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MustOwnProjectRequirement requirement)
        {
            var filterContext = context.Resource as AuthorizationFilterContext;
            if (filterContext == null)
            {
                context.Fail();
                return Task.CompletedTask;
            }

            var projectId = filterContext.RouteData.Values["id"].ToString();

            if (!Guid.TryParse(projectId, out Guid projectIdAsGuid))
            {
                context.Fail();
                return Task.CompletedTask;
            }

            var projectOwnerId = context.User.Claims.FirstOrDefault(c => c.Type == "sub").Value;

            if (!this.projectRepository.IsProjectOwner(projectIdAsGuid, projectOwnerId))
            {
                context.Fail();
                return Task.CompletedTask;
            }

            context.Succeed(requirement);
            return Task.CompletedTask;
        }
    }
}
```

Ha megvagyunk, akkor a Startup.cs-ben (még mindig az API-ban vagyunk) adjuk hozzá az új követelményt, és regisztráljuk a handlert a containerben. Mivel a repository-t is megkapja, ezért ugyanúgy AddScoped-ot használunk, mint a repo esetén.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddAuthorization(authorizationOptions =>
    {
        authorizationOptions.AddPolicy(
            "MustOwnProject",
            policyBuilder =>
            {
                policyBuilder.RequireAuthenticatedUser();
                policyBuilder.AddRequirements(new MustOwnProjectRequirement());
            });
    });

    services.AddScoped<IAuthorizationHandler, MustOwnProjectHandler>();

   ...
}
```

Ezután nem maradt más hátra, mint megfelelő Authorize attribútumok megadása a ProjectsControllerben:

```csharp
public class ProjectsController : Controller
{
    ...

    [HttpGet("{id}", Name = "GetProject")]
    [Authorize("MustOwnProject")]
    public IActionResult GetProject(Guid id)
    {
        ...
    }

    [HttpPost()]
    [Authorize("MustOwnProject")]
    public IActionResult CreateProject([FromBody] ProjectForCreation projectForCreation)
    {
        ...
    }

    [HttpDelete("{id}")]
    [Authorize("MustOwnProject")]
    public IActionResult DeleteProject(Guid id)
    {
       ...
    }
}
```

Ha elindítjuk a projekteket és bejelentkezünk, akkor láthatjuk, hogy Sionával csak Siona projektjeit, míg Duncannel csak Duncan projektjeit látjuk. Ha URI alapján Duncannel megpróbáljuk módosítani Siona valamelyik projektjét, akkor nem járunk sikerrel.


