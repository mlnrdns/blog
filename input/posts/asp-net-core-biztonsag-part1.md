Title: ASP.NET Core 2 biztonság OAuth2 és OpenId használatával 1. rész webapp
Date: 29/01/2019
Published: 2019-01-29 16:48:00
Author: mlnrdns
Tags: [oauth2, openid, security, identityserver4]
---

## OAuth2

A modern *Service Oriented Application*-ök megjelenésével egyre fontosabbá vált egy biztonságos protokoll létrehozása. A RESTful API-k megjelenésével egyre inkább olyan alkalmazásokat készítünk, amik különböző API-kkal kommunikálnak, amik közül akár több is nem a mi ellenőrzésünk alatt áll. Egy kliensalkalmazás a mi API-nk mellett kommunikálhat más API-kkal is, például az Online számla API-val, egymással párhuzamosan. Maguk a kliensalkalmazások is megváltoztak, már nem annyira csak szerver oldali webappokat vagy vastag klienseket készítünk. amik a vállalatok biztonságos környezetében léteznek, hanem sokkal inkább kliens oldali webappokat Angularral, amelyek közvetlenül csatlakoznak az API-nkhoz, mobilalkalmazásokat, amelyek szintén közvetlenül az API-nkhoz kapcsolódnak, és végül API-kat, amelyek más API-kkal kommunikálnak.

A fentiek miatt már nem nyújtanak védelmet a vállalat falai, ugyanis a kliens oldali alkalmazások gyakran publikus API-kat hívnak, emiatt több már nem lehetséges a form alapú autentikáció biztonsági okokból, nem küldhetük minden egyes kérésnél felhasználót és jelszót, ugyanis nagyon könnyen ellophatók.

Ez vezetett a token alapú biztonsághoz: a tokenek tulajdonképpen hozzájárulást fejeznek ki a felhasználó (**user**) részéről, amivel engedélyt adnak a kliensalkalmazásnak (**client**), hogy használjon egy adott API-t. Ez a token akár a kliens, de akár az API szintjén is használható, hogy engedélyezze (**authorization**) a hozzáférést.

Korábban magunk fejlesztettünk login screeneket, felhasználó- és jelszókezelést, jelszóhelyreállítást, stb. Ma már erre nincs szükség: ne az alkalmazás ellenőrizze, hogy egy felhasználó valóban az-e, akinek mondja magát; adjuk át ezt a feladatot egy **identity provider**nek (**IDP**). A felhasználó hitelesítéséért (**authentication**) és a személyazonosság biztosításáért (**proof of identity**) legyen az IDP felelős.

A fent felsoroltakból láthatjuk, hogy egy user account több különböző alkalmazással is kapcsolatban állhat. így az identity- és hozzáférésmenedzsment (**Identity and access management**: **IAM**) feladatok közösek az alkalmazások vonatkozásában. Nagyon fontos ezeknek az accountoknak a biztonsága, évről évre újabb és újabb algoritmusokat törnek fel, így sokkal célszerűbb egy helyen összefogni az összes alkalmazásra vonatkozó IAM-ot. Erre a problémára jelent megoldást az **OAuth2** és az **OpenId**, az előbbi az autorizációért, az utóbbi az autentikációért felelős.

