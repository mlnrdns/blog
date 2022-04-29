Title: Flask tutorial 1. rész Alapok
Published: 2017-09-09 11:48
Author: molnardenes
Tags: [flask]
---


A Flask egy egyszerű, könnyen használható Python microframework, ami kiválóan alkalmas skálázható és biztonságos webalkalmazások készítésére.


A webprogramozással ismerkedők számára több okból is nagyszerű választás:

* könnyedén "működésre bírható"
* aktív közösség áll mögötte
* elég jól dokumentált
* egyszerű és minimalista
* ugyanakkor elég rugalmasan bővíthető, ahogy újabb és újabb igényeink jelennek meg

A többrészes tutorial első részében megismerkedünk a virtuális környezet kialakításával, a Flask telepítésével, elkészítjük az első alkalmazásunkat és megismerkedünk az alapvető konfigurációs beállításokkal. A további részekben a következő területek fogjuk érinteni:

* [Template-ek, blueprint-ek](http://molnardenes.com/blog/flask-tutorial-2-resz-blueprintek-es-a-jinja2.html#flask-tutorial-2-resz-blueprintek-es-a-jinja2)
* Tesztelés
* Bővítmények: Flask-WTF
* Kapcsolódás adatbázishoz: SQLAlchemy
* Session-ök
* Logolás és hibakezelés
* Többnyelvűsítés
* Deploy


### Werkzeug

A Werkzeug egy **WSGI** library, ami arra szolgál, hogy biztosítsa a kommunikációt a http szerver és a Python kód között.

![Werkzeug](/assets/images/werkzeug.png)

Ennek a tutorialnak nem része a Werkzeug részletesebb bemutatása, de a weben nagyon sok információ található róla).

### Jinja2

A Jinja2 egy template engine Pythonhoz. Lehetőséget biztosít a template inheritance-re (ezzel nagyon szépen egységesíthetjük a teljes alkalmazásunk kinézetét), könnyen konfigurálható, Python kód írható benne (feltételes elágazások, for ciklusok). A tutorial következő részében ezzel sokkal részletesebben is meg fogunk ismerkedni.

### Fejlesztői környezet létrehozása

