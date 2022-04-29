Title: Shamir-féle titokmegosztás
Published: 2018-01-12 18:18
Author: molnardenes
Tags: [cryptography]
---

A napokban megtaláltam egy korábbi blogom backupját, ahol néhány érdekesnek tűnő korábbi bejegyzésre bukkantam. Ezek közül az egyik a Shamir-féle titokmegosztás. Sajnos a készítés pillanatában csak magyar nyelvű Excel állt rendelkezésemre, így a függvények neve magyarul szerepel, de könnyen kikereshető a megfelelő angol nyelvű függvény is igény esetén.

* * *

A **Shamir-féle titokmegosztás** egy olyan **kriptográfiai algoritmus**, melynél a titok különböző birtokosok között részekre van osztva, és az eredeti titok visszaállításához néhány vagy az összes titokrész szükséges. Általában nem szerencsés, ha minden résztvevő szükséges a helyreállításhoz, ezért szükséges egy tűréshatár megállapítása (ez könnyen belátható egy egyszerű példán keresztül: ha például kalózok elásnak egy kincset, majd egymás között szétvágják a térképet úgy, hogy minden darab szükséges a kincs megtalálásához, akkor bajba kerülnének, ha az egyikük a tengerbe veszne a térképdarabbal együtt. Ha viszont úgy osztják szét mondjuk heten egymás között, hogy bármelyik négy elégséges hozzá, akkor hárman "nyugodtan" eleshetnek tengeri csaták során, a kincs így is meglesz).