Az [**OAuth2**](https://oauth.net/2/) egy nyílt protokoll, ami biztonságos autorizációt teszt lehetővé web, mobil és desktop alkalmazásokból, tehát az OAuth2 feladata a kliensalkalmazások számára engedélyezést (authorization) adni egy-egy API használatához. Az OAuth2 definiál egy **access token**t, amit igényelni tud egy kliens, hogy hozzáférjen egy API-hoz. Emellett az OAuth2 azt is definiálja, hogy egy kliensalkalmazás hogyan autorizálható biztonságosan. Az IDP-ket másként **authorization service**-nek vagy **security token service**-nek nevezzük. Ilyen authorization service például az *Azure AD* vagy az *IdentityServer*.

Fontos: ezek az access tokenen csak arra szolgálnak, hogy hozzáférést biztosítsanak erőforrásokhoz, API-khoz, az nem a feladatuk, hogy hitelesítsenek egy felhasználót. Itt jön be a képbe az **OpenId Connect**.

## OpenId Connect

Az [**OpenId Connect**](https://openid.net/connect/) (**OIDC**) tulajdonképpen nem más, mint egy hitelesítési réteg (**identity layer**) az OAuth2 fölött, egyfajta kiterjesztése annak. Egy kliensalkalmazás igényelhet egy **identity token**t az access token mellett, amennyiben erre szüksége van. Ez az identity token arra szolgál, hogy bejelentkezzünk az alkalmazásba, míg az access token azt fogja közben biztosítani, hogy az alkalmazás hozzáférjen egy API-hoz. Az OpenId Connect definiál egy *UserInfo* endpointot, ahonnan a kliens információkat kaphat a bejelentkezett userről.

Egy kliensalkalmazásnak szüksége van egy felhasználó adataira. Ez a kliens a **relying party**, vagyis a függő fél, ugyanis az IDP-re támaszkodik, hogy beszerezze biztonságosan a megfelelő felhasználó adatait. A kliens létrehoz egy hitelesítési kérést (**authentication request**), ami átirányítja a felhasználót az IDP-re. A felhasználó itt azonosítja magát például egy felhasználói név-jelszó párossal. Amennyiben ez sikeres, az IDP létrehoz egy identity tokent és aláírja. Ez a token ellenőrizhető formában tartalmazza a felhasználó adatait. Ezután az IDP továbbítja az identity tokent a kliensnek, ami validálja azt. Sikeres validáció után már egy ellenőrzött, biztonságos identity van a kliens birtokában.

A különböző típusú alkalmazások különböző autentikációs megvalósítást igényelnek. Ennek az igénynek tesz eleget az OIDC azzal, hogy definiál alkalmazástípusokat (**client type**) és folyamatokat (**flow**).

### Alkalmazástípusok

Két alkalmazástípust különböztetünk meg: **public client** és **confidential client**. Fontos, hogy itt a kliensalkalmazásokról van szó, semmiképpen se keverjük a userekkel! 
A **confidential client** képes titokban tartani a **credential**-jeit (ezek a **clientid** és **clientsecret**). Tipikusan ilyen alkalmazások például az ASP.NET Core MVC-k, amik egy webszerveren futnak. Mivel ezek a szerveren futnak, ezért viszonylag biztonságos módon tárolhatják ott a credential-jeiket, a userek ehhez nem tudnak hozzáférni.
A **public client**ek másrészről nem képesek titokban tartani a credential-jeiket, ugyanis ezek a böngészőben vagy okostelefonokon futnak. Ilyen például egy Angular alkalmazás vagy éppen a natív mobilappok.

### Folyamatok és endpointok

A **flow**-k határozzák meg azt, hogy a tokenek hogyan kerülnek visszaküldésre a kliensnek. Az alkalmazás típusa, a követelmények nagyban meghatározzák, hogy melyik folyamatot kell használnunk. Ezek a folyamatok különböző **endpoint**-okkal kommunikálnak:

- **Authorization endpoint** (IDP szint): a kliens átiránytásra kerül erre az endpointra, hogy beszerezze az authorization és/vagy authentication tokent. OIDC esetén **TLS** használata kötelező! Autorizálás után az IDP visszairányít a kliensre.
- **Redirection endpoint** (kliens szint): az IDP ezen keresztül küldi vissza a tokent a kliensnek
- **Token endpoint** (IDP szint): a kliens közvetlenül redirection nélkül igényelhet tokent ezen keresztül az IDP-től. Tipikusan HTTP POST kérésben történik ez.

OpenId Connection folyamatok:

- **Authorization Code flow**: visszaad egy **authorization code**-ot az **authorization endpoint**ról és **token**t a **token endpoint**ról. Az authorization code egy rövid ideig élő hitelesítő adat (credential), ami arra szolgál, hogy ellenőrizze, hogy az IDP szinten bejelentkezett user ugyanaz, aki elindította a folyamatot a webalkalmazás szintjén. Ez a folyamat hosszabb idejű hozzáférést biztosít confidential clientek esetén.
- **Implicit flow**: visszaadja az összes **token**t az **authorization endpoint**ról, nincs authorization code. Public clientek esetén használatos. Nincs client authentication, mivel a publikus kliensek amúgy sem tudják titokban tartani a client secretjeiket. Nincs hosszabb idejű hozzáférés sem. Confidential clienteknél is használható egyébként.
- **Hybrid flow**: visszaad **token**t az **authorization endpoint**ról és a **token endpoint**ról is, tulajdonképpen az előző kettőnek egy keveréke. Alkalmas confidential clientek esetén. Hosszabb idejű hozzáférést is biztosít.

---

Egy ASP.NET Core MVC alkalmazás egy confidential client, aminél szükségünk van hosszabb idejű hozzáférésre, így az implicit flow kiesik a lehetőségek közül. Mára a hybrid flow a legjobb megoldás: lehetővé teszi, hogy először identity tokent kérjünk az authorization endpointról, amit így validálni tudunk, mielőtt megkérnénk az access tokent. Az OpenId Connect ezután hozzálinkeli az identity tokent az access tokenhez. Emiatt célszerű az OpenId Connectet választani a sima OAuth2 helyett.

## IdentityServer4

Az [**IdentityServer4**](http://docs.identityserver.io/en/latest/) egy OAuth2 és OpenId Connect framework ASP.NET Core 2-höz.

Készítettem egy egyszerű projektet, ami letölthető [Github](https://github.com/molnardenes/identityserver4-sample-app)ról. Clone-ozás után nyissuk meg Visual Studioban (vagy a választott IDE-ben). Három projektet tartalmaz jelenleg:

- API: ez tartalmazza a hívható API-t
- Client: ez a projekt a kliensalkalmazás
- Model: ez pedig a közös modell

Hozzunk létre egy új ASP.NET Core Web Application Empty projektet és nevezzük el Choam.IDP-nek. Ha létrejött a projekt, adjuk is hozzá rögtön az IdentityServer4 NuGetet:

```bash
dotnet new web --name Choam.IDP
dotnet add Choam.IDP package IdentityServer4
```

Mielőtt belemennénk az IdentityServer4 konfigurálásába, biztosra kell mennünk, hogy titkosított kapcsolaton keresztül lehessen elérni. Nyissuk meg a launchSettings.json fájlt és állítsuk be, hogy a **TLS** engedélyezve legyen. Ezt úgy tudjuk megtenni, hogy az iisSettings részben beállítjuk az sslPortot (alapértelmezetten 0) és az url-t. Az url https-sel kell, hogy kezdődjön és a végén ugyanazt a portot használjuk, mint ami az sslPortban meg van adva:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "https://localhost:44345/",
      "sslPort": 44345
    }
  }  
}
```

Ha most elindítjuk, akkor látni fogjuk, hogy a kapcsolat biztonságos:

![secured connection](/assets/images/secureconnection.png)

Ha első alkalommal használjuk a HTTPS-t, akkor előfordulhat, hogy a certificate nem a megfelelő store-ban van, így hibaüzenetet kapunk az alkalmazás indításakor. Ez könnyen megoldható:

1. Indítsuk el a Microsoft Management Console-t
2. File -> Add/Remove snap-in
3. A bal oldali listából válasszuk ki a Certificates-t és adjuk hozzá a jobb oldali listához. Ha választhatunk, akkor válasszuk a User accountot
4. OK
5. Ekkor megjelenik a Console Root alatt bal oldalon a Certificates - Current user lehetőség
6. Nyissuk le és keressük ki a Personal Certificates-ben a localhostot
7. Jobb klikk -> All tasks -> Export
8. Fogadjunk el minden default beállítást és exportáljuk az IDP projektünk mappájába
9. Ezután bal oldalon válasszuk a Trusted Root Certification Authorities Certificates-eit
10. Jobb klikk a Certificates-en -> All tasks -> Import
11. Tallózzuk be a kiexportált certificate-et


Most pedig szükségünk van az IdentityServer4 middleware konfigurálására, amit a Startup class **ConfigureServices** metódusában tudunk megtenni:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentityServer()
        .AddDeveloperSigningCredential();
}
```

A services objektumra meghívjuk az AddIdentityServert, hogy beregisztráljuk az identity szervert az ASP.NET Core beépített dependency injection containerébe. Meghívjuk az AddDeveloperSigningCredential() függvényt is, hogy ezzel alá tudjuk írni a tokeneket. Fontos, hogy ez csak fejlesztés közben maradhat, production környezetben szükséges lesz egy valódi certificate-re lecserélni ezt.

Ezután hozzunk létre egy új classt: **Config.cs** és tegyük **static**-ká. Ez az osztály fogja az in-memory konfigurációt tartalmazni. Adjunk hozzá néhány tesztfelhasználót (ehhez az IS4-nek van egy beépített TestUser osztálya, ami az *IdentityServer4.Test* namespace-ben található), valamint claim-eket hozzájuk (ezek a *System.Security.Claims* namespace-ben találhatók, hivatkozzuk be ezt is). Ezek a **claim**ek nem mások, mint információk a felhasználóról:

```csharp
using IdentityServer4.Models;
using IdentityServer4.Test;
using System.Collections.Generic;
using System.Security.Claims;

namespace Choam.IDP
{
    public static class Config
    {
        public static List<TestUser> GetUsers()
        {
            return new List<TestUser>
            {
                new TestUser
                {
                    SubjectId = "14527f77-4682-48ec-99b6-037464a0a8dc",
                    Username = "Siona",
                    Password = "password",

                    Claims = new List<Claim>
                    {
                        new Claim("given_name", "Siona"),
                        new Claim("family_name", "Atreides"),                        
                    }
                },
                new TestUser
                {
                    SubjectId = "7d8ba357-cb82-4252-8f18-4d8b0591144d",
                    Username = "Duncan",
                    Password = "password",

                    Claims = new List<Claim>
                    {
                        new Claim("given_name", "Duncan"),
                        new Claim("family_name", "Idaho"),                        
                    }
                }
            };
        }
    }
}
```

Most, hogy már megvannak a tesztfelhasználók és claimeket is kaptak, vissza kell tudnunk ezeket adni egy **token**ben. Ez pedig úgy lehetséges, hogy **scope**-okat hozunk létre, ugyanis ezek a claimek szorosan kapcsolódnak a scope-okhoz. Hozzunk létre egy static metódust, ami egy **IdentityResource** IEnumerable-t ad vissza. Az IdentityResource az IdentityServer4.Models namespace-ben található, hivatkozzuk be ezt is. Az IdentityResource mappelve van a scope-ra, ami hozzáférést ad felhasználóhoz kapcsolódó adatokhoz.

```csharp
public static IEnumerable<IdentityResource> GetIdentityResources()
{
    return new List<IdentityResource>
    {
        new IdentityResources.OpenId(),
        new IdentityResources.Profile(),                
    };
}
```

Milyen scope-okat támogasson az alkalmazásunk? Tekintve, hogy OpenId Connectet használunk, így azt biztosan szeretnénk. Ez bebiztosítja, hogy az azonosító, esetünkben a SubjectId visszaadásra kerül. Azaz, ha egy kliensalkalmazás kéri az **OpenId scope**-ot, a **user identifier claim** visszaadásra kerül. Emellett támogatjuk a **Profile scope**-ot, ami a profilhoz kapcsolódó claimekre van mappelve, így visszaadásra kerülnek azok, például given_name és family_name.

Végül szükséges hozzáadni azokat a kliensalkalmazásokat, amelyeket használni szeretnénk. Így adjunk hozzá egy harmadik static metódust, ami visszaadja a Client-eket. Egyelőre legyen egy üres lista, a későbbiekben ezt fel fogjuk tölteni.

```csharp
public static IEnumerable<Client> GetClients()
{
    return new List<Client>();    
}
```

Ha ezekket megvagyunk, menjünk vissza a ConfigureServices metódsuhoz és adjuk hozzá a usereket, claimeket és clienteket:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentityServer()
        .AddDeveloperSigningCredential()
        .AddTestUsers(Config.GetUsers())        
        .AddInMemoryIdentityResources(Config.GetIdentityResources())        
        .AddInMemoryClients(Config.GetClients());
}
```

Ha ezekkel megvagyunk, megyünk lejjebb a **Configure** metódushoz, ahol hozzáadhatjuk az identity szervert a **request pipeline**-hoz:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseIdentityServer();
}
```

