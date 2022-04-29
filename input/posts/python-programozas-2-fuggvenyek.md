Title: Python-programozás 2 Függvények
Published: 2012-11-15 22:08
Author: molnardenes
Tags: [alapok]
---

Már rögtön a fejezet elején szükséges megjegyeznünk, hogy a Python a
függvény kifejezést használja mind a valódi függvények, mind az
eljárások jelölésére. Az **eljárás** olyan programkódrészlet, amely
adattranszformációt hajt végre, vagy tevékenységet végez. Használatának
igazi előnye akkor jelentkezik, amikor nagyobb programokban egy-egy
utasítássorozatot többször meg kell hívni. Az eljárások használata
áttekinthetőbbé teszi a programot, és az esetleges hibák javítása is
egyetlen helyen elvégezhető. **Függvénynek** nevezünk egy olyan
alprogramot, aminek eredménye egyetlen jól körülhatárolt érték vagy
objektum. Ezt az értéket vagy objektumot a **függvény visszatérési
értékének** nevezzük. Az eljárás és a függvény közötti pontos
különbséget a későbbiekben tisztázni fogjuk.

Az első bejegyzésben megismerkedtünk a különböző matematikai
operátorokkal. Beláthatjuk azonban, hogy számtalan olyan műveletet
fogunk végrehajtani, amire nincsenek megfelelő operátorok. Ekkor jönnek
a képbe a függvények. Tegyük fel, hogy néhány szám közül szeretnénk
kiválasztani a legkisebbet. Erre nem létezik semmiféle műveleti jel. A
Python rendelkezik úgynevezett **beépített** vagy **előre definiált
függvényekkel**, melyeknek a segítségével könnyedén végrehajthatunk
bonyolultabb műveleteket is. A legkisebb elem kiválasztására használt
függvény a *min* függvény lesz.

![Python
shell](/assets/images/fuggveny1.png)

Ha elkezdjük gépelni a függvényünk nevét, és nyitunk egy zárójelet,
akkor láthatjuk, hogy felugrik egy sárga pop-up doboz. Ez azt a célt
szolgálja, hogy emlékeztesse a programozót arra, hogy a függvény mit
csinál, és milyen argumentumokat vár.

```python
>>> min (19.6, 27.8, 136, 2.1, 8)
2.1
```

A példánkban láthatjuk, hogy a függvények megadása a függvény nevével és
hozzákapcsolt zárójelekkel történik. A zárójelben, vesszővel elválasztva
egymástól, adjuk át a függvénynek az argumentumokat. Jelen esetben a
függvényünk visszatérési értéke *2.1*. Ez azt jelenti, hogy az
argumentumként megadott számok közül ez volt a legkisebb. A függvények
meghívásának általános formája: **függvény_neve (argumentumok)**.

A dir paranccsal kilistáztathatjuk a Python összes beépített függvényét.

```python
>>> dir (__builtins__)
```

------------------------------------------------------------------------

A beépített függvények mellett lehetőségünk van saját függvényeket is
létrehozni. Tegyük is ezt meg! Példánkban létrehozzuk az *getSquare* függvényt,
amely egy adott számot négyzetre emel. Nézzük meg először, hogyan is
kell egy függvényt létrehozni, majd elemezzük ezt ki lépésről lépésre.

```python
>>> def getSquare(x):
		return x**2
```

Függvény létrehozása a **def** kulcsszóval történik. Ezután adjuk meg a
függvény nevét, majd zárójelbe a szükséges paramétereket. Ha egyetlen
paraméter sem szükséges a függvényhez, akkor üres zárójeleket rakunk:
(). Ezután kettőspont következik, majd a következő sort beljebb kezdve
(általánosan elfogadott a 4 space) megírjuk a függvény törzsét. A
**return** kulcsszó megadja a függvény visszatérési értékét, majd kilép
a függvényből. Figyeljük meg, hogy a függvény belseje **indentálva**
(sorok vízszintesen tagolva) van.

Adjuk meg a függvényünk paramétereként a 4-et:

```python

>>> getSquare(4)
16
```

