Title: Tudományos számítások Pythonban NumPy, SciPy alapok
Published: 2019-01-15 11:30
Author: molnardenes
Tags: [numpy, scipy]
---

A régi blog hagyatékából....

* * *

Tudományos számítások Pythonban való elvégzésére két kiváló kiegészítő csomagot találunk. A **NumPy** modullal lehetővé válik a munkavégzés nagy, többdimenziós tömbökkel és mátrixokkal. A **SciPy** tovább bővíti a NumPy funkcionalitását olyan matematikai eljárásokkal, mint integrálás, differenciálegyenletek megoldása, regresszió, Fourier-transzformáció, optimalizáció. Ez a két csomag nem alaptartozéka a Pythonnak, külön telepítendők. Letölthetők innen:

* [NumPy](http://www.numpy.org/)
* [SciPy](https://www.scipy.org/)

A NumPy legfontosabb objektumai a homogén, többdimenziós tömbök. A **tömb** (**array**) olyan adatszerkezet, amely azonos típusú elemek egy sorozatának tekinthető, és elemeit a sorszámukon (index) keresztül érhetjük el. A tömbök tulajdonképpen tekinthetők listáknak a következő megszorításokkal: Minden elemüknek azonos típusúnak kell lenniük (pl. integer, float vagy komplex számok), az elemek számát előre ismerni kell, utólag ez nem módosítható. Ha a tömbelemek több index megadásával címezhetők meg, akkor több dimenziós tömbről beszélünk. Több lehetőségünk is van tömbök létrehozására. Az első módszer egy tömb létrehozása listából vagy tuple-ből az **array** függvénnyel:

```python
import numpy as np
sample_list = [1,2,0,1,1]
sample_array = np.array(lista)
sample_array
array([1, 2, 0, 1, 1])
```

Kétdimenziós tömbök létrehozása a következő módon lehetséges:

```python
two_dimensional = np.array( ((11, 12, 13), (21, 22, 23), (31, 32, 33)) )
two_dimensional
array([[11, 12, 13],
       [21, 22, 23],
       [31, 32, 33]])
```
Háromdimenziós tömbök létrehozására is van lehetőségünk:

```python
haromdimenzios = np.array( ( ((111,112), (121,122) ), ((211,212),(221,222)) ) )
haromdimenzios
array([[[111, 112],
        [121, 122]],

       [[211, 212],
        [221, 222]]])
```

Az **ndim** metódussal megtudhatjuk egy tömbről, hogy hány dimenziós. Pythonban a dimenziót **rangnak** nevezzük. A **shape** metódus megadja a tömb dimenzióit. Egy n sort és m oszlopot tartalmazó tömb <em>n</em> x <em>m</em> dimenziós. A **size** metódus segítségével megkapjuk a tömb elemeinek a számát. Nézzünk ezekre is példát! Az előző kétdimenziós tömbre alkalmazzuk a fenti metódusokat:

```python
two_dimensional.ndim
2
two_dimensional.shape
(3, 3)
two_dimensional.size
9
```

Gyakran előáll olyan helyzet, amikor a tömb elemeit nem ismerjük, de a tömb méretét igen. Szerencsére rendelkezésünkre áll néhány függvény, amelynek a segítségével egy általunk megadott dimenziójú tömböt feltölt 0-kal (**zeros** függvény), 1-ekkel (**ones** függvény), vagy véletlen számokkal (**random** függvény):

```python
np.zeros( (2, 3) )
array([[ 0.,  0.,  0.],
       [ 0.,  0.,  0.]])
np.ones( (2, 2, 3) )
array([[[ 1.,  1.,  1.],
        [ 1.,  1.,  1.]],

       [[ 1.,  1.,  1.],
        [ 1.,  1.,  1.]]])
np.empty( (2, 2) )
array([[  1.74520692e-292,   1.74529866e-292],
       [  6.73539535e-291,   1.67070026e-163]])
```

Jó, ha még két függvényt megjegyzünk, amelyek segítségével tömböket tölthetünk fel. Ezek az **arange**(eleje, vége, lépésszám), valamint a **linspace**(eleje, vége, elemszám):

```python
np.arange(0, 5, 0.5)
array([ 0. ,  0.5,  1. ,  1.5,  2. ,  2.5,  3. ,  3.5,  4. ,  4.5])
np.linspace(0, 5, 8)
array([ 0.        ,  0.71428571,  1.42857143,  2.14285714,  2.85714286,
        3.57142857,  4.28571429,  5.        ])
```


A tömbökkel végzet aritmetikai műveletek elemenként történnek, azaz például összeadás esetén az egyik tömb 1,2 indexű elemét a másik tömb 1,2 indexű elemével adjuk össze. Szorzásnál szintén így működik, viszont a **dot** függvény segítségével a mátrixszorzatukat kapjuk meg (a mátrixokról később lesz még szó):

```python
a = np.array ( [10, 30, 70] )
b = np.array ( [5, 15, 35] )
a - b
array([ 5, 15, 35])
a ** 2
array([ 100,  900, 4900])
a < b array([False, False, False], dtype=bool) a < 30 array([ True, False, False], dtype=bool) x = np.array( [[1,1], [0,1]] )
y = np.array( [[2,0], [3,4]] )
x * y
array([[2, 0],
       [0, 4]])
np.dot(x, y)
array([[5, 4],
       [3, 4]])
```

<hr />

A tömbök szeletelhetők, elemei indexeik alapján hozzáférhetők és elemeiken végigiterálhatunk. Az egydimenziós tömbök esetén ez lényegében megegyezik a listáknál tanult móddal. Többdimenziós tömbök esetén dimenziónként rendelkeznek egy-egy indexszel, amiket tuple-ként tudunk megadni. Nézzünk ezekre néhány példát:

```python
sample_array = np.array( ((0, 1, 2, 3), (10, 11, 12, 13), (20, 21, 22, 23), (30, 31, 32, 33), (40, 41, 42, 43)) )
sample_array
array([[ 0,  1,  2,  3],
       [10, 11, 12, 13],
       [20, 21, 22, 23],
       [30, 31, 32, 33],
       [40, 41, 42, 43]])
sample_array[4, 2]
42
sample_array[0:5, 3]
array([ 3, 13, 23, 33, 43])
sample_array[2:4, : ]
array([[20, 21, 22, 23],
       [30, 31, 32, 33]])
```

A <em>sample_array[4, 2]</em> esetén a 4-es indexű sor 2-es indexű oszlopának elemét kapjuk meg. A <em>sample_array[0:5, 3]</em> a 0. indexű sortól a 4. indexű sorok 3. indexű elemeit jelenti. A sample_array<em>[2:4, : ]</em> pedig a 2-3. sor összes elemét adja meg. Többdimenziós tömbök esetén az iterálás az első dimenzió, vagyis a sor (ez az első index) alapján történik. Ha elemenként szeretnénk végigiterálni, akkor a <strong>flat</strong> attribútum használata a megoldás:

```python
for sor in sample_array:
    print(sor)

[0 1 2 3]
[10 11 12 13]
[20 21 22 23]
[30 31 32 33]
[40 41 42 43]

for elem in sample_array.flat:
    print(elem, end=' ')

0 1 2 3 10 11 12 13 20 21 22 23 30 31 32 33 40 41 42 43
```

Lehetőségünk van arra is, hogy egy tömb dimenzióit megváltoztassuk. Ezt több módon is megtehetjük. Az egyik lehetséges formája: sample_array.<strong>shape</strong> = (x,y). Ekkor egy x * y dimenziójú tömböt hozunk létre a sample_array tömbünkből. Ha nem szeretnénk végleg megváltoztatni a tömböt, akkor a <strong>reshape</strong> függvényt kell használnunk, ez ugyanis az argumentumként megadott dimenzióval adja vissza a tömbünket anélkül, hogy azt véglegesen megváltoztatná. Harmadik módszer a <strong>resize</strong> függvény, amely végérvényesen megváltoztatja a tömböt. A <strong>transpose</strong> függvénnyel pedig megcserélhetjük a tömb dimenzióit. Lássuk őket gyakorlatban:

```python
sample_array.shape = (2, 10)
sample_array
array([[ 0,  1,  2,  3, 10, 11, 12, 13, 20, 21],
       [22, 23, 30, 31, 32, 33, 40, 41, 42, 43]])
sample_array.resize(5, 4)
sample_array
array([[ 0,  1,  2,  3],
       [10, 11, 12, 13],
       [20, 21, 22, 23],
       [30, 31, 32, 33],
       [40, 41, 42, 43]])
sample_array.reshape(10, 2)
array([[ 0,  1],
       [ 2,  3],
       [10, 11],
       [12, 13],
       [20, 21],
       [22, 23],
       [30, 31],
       [32, 33],
       [40, 41],
       [42, 43]])
sample_array
array([[ 0,  1,  2,  3],
       [10, 11, 12, 13],
       [20, 21, 22, 23],
       [30, 31, 32, 33],
       [40, 41, 42, 43]])
sample_array.transpose()
array([[ 0, 10, 20, 30, 40],
       [ 1, 11, 21, 31, 41],
       [ 2, 12, 22, 32, 42],
       [ 3, 13, 23, 33, 43]])
sample_array
array([[ 0,  1,  2,  3],
       [10, 11, 12, 13],
       [20, 21, 22, 23],
       [30, 31, 32, 33],
       [40, 41, 42, 43]])
```

<hr />
Mind a NumPy, mind a SciPy rendelkezik saját lineáris algebrai modullal. Azok tárgyalása egy későbbi poszt témája lesz, ebben a posztban a lineáris algebrai alapokról lesz szó. A NumPy segítségével könnyen össze tudunk adni két vektort. Matematikai tanulmányainkból tudjuk, hogy, ha adott két vektor, akkor az egyik vektor végpontjából indítjuk a másik vektort, és így az első kezdőpontjából a második végpontjába mutató vektort a két vektor összegvektorának nevezzük. Két vektor különbségét úgy kapjuk meg, hogy az egyikhez hozzáadjuk a másik ellentettjét. Ha az <em>a</em> és <em>b</em> vektort egy pontból indítjuk, akkor a <em>b</em> végpontjából az <em>a</em> végpontjába mutató vektor az <em>a-b</em> vektor. Ugyanilyen könnyen kiszámíthatjuk két vektor skaláris szorzatát (dot product), ami nem más, mint a két vektor hosszának és az általuk közbezárt szög koszinuszának szorzata: 

![image](/assets/images/vectors1.gif)

 De ugyanilyen egyszerű két vektor vektoriális szorzatának (cross product) a kiszámítása is, ahol az eredményvektor abszolútértéke a két vektor hosszának és a közbezárt szögük szinuszának szorzata, állása merőleges mind a-ra, mind b-re, valamint iránya olyan, hogy az a, b és c jobbsodrású vektorrendszert alkot: 
 
![image](/assets/images/vectors2.gif)

 Részletesen lásd a vektoriális szorzatról szóló Wikipedia <a href="http://hu.wikipedia.org/wiki/Vektori%C3%A1lis_szorzat" title="Vektoriális szorzat a Wikipedián">szócikket</a>! Nézzük ezeket NumPy-jal:</p>

```python
x = np.array([2, 6])
y = np.array([4, 1])
z = x + y
z
array([6, 7])
v = x - y
v
array([-2,  5])
x = np.array([-2, 3, 5])
y = np.array([-6, 7, 9])
np.dot(x,y)
78
x = np.array([0,0,1])
y = np.array([1,0,0])
np.cross(x,y)
array([0, 1, 0])
np.cross(y,x)
array([ 0, -1,  0])
```

Végül néhány megjegyzést tegyünk a mátrixokról is. A NumPy rendelkezik beépített <strong>matrix</strong> osztállyal, ami az <em>array</em> egy alosztálya, megörökli annak minden attribútumát és metódusát. A különbség az, hogy a mátrixok kifejezetten kétdimenziósak szemben az akárhánydimenziós tömbökkel. Általában szerencsésebb mátrixok esetén is a tömböket használni, de erről egy későbbi bejegyzésben lesz részletesen szó. Előnye a <em>matrix</em> osztálynak, hogy nem szükséges a <em>dot</em> függvény használata, ugyanis a <em>*</em> jel is mátrixszorzatot fog eredményezni:

```python
x = np.array( ((7,4), (8, 5)) )
y = np.array( ((1,3), (-1, 5)) )
x * y
array([[ 7, 12],
       [-8, 25]])
np.dot(x, y)
array([[ 3, 41],
       [ 3, 49]])
x = np.matrix( ((7,4), (8, 5)) )
y = np.matrix( ((1,3), (-1, 5)) )
x * y
matrix([[ 3, 41],
        [ 3, 49]])
```