Ha ezzel is elkészültünk, próbáljuk is ki.

Ha mindent jól csináltunk, akkor futnia kell az identity szervernek, amit ki is próbálhatunk, ha a böngészőben elnavigálunk a **discovery endpoint**ra: *`http://localhost:44345/.well-known/openid-configuration`*. A discovery endpoint minden esetben a **.well-known/openid-configuration** URI-n érhető el. Ha jók vagyunk, akkor a következőt látjuk a böngészőben:

```json
{
  "issuer": "http://localhost:44345",
  "jwks_uri": "http://localhost:44345/.well-known/openid-configuration/jwks",
  "authorization_endpoint": "http://localhost:44345/connect/authorize",
  "token_endpoint": "http://localhost:44345/connect/token",
  "userinfo_endpoint": "http://localhost:44345/connect/userinfo",
  "end_session_endpoint": "http://localhost:44345/connect/endsession",
  "check_session_iframe": "http://localhost:44345/connect/checksession",
  "revocation_endpoint": "http://localhost:44345/connect/revocation",
  "introspection_endpoint": "http://localhost:44345/connect/introspect",
  "device_authorization_endpoint": "http://localhost:44345/connect/deviceauthorization",
  "frontchannel_logout_supported": true,
  "frontchannel_logout_session_supported": true,
  "backchannel_logout_supported": true,
  "backchannel_logout_session_supported": true,
  "scopes_supported": [
    "openid",
    "profile",
    "offline_access"
  ],
  "claims_supported": [
    "sub",
    "name",
    "family_name",
    "given_name",
    "middle_name",
    "nickname",
    "preferred_username",
    "profile",
    "picture",
    "website",
    "gender",
    "birthdate",
    "zoneinfo",
    "locale",
    "updated_at"
  ],
  "grant_types_supported": [
    "authorization_code",
    "client_credentials",
    "refresh_token",
    "implicit",
    "password",
    "urn:ietf:params:oauth:grant-type:device_code"
  ],
  "response_types_supported": [
    "code",
    "token",
    "id_token",
    "id_token token",
    "code id_token",
    "code token",
    "code id_token token"
  ],
  "response_modes_supported": [
    "form_post",
    "query",
    "fragment"
  ],
  "token_endpoint_auth_methods_supported": [
    "client_secret_basic",
    "client_secret_post"
  ],
  "subject_types_supported": [
    "public"
  ],
  "id_token_signing_alg_values_supported": [
    "RS256"
  ],
  "code_challenge_methods_supported": [
    "plain",
    "S256"
  ]
}
```

