Title: Entity Framework Core 1. rész alapok
Published: 2017-11-08 15:19
Author: molnardenes
Tags: [ORM, entity framework core]
---

Az *Entity Framework* egy **ORM** (*object-relational mapping*) keretrendszer .NET környezetben. Az object-relational mapperek használatával lehetővé válik többek között az adatbázis adatainak létrehozása, lekérdezése és manipulálása objektum orientált szemlélet megvalósításával.

Az **Entity Framework Core** az Entity Framework pehelysúlyú, cross-platform verziója. Az EF Core támogatja a legfontosabb adatbázisokat, mint például SQL Server, SQLite, PostgreSQL, SQL Server CE, MySQL.

Használatával a fejlesztés során a business objectekre több figyelmet fordíthatunk, gondoskodik helyettünk a connectionStringről, a megfelelő parancsokról, stb.

Az EF Core open source, így bárki által szabadon hozzáférhető, sőt szerkeszthető a forráskódja, elérhető az alábbi [linken](https://github.com/aspnet/EntityFrameworkCore).

## EF Core 2 telepítése

A .NET Core 2 alkalmazások fejlesztéséhez mindenképpen szükség van a .NET Core 2.0 SDK-ra, ami letölthető [innen](https://www.microsoft.com/net/download). Ha az EF Core 2-t a teljes .NET Frameworkon szeretnénk használni és nem a .NET Core-on, akkor használjuk a Visual Studio 2017 legalább 15.3 verzióját, Visual Studio 2015 esetén pedig upgrade-eljük a NuGet-et a 3.6.0 verzióra.

A Visual Studio korábbi verzió használata esetén a project fájlunkhoz adjuk hozzá a következő sorokat:

```xml
<AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
<GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
```

## EF Core 2 workflow

![EF Core Basic workflow](/assets/images/ef_basic_workflow.png)

1. Mikor az **Entity Framework**kel dolgozunk, az első lépés általában a **domain class**ok (vagy **business class**ok) létrehozása
2. Ezután az Entity Framework API-k segítségével létrehozzuk a domain classok alapján az **adatmodell**ünket
3. Szintén az EF API-k segítségével létrehozhatunk **LINQ To Entities** lekérdezéseket az osztályokkal kapcsolatban
4. Az EF figyelemmel követi az objektumok állapotát, SQL lekérdezéseket hoz létre és végre is hajtja azokat, valamint a lekérdezések eredményéből osztályokat generál számunkra

## Solution létrehozása

Hozzunk létre egy Console Appot Visual Studioban a .NET Core-t célozva, és nevezzük el **ToDoApp** néven:

![EF ConsoleApp](/assets/images/ef_consoleapp.png)

Következő lépésként adjuk hozzá az **Microsoft.EntityFrameworkCore.SqlServer** package-t vagy a NuGet Package Manager UI-n vagy a Package Manager Console-on keresztül. Ezek a Tools => NuGet Package Manager menüből érhetők el. A példában a console-t használom, de a grafikus felület is tökéletesen megfelelő volna. A package végén az SqlServer azt jelzi, hogy adatbáziskiszolgálónak az SQL Servert fogjuk használni, de ezenkívül sok más adatbázishoz is elérhető a megfelelő package.

```bash
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

## Business classok és kapcsolataik

A példánkban egy nagyon egyszerű ToDo alkalmazás osztályait hozzuk létre. Vannak projektjeink, amelyekhez feladatok tartoznak. Egy-egy feladatnak mindig van egy felelőse, aki az adott feladat elvégzéséért felel. Egy feladat egy projekthez tartozhat csak.

![adatbázis](/assets/images/ef_db1.png)

A business classok részére hozzunk létre egy Model nevű könyvtárat a projekten belül. Figyeljünk arra, hogy az osztályaink **publikusak** legyenek! Az osztályok a következőképpen néznek ki

**Consultant**

```csharp
using System.Collections.Generic;

namespace ToDoApp.Models
{
    public class Consultant
    {
        public int Id { get; set; }
        public string Name { get; set; }               
        public IEnumerable<Task> Tasks { get; set; }        
    }
}
```

Ebben az osztályban az *Id* property szolgál az egyedi azonosító nyilvántartására, a *Name* tartalmazza a Consultant nevét, a *Tasks* property pedig az adott consultanthoz tartozó feladatokat jelöli.

**Project**

```csharp
using System.Collections.Generic;

namespace ToDoApp.Models
{
    public class Project
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Description { get; set; }
        public IEnumerable<Task> Tasks { get; set; }        
    }
}
```

A **Project** osztályban az *Id* szintén az egyedi azonosító, a *Name* a projekt elnevezését jelöli, míg a *Description* a projekt rövid leírását. A *Tasks* az adott projekthez tartozó feladatok nyilvántartására szolgál.

**Task**

```csharp
namespace ToDoApp.Models
{
    public class Task
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Description { get; set; }        
        public Project Project { get; set; }
        public int ProjectId { get; set; }
        public Consultant Consultant { get; set; }
        public int? ConsultantId { get; set; }
    }
}
```

Ebben az osztályban az *Id* a fentiekhez hasonlóan egyedi azonosító, a *Name* és a *Description* a feladat elnevezését, valamint rövid leírását tartalmazza. A *Project* és a *Consultant* **referencia** vissza a projektre és a consultantre. Ezeket nevezzük **navigation property**-nek is. A *ProjectId* és a *ConsultantId* pedig explicit property, ami a **foreign key**-t tartalmazza, a *Project Id*-t és a *Consultant Id*-t. Egy feladat nem létezhet projekten kívül, viszont olyan helyzet előfordulhat, hogy a feladathoz egyelőre még nincs hozzárendelve felelős, ezért **Nullable** property-ként hozzuk létre a *ConsultantId*-t.

## One-To-Many kapcsolatok definiálása

Az Entity Framework modellek esetén az egyes osztályok közötti kapcsolatokat **navigation property**-kkel tudjuk létrehozni. A navigation property-ket akkor használjuk, amikor nem primitív adattípusokkal dolgozunk. Minden osztályunk tartalmaz olyan property-ket, amiknek a típusa könnyen megfeleltethező az adatbázis kiszolgáló egy-egy adattípusának (string, int a fenti példákban), ugyanakkor Project, Task vagy Consultant adattípus nem létezik egyik adatbázis kiszolgáló esetében sem. Ilyenkor sietnek segítségünkre a navigation property-k.

A *Task* osztálydefiníciónk lehetővé teszi a *Consultant* **navigation property**-nek köszönhetően, hogy minden feladatnak legyen egy felelőse (itt ebben az esetben **reference** navigation property-ről beszélünk, az értéke 0 vagy 1 lehet). A *Consultant* osztályban a *Tasks* navigation property (ezt **collection** navigation property-nek is nevezzük) biztosítja, hogy egy Consultantnak több feladata lehessen.

A független osztálynak azt tekintjük, amelyikben a collection navigation property található, a függőnek pedig azt, amelyikben a reference. 

A navigation property-knek köszönhetően könnyedén navigálhatunk az egyes osztályok között akár kódból is:

```csharp
foreach (var task in consultant.Tasks)
{
    ...
}

