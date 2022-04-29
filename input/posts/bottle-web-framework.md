Title: A Bottle web framework 1. rész alapok
Published: 2017-08-24 23:32
Author: molnardenes
Tags: [bottle]
---

A Bottle egy gyors, egyszerű, de rendkívül hatékony mikro framework, ami kiválóan alkalmas kisebb webalkalmazások elkészítésére. Emellett egy nagyon jó lehetőség azok számára, akik most kezdenek ismerkedni a webfejlesztéssel.

![Python
shell](/assets/images/bottle-logo.png)

Tulajdonképpen egyetlen nagy fájlból áll, ami kiválóan alkalmassá teszi arra, hogy megértsük, hogyan működik egy **WSGI** web framework. Minden olyan információ, amire szükségünk van ahhoz, hogy lássuk, hogyan kapcsolódik az alkalmazásunk a Bottle-hoz, ebben a [nagy fájlban](https://github.com/bottlepy/bottle/blob/master/bottle.py) megtalálható.

### A Bottle telepítése

Elsőként hozzunk létre egy [virtuális környezetet](http://molnardenes.com/blog/virtualis-kornyezet-kialakitasa.html#virtualis-kornyezet-kialakitasa) az új Bottle projektünknek. Miután aktiváltuk a **workon** paranccsal, telepítsük fel a Bottle-t a pip segítségével (vagy az is megoldás, ha egyszerűen bemásoljuk a bottle.py állományt a projekt könyvtárunkba):

```bash
pip install bottle
```

### Az első webalkalmazás

Az első webalkalmazásunkat tulajdonképpen néhány sornyi kóddal létre is tudjuk hozni. Lépjünk be a könyvtárunkba és hozzunk létre egy **app.py** nevű állományt. Győződjünk meg róla, hogy a **bottle.py** megtalálható a fájlunk mellett. Írjuk meg az első kódrészletünket:


```python
from bottle import route, run

@route('/hello')
@route('/hello/<user>')
def hello(user = 'World'):
    return 'Hello %s' % user

run(host='0.0.0.0', port=8080, debug=True)
```

Mentsük el az állományt, majd adjuk ki a következő parancsot:

```bash
python app.py
```

Ha mindent jól csináltunk, akkor a következő üzenet fogad majd minket:

```bash
Bottle v0.13-dev server starting up (using WSGIRefServer())...
Listening on http://0.0.0.0:8080/
Hit Ctrl-C to quit.
```

Most nyissunk egy böngészőt és írjuk be a címsorba a számítógépünk ip címét, adjunk meg a portot és a hello-t:

```bash
http://xxx.xxx.x.x:8080/hello
```

A várt output szerint a böngészőnkben megjelenik a *Hello World* szöveg.

Most pedig adjunk meg a hello után egy nevet is:

```bash
http://xxx.xxx.x.x:8080/hello/Pythonista
```

A fenti után a *Hello Pythonista* szöveget kell visszaadnia a böngészőnknek.

### A route() decorator

A fenti kódrészletben a **route()** decorator feladata, hogy egy függvényt hozzákapcsoljon egy url-hez. A példánkban a /hello útvonalat hozzákötjük a hello() függvényhez. Ezek a **route**-ok az egyik legfontosabb elemei ennek a frameworknek: minden alkalommal, amikor egy url-t meghívunk, lefut a hozzárendelt függvény, ami a visszatérési értékét átadja a böngészőnek.

Ahogy láthatjuk, egy függvényhez több url-t is hozzárendelhetünk nyugodtan, és wildcardokat is alkalmazhatunk ezekben. Az első példánk esetén **statikus route**-ról beszélhetünk, míg a második egy **dinamikus route**, hiszen a *user* változóba bármilyen tetszőleges nevet átadhatunk.

A wildcardok tetszőlegesek lehetnek, de csak az első **/** jelig tartanak, ez biztosítja, hogy akár több wildcard is megadható egy url-hez egyértelműen (például */<action>/<item>*.

### Wildcard szűrők

Lehetőségünk van arra is, hogy szűrőket alkalmazzunk a wildcardjainkon. Ez jól jöhet, akkor, ha mondjuk mindenképpen egy számot várnánk eredményül, vagy ha egy adott reguláris kifejezésnek meg kell, hogy feleljen az url-ünk. Ilyenkor a változónk után egy kettőspontot követően megadjuk, hogy milyen típusú elemet várunk. Néhány példa:

```python
@route('/object/<id:int>')
def show_id(id):
    ...

@route('/show/<name:re:[a-z]+>')
def show_name(name):
    ...
```

A következő bejegyzésben a template-ekkel fogunk megismerkedni.