Ha végigfutjuk, látjuk, hogy elérhetők az endpointok, claimek, scope-ok és még sok egyéb infó, amikről a későbbiekben fogunk még beszélni.

Alapértelmezetten nem létezik UI az IdentityServer4-hez, de lehetőségünk van sajátot készíteni, illetve egy starter UI rendelkezésre áll egy [Github repoban](https://github.com/IdentityServer/IdentityServer4.Quickstart.UI). Töltsük is ezt le a projektünkbe. A Powershell jó szolgálatot tesz ebben:

```bash
iex ((New-Object System.Net.WebClient).DownloadString('https://raw.githubusercontent.com/IdentityServer/IdentityServer4.Quickstart.UI/master/getmaster.ps1'))
```

Ha letöltődött, és visszalépünk az IDE-nkbe, akkor láthatjuk az új mappákat. Tulajdonképpen egy MVC projekt mappái kerültek bele a projektünkbe. Ez a UI bármikor testreszabható teljesen egyedire. Azonban ahhoz, hogy használni tudjuk, szükség lesz még a **Startup.cs** két függvényének módosítására:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.AddIdentityServer()
        .AddDeveloperSigningCredential()
        .AddTestUsers(Config.GetUsers())        
        .AddInMemoryIdentityResources(Config.GetIdentityResources())                
        .AddInMemoryClients(Config.GetClients());
}


public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseIdentityServer();
    app.UseStaticFiles();
    app.UseMvcWithDefaultRoute();
}
```

Mentsünk el minden változtatást és indítsuk el a projektet. Ha a böngészőben a localhostra navigálunk, akkor látni fogjuk, hogy elindult az identity szerverünk:

![identity szerver starter ui](/assets/images/identity_starterui.png)

## Hybrid flow

Most, hogy már készen áll az identity szerverünk, felkonfigurálhatjuk, hogy **hybrid flow**-val be tudjunk jelentkezni a kliensalkalmazásunkba. Lépjünk vissza a Config.cs GetClients metódusához és egészítsük ki a klienssel (hivatkozzuk az IdentityServer4 namespace-t az IdentityServerConstant eléréséhez):

```csharp
public static IEnumerable<Client> GetClients()
{
    return new List<Client>()
    {
        new Client
        {
            ClientName = "CHOAM Projects",
            ClientId = "choamprojectsclient",
            AllowedGrantTypes = GrantTypes.Hybrid,            
            RedirectUris = new List<string>()
            {
                "https://localhost:44365/signin-oidc"
            },            
            AllowedScopes =
            {
                IdentityServerConstants.StandardScopes.OpenId,                
            },
            ClientSecrets =
            {
                new Secret("superSecretPassword".Sha256())
            }
        }
    };
}
```

A **hybrid flow** redirection alapú, vagyis az authorization code és a token a böngészőnek az URI-n keresztül kerül átadásra **redirection**ön keresztül. A **RedirectUris**-ban azt a URI-t kell megadnunk, ahol a kliensalkalmazás tokeneket kaphat. Szintén itt tudjuk beállítani, hogy mely scope-okat engedélyezzük. Egyelőre legyen ez az OpenId scope, ami biztosítja, hogy a felhasználó identity-je visszaadásra kerüljön.

Következő lépésként a klienst kell felkészíteni arra, hogy képes legyen kezelni az OpenId Connect folyamatot és tudja tárolni a felhasználó identity-jét. Nyissuk meg a kliensalkalmazás **Startup.cs**-jét:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    
    services.AddMvc();        
    services.AddSingleton<IHttpContextAccessor, HttpContextAccessor>();    
    services.AddScoped<IProjectHttpClient, ProjectHttpClient>();

    services.AddAuthentication(options =>
{
    options.DefaultScheme = "Cookies";
    options.DefaultChallengeScheme = "oidc";
}).AddCookie("Cookies",
  (options) =>
  {
      options.AccessDeniedPath = "/Authorization/AccessDenied";
  })
  .AddOpenIdConnect("oidc", options =>
  {
      options.SignInScheme = "Cookies";
      options.Authority = "https://localhost:44345";
      options.ClientId = "choamprojectsclient";
      options.ResponseType = "code id_token";
      options.Scope.Add("openid");
      options.Scope.Add("profile");      
      options.SaveTokens = true;
      options.ClientSecret = "superSecretPassword";      
  });
}
```