task.Consultant = new Consultant();
```

A fenti példáinkban mi a navigation property mellett expliciten is megadtuk a foreign key-t minden esetben. Az így megadott kapcsolat egy teljesen definiált kapcsolatnak tekinthető, ezért ha nem Nullable-ként adtuk volna meg a ConsultantId-t, akkor mindenképpen kötelező elem, ugyanis ilyen esetekben a referencia constraint **Cascade** lenne. Ez azt jelentené, hogy amennyiben törlünk egy consultantet, törlődne az összes olyan task, ami az övé volt. A valóságnak ez azért nem felelne meg, ezért szükséges Nullable-re állítani a foreign key-t. A task és projekt viszonyában viszont helyes eljárás a Cascade, ugyanis ha megszűnik egy projekt, akkor a projekt feladatai is okafogyottá válnak, így ott maradhat a not nullable foreign key.

## Az adatmodell létrehozása

A business classok után szükségünk lesz az adatmodell létrehozására. Az adatmodellünket a **DbContext**ből fogjuk származtatni. Ahhoz azonban, hogy elérjük a **DbContext**et, telepítenünk kell a **Microsoft.Entityframeworkcore.Tools** package-t. Ez a telepítés történhet grafikus felületen, vagy a Package Manager Console-ból is:

```bash
Install-Package Microsoft.EntityFrameworkCore.Tools
```

Ha megvagyunk, hozzuk létre a Models mappában a **ToDoContext.cs** állományt:

```csharp
using Microsoft.EntityFrameworkCore;