Elsőként hozzunk létre egy [virtuális környezetet](http://molnardenes.com/blog/virtualis-kornyezet-kialakitasa.html#virtualis-kornyezet-kialakitasa) az új Flask projektünknek, majd telepítsük is a Flask-ot:

```bash
mkvirtualenv my_blog
pip install flask
```

Ha kész vagyunk, hozzuk létre a könyvtárszerkezetünket:

```bash
mkdir my_blog\my_blog
```

Állítsuk be, hogy a virtuális környezetünk alapértelmezett könyvtára ez legyen:

```bash
cd my_blog
setprojectdir .
```

Amikor telepítünk egy csomagot, akkor célszerű rögtön létrehozni egy requirements.txt állományt, aminek segítségével a későbbiekben könnyen újratelepíthetjük a teljes virtuális környezetet. Lépjünk be a külső my_blog mappába és adjuk ki a következő parancsot:

```bash
pip freeze > requirements.txt
```

Ezzel beíródtak azok a csomagok ebbe az állományba, amelyeket eddig telepítettünk vagy dependency-ként települtek. A felépítése úgy néz ki, hogy egy sorba kerül a csomag neve, majd két kötőjel és a csomag verziója. Ha nem adunk meg verziószámot, akkor mindig a legújabb csomag kerül telepítésre az adott futtatás esetén. A miénk elvileg így néz most ki:

```bash
click==6.7
Flask==0.12.2
itsdangerous==0.24
Jinja2==2.9.6
MarkupSafe==1.0
Werkzeug==0.12.2
```

Lépjünk be a belső my_blog mappába és hozzunk létre egy **app.py** nevű állományt a következő tartalommal:

### Az első Flask alkalmazás


```python
from flask import Flask


def create_app():
    """
    Létrehoz egy Flask alkalmazást a Flask application pattern használatával

    :return: Flask app
    """
    app = Flask(__name__)

    @app.route('/')
    def index():
        """
        Megjeleníti a Hello from Flask! üzenetet
        """

        return 'Hello from Flask!'
  
    return app

if __name__ == '__main__':
    create_app().run()
```

Mentsük el az állományt, majd adjuk ki a következő parancsot:

```bash
python app.py
```

Ha mindent jól csináltunk, akkor a következő üzenet fogad majd minket:

```bash
* Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Ha most elnavigáluk a böngészőnkben a fenti címre, akkor a **Hello from Flask!** üzenetet kell kapnunk.

A kódunk első sorában beimportáljuk a **Flask**ot. A negyedik sor az, ami igazán érdekes most számunkra. A **create_app()** függvény a **Flask application factory pattern**t valósítja meg. Lényegében ez azt jeleneti, hogy az alkalmazás inicializálását egy metódusba csomagoljuk. Miért jó ez? A következő fejezetben megismerkednük a konfigurációs beállításokkal majd, de már most sejthető, hogy a fejlesztés során más konfigurációs beállításokat kell használnunk, mint éles környezetben:

```python
from my_blog import app
```

Ebben az esetben, ahogy beimportáljuk az **app**ot máris inicializáltuk. Ezután már nem tudjuk változtatni a konfigurációs beállításokat vagy például az adatabáziskapcsolatokat.

```python
from my_blog import create_app

app = create_app(DB_CONN='production')
```

Itt a második példában az importtal csak beimportáltuk a factory-t, és csak a következő sorban történt meg az inicializálás.

Bár a Python nem statikus típuskezelést használ, de a [docstringben](http://molnardenes.com/blog/python-programozas-2-fuggvenyek.html) megadhatjuk a függvény visszatérési értékét, hogy más fejlesztőket ezzel is segítsünk.

A 10. sorban példányosítjuk a Flask alkalmazásunkat. A **\_\_name\_\_** paraméter egy stringet tárol, aminek értéke attól függ, hogy honnan hívjuk meg a függvényt. A mi példánkban, ha parancssorból hívjuk meg, akkor **\_\_main\_\_** lesz az értéke, ha egy másik Python modulból, akkor pedig **my_blog**.

A 12. sorban a **route** decoratorral a Flask példányunkban egy függvényt hozzákapcsolunk egy url-hez. Ezek a **route**-ok az egyik legfontosabb elemei ennek a frameworknek: minden alkalommal, amikor egy url-t meghívunk, lefut a hozzárendelt függvény, ami a visszatérési értékét átadja a böngészőnek. A függvénynek egyből a decorator alatt kell lennie, tetszőlegesen elnevezhető, de célszerű beszédes neveket adni.

A 20. sorban a függvény visszadja a Flask appunkat.

Az utolsó sorban lévő kód, akkor fut le, ha parancssorból futtatjuk az alkalmazásunkat. A **run()** metódus elindítja a Flask saját szerverét, ami folyamatosan fut a háttérben, így az alkalmazás elérhető a böngészőből.

### Konfigurációs beállítások

Hozzuk létre a következő könyvtárszerkezetet és a benne lévő állományokat:

```bash
└───my_blog
    │   requirements.txt
    ├───my_blog
    │       app.py
    │       __init__.py
    ├───config
    │       settings.py
    │       __init__.py
    └───instance
            settings.py
            __init__.py
```


Feltűnhet a sok **\_\_init\_\_.py** fájl. Mire való ez? A Python azokat a könyvtárakat, amelyek tartalmazzák ezt a fájlt, **package**-nek tekinti, így könnyen beimportálhatjuk ezeket egy másik Python állományba. Például az app.py-ből könnyen meghívhatjuk a create_app függvényt egy másik fájlban (mint egy kicsit följebb meg is tettük):

```python
from my_blog import create_app

app = create_app(DB_CONN='production')
```

Ez az init állomány az esetek nagy részében üres, annyi csak a lényeg, hogy ott legyen a mappában ilyen néven.

A config/settings.py és az instance/settings.py állományokba a következő tartalmat tegyük:

**config/settings.py**

```bash
DEBUG=True
```

**instance/settings.py**

```bash
DEBUG=False
```

Most egy kicsit alakítsuk át a create_app függvényünket a következó módon:

```python
def create_app():
    """
    Létrehoz egy Flask alkalmazást a Flask application pattern használatával

    :return: Flask app
    """
    app = Flask(__name__, instance_relative_config=True)

    app.config.from_object('config.settings')
    app.config.from_pyfile('settings.py', silent=True)

    @app.route('/')
    def index():
        """
        Megjeleníti a Hello from Flask! üzenetet
        """

        return 'Hello from Flask!'
    return app
```

Példányosításnál átadtuk a Flasknak, az **instance_relative_config** paramétert. Ez azt eredményezi, hogy a Flask keresni fog egy **instance package**-et, amit ugyanott keres, ahol a \_\_name\_\_ modult, ami ebben az esetben a my_blog ugye. 

Ezután betöltünk egy **config.settings** modult, amit a config könyvtár settings.py állományában keres a Flask, majd pedig betölt egy settings.py fájlt, ami az instance mappában található. A **silent** paraméter itt arra szolgál, hogy a program ne álljon le abban az esetben se, ha nem találná ezt az állományt. Miért van erre szükség? Meg egyáltalán miért van szükség két configra?

A **config.settings** fejlesztési környezetben lesz használva, általában verziókövetéshez is hozzá van adva, így nem tartalmaz érzékeny adatokat. Ezzel szemben az **instance/settings.py** production környezetben használatos, titkos információkat tartalmaz (API-k, e-mail loginek, adatbázis jelszavak), így sosem szabad verziókövetéshez hozzáadni.

Az instance, mivel később fut le, ezért felülírja a configosat. Ezért most a fejlesztés idejére ezt a settings.py fájlt nevezzük át, hogy ne zavarja a munkánkat addig.

A **DEBUG=True** sor a **config.settings**-ben azt eredményezi, hogy hiba esetén a teljes stack trace-t látni fogjuk a terminálban. Emellett tetszőleges kód futtatását is lehetővé teszi a Flask példánnyal kapcsolatban. Nagyon fontos, hogy soha ne használjunk production környezetben debug módot!

A konfigurációs beállításokba tetszőleges értékeket megadhatunk, amikre a következőképpen tudunk hivatkozni:

**config/settings.py**

```bash
HELLO='Hello from Config'
```

**my_blog/app.py**-ban a return 'Hello from Flask!' helyett adjuk át a következőt:

```python
return app.config['HELLO']
```

Arra kell figyelni, hogy a változónevek case-sensitive-ek.

A sorozat következő részében a template-ekkel és blueprintekkel fogunk ismerkedni.