Meghívjuk az **AddAuthentication**t, hogy bekonfiguráljuk az authentication middleware-t. Az optionsben a default scheme-t Cookies-ra állítjuk, majd meghívjuk az **AddCookie**-t, aki konfigurálja a cookie handlert, ami lehetővé teszi a kliensnek a cookie alapú autentikációt: amint egy identity token validálásra kerül és claim token lesz belőle, egy encryptált cookie-ban kerül eltárolásra, ami a későbbi requestek során felhasználható lesz.

Következő lépésben az OpenId Connectet is konfigurálni kell, amit az **AddOpenIdConnect** meghívásával és az **oidc handler** beállításával tudunk megtenni. Végeredményben ez a handler fogja kezelni az authorization, token és egyéb requesteket és intézni fogja a token validálást.

Az **AddOpenIdConnectet** az **oidc scheme**-re regisztráljuk, ami azt biztosítja, hogy amikor autentikálást igényel valahol a kliens, akkor az OpenId Connect lesz az alapértelmezett választás, ugyanis fentebb ezt állítottuk be a DefaultChallengeScheme-nek. A **SignInScheme**-t állítsuk Cookies-ra, ahogya fentebb is ez lett az alapértelmezett, így biztosítjuk, hogy a sikeres autentikáció eredménye el lesz tárolva egy encryptált cookie-ban. Az **Authority**-t az IDP-nkre kell irányítani, ez lesz az OIDC flow IDP részéért felelős. A middleware innen fogja kiolvasni azt a metaadatot a discovery endpointról, ezáltal ismertté válik számára, hogy hol találja a többi endpointot és információt. A **ClientId**nak ugyanazt az értéket adjuk meg, amit az IDP-ben regisztráltunk korábban. A **ResponseType**-ban meg kell adni, hogy milyen **grant**ot használuk, a hybrid flow esetén az egyik ilyen a *code id_token*. Ezután a **scope**-okat tudjuk megadni, az openid scope szükséges, a profile pedig a biztosítja, hogy a profilinformációkat is visszakapjuk. A **SaveTokens** true-ra állításával elérjük, hogy a middleware elmentse és később felhasználja a tokeneket. A **ClientSecret**nek ugyanazt az értéket kell kapnia, mint amit az IDP szinten beállítottunk.

Ahhoz, hogy az autentikációs middleware-t használni tudjuk és csak a nem autentikált felhasználók számára blokkoljuk a hozzáférést, adjuk hozzá a **UseAuthentication**t a **Configure** metódusban, és mindenképpen a UseMvc elé (ezzel biztosíthatjuk, hogy csak az autentikált userek férnek majd hozzá):

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Shared/Error");
    }

    app.UseAuthentication();

    app.UseStaticFiles();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Project}/{action=Index}/{id?}");
    });
}
```

Utolsó lépésként már csak azt kell megtennünk, hogy biztosítsukaz alkalmazás egyes területeinek, hogy csak autentikált felhasználók férjenek hozzá. A kliensalkalmazás **ProjectController**ét controller szinten módosítsuk ennek megfelelően. Ezt az **Authorize** attribútum megadásávan tudjuk elérni. Ez a Microsoft.AspNetCore.Authorization namespace-ben található meg.

```csharp
[Authorize]
public class ProjectController : Controller
{
    ...
}
```

Ezután indítsuk el mindhárom projektet, az Choam.IDP-t, a Choam.ERP.API-t és a Choam.ERP.Client-et is, és ha a Client címére navigálunk, akkor egy bejelentkezési képernyőnek kellene fogadni minket, ha mindent jól csináltunk:

![login screen](/assets/images/is4login.png)

Itt jelentkezzünk be Siona vagy Duncan adataival. Követező lépésben az engedélyünket kéri az identity szerver, hogy az adatainkat kiadhassa a kliensnek:

![give permission](/assets/images/permission1.png)

Ha bejelentkeztünk és megnézzük a Debug outputot, akkor látni fogjuk, hogy a middleware kért és kapott egy validity tokent az authorization endpointról, majd meghívta a token endpointot, ahonnan egy új identity tokent kapott, validálta és egy claims identity-t hozott létre belőle:

```bash
IdentityServer4.Validation.TokenRequestValidator:Debug: Start token request validation
IdentityServer4.Validation.TokenRequestValidator:Debug: Start validation of authorization code token request
IdentityServer4.Validation.TokenRequestValidator:Debug: Validation of authorization code token request success
IdentityServer4.Validation.TokenRequestValidator:Information: Token request validation success
{
    
  "ClientId": "choamprojectsclient",
  "ClientName": "CHOAM Projects",
  "GrantType": "authorization_code",
  "AuthorizationCode": "8ebd12187289229fba6f2e5bc0314e51187b91683cb27f2f7f880559b4e24f32",
  "Raw": {
    "client_id": "choamprojectsclient",
    "client_secret": "***REDACTED***",
    "code": "8ebd12187289229fba6f2e5bc0314e51187b91683cb27f2f7f880559b4e24f32",
    "grant_type": "authorization_code",
    "redirect_uri": "https://localhost:44365/signin-oidc"
  }
}
```

Most már be tudunk jelentkezni az alkalmazásba az IDP-vel, úgyhogy ideje, hogy ki is tudjunk onnan jelentkezni. Első lépésben rakjunk fel egy Logout buttont a navbarra. Ez csak akkor látszódjon, ha be vagyunk jelentkezve. Módosítsuk ennek megfelelő a Choam.ERP.Client projektben a _Layout.cshtml fájl vonatkozó részét. A Logout actiont a ProjectControllerben fogjuk implementálni:

```csharp
<div class="navbar-collapse collapse d-sm-inline-flex flex-sm-row-reverse">
    <ul class="navbar-nav flex-grow-1">
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-controller="Project" asp-action="Index">Home</a>
        </li>
        <li class="nav-item">
            @if (User.Identity.IsAuthenticated)
            {
                <a class="nav-link text-dark" asp-area="" asp-controller="Project" asp-action="Logout">Logout</a>
            }
        </li>
    </ul>