namespace ToDoApp.Models
{
    public class ToDoContext : DbContext
    {
        public DbSet<Task> Tasks { get; set; }
        public DbSet<Project> Projects { get; set; }
        public DbSet<Consultant> Consultants { get; set; }
    }
}
```

### DbContext

Az Entity Framework Core **DbContext** osztálya egy API, ami a következőkért felel:

* adatbáziskapcsolatok létrehozása és menedzselése
* adatokkal kapcsolatos műveletek, lekérdezések
* változáskövetés: figyelemmel követi az objektumok állapotát (hozzáadott, változatlan, változtatott, törölt)
* modellépítés
* objektumok chache-elése: chache-eli az objektumokat, és a memóriában lévő objektumokkal kapcsolatban onnan veszi az adatot, és nem hajt végre újabb lekérdezést az adatbázisból
* adatok mappelése
* tranzakciókezelés: a SaveChanges() metódus egy **transaction**t hoz létre és az összes lekérdezést abba rakja bele, hiba esetén **rollback**et hajt végre


### DbSet

A **DbSet** lényegében a **repository pattern** egy implementációja. A DbSetek a DbContext property-jeként kerültek hozzáadásra, az adatbázistáblákat jelképezik. Alapesetben az adatbázistáblák elnevezését is ezek adják. A fenti példánkban a Tasks property egy Task listát jelképezi, ami konvenció szerint az adatbázis Tasks táblájára kerül mappelésre. A Projects egy projektgyűjteményt jelképez és a Projects adatbázistáblára mappelődik, a Consultant ugyanígy.

A **DbSet**ek a **memóriában** lévő objektum kollekciókat jelentik. Ahhoz, hogy bármilyen módosítás az adatbázisban is végbe menjen, szükséges lesz a *SaveChanges()* metódus (erről részletesen a következő bejegyzésben) meghívása.

A létrehozott DbSetekre közvetlenül tudunk lekérdezéseket futtatni, például:

```csharp
var _context = new ToDoContext();
_context.Consultants.ToList();
```

### Az adatbáziskiszolgáló és connection string beállítása

Az EF Core-ban explicit módon szükséges az adatbáziskiszolgáló és a connection string megadása, amire lehetőség van a DbContextben. A DbContext rendelkezik egy virtuális metódussal, az **OnConfiguring**gal, aminek paramétere a **DbContextOptionsBuilder**. Az optionsBuilderben be tudjuk állítani a kiszolgálót a **UseSqlServer extension method**nak köszönhetően, ami azért érhető el, mert referenciaként behivatkoztuk a projektünk elején.

```csharp
using Microsoft.EntityFrameworkCore;

