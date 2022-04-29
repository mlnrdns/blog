Title: Entity Framework Core 2. rész Fluent API, one-to-one és many-to-many kapcsolatok
Published: 2017-12-01 06:54
Author: molnardenes
Tags: [ORM, entity framework core]
---

Az előző bejegyzésben megnéztük, hogy hogyan tudunk létrehozni **konvenció** alapján **one-to-many** kapcsolatokat. Konvenció alapján ugye akkor jön létre kapcsolat két entitás között, ha **navigation property**-ket szerepeltetünk (egy property navigation property, ha olyan adattípusú, amely nem feleltethető meg egyetlen olyan típusnak sem, ami az általunk használt adatbáziskiszolgáló számára értelmezhető).

## Fluent API

A konvención túl **Fluent API** segítségével is létrehozhatjuk a korábbi one-to-many kapcsolatainkat, ebből a bejegyzésből pedig az is kiderül, hogy a one-to-one és a many-to-many kapcsolatok létrehozásához viszont nélkülözhetetlen.

A korábbi one-to-many kapcsolatot a következő módon tudjuk beállítani Fluent API-val:

```csharp
using Microsoft.EntityFrameworkCore;

namespace ToDoApp.Models
{
    public class ToDoContext : DbContext
    {
        ...

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Task>()
                .HasOne(t => t.Project)
                .WithMany(p => p.Tasks)
                .IsRequired()
                .OnDelete(DeleteBehavior.Cascade);
        }
    }
}
```

Látható, hogy a navigation property-k beazonosítása után a **HasOne**/**HasMany** és a **WithOne**/**WithMany** használatával tudjuk a kapcsolatokat beállítani. A HasOne/HasMany annak az entitásnak a navigation property-jére vonatkozik, amit éppen konfigurálunk (a fenti példában a Taskra), míg a WithOne/WithMany az inverz navigációért felel. A HasOne és WithOne a reference navigation property-k esetén használatos, míg a HasMany és a WithMany a collection navigation property-kre vonatkozik.

Az előző bejegyzésben láttuk, hogy konvenció alapján egy foreign key opcionálissá tehető, ha Nullable típusokat használunk. Ha azonban nem áll rendelkezésre Nullable típus, akkor a Fluent API segítségével van erre lehetőségünk az **.IsRequired(false)** megadásával. Ha egy foreign key kötelezően megadandó, akkor: **.IsRequired()**.

Amit még mindenképpen érdemes a Fluent API segítségével konfigurálni az a **DeleteBehavior**, vagyis hogyan viselkedjenek az entitások a független entitás törlése esetén. A példánkban: mi legyen a *Task*kal, ha a Taskot tartalmazó *Project*et töröljük? A következő lehetőségek közül választhatunk:

* **Cascade** a függő entitások törlődnek a memóriában és az adatbázisban is
* **ClientSetNull** ez a default viselkedés, memóriában a foreign key nullra állítódik, adatbázis változatlan marad
* **SetNull** mind a memóriában, mind az adatbázisban a foreign kell nullra állítódik
* **Restrict** nem történik változtatás

## Many-to-many kapcsolatok

Az **EF Core**-ban a jelenlegi **2.0** verzióban és a következő 2018 első vagy második negyedévére tervezett **2.1** sem várható még, hogy egy **join table**-t reprezentáló entitás nélkül létre lehessen hozni many-to-many kapcsolatot.

Jelenleg tehát ez úgy lehetséges, hogy létrehozunk egy entitást, ami a join table funkciót fogja ellátni és külön-külön implementálunk két one-to-many kapcsolatot.

A many-to-many kapcsolatokat tartalmazó adatbázisunk így néz most ki:

![adatbázis](/assets/images/ef_db2.png)

A Project és a Consultant osztályokat kibővítjük a kötőentitásra vonatkozó navigation property-kkel, a kötőentitást pedig létrehozzuk:

**UserProject**

```csharp
using Microsoft.EntityFrameworkCore;

namespace ToDoApp.Models
{
    public class ConsultantProject
    {
        public Consultant Consultant { get; set; }
        public int ConsultantId { get; set; }
        public Project Project { get; set; }
        public int ProjectId { get; set; }
    }
}
```

**Consultant**