</div>
```

Az alkalmazásból való kijelentkezéshez szükséges az authentication ticketet tartalmazó cookie törlése. Ezt a HttpContext-re meghívott SignOutAsync metódussal tudjuk megtenni, aminek a paraméterének meg kell egyeznie azzal scheme névvel, amit beállítottunk a cookie authentication middleware-ben fentebb. Fontos, hogy ezzel még csak a kliensből jelentkezünk ki, az IDP-ben továbbra is bejelentkezve marad a felhasználó, úgyhogy egyidejűleg onnan is szükséges kijelentkeztetni. Ezt szintén a scheme megadásával tudjuk megtenni, ami a jelenleg a mi esetünkben az oidc lesz. Ez átirányt az IDP end session endpointjára, ahol az IDP törölni tudja a saját cookie-jait.

A ProjectController.cs-hez tehát adjuk hozzá a következő metódust:

```csharp
public async Task Logout()
{
    await HttpContext.SignOutAsync("Cookies");
    await HttpContext.SignOutAsync("oidc");
}
```

Még egy dolog szükséges, hogy véglegesnek tekinthessük egyelőre a logout funkciónkat is. Nem adtuk meg az IDP-nek, hogy hová irányítson minket vissza kijelentkezés után. Pótoljuk ezt most az IDP **Config.cs** fájljában a **PostLogoutRedirectUris** megadásával:

```csharp
public static IEnumerable<Client> GetClients()
{
    return new List<Client>()
    {
        new Client
        {
            ...

            RedirectUris = new List<string>()
            {
                "https://localhost:44365/signin-oidc"
            },
            PostLogoutRedirectUris = new List<string>()
            {
                "https://localhost:44365/signout-callback-oidc"
            },
            
            ...
        }
    };
}
```

Az IdentityServer4 Quickstart UI alapértelmezetten nem irányít automatikusan vissza, csak egy return linkre kattinva. Ezt könnyen tudjuk módosítani az IDP/Quickstart/Account/AccountOptions.cs fájlban az **AutomaticRedirectAfterSignOut** **true**-ra állításával. Ha ezzel is megvagyunk, futtassuk mindhárom projektek és próbáljuk ki a teljeskörű be- és kijelentkezési folyamatot.

Ha megnéznénk most a claim-eket, láthatnánk, hogy nem került visszaadásra a given_name és a family_name. Ezeket az IDP-ről a **UserInfo endpoint**on keresztül tudjuk megkapni. Ezt az endpointot a kliensek tudják meghívni, hogy bővebb információt kérjenek le a userről. Hogy ezt meg tudja tenni, olyan **access token**ekkel kell rendelkeznie, amelyek tartalmazzák azokat a scope-okat, amelyek a megfelelő claimekhez tartoznak.

A hybrid flow-ban visszakapunk egy identity tokent a token endpointról, ugyanakkor egy access tokent is kapunk mellette. Ezután a middleware egy újabb kérést küldd, ezúttal a UserInfo endpointra, és csatolja az access tokent. Az IDP-n validálódik az access token, és a UserInfo endpoint visszaadja az access tokenben található scope-ok claimjeit.

Hogy ezeket visszakapjuk, egy sort hozzá kell adnunk a kódunkhoz. A kliens Startup.cs ConfigureServices függvényébe illeszük be a *GetClaimsFromUserInfoEndpoint = true*-t. Emellett van, hogy a claims identity-k más névvel kerülnek visszaadásra mapping miatt, mint amit az identity tokenben látunk (például az identity token sub eleme .../nameidentifier néven szerepel a claims identity-ben). Hogy ezt elkerüljük, a Startup függvényben töröljük a mappingokat. A JwtSecurityTokenHandler a System.IdentityModel.Tokens.Jwt namespace-ben van.

```csharp
public Startup(IConfiguration configuration)
{
    Configuration = configuration;
    JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Clear();
}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddSingleton<IHttpContextAccessor, HttpContextAccessor>();
    services.AddScoped<IProjectHttpClient, ProjectHttpClient>();

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
        options.Authority = "https://localhost:44385";
        options.ClientId = "choamprojectsclient";
        options.ResponseType = "code id_token";
        options.Scope.Add("openid");
        options.Scope.Add("profile");
        options.SaveTokens = true;
        options.ClientSecret = "superSecretPassword";
        options.GetClaimsFromUserInfoEndpoint = true;
    });
}
```

Vizsgáljuk meg most, hogyan is épül fel egy identity token. Írassuk ki a Debug outputra. Illesszük be ezt a két sor kódot a Client ProjectController.cs Index metódusának elejére, és rakjunk egy breakpointot utána:

```csharp
var identityToken = await HttpContext
                .GetTokenAsync(OpenIdConnectParameterNames.IdToken);           