namespace ToDoApp.Models
{
    public class ToDoContext : DbContext
    {
        public DbSet<Task> Tasks { get; set; }
        public DbSet<Project> Projects { get; set; }
        public DbSet<Consultant> Consultants { get; set; }      

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer("Server = .; Database = ToDoApp; Trusted_Connection = True; ");
        }
    }
}
```

## Migration

Alkalmazásfejlesztés során az újabb és újabb követelmények megjelenésével gyakran változik a modellünk, és elengedhetetlen, hogy ezzel az adatbázis is szinkronban legyen. A **migration** ezt teszi lehetővé. A migrationöket a Package Manager Console-ból tudjuk alapból végrehajtani, de létezik parancssori alkalmazás is erre, ha külön telepítjük a megfelelő package-t. A példákban a PMC-t használjuk majd az egyszerűség kedvéért.

![Migration workflow](/assets/images/migration_workflow.png)

A fenti ábrán is jól látható a folyamat, ami a migration lényegét jelenti. Ha létrejön vagy változik a modell, akkor szükséges létrehozni egy migrationt, ami ezt leképezi. A migration lefuttatása után azt alkalmazva az adatbázisra szinkronba kerül egymással a modell és az adatbázis.

### Migration létrehozása

Amikor létrehozunk egy **migration**t, a keretrendszer összehasonlítja a modell állapotát az előző migrationnel (már ha van olyan) és generál egy fájlt, amiben egy osztályt hoz létre olyan néven, ahogyan elneveztük a migrationt. Ez az osztály a **Microsoft.EntityFrameworkCore.Migrations.Migration** osztályból öröklődik és tartalmazza az **Up** és **Down** metódusokat. Az előbbi az összes módosítást tartalmazza a modellel kapcsolatban, ami az előző migration óta történt az adatbázishoz képest. Az utóbbi pedig visszavonja ezeket a változásokat, visszaállítva az adatbázist az előző migration állapotába. Ezzel egyidőben egy ModelSnapshot állomány is létrejön vagy módosul, ha már létezett, ami a modellünk éppen aktuális állapotát tartalmazza.

Hozzuk létre az első migration-ünket. Adjuk ki a következő parancsot a PMC-ben:

```bash
add-migration InitialMigration
```

Ha sikeresen lefutott a script, akkor létrejön egy Migrations mappa, ami tartalmazza a migration-ünket, valamint egy snapshot állományt, ami a modell jelenlegi állapotát mutatja. A migration fájlban láthatók a tábláink, azok közötti kapcsolatok, stb.

### Migration alkalmazása scriptként és közvetlenül az adatbázisra

Tesztadatbázisra általában közvetlenül tudjuk alkalmazni a migrationt, ilyenkor nincs más dolgunk, mint kiadni az *update-database* parancsot:

```bash
update-database
```

Ezzel szinkronba kerül az adatbázis és a modellünk. Ha megnyitjuk az adatbázisunkat SQL Serverben, láthatjuk, hogy létrejöttek a táblák és az azok közötti kapcsolatok is rendben.

Előfordulhat azonban olyan helyzet, production adatbázisoknál elég gyakran, hogy nem tudjuk közvetlenül az adatbázisra alkalmazni a migrationt. Ilyenkor lehetőségünk van egy **SQL script**et generálni, ami aztán lefuttatható a megfelelő adatbázison. Ennek létrehozása a *script-migration* paranccsal történik.

```bash
script-migration
```

Amennyiben az **-idempotent** paraméterrel alkalmazzuk a migrationt, akkor a migration history-t ellenőrizve csak azokat a módosításokat alkalmazza, amelyek korábban még nem kerültek alkalmazásra.

```bash
script-migration -idempotent
```

Ebben a bejegyzésben megismerkedtük az Entity Framework alapjaival, az adatmodell kialakításával, az egy-a-többhöz kapcsolat létrehozásával, a migrationnel és az adatbázis legenerálásával. A következő bejegyzésben megismerkedünk az egy-az-egyhez és a több-a-többhöz kapcsolatok kialakításával, valamint a reverse engineeringgel (amikor már meglévő adatbázisból hozzuk létre a modellünket) és az egyszerűbb lekérdezésekkel.
