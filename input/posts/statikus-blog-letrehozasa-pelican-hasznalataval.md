Title: Statikus blog létrehozása Pelican használatával
Published: 2016-10-15 05:22
Author: molnardenes
Tags: [Pelican, blog]
---

Hosszú tervezgetés után végre rászántam magam, hogy lecseréljem a Wordpress blogomat, és egy statikus változatra váltsak. 2016-ban kissé elavultnak tűnt már egy php alapú weboldal. Az új blog létrehozása egy Python-alapú statikus bloggenerátor, a [Pelican](http://blog.getpelican.com/ "Pelican") használatával történt.

A statikus blogok **Markdown** vagy **reStructuredText** használatával készülnek. Ezek a szövegszerkesztési módok lehetővé teszik, hogy lényegében fel sem kell emelnünk a kezünket a billentyűzetről szövegszerkesztés közben. A szövegek formázáshoz néhány speciális jelet meg kell tanulnunk, de ez igen csekély ár, ha figyelembe vesszük, hogy mennyivel növeli a hatékonyságot az ilyenfajta szüvegalkotás. A fent említett eszközök egy tetszőleges szöveget alakítanak HTML-lé (vagy éppen PDF formátummá, stb.). További nagy előnyük, hogy igen könnyen nyomonkövethetől a változások a szövegben bármelyik verziókövető rendszerrel.

Nincs szükség php ismeretre, semmiféle adatbázis felsetupolásával nem kell foglalkoznunk. A deployolás nem más, mint a fájlok szerverre történő másolása, a tesztelés akár offline módban is történhet (leggyakoribb esetben egy kisebb szerver szkript is "jár" a blogmotorokhoz, amellyel gyerekjáték a tesztelés). 

A fentiekből következően semmiféle speciális tárhelyre nincs szükségünk, nagyon olcsón fenn tudjuk tartani a tárhelyünket (több ingyenes megoldás is van statikus webhelyek tárolásához, elég csak például a [GitHub Pages](https://pages.github.com/ "GitHub Pages")re gondolni, amelyhez saját domaint is rendelhetünk szintén ingyen).

## A Pelican telepítése ##
Első lépésben hozzunk létre egy virtuális környezetet (részletesen [ebben]({filename}/virtualis-kornyezet-kialakitasa.md) a bejegyzésben foglalkoztunk a témával) és egy könyvtárat a blogunk számára.

```shell
mkvirtualenv pelican_blog
```

Létrehozásnál automatikusan belép az új virtuális környezetben, később a **workon**nal könnyen beléphetünk:

```shell
workon pelican_blog
```

Még egy utolsó lépést tegyünk meg, és rendeljük hozzá az újonnan létrehozott könytárat a környezethez a **setprojectdir** paranccsal. Ez azért előnyös, mert mindig automatikusan ebbe a könyvtárba fog belépni, ha aktiváljuk a virtuális környezetünket:

```shell
PROJEKTÜNK_ELÉRÉSI_ÚTVONALA\setprojectdir .
```

Miután meggyőződtünk róla, hogy aktív a **pelican_blog** környezetünk (a parancssorunk a pelican_blog előtaggal kezdődik):

```shell
(pelican_blog) C:\...\blog
```

Első dolgunk a Pelican telepítése:

```shell
pip install Pelican
```

Mivel alapértelmezetten reStructuredText formátumot használ, de mi **Markdown**t szeretnénk, ezért szükséges még a Markdown telepítése is:

```shell
pip install markdown
```

## Blogstruktúra létrehozása ##
Miután telepítettük a szükséges csomagokat, hozzuk létre a blogunk struktúráját:

```shell
pelican-quickstart
```

A parancs kiadása után néhány kérdésre kell válaszolnunk első körben. Az itt megadott adatok a későbbiekben módosíthatók lesznek természetesen:

```shell
(pelican_blog) C:\...\blog>pelican-quickstart
Welcome to pelican-quickstart v3.6.3.

This script will help you create a new Pelican-based website.

Please answer the following questions so this script can generate the files
needed by Pelican.


Using project associated with current virtual environment.Will save to:
C:\....\blog

> What will be the title of this web site? pelican_blog
> Who will be the author of this web site? molnardenes
> What will be the default language of this web site? [en] hu
> Do you want to specify a URL prefix? e.g., http://example.com   (Y/n) y
> What is your URL prefix? (see above example; no trailing slash) http://molnard
enes.hu/blog
> Do you want to enable article pagination? (Y/n)
> How many articles per page do you want? [10]
> What is your time zone? [Europe/Paris] Europe/Budapest
> Do you want to generate a Fabfile/Makefile to automate generation and publishi
ng? (Y/n) n
> Do you want an auto-reload & simpleHTTP script to assist with theme and site d
evelopment? (Y/n)
> Do you want to upload your website using FTP? (y/N)
> Do you want to upload your website using SSH? (y/N)
> Do you want to upload your website using Dropbox? (y/N)
> Do you want to upload your website using S3? (y/N)
> Do you want to upload your website using Rackspace Cloud Files? (y/N)
> Do you want to upload your website using GitHub Pages? (y/N)
Done. Your new project is available at C:\...\blog
```

Ha végeztünk, a következő struktúra jött létre:

```shell
blog/
  ├── content
  ├── output
  ├── pelicanconf.py
  └── publishconf.py
```

A két .py kiterjesztésű fájl a konfigurációs állományok, a **content** mappába kerülnek markdown formában a bejegyzések, az **output**ba pedig a generált html állományok.


## A konfigurációs fájlok ##
A quickstart során létrejött két állomány: **pelicanconf.py** és **publishconf.py**, melyek a konfigurációs beállításokat tartalmazzák. A tartalom elkészítése után, ha szeretnéd látni, hogy mit hoztál létre, a pelicanconf állományt használjuk, publikálásnál mindkét állományt együttesen.

A fájlok viszonylag magukért beszélnek, itt elérhető az én [pelicanconf.py](https://github.com/molnardenes/blog/blob/master/pelicanconf.py "pelicanconf.py") és [publishconf.py](https://github.com/molnardenes/blog/blob/master/publishconf.py "publishconf.py") verzióm. Célszerú a **RELATIVE_URLS**-t a pelicanconf állományban True-ram a publishconfban False-ra állítani.

## Téma beállítása ##
Az alapértelmezett témán felül rengeteg téma áll rendelkezésre. Hogy ezeket elérjük, célszerű clone-ozni a teljes pelican-themes könyvtárat a blogunk gyökerébe (hogy ezt meg tudjuk tenni, telepítve kell lennie a **Git**-nek):

```shell
git clone https://github.com/getpelican/pelican-themes.git
```

Ha ezt megtettük, akkor a **pelicanconf.py**-ban megadhatjuk, hogy melyik témát szeretnénk használni (ebben a blogban a Flex témát használom):

```python
THEME = '../pelican-themes/Flex'
```

## Pluginek ##
Nagyon sok plugin is rendelkezésre áll a Pelicanhoz (sőt mi magunk is készíthetünk nyugodtan). Hogy ezeket elérjük, clone-ozzuk ezeket is:

```shell
git clone https://github.com/getpelican/pelican-plugins.git
```

Ha megvagyunk, állítsuk be a **pelicanconf.py**-ben a szükséges plugineket:

```python
PLUGIN_PATHS = ['../plugins']
PLUGINS = ['render_math', 'series', 'post_stats']
```

## Bloggenerálás automatizálása ##
A Pelican a Makefile-t és egy unix scriptet használ a html fájlok generálásához. Mi azonban Windowson szeretnénk ezt megvalósítani, úgyhogy mikor erre rákérdezett a Pelican, azt választottuk, hogy ne generálja le nekünk ezeket a scripteket. Ehelyett készítsünk három **.bat** állományt, amelyek segítségével egy kattintás lesz csak a blogunk legenerálása.

**peldev.bat**

```shell
pelican content --debug --autoreload  --output output --settings pelicanconf.py
```

**pelserver.bat**

```shell
pushd output
python -m pelican.server
popd
```

**pelpublish.bat**

```shell
pelican content --output output --settings publishconf.py
```

A bejegyzés készítése után, ha szeretnénk megnézni, hogyan is néz ki, akkor az első két állományt lefuttatjuk:

```shell
peldev.bat
pelserver.bat
```

Ha minden rendben zajlott, akkor a **http://127.0.0.1:8000** címen el is érhető a blogunk. Ha elégedettek vagyunk az eredménnyel, futtassuk le a **pelpublish.bat**ot és másolhatjuk is fel a szerverünkre az output könyvtár tartalmát.


## Verziókövetés beállítása ##
Hozzunk létre egy **repository**-t a blogunk számára **GitHub**on, majd lépjünk be a blogunk gyökérkönytárába:

```shell
git init
git add .
git commit -m "initial commit"
git remote add origin https://github.com/molnardenes/blog.git
git push -u origin master
```

Minden esetben, amikor létrehozunk egy új bejegyzést, kipusholjuk a változtatásokat, lefuttatjuk a pelpublish.bat fájlt, és felmásoljuk a szerverre az állományokat.