Debug.WriteLine($"Identity token: {identityToken}");
```

Ha rendben lefutott, akkor a Debug outputon a következőt látjuk:

```bash
Identity token: eyJhbGciOiJSUzI1NiIsImtpZCI6ImUyODc0ZTNiNjhlYWM1NTFlZmE2Zjg2ZmU5Yzc0OTkxIiwidHlwIjoiSldUIn0.eyJuYmYiOjE1NDg3NjAxMjksImV4cCI6MTU0ODc2MDQyOSwiaXNzIjoiaHR0cHM6Ly9sb2NhbGhvc3Q6NDQzODUiLCJhdWQiOiJjaG9hbXByb2plY3RzY2xpZW50Iiwibm9uY2UiOiI2MzY4NDM1NjkxMzY3NDkxOTIuTkdGbVpXUXlPR1F0TW1VNFpTMDBaamsyTFdFMFpUSXRPRGd3TURjd1pqRm1ZV1poTjJSaFpHSm1Zekl0T1RoaU5DMDBaRFkzTFdJeFpHRXRaVEEzWTJJeE1UQXhOVFUwIiwiaWF0IjoxNTQ4NzYwMTI5LCJhdF9oYXNoIjoiandqby1PdDBjM3RUQ3ZMdDVPQ1pOdyIsInNpZCI6ImFmYWE3YjQwNTQxM2RlMzE1NDIxMzMwZTc5ZjA5ZTgxIiwic3ViIjoiN2Q4YmEzNTctY2I4Mi00MjUyLThmMTgtNGQ4YjA1OTExNDRkIiwiYXV0aF90aW1lIjoxNTQ4NzYwMTI1LCJpZHAiOiJsb2NhbCIsImFtciI6WyJwd2QiXX0.dzPBkAsnsHzb2jY4Koo6ThEhZ5mtbqbabgRbgszl4_QMqmgOn9r1xlmJs-dpDJ4IdJ3q4vx7xIDuULuU1egKt-H70mGnpHu1cHikTQa14dkZTWbpUR8qGr95YHRgqo_R8iUkFVtptymP4KrmM7guh4hjidjbfmlJeJIcbf4cKAnF-VaQ5s8Aomm3D-T4cY_IZwQBHLhjoEqRaqXS9yjrweN_T98GroxHPGmMuOCFUsKAgRjfoy-O2mZA2U2mh3dYm5SpdStxJBk49bmlzqcVcnzBA1gDJkWNyJCqCNX-SrSHaTq8bVo8pBkGTwEXqeUE69R6FdSiHcJky-so-0gl8w
```

A token base64 encode-dal van elkódolva, a tartalmát könnyen kinyerhetjük. Használjuk erre a https://jwt.io/ oldalt:

![jwt](/assets/images/jwtio.png)

A számunkra jelenleg fontosabb elemei:

- **sub**: Subject = a felhasználó azonosítója
- **iss**: Issuer = az identity token kiállítója, esetünkben ez az IDP URI-ja
- **aud**: Audience = a kliensalkalmazás a ClientId-val azonosítva
- **iat**: Issued At - a kiállítás időpontja (1970. január 1. óta eltelt másodpercben kifejezve)
- **exp**: Expiration - a lejárat ideje, ettől az időponttól nem érvényes a token
- **nbf**: Not Before - érvényesség kezdete, eddig az időpontig nem érvényes a token
- **auth_time**: Authentication Time - az eredeti autentikálás időpontja
- **amr**: Authentication Methods References - autentikációs methodok azonosítói json array-ként. A mi esetünkben pwd, azaz password
- **nonce**: Number only to be used once - a kliensnél generálódik és az IDP visszaküldi a tokenben. A validáció során ezeknek az összehasonlítása segít a **CSRF** (cross-site request forgery) támadások ellen
- **c_hash**: Code Hash - a hashelt kód egy részlete, ez köti az authorization code-ot az identity tokenhez
- **at_hash**: Access Token Hash - a hashelt access token egy részlete, ez köti az access tokent az identity tokenhez

---

A bejegyzés végén még mindenképp szeretném bemutatni, hogy hogyan tudjuk manuálisan meghívni a UserInfo endpointot igény esetén. Láttuk, hogy az authentication middleware automatikusan képes meghívni, de mi szeretnénk, hogy cookie méretének minél kisebben tartása és a lehető legfrissebb információkért ne tároljuk el a claims identity-be.

Ehhez először az IDP Config.cs fájlban kell egy új IdentityResource-t hozzáadni:

```csharp
public static IEnumerable<IdentityResource> GetIdentityResources()
{
    return new List<IdentityResource>
    {
        new IdentityResources.OpenId(),
        new IdentityResources.Profile(),
        new IdentityResources.Address()
    };
}
```

Ezután a Client esetén is engedélyezni kell az Address scope-ot:

```csharp
public static IEnumerable<Client> GetClients()
{
    return new List<Client>()
    {
        new Client
        {
            ...

            AllowedScopes =
            {
                IdentityServerConstants.StandardScopes.OpenId,
                IdentityServerConstants.StandardScopes.Profile,
                IdentityServerConstants.StandardScopes.Address
            },
            
            ...
        }
    };
}
```
Végül pedig minden felhasználóhoz adjuk hozzá az address claim-et:

```csharp
public static List<TestUser> GetUsers()
{
    return new List<TestUser>
    {
        new TestUser
        {
            SubjectId = "14527f77-4682-48ec-99b6-037464a0a8dc",
            Username = "Siona",
            Password = "password",

            Claims = new List<Claim>
            {
                new Claim("given_name", "Siona"),
                new Claim("family_name", "Atreides"),
                new Claim("address", "Fish Speaker House")
            }
        },
        new TestUser
        {
            SubjectId = "7d8ba357-cb82-4252-8f18-4d8b0591144d",
            Username = "Duncan",
            Password = "password",

            Claims = new List<Claim>
            {
                new Claim("given_name", "Duncan"),
                new Claim("family_name", "Idaho"),
                new Claim("address", "Botanical Testing Station")
            }
        }
    };
}
```

Ha ezzel megvagyunk, akkor a kliensben is hozzá kell adnunk az address scope-ot a ConfigureServices metódusban:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    
    }).AddOpenIdConnect("oidc", options =>
    {
        ...

        options.Scope.Add("openid");
        options.Scope.Add("profile");
        options.Scope.Add("address");
        
        ...
    });
}
```