A cél egy ![\inline D](http://latex.codecogs.com/gif.latex?\inline&space;D) adat megosztása ![n](http://latex.codecogs.com/gif.latex?n) darab részre úgy, hogy ![\inline k](http://latex.codecogs.com/gif.latex?\inline&space;k) vagy több ![\inline D_{i}](http://latex.codecogs.com/svg.latex?\inline&space;D_{i}) rész segítségével ![\inline D](http://latex.codecogs.com/svg.latex?\inline&space;D) visszaállítható, ugyanakkor ![\inline k-1](http://latex.codecogs.com/svg.latex?\inline&space;k-1) vagy kevesebb ![\inline D_{i}](http://latex.codecogs.com/svg.latex?\inline&space;D_{i}) rész ismerete alapján ![\inline D](http://latex.codecogs.com/svg.latex?\inline&space;D) nem határozható meg. Ezt a sémát a ![\inline (k, n)](http://latex.codecogs.com/svg.latex?\inline&space;(k,&space;n)) tűrési sémának nevezzük, amennyiben ![\inline k = n](http://latex.codecogs.com/svg.latex?\inline&space;k&space;=&space;n), akkor minden birtokos szükséges a titok visszaállításához.

A titok szétosztásához válasszunk egy ![\inline p](http://latex.codecogs.com/svg.latex?\inline&space;p) prímszámot, ami nagyobb, mint a titok. Ezután ![\inline n](http://latex.codecogs.com/svg.latex?\inline&space;n) részre osztjuk a titkot, amelyből ![\inline k](http://latex.codecogs.com/gif.latex?\inline&space;k) darab lesz szükséges a ![\inline D](http://latex.codecogs.com/gif.latex?\inline&space;D) titok helyreállításához. Véges test (hogy a struktúránk véges test, azt ![\inline p](http://latex.codecogs.com/svg.latex?\inline&space;p) prím mivolta biztosítja) feletti algebrai egyenleteket használunk, vagyis a műveleteket modulus ![\inline p](http://latex.codecogs.com/svg.latex?\inline&space;p) fogjuk elvégezni. A titok megosztásához generálunk egy tetszőleges ![\inline k-1](http://latex.codecogs.com/svg.latex?\inline&space;k-1)-ed fokú polinomot a ![\inline p](http://latex.codecogs.com/svg.latex?\inline&space;p) elemű test felett. Ha tehát egy ![\inline (k, n)](http://latex.codecogs.com/svg.latex?\inline&space;(k,&space;n)) megosztási sémát akarunk definiálni, akkor a polinom a következőképpen néz ki : ![\inline f(x)= D + a_{1}x + a_{2}x^{2}+a_{3}x^{3}+...+a_{{k-1}}x^{{k-1}}](<http://latex.codecogs.com/svg.latex?\inline&space;f(x)=&space;D&space;+&space;a_{1}x&space;+&space;a_{2}x^{2}+a_{3}x^{3}+...+a_{{k-1}}x^{{k-1}}>), ahol az ![a_{i}](http://latex.codecogs.com/svg.latex?a_{i}) véletlenszerűen választott értékek. Határozzuk meg bármelyik ![\inline n](http://latex.codecogs.com/svg.latex?\inline&space;n) pontját, ahol ![\inline i = 1,..., n](http://latex.codecogs.com/svg.latex?\inline&space;i&space;=&space;1,...,&space;n) és ![\inline (i, f(i))](http://latex.codecogs.com/svg.latex?\inline&space;(i,&space;f(i))). Minden birtokos kap egy bemenő értéket és az arra a polinommal kiszámolt értéket. Bármilyen részhalmaza a ![\inline k](http://latex.codecogs.com/svg.latex?\inline&space;k) pároknak meghatározza az együtthatóit a görbeillesztésnek. A titok a ![\inline D](http://latex.codecogs.com/gif.latex?\inline&space;D) konstans.

Az árnyékokat (az árnyék elnevezés onnan ered, hogy egy másik módszerben vetítéssel osztják el a titkot) a polinom ![\inline n](http://latex.codecogs.com/svg.latex?\inline&space;n) különböző pontján kiszámolt helyettesítési értékként kapjuk meg:

![\inline k_{i}= f(x_{i})\: mod\: p.\: (i=1,..,n)](<http://latex.codecogs.com/svg.latex?\inline&space;k_{i}=&space;f(x_{i})\:&space;mod\:&space;p.\:&space;(i=1,..,n)>)

Azaz az első árnyék lehet a polinom értéke az ![\inline x= 1](http://latex.codecogs.com/svg.latex?\inline&space;x=&space;1) helyen, a második árnyék az ![\inline x= 2](http://latex.codecogs.com/svg.latex?\inline&space;x=&space;2) helyen, és így tovább (Ezek az értékek tetszőlegesen választhatók, bármely ![\inline x= 1, 2,..., n](http://latex.codecogs.com/svg.latex?\inline&space;x=&space;1,&space;2,...,&space;n), ahol ![\inline n < p, n\in \mathbb{Z}^+](http://latex.codecogs.com/svg.latex?\inline&space;n&space;<&space;p,&space;n\in&space;\mathbb{Z}^+) megfelelő). Ezzel sikeresen szétosztottuk a titkot.

Nézzünk egy egyszerű példát! Előtte azonban fontos megjegyezni, hogy a valóságban természetesen hatalmas, több ezer jegyű számokkal dolgoznak. A példánk helyreállítási sémája ![\inline (3,5)](http://latex.codecogs.com/svg.latex?\inline&space;(3,5)) lesz, más küszöb alkalmazása esetén értelemszerűen arra kell kiterjeszteni az itt leírtakat.

Excelben a szétosztás meglehetősen egyszerű. Felvesszük a szükséges modulust, ![\inline D](http://latex.codecogs.com/svg.latex?\inline&space;D)-t, az ![\inline a_{i}](http://latex.codecogs.com/svg.latex?\inline&space;a_{i})-ket, valamint az ![\inline x_{i}](http://latex.codecogs.com/svg.latex?\inline&space;x_{i})-ket. A példánkban legyen a titok 11 (C2 cella), a polinom véletlenszerűen választott együtthatói a 7 és a 8 (D2 és E2 cella), a modulusként választott prím pedig 13 (A2 cella). A polinom értékeit vizsgáljuk meg az ![\inline x_{1}...x_{5}](http://latex.codecogs.com/svg.latex?\inline&space;x_{1}...x_{5}) helyeken (B5-B9 cella). Az egyes árnyékokat a ![\inline D+a_{1}*x_{i}+a_{2}*{x_{i}}^2](http://latex.codecogs.com/svg.latex?\inline&space;D+a_{1}*x_{i}+a_{2}*{x_{i}}^2) összeg modulussal képzett maradékaként kapjuk meg:

[![Shamir-féle titokmegosztás (szétosztás)](/assets/images/shamir_szetoszt.png "Shamir-féle titokmegosztás (szétosztás)")](/assets/images/shamir_szetoszt.png)

A titok visszaállításához legalább 3 árnyékra lesz szükségünk. A visszaállítást kétféleképpen fogjuk megcsinálni, elsőként **Gauss-eliminációval**, majd **Lagrange-interpolációval**.

* * *

## Gauss-elimináció

Gauss-eliminációval Excelben a következőképpen néz ki. Elsőként kiválasztunk 3-at a birtokosoknál lévő árnyékok ![\inline (x,f(x))](http://latex.codecogs.com/svg.latex?\inline&space;(x,f(x))) közül, majd megadjuk a ![D](http://latex.codecogs.com/svg.latex?D) és az ![\inline a_{i}](http://latex.codecogs.com/svg.latex?\inline&space;a_{i}) együtthatóit:

[![Gauss-elimináció 1](/assets/images/shamir_gauss1.png)](/assets/images/shamir_gauss1.png)

A Gauss-elimináció következő lépéseiben kiiktatjuk először az ![\inline a_{2}](http://latex.codecogs.com/svg.latex?\inline&space;a_{2})-t, majd az ![\inline a_{1}](http://latex.codecogs.com/svg.latex?\inline&space;a_{1})-et. Ezt többszörös keresztbe szorzásokkal tudjuk megtenni. Elsőként az I egyenlet bal oldalát szorozzuk a II egyenlet ![\inline a_{2}](http://latex.codecogs.com/svg.latex?\inline&space;a_{2}) értékével, majd ebből kivonjuk a II egyenlet baloldalának és az I egyenlet ![\inline a_{2}](http://latex.codecogs.com/svg.latex?\inline&space;a_{2}) értékének szorzatát, és vesszük az eredmény a modulussal vett maradékát. Ezután az I egyenlet ![D](http://latex.codecogs.com/svg.latex?D) értékét szorozzuk a II egyenlet ![\inline a_{2}](http://latex.codecogs.com/svg.latex?\inline&space;a_{2})-jével, majd a II egyenlet ![D](http://latex.codecogs.com/svg.latex?D)-jét az I egyenlet ![\inline a_{2}](http://latex.codecogs.com/svg.latex?\inline&space;a_{2})-jével, és vesszük a modulussal vett maradékot. És így tovább... A képletek relatív hivatkozásokat használnak, így oldalra és lefelé húzva megfelelő értékeket adnak vissza:

[![Gauss-elimináció 2](/assets/images/shamir_gauss2.png)](/assets/images/shamir_gauss2.png)

Az utolsó lépésnél előkerül egy újabb probléma, amire figyelni kell. Az egyenlet bal oldalát a jobb oldal **multiplikatív inverzével** kell szoroznunk. A multiplikatív inverz meghatározása a feladatunkban elég egyszerű, csupán egy 12x12-es szorzótáblát kell létrehozni, és minden esetben a szorzatokat modulus 13 számítjuk. Az egyes számoknak az inverzei azok a számok, amelyekkel a metszéspontjukban 1 az eredmény, mivel két szám akkor inverze egymásnak, ha a szorzatuk 1 (a képlet jobbra és lefelé szintén másolható, mivel vegyes hivatkozásokat használ):

[![Multiplikatív inverz számítása](/assets/images/mult_inverz.png)](/assets/images/mult_inverz.png)

Nincs más hátra, mint a végeredmény meghatározása. A jobb oldal multiplikatív inverzét kikeressük képlettel (INDEX és HOL.VAN kombinációjával), majd ezzel beszorozzuk a bal oldalát, és amint a képen is látszik, visszakaptuk a titkunkat, a 11-et, vagyis a visszafejtés sikerült. Ez bármelyik 3 árnyékkal működni fog:

[![shamir_gauss3](/assets/images/shamir_gauss31.png)](/assets/images/shamir_gauss31.png)

* * *

## Lagrange-interpoláció

A Lagrange-interpoláció is hasonlóan könnyedén elkészíthető Excelben, azonban kicsivel több figyelmességet igényel. Az előző munkafüzetről felhasználjuk a kiindulási értékeket.:

[![Lagrange-interpoláció 1](/assets/images/shamir_lagrange11.png)](/assets/images/shamir_lagrange11.png)

A következő lépésben felveszünk egy 5x5-os segédtáblázatot, és az első sor és első oszlop metszeteinél lévő cellákban a következőképpen vesszük fel az értékeket: Mindenhol a felső sort osztjuk a felső sor és első oszlop különbségével:

[![lagrange_segedtablazat](/assets/images/lagrange_segedtablazat.png)](/assets/images/lagrange_segedtablazat.png)

Ezután a nevező inverzével megszorozzuk a számlálót. Ha a nevező negatív, akkor az abszolútérték inverzét keressük ki, majd vesszük az eredmény ![\inline -1](http://latex.codecogs.com/svg.latex?\inline&space;-1)-szeresét. Képleteket használjunk itt is. Ez egy elég összetett képlet lesz, ugyanis több feltételt is vizsgálnia kell:

*   Ha a nevező ![\inline 0](http://latex.codecogs.com/svg.latex?\inline&space;0), akkor a cellába ![\inline 1](http://latex.codecogs.com/svg.latex?\inline&space;1) kerül
*   Egyébként a cellába a számláló szorozva a nevező inverzével érték kerül
*   Kivéve ha negatív a nevező, mert akkor az abszolútérték inverzét keressük, és vesszük az eredmény ![\inline -1](http://latex.codecogs.com/svg.latex?\inline&space;-1)-szeresét

Az inverzet is függvénnyel számítsuk lehetőleg (INDEX és HOL.VAN egymásba ágyazásokkal):

[![shamir_lagrange2](/assets/images/shamir_lagrange21.png)](/assets/images/shamir_lagrange21.png)

Miután megvan a táblázatunk, létrehozunk egy újat, most egy 3x3-asat, amelyben a felső sor és az első oszlop értékei az általunk választott 3 árnyék x lesz. Függvénnyel (INDEX - HOL.VAN - HOL.VAN) kikeressük az 5x5-ös táblázatból a metszetekben szereplő értékeket.

[![shamir_lagrange3](/assets/images/shamir_lagrange3.png)](/assets/images/shamir_lagrange3.png)

Végül pedig nincs más dolgunk, mint a kiválasztott ![\inline x](http://latex.codecogs.com/svg.latex?\inline&space;x)-ekhez tartozó ![\inline f(x)](http://latex.codecogs.com/svg.latex?\inline&space;f(x))-ekkel beszorozni a táblázatunk soronkénti szorzatát, ennek a nagy szorzatnak venni a modulussal vett maradékát, majd az így kapott értékek összegének modulussal vett maradéka lesz a végeredményünk, vagyis a titok:

[![shamir_lagrange4](/assets/images/shamir_lagrange4.png)](/assets/images/shamir_lagrange4.png)