Mikor meghívjuk a függvényt, az x paramétert hozzárendeljük a 4
memóriacíméhez. Ezekből látszik, hogy a függvények meghívása lényegében
egy kifejezés, ezért az eredményünk eltárolható egy változóban. Hozzunk
létre egy *result* változót, tároljuk el benne a függvényünk
végeredményét, majd írjuk ki.

```python
>>> result = getSquare(4)
>>> result
16
```

Most írjunk egy függvényt, ami kiszámítja egy háromszög területét.
Tudjuk, hogy ehhez ismernünk kell a háromszög baseját és az basehoz
tartozó magasságot. Ennek a függvénynek tehát két paramétere lesz.
Lássuk:

```python
>>> def area(base, height):
		return base * height / 2
>>> area (4, 7)\
14.0
```

Az eddig megírt kódjaink elvesznek, ha kilépünk az IDLE-ből. Hogy újra
és újra használhassuk a programjainkat, mentsük el őket egy fájlba. Ezt
a következőképpen tehetjük meg. *File ->>> New Window*. Beírjuk a
kódunkat, majd *File ->>> Save*. Itt megadjuk a kívánt fájlnevet, majd
a kiterjesztést is: *.py*. Ahhoz hogy ezekben a fájlokban lévő
függvényeket használhassuk a shellben, szükséges az előzetes futtatásuk:
*Run ->>> Run Module*.

------------------------------------------------------------------------

Mi a teendők akkor, ha van egy *area* függvényünk, és szeretnénk
megtudni, hogy több háromszög közül melyik a legnagyobb terület.
Természetesen lehetőségünk van arra, hogy egyesével végigvegyük őket, és
így döntsük el, hogy melyik a legnagyobb területű. Ennél azonban sokkal
jobb módszer, ha az *area* függvényre használjuk a *max* függvényt. Ez
tehát egy kiváló példa arra, hogy egy függvényen belül meghívhatunk egy
másik függvényt. Így néz ki tehát:

```python
>>> max (area (8, 6), area (12, 26), area (6, 9))
156.0
```

------------------------------------------------------------------------

A fejezet végén szeretnék néhány tanácsot adni a függvények
létrehozásához. Nézzük meg a következő függvényt:

```python
>>> def area(base, height):
		'''(number, number) ->>> number
		A függvény megadja a base baseú és a height magasságú
		háromszög területét.

		>>> area(4, 6)
		12
		>>> area(12, 44)
		264
		'''
		
		return base * height / 2
```

A három idézőjelen belül minden a függvény úgynevezett **docstringje**,
amely a függvény működését dokumentálja. A docstringet a
függvénydeklaráció utáni sorban kell megadni. Megadása nem kötelező, de
erősen ajánlott. Az IDLE-ben, amikor beírod egy függvény nevét, a hozzá
tartozó docstring első sora megjelenik a buboréksúgóban. Ez megadja
neked, hogy milyen értékeket vár a függvény, és hogy miket fog
visszaadni. A függvény megírásának ajánlott lépései:

-   A docstring első sorában megadjuk a paraméterek és a visszatérési
    érték típusát
-   Ezután egy-két sorban megadjuk, hogy mit csinál pontosan a függvény,
	az érthetség kedvéért magyarul írtam, de szerencsésebb angol nyelven megadni
-   Írunk néhány példát
-   Majd megírjuk a függvény törzsét
-   Végül ajánlott tesztelni a függvényünket

------------------------------------------------------------------------

Miután megismerkedtünk a függvények basejaival, szeretném, ha egy kicsit
jobban megértenénk, hogy mi történik egy  függvény meghívásakor. Milyen
műveletek zajlanak a háttérben, miközben mi egy változóból meghívunk egy
függvényt. Ehhez a korábban definiált *area* függvényt fogjuk
használni.

```python
>>> def area(base, height):
		result = base * height / 2
		return result
>>> triangle_area = area (5, 8)
20.0
```