Hogy ezeket meg tudjuk jeleníteni, adjunk hozzá egy a klienshez egy View-t, egy ViewModelt és módosítsuk a ProjectControllert:

**ViewModels/AddressViewModel.cshtml**

```csharp
namespace Choam.ERP.Client.ViewModels
{
    public class AddressViewModel
    {
        public string Address { get; private set; }

        public AddressViewModel(string address)
        {
            Address = address;
        }
    }
}
```

**Views/Project/Address.cshtml**

```csharp
@model Choam.ERP.Client.ViewModels.AddressViewModel

<h2>Location of the logged in user</h2>

<div class="container">
    <div class="text text-info">@Model.Address</div>
</div>
```

**Views/Shared/_Layout.cshtml**

```csharp
<div class="navbar-collapse collapse d-sm-inline-flex flex-sm-row-reverse">
    <ul class="navbar-nav flex-grow-1">
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-controller="Project" asp-action="Index">Home</a>
        </li>
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-controller="Project" asp-action="Address">Location</a>
        </li>
        <li class="nav-item">
            @if (User.Identity.IsAuthenticated)
            {
                <a class="nav-link text-dark" asp-area="" asp-controller="Project" asp-action="Logout">Logout</a>
            }
        </li>
    </ul>
</div>
```

Végül a controllerben írjuk meg az Address actiont. Innen fogjuk meghívni a UserInfo endpointot manuálisan, ehhez szükségünk lesz az **IdentityModel NuGet**re:

```bash
dotnet add Choam.ERP.Client package IdentityModel
```

Az IdentityModel package helper classokat tartalmaz, amik segítenek például az endpoint URI-k összeállításában, az eredmények parse-olásában. Először hozzunk létre egy discoveryClientet, ami segít az URI-k felderítésében. A DiscoveryClient az IdentityModel.Client namespace-ben található. Paraméterként az IDP-nk URI-ját szükséges megadni. Ha erre meghívjuk a GetAsync metódust, akkor megkapjuk a metadata response-t. Ez a metadata response tartalmazza a UserInfoEndpoint elérhetőségét, így könnyen létre tudunk hozni ezzel egy új **UserInfoClient**et. Ezután pedig szükségünk lesz egy access tokenre, hogy meghívhassuk ezt az endpointot, amit a GetTokenAsync metódus HttpContexten történő meghívásával tudunk megkapni. Ezután a userInfoClientet meghívva a GetAsync metódust és paraméterben átadva az access tokent, válaszban megkapjuk a claimeket, ahonnan könnyen ki tudjuk szedni az addresst. Nézzük a kódot:

```csharp
public async Task<IActionResult> Address()
{
    var discoveryClient = new DiscoveryClient("https://localhost:44385");
    var metadataResponse = await discoveryClient.GetAsync();
    var userInfoClient = new UserInfoClient(metadataResponse.UserInfoEndpoint);

    var accessToken = await HttpContext.GetTokenAsync(OpenIdConnectParameterNames.AccessToken);

    var response = await userInfoClient.GetAsync(accessToken);

    var address = response.Claims.FirstOrDefault(c => c.Type == "address")?.Value;

    return View(new AddressViewModel(address));
}
```

Ezt a bejegyzést egy sorozat első tagjának szántam, a későbbiekben kitérünk majd a role-based és attribute-based access controlra, a tokenek élettartamára és a refresh tokenekre. Jelenleg memóriában tárolunk minden információt, a következő bejegyzésekben szó lesz arról, hogy hogyan tudjuk ezeket SQL Serveren tárolni, és arról is hogy hogyan tudunk third-party logineket használni (például Google autentikációt).