Title: Flask tutorial 2. rész Blueprintek és a Jinja2
Published: 2017-09-12 11:48
Author: molnardenes
Tags: [flask]
---


A Flask tutorial második részében a **blueprint**ekkel és a **Jinja2** template-ekkel fogunk megismerkedni.

### Blueprint

A tutorial [előző részében](http://molnardenes.com/blog/flask-tutorial-1-resz-alapok.html#flask-tutorial-1-resz-alapok) a route-unkat a create_app függvénybe helyeztük el. Kisebb alkalmazásoknál, ahol összesen néhány route-tal kell számolni, ez még nem is lenne nagyobb probléma. Olyan esetekben viszont, ahol több route-ot is kezelnünk kell, nagyon megnehezíti a kód kezelését.

Képzeljük el, hogy szeretnénk egy részletes admin felületet létrehozni, vagy esetleg minden felhasználó saját user page-et kapna. Ez végeredményben oda vezetne, hogy minden állományunk egy könyvtárba lenne ömlesztve és a create_app függvényünkben közel száz route kapna helyet, meg lenne többszáz statikus fájlunk.

Ilyenkor jönnek képbe a **blueprint**ek, amiknek a segítségével komponensekre bonthatjuk az alkalmazásunkat. Fontos megemlíteni, hogy ezek a blueprintek nem önálló alkalmazások, önállóan nem képesek futni, regisztrálnunk kell őket a valódi Flask alkalmazásban.

Mielőtt ezt megtennénk, hozzuk létre a szükséges könyvtárakat:

```bash
└───my_blog
    ├───my_blog
    ├───config    
    ├───instance
    ├───blueprints
    │   └───page
    │       └───templates
    │           └───page
    ├───static
    └───templates
        └───layouts
```
A static mappába fogjuk tárolni például a képeket, css-t, scripteket vagy éppen a fontokat. Itt jó szokás lehet, ha a külső asseteket egy **vendor** nevű almappában tároljuk érintetlenül, így mindig meg fogjuk tudni különböztetni a sajátjainktól ezeket, és ha ezek update-jére kerül a sor, akkor biztosak lehetünk benne, hogy nem veszítünk el semmi módosítást, amit végeztünk a fájlokon.

A templates mappába kerülnek a **template**-ek értelemszerűen. Célszerű az egy-egy **blueprint**hez tartozó template-eket az adott blueprinthez tartozó mappában külön tárolni, mintha külön álló package-ekről lenne szó. Ez csak szokás kérdése, a **Flask** ebben rugalmasan viselkedik. Ha valakinek az a kézenfekvőbb, akkor tarthatja az összes template-et egy mappában.

A blueprints mappában található **page** blueprint fogja képezni az alkalmazás statikus oldalainak (például felhasználási feltételek) alapját.

Ahhoz, hogy egy blueprintet használjunk, szükséges regisztrálni a Flask alkalmazásban. Módosítsuk ennek megfelelően az **app.py** állományunkat:

```python
from flask import Flask
from my_blog.blueprints.page import page

def create_app():
    """
    Létrehoz egy Flask alkalmazást a Flask application pattern használatával

    :return: Flask app
    """
    app = Flask(__name__, instance_relative_config=True)

    app.config.from_object('config.settings')
    app.config.from_pyfile('settings.py', silent=True)

    app.register_blueprint(page)
    
    return app
```

Rögtön észrevehető, hogy eltűnt az app.route decorator a create_app függvényből, és helyette beregisztráltuk a fentebb beimportált **page blueprint**et. Nem kell megijedni, nem mondtuk örökre búcsút a route-nak, ugyanis az átkerült a page-be.

A **my_blog/blueprints/page** mappában hozzunk létre egy **\_\_init\_\_.py** és egy **views.py** nevű állományt. Az init arra szolgál, hogy a Flask **package**-ként tekintsen a mappára.

**\_\_init\_\_.py**

```python
from my_blog.blueprints.page.views import page.
```

**views.py**

```python
from flask import Blueprint

page = Blueprint('page', __name__, template_folder='templates')

@page.route('/')
def index():
    return 'Hello from Blueprint!'
```

A views.py állományban először beimportáljuk a Blueprintet, majd létre is hozunk egyet. A Blueprint osztálynak három argumentumot adtunk át:

1. a blueprint neve
2. a második argumentum az, ami igazán fontos, ugyanis ez az **import_name**. Ennek legyen ugyanaz a neve, mint a package-ünknek (vagyis page), ugyanis a Flask ezt az import name-t használja amikor a blueprint template mappáját keresi, vagy különböző fájlokat és objektumokat a blueprintből
3. a template mappa elérési útvonala

Megfigyelhetjük, hogy itt a **route**-ot már nem az apphoz, hanem a **blueprint**hez rendeltük hozzá.

### Jinja2

A **Jinja2** egy modern template-ing nyelv Pythonhoz, amelynek lényege, hogy változókat és minimális programozási logikát is tartalmazhatnak, ezek azonban HTML-lé (vagy éppen PDF-fé) renderelés előtt felveszik valódi értéküket. A változók és a programozási logika tagek közé kerülnek. A Jinja esetén például Python kódokat a **{% ... %}** tagek között tudunk használni, míg változókat a **{{  ... }}** tagek közé tudunk berakni. A Jinja template-ek tulajdonképpen **html** állományok, amelyek szokás szerint a **/templates** mappákban találhatók a Flask projektekben.

#### Template inheritance

A Jinja2 template-ek egy nagyon hasznos tulajdonsága, hogy képesek öröklődést használni. Ez nagyon hasznos abból a célból, hogy az alkalmazás minden oldalának a felépítése ugyanolyan legyen, így egységesebb képet mutathat. Ezt az **{% extends %}** és a **{% block %}** tagekkel fogjuk tudni elérni.

Gondoljuk csak végig, hogy ahogy az alkalmazásunk egyre nő, egyre több és több template-et kell hozzáadnunk, és ezek mindegyikében lesz közös kód (navigation bar, Javascript könyvtárak, CSS-ek, stb.). Ezeket valahogy szinkronban kell tartani, ami hatalmas munka lenne. Ilyenkor jön képbe az inheritance, ahol ezeket a közös részeket egy szülő template-be rakunk, ahol elég egyszer kezelni őket és az összes gyermek template megörökli innen a beállításokat. Lehetőleg minden ismétlődő kódot célszerű a template-ekbe elhelyezni..

Készítsük el most az alkalmazásunk alap sablonját, ami a **my_blog/templates/layouts/base.html** állományban lesz:

```html
<!DOCTYPE html>
<html>
  <head>    
    <meta name="description"
          content="{% block meta_description %}{% endblock %}">

    <title>{% block title %}{% endblock %}</title>    
  </head>
  <body>
    <nav class="navbar navbar-default navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">          
          <a href="{{ url_for('page.index') }}">
            <img src="{{ url_for('static', filename='images/my_blog.jpg') }}"
                 class="img-responsive"
                 width="229" height="50" title="My Blog" alt="My Blog"/>
          </a>
        </div>
      </div>
    </nav>

    <main class="container">
      <div class="md-margin-top">{% block heading %}{% endblock %}</div>
      {% block body %}{% endblock %}
    </main>

    <footer class="footer text-center">
      <div class="container">
        <ul class="list-inline">
          <li class="text-muted">My Blog &copy; 2017</li>
          <li><a href="{{ url_for('page.contact') }}">Contact</a></li>
          <li><a href="{{ url_for('page.terms') }}">Terms of Service</a></li>
        </ul>
      </div>
    </footer>
  </body>
</html>
```

Feltűnhet a korábban említett **{% block %}** tag, ami azt jelzi a Flasknak, hogy az adott blockot a gyermek template fogja kitölteni. Tulajdonképpen a templating engine felé is jelezzük, hogy egy gyermek template felülírhatja ezt a szakaszt.

Ami még feltűnhetett az az **url_for**. Ez tulajdonképpen azt a célt szolgálja, hogy ne kellhen az url-eket hardcode-olni a programba. Logikusan hangzik, hiszen ha egy url-re több helyen is hivatkozunk, és változik később, akkor az összes helyet meg kell keresni, ahol előfordul és mindenhol módosítani. Ez nem hangzik túl jól, sokkal jobban járunk, ha csak egy helyen kell változtatnunk. Ezeket a **my_blog/blueprints/page/views.py** állományban tudjuk beállítani. Ezzel elérjük, hogy a Flask megkeresse a paramlterként átadott függvényt és az ahhoz tartozó url-t:

```python
from flask import Blueprint, render_template

page = Blueprint('page', __name__, template_folder='templates')

@page.route('/')
def index():
    return render_template('page/index.html')

@page.route('/terms')
def terms():
    return render_template('page/terms.html')
```

A **render_template** függvény megjeleníti a paraméterként átadott template-et.

Nézzük, hogyan tudunk egy gyermek template-et létrehozni a szülőből:

**my_blog/blueprints/page/templates/page/index.html**

```html
{% extends 'layouts/base.html' %}

{% block title %}This is my blog{% endblock %}
{% block meta_description %}My blog about my Flask and Jinja2 templating engine{% endblock %}

{% block body %} 
 
 ...
 
{% endblock %}
```


```html
{% extends 'layouts/base.html' %}

{% block title %}Terms of Service{% endblock %}
{% block heading %}<h2>Terms of Service</h2>{% endblock %}

{% block body %}

...

{% endblock %}
```

Az **{% extends %}** jelzi a Jinja-nak, hogy ez a template kibővíti egy másik template-et, jelen esetben a base.html-t. Tulajdonképpen egy linket hoz létre a két template között. A **{% block title %}** segítségével minden template-ünknek saját nevet adhatunk, így mindig tudni fogja felhasználó, hogy éppen melyik oldalon áll.

Ebben a bejegyzésben megismerkedhettünk a blueprintekkel és Jinja2 template nyelvvel. 