Amint a Python értelmezi a függvénydefiníciónkat, létrehoz egy *area*
nevű változót, amely tartalmazza a függvényobjektumunk memóriacímét. Ez
az objektum (az objektumokról a későbbiekben lesz szó részletesen)
tárolja az összes információt a függvényről, beleértve a végrehajtandó
kódot, a paramétereket és ha rendelkezik vele, akkor a docstringet is.
Megfigyelhetjük, hogy a *triangle_area* változó nem jött még
létre. Ez majd csak azután történik meg, hogy a hozzárendelés jobb
oldalán lévő függvényhívást végrehajtjuk. Az így létrejött változó fogja
tárolni a visszatérési érték memóriacímét.

![fv1](/assets/images/visualize2.jpg)

A függvényhívás végrehajtásához szükséges az argumentum kiértékelése.
Ekkor két új memóriacím jön létre a szaggatott vonal jobb oldalán.
Emellett létrejön egy új memóriaterület, amely azt tartalmazza, hogy mi
történik, miközben a *area* függvény végrehajtódik.

Amint látjuk, létrejött két új változó, a base és a height, amik az 5
és a 8 értékek memóriacímeire mutatnak. Az szaggatott vonal jobb oldalán
található memóriaterület neve a **heap**. Ez tartalmazza az összes
értéket, amely a program végrehajtása során jön létre. A vonal bal
oldalán lévő memóriaterület neve a **call stack**, vagy **hívási
verem**. Ha meghívunk egy függvényt, egy új **veremterület** (**stack
frame**) jön létre a verem tetején. Amint a függvény visszaad egy
értéket, a veremterület megszűnik, és az irányítást visszaadja a
függvény meghívójának. Minden veremterület saját változókat tárol. A
főterület olyan változókat tartalmaz, amelyek a függvényeken kívül
lettek létrehozva, míg a függvény veremterülete a paramétereket és a
**helyi változókat** (**local variable**) tartalmazza. A helyi
változókat a függvényen belül hozzuk létre, ezek csak a függvényen
belülről érhetők el.

![fv1](/assets/images/visualize3.jpg)

A függvényünkön belül létrehoztunk egy új változót, az resultot. Ez
tartalmazza a 20-as értéket. Nincs más dolgunk ezután, mint végrehajtani
a return utasítást, ami kiértékeli az result változót, melynek az
értéke 20.0 lesz. Ehhez az értékhez az x4 memóriacímet rendeli.

Az alsó ábrán most már láthatjuk, hogy mi lesz a függvényünk
visszatérési értéke. A következő lépéssel elhagyjuk a függvényünket. A
program visszatér oda, ahol a függvényhívás előtt éppen volt.

![fv1](/assets/images/visualize4.jpg)

![fv1](/assets/images/visualize5.jpg)

Visszatértünk tehát oda, ahol meghívtuk a areaet az 5 és a 8
argumentumokkal. Visszakapjuk a 20.0 memóriacímét, és így létrejön az új
változónk a főmodulban, a *triangle_area*. Ez a változó az x4
memóriacímet tárolja.

A *area* függvény veremterülete eltűnt a számítógép memóriájából. Ez
történik, mikor kilépünk egy függvényből. Amint látható, a program
futása során a főmodulban létrehozott változók továbbra is elérhetők, a
függvényben létrehozott lokális változók azonban nem!

![fv1](/assets/images/visualize6.jpg)

------------------------------------------------------------------------

A Python számtalan függvénnyel rendelkezik, amelyeknek nagy része nem
beépített függvény. A függvények modulokban tárolódnak, és jeleznünk
kell a programunkban, ha egy ilyen nem beépített függvényt szeretnénk
használni. Ugyanígy a saját függvényeinket is eltárolhatjuk egy
modulban, amelyből a későbbiekben importálhatjuk a szükséges
függvényeket. Az importálás általános formája: **import modul_név**. A
programon belül, ha használni szeretnénk a beimportált modul egyik
függvényét, azt a következőképpen tehetjük meg:
**modul_név.függvény_név**. Nézzünk is rá egy példát (Hérón-képlet):

```python
>>> import math
>>> def area_heron(a, b, c):
		s = (a + b + c) / 2
		area = math.sqrt (s * (s - a) * (s - b) * (s - c))
		return area\
>>> triangle_area = area_heron (3, 4, 5)
6.0
```