```csharp
using Microsoft.EntityFrameworkCore;

namespace ToDoApp.Models
{
    public class Consultant
    {
        public int Id { get; set; }
        public string Name { get; set; }               
        public IEnumerable<Task> Tasks { get; set; }  
        public IEnumerable<ConsultantProject> ConsultantProjects { get; set; }
}
```

**Project**

```csharp
using Microsoft.EntityFrameworkCore;

namespace ToDoApp.Models
{
    public class Project
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Description { get; set; }
        public IEnumerable<Task> Tasks { get; set; }
        public IEnumerable<ConsultantProject> ConsultantProjects { get; set; }
    }
}
```

Ha ezzel megvagyunk, létre kell hoznunk egy **composite foreign key**-t (ilyenkor a kulcs több különböző property-ből áll) a Fluent API segítségével, valamint definiálni az entitások között kapcsolatokat és a törlés esetén alkalmazandó viselkedést. Composite foreign key csak és kizárólag Fluent API-val hozható létre, konvenció alapján vagy Data Annotation-nel (erről későbbi bejegyzésben lesz szó) jelenleg nem, és egyelőre úgy tűnik, hogy nem is tervezik.

Bővítsük ki a **ToDoContext.cs** állományt a szükséges adatokkal:

```csharp
using Microsoft.EntityFrameworkCore;

namespace ToDoApp.Models
{
    public class ToDoContext : DbContext
    {
        ...

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<ConsultantProject>()
                .HasKey(c => new { c.ConsultantId, c.ProjectId });
            modelBuilder.Entity<ConsultantProject>()
                .HasOne(cp => cp.Consultant)
                .WithMany(u => u.ConsultantProjects)
                .HasForeignKey(cp => cp.ConsultantId)                
                .OnDelete(DeleteBehavior.Restrict);                
            modelBuilder.Entity<ConsultantProject>()
                .HasOne(cp => cp.Project)
                .WithMany(p => p.ConsultantProjects)
                .HasForeignKey(cp => cp.ProjectId)
                .OnDelete(DeleteBehavior.Restrict);
                
            base.OnModelCreating(modelBuilder);
        }
    }
}
```

## One-to-one kapcsolatok

Ha szeretnénk a consultantek esetén profilképet megjeleníteni, akkor szükségünk van egy one-to-one kapcsolat létrehozására, hiszen egy profilkép csak egy consultanthez tartozhat és egy consultantnek egyszerre csak egy profilképe lehet látható.

**ProfilePicture**

```csharp
using Microsoft.EntityFrameworkCore;

namespace ToDoApp.Models
{
    public class ProfilePicture
    {
        public int ProfilePictureId { get; set; }
        public byte[] Image { get; set; }
        public string Caption { get; set; }

        public int ConsultantId { get; set; }
        public Consultant Consultant { get; set; }
}
```

**Consultant**

```csharp
using Microsoft.EntityFrameworkCore;

namespace ToDoApp.Models
{
    public class Consultant
    {
        public int Id { get; set; }
        public string Name { get; set; }                       
        public IEnumerable<Task> Tasks { get; set; }  
        public IEnumerable<ConsultantProject> ConsultantProjects { get; set; }
        public ProfilePicture ProfilePicture { get;set; }
}
```

Az első lépésben már látható a one-to-many kapcsolathoz képest egy különbség: itt nem collection property-t vezettünk be a foreign key-hez, hanem egy szimpla referencia property-t. A Fluent API-ban a **HasOne** és **WithOne** segítségével fogjuk beállítani ezt a kapcsolatot. Itt azonban van még egy trükk, ugyanis amíg a one-to-many kapcsolat esetén egyértelmű, hogy melyik a függő és a független entitás (függő, amelyben a referencia navigation property van, független, amiben a collection navigation property), itt nekünk kell ezt expliciten megadni a **.HasForeignKey** segítségével:

```csharp
using Microsoft.EntityFrameworkCore;

namespace ToDoApp.Models
{
    public class ToDoContext : DbContext
    {
        ...

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            ...

            modelBuilder.Entity<Consultant>()
                .HasOne(c => c.ProfilePicture)
                .WithOne(p => p.Consultant)
                .HasForeignKey<ProfilePicture>(p => p.ConsultantId)

            ...    
        }
    }
}
```
