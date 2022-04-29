Title: Build folyamat optimalizálása Cake-kel
Published: 2019-08-15
Author: molnardenes
Tags: [cake, azure devops, build, continous integration, gitversion]
---

A szoftverfejlesztési folyamat során rendkívül fontos, hogy az alkalmazásunk könnyen buildelhető és deployolható legyen ismételhető módon, különböző környezetekben is akár. Ha a build folyamat egyenlő az IDE-ben nyomott F5-tel, akkor bizony baj van. Ezt nagyon jól összefoglalta már Jeff Atwood a [The F5 Key Is Not a Build Process](https://blog.codinghorror.com/the-f5-key-is-not-a-build-process/) posztjában.

A fenti probléma egy kiváló megoldása lehet a [Cake](https://cakebuild.net/), ami egy Roslynre épülő cross-platform build automatizáló rendszer Windowsra, Linuxra és macOS-re. Nagy előnye, hogy a scriptek írása tulajdonképpen C#-pal történik, ami egy .NET fejlesztő számára nem elhanyagolható tény. További nagyon nagy előnye, hogy verziókövetőbe tehető.

A build futtatásához két fájlra van szükség:
* **build.cake**: maga a script fájl, amit a bootstrapper futtat
* **build.ps1** (*vagy build.sh Linux és macOS alatt*): bootstrapper fájl, változtatás nélkül futtatható: minden szükséges alkalmazást és package-t letölt, ami a script futásához szükséges

## A cake scriptek felépítése

### Task

A lelke a scriptnek, tulajdonképpen egy unit of workként tekinthetünk erre, utasításokat tartalmaz, amelyek meghatározott sorrendben hajtódnak végre. Egy **Task** feladata, hogy egyetlen dolgot megvalósítson, például futassa le a teszteket vagy hozzon létre egy NuGet package-et. Felépítése meglehetősen egyszerű:


```csharp
Task("Build")
    .Does(() =>
    {
        // build kódja
    });


Task("Running-Tests")
    .IsDependentOn("Build")
    .Does(() =>
    {
        // tesztfuttatás kódja
    });

Task("Default")
    .IsDependentOn("Running-Tests")
    .Does(() =>
    {
        // üresen hagyhatjuk
    });

```

A taskok a task nevéből és egy anonim metódusból állnak. Lehetőségünk van a különböző taskok között függőséget létrehozni: a fenti példában a *Default* task csak a *Running-Tests* lefutása után hívódik meg, ami pedig csak a *Build* task lefutása után, így minden task le fog futni a scriptünkben.

### RunTarget

Miután létrehoztuk a taskjainkat, el kell döntenünk, hogy melyik fusson le először: ezt a RunTarget meghívásával tudjuk megtenni, aminek paraméterként az elsőnek futó Task nevét adjuk át. Ha szeretnénk, hogy az összes task lefusson, akkor a *Running-Tests**-et szükséges meghívni, hiszen az függ a *Build*től, így az is le fog futni:

```csharp
RunTarget("Default");
```

A mindennapi fejlesztés során azonban előfordulhat, hogy nem mindig ugyanazt a taskot akarjuk először futtatni, vagy éppen csak egy részét akarjuk a scriptnek használni, ezért jó lenne, ha a script argumentumaként adhatnánk meg az először futó task nevét.

### Argument

A fenti kérdés az **Argument** metódus hívásával meg is oldódik: első paraméterként a script paraméterének nevét, másodikként pedig opcionálisan annak default értékét adhatjuk meg, és a futás eredményét akár egy változóban is eltárolhatjuk:

```csharp
var target = Argument("target", "Default");

// Taskok

RunTarget(target);
```

### Aliasok

A fenti példákban a **Task**, az **Argument**, a **RunTarget** és az **IsDependentOn** úgynevezett **alias**ok, amik kényelmi metódusok lényegében, és könnyen hozzáférhetők a cake scriptből. Sok beépített alias van már eleve a Cake-ben, de készíthetünk saját addineket, illetve elérhető rengeten addin, amik szintén saját aliasokkal rendelkeznek. Néhány beépített alias, amit a példák során használni fogunk:

* **WithCriteria**: egy predicate, aminek teljesülése során fut csak le az adott task. Gyakorlatban mi például a verziózásnál használjuk, ahol a lokális buildek során nem változtatjuk az assembly-k verziószámát, csak az Azure Devops-os buildek során

* **ContinueOnError**: a végrehajtás során előfordul hibák elnyelésére szolgál, mi nem használjuk a mindennapok során

* **Setup** és **Teardown**: ha szeretnénk valamit végrehajtani az első task előtt és az utolsó után, akkor azt is megtehetjük. Hasznos lehet például, ha valahol egy szervert el kell indítani, majd a build folyamat végével leállítani.

* **EnvironmentVariable**: a környezeti változókhoz férhetünk hozzá: eltárolhatjuk a version controlos bejelentkezési adatainkat, connection stringeket környezeti változókba, és könnyen hozzájuk férhetünk


### Preprocesszor direktívák

Ahhoz, hogy harmadik féltől származó eszközöket is használjunk, szükségünk van azoknak a letöltésére. Ilyeneket lehetnek például a test runnerek, vagy code coverage generáló tooluk, stb. Fentebb említettük, hogy ezeknek a függőségeknek a letöltése a bootstrapper feladata. De hogy történik ez? A cake script maga fogja ezeket tartalmazni: megadhatjuk a scriptünkben, hogy melyik csomagokra van szükségünk és ezt a Cake le fogja tölteni nekünk a build folyamat során. Ezt a célt szolgálják a preprocesszor direktívák. Ezek a következők:

#### #tool

Harmadik féltől származó parancssori alkalmazásokat tölt le és helyez el a **.\tools** könyvtárban. Ez egy speciális könyvtár, minden esetben, amikor egy külső csomag aliasát használjuk a scriptünkben, a Cake ebben a könyvtárban keresi a megfelelő futtatható állományt.

```csharp
#load nuget:?package=Cake.YouTrack
```

Amennyiben nem adunk meg külön verziót, mindig a legfrissebbet fogja a Cake letölteni, amivel azért érdemes óvatosan bánni, hiszen ha egy breaking change került be a csomagba, akkor a scriptunk elképzelhetően nem fog vagy nem megfelelően fog lefutni. A verziót a *&version=* segítségével tudjuk pontosítani:

```csharp
#load nuget:?package=Cake.YouTrack&version=0.1.3
```

#### #load

A **load** direktíva külső Cake scriptek hivatkozására szolgál. Olyan esetekben lehet hasznos, ha a különböző build scriptjeink egy közös, utility jellegű scriptet használnak. Használata:

```csharp
#load "scripts/utilities.cake"
```

#### #addin

Az **addin** direktíva NuGet csomagok telepítésére és hivatkozára szolgál:


```csharp
#addin nuget:?package=Cake.YouTrack&version=0.1.3
```


## Az első cake scriptünk

Most, hogy megvannak a legfontosabb alapfogalmak, hozzáfoghatunk egy teljes build script létrehozásához:

```csharp
//////////////////////////////////////////////////////////////////////
// ARGUMENTS
//////////////////////////////////////////////////////////////////////

var target = Argument("Target", "Default");
var configuration = Argument("Configuration", "Release");


//////////////////////////////////////////////////////////////////////
// PREPARATION
//////////////////////////////////////////////////////////////////////

var solutionDirectory = new DirectoryInfo(".").FullName;
var solutionName = "Cake.YouTrack.sln";
var solution = $"{solutionDirectory}/{solutionName}";
var artifactsDirectory = $"{solutionDirectory}/artifacts/";
var buildDirectory = $"{solutionDirectory}/artifacts/build/Cake.YouTrack";
var testResultsDirectory = $"{solutionDirectory}/artifacts/tests";
var coverageDirectory = $"{testResultsDirectory}/coverage";

//////////////////////////////////////////////////////////////////////
// SETUP
//////////////////////////////////////////////////////////////////////

Setup(context =>
{
    EnsureDirectoryExists(artifactsDirectory);    
    EnsureDirectoryExists(buildDirectory); 
});

//////////////////////////////////////////////////////////////////////
// TASKS
//////////////////////////////////////////////////////////////////////

Task("Clean")
    .Does(() =>
    {        
        Information("Cleaning artifact directory");       
        CleanDirectory(artifactsDirectory);    
        CleanDirectory(buildDirectory);
    });


Task("Restore-NuGet-Packages")
    .IsDependentOn("Clean")
    .Does(() =>
    {
        Information("Restoring NuGet packages");
        NuGetRestore(solution);        
    });


Task("Build")
    .IsDependentOn("Restore-NuGet-Packages")
    .Does(() =>
    {
        Information("Start building Cake.YouTrack");
        MSBuild(solution, new MSBuildSettings()
                .SetConfiguration(configuration)
                .SetMSBuildPlatform(MSBuildPlatform.Automatic)
                .WithProperty("SolutionDir", solutionDirectory)
                .WithProperty("OutDir", buildDirectory));
    });

//////////////////////////////////////////////////////////////////////
// TASK TARGETS
//////////////////////////////////////////////////////////////////////

Task("Default")
    .IsDependentOn("Build");


//////////////////////////////////////////////////////////////////////
// EXECUTION
//////////////////////////////////////////////////////////////////////

RunTarget(target);
```

Hogyan tudjuk mindezt lefuttatni? Helyezzük a build.cake és a build.ps1 állományt az sln állománnyal egy könyvtárba, és futtassuk a bootstrappert Powershellből:

```shell
.\build.ps1
```

A következő eredményt kellene látnunk:

```shell
Preparing to run build script...
Running build script...

----------------------------------------
Setup
----------------------------------------

========================================
Clean
========================================
Cleaning artifact directory

========================================
Restore-NuGet-Packages
========================================
Restoring NuGet packages
MSBuild auto-detection: using msbuild version '16.0.461.62831' from 'C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\MSBuild\Current\bin'.
Committing restore...
Assets file has not changed. Skipping assets file writing. 

...

========================================
Build
========================================
Start building Cake.YouTrack
Microsoft (R) Build Engine version 16.0.461+g6ff56ef63c for .NET Framework
Copyright (C) Microsoft Corporation. All rights reserved.

Building the projects in this solution one at a time. To enable parallel build, please add the "-m" switch.
Build started 2019.08.14 10:07:03.

...

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:04.54

...

========================================
Default
========================================

Task                          Duration
--------------------------------------------------
Setup                         00:00:00.0247926
Clean                         00:00:00.0853633
Restore-NuGet-Packages        00:00:03.5102702
Build                         00:00:04.9586256
--------------------------------------------------
Total:                        00:00:08.5770517
```

A scriptünk elején definiáljuk az átadható argumentumokat és azokat default értékét: ha argumentum nélkül futtatjuk a scriptet, akkor a *Default* task fog lefutni, és *Release* módban zajlik a buildelés. Ha mondjuk csak a *Build* taskot szeretnék lefuttatni, akkor a következő módon kell eljárnunk:

```shell
.\build.ps1 -Target Build
```

Az argumentumok után definiáltunk néhány globális változót, amiket a scripten belül több alkalommal is használni fogunk. A *Setup* blokkban az **EnsureDirectoryExists** alias segítségével létrehozzuk az output könytárakat, amennyiben azok esetleg nem léteznének. A *Clean* task során ürítjük az output könytárak tartalmát, hogy egy előző build során odakerült fájlok ne okozzanak problémát a jelenlegi build során: ehhez a **CleanDirectory** aliast használjuk, ami rekurzívan törli a megadott mappák teljes tartalmát.

A következő taskban a **NuGetRestore** alias segítségével visszallítjuk a NuGet csomagokat, de csak miután lefutott a Clean task. Ha a NuGetRestore task befejeződik, a Cake a **Build** taskot futtatja le, ami az **MSBuild** aliasnak köszönhetően az MSBuilddel hajtja végre a buildelést. Az MSBuildSettingsben előre definiált extension methodok segítségével tudjuk felkonfigurálni a buildünket. Ezek részletesebben [itt](https://cakebuild.net/api/Cake.Common.Tools.MSBuild/MSBuildSettings/) megtalálhatók.
A **Default** task pedig a Buildtől függ, így ha nem adunk meg paramétert futtatás során, akkor a teljes scriptünk le fog futni a **RunTarget** aliasszal.

## Tesztek és code coverage riportok

A build folyamat elengedhetetlen része kellene, hogy legyen a unit tesztek futtatása és jó, ha van code coverage riportunk is. Teszteléshez mi az xUnitot használjuk, de NUnit, MSTest is ugyanúgy használható lenne. Első lépésben szükségünk lesz egy xUnit runnerre, amihez le kell töltenünk a megfelelő NuGet csomagot, majd hozzá kell adnunk a scripthez egy újabb taskot:

```csharp
#tool nuget:?package=xunit.runner.console&version=2.2.0

Task("Clean")
....
Task("Restore-NuGet-Packages")
...
Task("Build")
...

Task("Running-Tests")
    .IsDependentOn("Build")
    .Does(() =>
    {
        Information("Running unit tests");
        XUnit2($"{buildDirectory}/*Tests.dll")
    });
```

A fenti példa lefuttatja a testrunnert minden Tests-re végződő dll-re.

Hasznos lenne a ezt valahogy vizuálisan is megjeleníteni magunknak, hogy tudjuk, hol szükséges még rágyúrni kicsit a tesztekre. Erre szolgál a code coverage riport, amire az open source **OpenCover**t fogjuk használni, a riport megjelenítését pedig a szintén open source **ReportGenerator**ral fogjuk elvégezni. Kicsit módosítsuk ez alapján a *Running-Tests* taskot, de előtte töltsük le a két NuGet csomagot:

```csharp
#tool nuget:?package=OpenCover&version=4.7.922
#tool nuget:?package=ReportGenerator&version=4.2.11

Task("Running-Tests")
    .IsDependentOn("Build")
    .Does(() =>
    {
        OpenCover(tool => tool.XUnit2($"{buildDirectory}/*Tests.dll",
             new XUnit2Settings
             {
                OutputDirectory = testResultsDirectory,
                XmlReport = true,
                HtmlReport = true,
                ShadowCopy = false
            }),
            $"{coverageDirectory}/coverage.xml",
            new OpenCoverSettings()
                .WithFilter("+[Cake.YouTrack*]*")
                .WithFilter("-[Cake.YouTrack.*Tests]*"));
    });
```

Viszonylag straightforward megoldás, az **OpenCoverSettings**ben célszerű beállítani egy filtert, ami arra szolgál, hogy a solutionben szereplő összes projektből generáljunk riportot, leszámítva magukat a unit teszteket, így azok nem zavarnak be a coverage riportba. 

Ha ez kész, hozzunk létre egy újabb taskot a riport vizualizálására:

```csharp
Task("Generate-Coverage")
    .IsDependentOn("RunTests")
    .Does(() =>
    {
        ReportGenerator($"{coverageDirectory}/coverage.xml", coverageDirectory);
    });
```


## Verziózás - GitVersion

Szintén egy fontos dolog az alkalmazásunk megfelelő verziókkal történő ellátása. Erre nagyon sokféle mód ismert, mi most a **GitVersion**t fogjuk használni. A GitVersion a verziókat a Git repository-k alapján számítja, és nagy előnye a konfigurálhatóság és az a tény, hogy szinte minden modern CI rendszerrel integrálható. A használatához szükséges, hogy a Cake letöltse a GitVersion.CommandLine csomagot, így ezt mindenképp tegyük meg a scriptünk elején:

```csharp
#tool nuget:?package=GitVersion.CommandLine&version=5.0.0

Task("Version")
    .IsDependentOn("Generate-Coverage")
    .Does(() =>
    {
        var versionInfo = GitVersion();
        Information($"Building for semantic version {versionInfo.FullSemVer}");  
        
        if (!BuildSystem.IsLocalBuild)
        {
            GitVersion(new GitVersionSettings
            {
                OutputType = GitVersionOutput.BuildServer,
                UpdateAssemblyInfo = true
            });
        }
    });
```

A *BuildSystem.IsLocalBuild* alias segítségével megadhatjuk, hogy az assembly-k verziószámát csak a build szerveren végrehajtott buildek után változtassuk, hogy a lokális buildelések ebbe ne zavarjanak be.

## Continous integration: Azure DevOps

A buildfolyamat teljes automatizálásának utolsó lépése a continous integration, amit ezúttal Azure DevOps-szal fogunk megoldani. Szerencsére elérhető egy Cake extension Azure DevOpshoz, ami telepíthető a [Visual Studio marketplace](https://marketplace.visualstudio.com/items?itemName=cake-build.cake)ről. Ezután nincs más dolgunk mint egy új Cake taskot hozzáadni:

![AzureDevops](/assets/images/azuredevopscake1.png)

Ha hozzáadtuk a jobot, tulajdonképpen annyi dolgunk maradt, hogy bekapcsoljuk a megfelelő triggereket:

![Triggers](/assets/images/azuredevopscake2.png)

Mind a develop, mind a master branchünkön bekapcsoltuk a continous integration lehetőséget, így minden push után le fog futni a buildünk. Hogy a build során létrejött állományok elérhetők legyenek, szükséges az artifactek letölthetővé tétele, amit szintén a Cake scriptünkben fogunk megtenni.

## Artifactek feltöltése

Ahogy az elején is jeleztem, a Cake nagyon jól együttműködik szinte az összes modern CI rendszerrel, így nagyon könnyű dolgunk lesz:

```csharp
Task ("UploadArtifacts")
    .IsDependentOn("Pack")
    .WithCriteria(BuildSystem.IsRunningOnAzurePipelinesHosted)
    .Does(() =>
    {
        TFBuild.Commands.UploadArtifactDirectory(nugetDirectory, "NuGetPackage");
    });
```

A WithCriteria(BuildSystem.IsRunningOnAzurePipelinesHosted) arra szolgál, hogy a task csak akkor fut le, ha Azure DevOpson fut éppen.