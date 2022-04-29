Title: Prímtesztek
Published: 2018-01-15 11:35
Author: molnardenes
Tags: [prime test]
---

A régi blog hagyatékának harmadik bejegyzése:

* * *

Ebben a bejegyzésben a különböző **prímtesztek** Python implementációit fogjuk végigvenni. Prímtesztnek azokat az algoritmusokat nevezzük, amelyek segítségével képesek vagyunk megállapítani egy adott számról, hogy prím-e.

Erre nyilvánvalóan a legegyszerűbb, ha egy ![\inline n](http://latex.codecogs.com/gif.latex?\inline&space;n) szám esetén végigvesszük az összes számot ![\inline 2](http://latex.codecogs.com/gif.latex?\inline&space;2)-től ![\inline n-1](http://latex.codecogs.com/gif.latex?\inline&space;n-1)-ig, és megnézzük, hogy bármelyikkel osztható-e ![\inline n](http://latex.codecogs.com/gif.latex?\inline&space;n). Ha nem, akkor ![\inline n](http://latex.codecogs.com/gif.latex?\inline&space;n) prím, egyébként pedig összetett szám. De nézzük meg most például a ![\inline 20](http://latex.codecogs.com/gif.latex?\inline&space;20) osztóit: ![\inline 2, 4, 5, 10](http://latex.codecogs.com/gif.latex?\inline&space;2,&space;4,&space;5,&space;10). Láthatjuk, hogy a legnagyobb osztója a ![\inline 10](http://latex.codecogs.com/gif.latex?\inline&space;10). Kimondhatjuk, hogy egy ![\inline n](http://latex.codecogs.com/gif.latex?\inline&space;n) szám legnagyobb osztója kisebb vagy egyenlő ![\inline \frac{n}{2}](http://latex.codecogs.com/gif.latex?\inline&space;\frac{n}{2}). Még továbbgondolva: ![\inline 100 = 2*50 = 4*25 = 5*20 = 10*10 = 20*5 = 25*4 = 50*2](http://latex.codecogs.com/gif.latex?\inline&space;100&space;=&space;2*50&space;=&space;4*25&space;=&space;5*20&space;=&space;10*10&space;=&space;20*5&space;=&space;25*4&space;=&space;50*2). Amint elértük a ![\inline 10](http://latex.codecogs.com/gif.latex?\inline&space;10)-et, ami ![\inline \sqrt{100}](http://latex.codecogs.com/gif.latex?\inline&space;\sqrt{100}), a tényezők ugyanazok, csak helyet cseréltek. Így megállapíthatjuk, hogy elégséges ![\inline \sqrt{n}](http://latex.codecogs.com/gif.latex?\inline&space;\sqrt{n})-ig vizsgálni az osztókat, az ennél nagyobbakat kizárhatjuk, csakúgy, mint ![\inline 2](http://latex.codecogs.com/gif.latex?\inline&space;2) többszöröseit.

Prímek megtalálására egy lehetséges módszer az **Eratoszthenész szitája** néven ismert eljárás. Ennek lényege, hogy felírjuk a számokat ![\inline 2](http://latex.codecogs.com/gif.latex?\inline&space;2)-től ![\inline n](http://latex.codecogs.com/gif.latex?\inline&space;n)-ig, majd aláhúzzuk a ![\inline 2](http://latex.codecogs.com/gif.latex?\inline&space;2)-t, többszöröseit pedig áthúzzuk. Ezután aláhúzzuk a ![\inline 3](http://latex.codecogs.com/gif.latex?\inline&space;3)-at, többszöröseit pedig áthúzzuk, és így tovább. Az áthúzott számok összetettek, míg az aláhúzottak a prímszámok lesznek:

```python
import math
def sieve_of_eratosthenes(n):
    "n-nél kisebb prímeket adja vissza"
    sieve = [True] * n
    sieve[0] = False
    sieve[1] = False

    for i in range(2, int(math.sqrt(n)) + 1):
        p = i * 2
        while p < n:
            sieve[p] = False
            p += i
    primes = []
    for i in range(n):
        if sieve[i] == True:
            primes.append(i)
    return primes
```

A kódunk elején létrehozunk egy True értékeket tartalmazó listát, majd a ![\inline 0](http://latex.codecogs.com/gif.latex?\inline&space;0)-t és ![\inline 1](http://latex.codecogs.com/gif.latex?\inline&space;1)-et hamisra állítjuk, mivel nem prímek. Ezután végigiterálunk ![\inline 2](http://latex.codecogs.com/gif.latex?\inline&space;2)-től ![\inline \sqrt{n}](http://latex.codecogs.com/gif.latex?\inline&space;\sqrt{n}) -ig. A ![\inline p](http://latex.codecogs.com/gif.latex?\inline&space;p) változónk fogja tartalmazni az ![\inline i](http://latex.codecogs.com/gif.latex?\inline&space;i) szorzatait (pl.: ![\inline 2*2, 2*3, 2*4, 2*5](http://latex.codecogs.com/gif.latex?\inline&space;2*2,&space;2*3,&space;2*4,&space;2*5), stb.), és ezeket mindet hamisra állítjuk. A végén nincs más dolgunk, mint ismét végigiterálni a listán, és az igaz indexeket hozzáadni egy másik listához, ami a prímeket fogja így tartalmazni.

* * *

A legnépszerűbb prímtesztek valószínűségelméleti tesztek. A vizsgálni kívánt ![\inline n](http://latex.codecogs.com/gif.latex?\inline&space;n) szám mellett használnak egy másik, véletlenszerűen választott ![\inline a](http://latex.codecogs.com/gif.latex?\inline&space;a) számot. Ezek az eljárások többnyire azt vizsgálják, hogy egy szám összetett-e, és ha nem, akkor prím. A hiba lehetőségét úgy csökkentik, hogy a tesztet több szabadon választott ![\inline a](http://latex.codecogs.com/gif.latex?\inline&space;a) értékkel is elvégzik.

A legegyszerűbb valószínűségelméleti módszer a **Fermat-prímteszt**, amelynek alapját a **kis Fermat-tétel** adja. Eszerint, ha ![\inline n](http://latex.codecogs.com/gif.latex?\inline&space;n) prímszám és ![\inline a \in \mathbb{Z}](http://latex.codecogs.com/gif.latex?\inline&space;a&space;\in&space;\mathbb{Z}) egy e prímhez relatív prím, akkor: ![\inline a^{n-1} \equiv 1\; (mod\;n)](http://latex.codecogs.com/gif.latex?\inline&space;a^{n-1}&space;\equiv&space;1\;&space;(mod\;n)) :

```python
def fermat(n):
    "ha n prím, igaz értékkel tér vissza"
    if n == 2:
        return True
    if not n & 1:
        return False
    return pow(2, n-1, n) == 1
```

A fenti kongruencia prímek esetében biztosan fennáll, összetett számok esetén csak ![\inline \frac{1}2{}](<http://latex.codecogs.com/gif.latex?\inline&space;\frac{1}{2}>) valószínűséggel teljesül. A fennmaradó kivételes esetekben ![\inline n](<http://latex.codecogs.com/gif.latex?\inline&space;n>)-et **univerzális álprímnek** nevezzük.

* * *

A **Solovay-Strassen teszt** használatával egy adott _n_ számról a következő módon tudjuk eldönteni, hogy prímszám-e. Választunk egy véletlenszerű _![\inline a](http://latex.codecogs.com/gif.latex?\inline&space;a)_ számot, amelyre igaz, hogy ![\inline 0<a<n](http://latex.codecogs.com/gif.latex?\inline&space;0<a<n). Ha ![\inline lnko(a, n) > 1](<http://latex.codecogs.com/gif.latex?\inline&space;lnko(a,&space;n)&space;>&space;1>), akkor _![\inline n](http://latex.codecogs.com/gif.latex?\inline&space;n)_ összetett szám. Ha nem nagyobb, akkor kiszámítjuk egyrészt ![\inline a^{\frac{n-1}{2}}](http://latex.codecogs.com/gif.latex?\inline&space;a^{\frac{n-1}{2}}) _![\inline n](http://latex.codecogs.com/gif.latex?\inline&space;n)_-nel vett legkisebb abszolút értékű maradékát, másrészt a ![\inline \left ( \frac{a}{n} \right )](<http://latex.codecogs.com/gif.latex?\inline&space;\left&space;(&space;\frac{a}{n}&space;\right&space;)>) értékét, ami nem más, mint a Jacobi-szimbólum (ami pedig a példakódunkban használt Legendre-szimbólum általánosítása összetett számokra). Ha ![\inline n](http://latex.codecogs.com/gif.latex?\inline&space;n) prím, akkor a két érték megegyezik. Ha ![\inline n](http://latex.codecogs.com/gif.latex?\inline&space;n) összetett, akkor legfeljebb ![\inline \frac{1}2{}](http://latex.codecogs.com/gif.latex?\inline&space;\frac{1}{2}) annak a valószínűsége, hogy véletlen ![\inline a](http://latex.codecogs.com/gif.latex?\inline&space;a)-ra ez a két érték megegyezik. Ezért sokszor ismételjük a fenti próbát véletlenszerűen választott ![\inline a](http://latex.codecogs.com/gif.latex?\inline&space;a) értékekkel. Ha a két szám akár csak egyszer is eltér, akkor biztosak lehetünk benne, hogy _![\inline n](http://latex.codecogs.com/gif.latex?\inline&space;n)_ összetett. Ha viszont mindig megegyeznek, akkor igen nagy valószínűséggel prím.

```python
def solovay_strassen(n, k=10):
    "ha n prím igaz értékkel tér vissza"
	if n == 2:
		return True
	if not n & 1:
		return False

	def legendre(a, p):
		if p < 2:
			raise ValueError('p nem lehet 2-nél kisebb!')
		if (a == 0) or (a == 1):
			return a
		if a % 2 == 0:
			r = legendre(a / 2, p)
			if p * p - 1 & 8 != 0:
				r *= -1
		else:
			r = legendre(p % a, a)
			if (a - 1) * (p - 1) & 4 != 0:
				r *= -1
		return r

	for i in xrange(k):
		a = randrange(2, n - 1)
		x = legendre(a, n)
		y = pow(a, (n - 1) / 2, n)
		if (x == 0) or (y != x % n):
			return False

return True
```

* * *

Az eddigieknél egy hatékonyabb módszer a **Miller-Rabin prímteszt.** Tetszőlegesen válasszunk egy ![\inline n](http://latex.codecogs.com/gif.latex?\inline&space;n) páratlan számot, és egy ![\inline 1<a<n](http://latex.codecogs.com/gif.latex?\inline&space;1<a<n), ![\inline a\in \mathbb{N}](http://latex.codecogs.com/gif.latex?\inline&space;a\in&space;\mathbb{N}) számot. Ha ![\inline (a,n) \neq 1](<http://latex.codecogs.com/gif.latex?\inline&space;(a,n)&space;\neq&space;1>), akkor ![\inline n](http://latex.codecogs.com/gif.latex?\inline&space;n) összetett, ha ![\inline (a,n) = 1](<http://latex.codecogs.com/gif.latex?\inline&space;(a,n)&space;=&space;1>), akkor állítsuk elő ![\inline (n-1)](http://latex.codecogs.com/gif.latex?\inline&space;(n-1))-et az ![\inline n-1 = 2^s r](http://latex.codecogs.com/gif.latex?\inline&space;n-1&space;=&space;2^s&space;r) alakban, ahol ![\inline r](http://latex.codecogs.com/gif.latex?\inline&space;r) páratlan. Következő lépésben teszteljük az ![\inline a^{2^s r} \equiv 1\; (mod\: n)](http://latex.codecogs.com/gif.latex?\inline&space;a^{2^s&space;r}&space;\equiv&space;1\;&space;(mod\:&space;n)) kongruencia teljesülését, majd kezdjünk gyökvonások sorozatába! Az első gyökvonás után a következő lehetőségeink vannak:

*   Ha ![\inline a^{2^{s-1} r} \not\equiv \pm 1\; (mod\: n)](http://latex.codecogs.com/gif.latex?\inline&space;a^{2^{s-1}&space;r}&space;\not\equiv&space;\pm&space;1\;&space;(mod\:&space;n)), akkor ![\inline n](http://latex.codecogs.com/gif.latex?\inline&space;n) összetett.
*   Ha ![\inline a^{2^{s-1} r} \equiv 1\; (mod\: n)](http://latex.codecogs.com/gif.latex?\inline&space;a^{2^{s-1}&space;r}&space;\equiv&space;1\;&space;(mod\:&space;n)), akkor végezzünk újabb gyökvonást!
*   Ha ![\inline a^{2^{s-1} r} \equiv -1\; (mod\: n)](http://latex.codecogs.com/gif.latex?\inline&space;a^{2^{s-1}&space;r}&space;\equiv&space;-1\;&space;(mod\:&space;n)), akkor ![\inline a](http://latex.codecogs.com/gif.latex?\inline&space;a)tanúsítja ![\inline n](http://latex.codecogs.com/gif.latex?\inline&space;n) prímségét. 

A gyökvonásokat addig folytatjuk, amíg lehetséges. Ha a gyökvonások végén ![\inline a^r \equiv 1 \: (mod \:n)](http://latex.codecogs.com/gif.latex?\inline&space;a^r&space;\equiv&space;1&space;\:&space;(mod&space;\:n)) kongruencia teljesül, akkor is azt mondjuk, hogy ![\inline a](http://latex.codecogs.com/gif.latex?\inline&space;a) tanúskodik ![\inline n](http://latex.codecogs.com/gif.latex?\inline&space;n) prímsége mellett.

```python
import random


def miller_rabin(n, k=10):
    "ha n prím igaz értékkel tér vissza"

    s = n - 1
    t = 0
    while s % 2 == 0:
        s = s // 2
        t += 1

    for trials in range(k):
        a = random.randrange(2, n - 1)
        v = pow(a, s, n)
        if v != 1:
            i = 0
            while v != (n - 1):
                if i == t - 1:
                    return False
                else:
                    i = i + 1
                    v = (v ** 2) % n
    return True
```

Az előzőektől eltérő módon, egy ![\inline n](http://latex.codecogs.com/gif.latex?\inline&space;n) összetett szám esetén, a Miller–Rabin prímtesztben választható ![\inline a](http://latex.codecogs.com/gif.latex?\inline&space;a) értékek legfeljebb az ![\inline \frac{1}{4}](http://latex.codecogs.com/gif.latex?\inline&space;\frac{1}{4})-e tanúskodik ![\inline n](http://latex.codecogs.com/gif.latex?\inline&space;n) prímsége mellett. Ez azt jelenti, hogy ![\inline k](<http://latex.codecogs.com/gif.latex?\inline&space;k>) teszt elvégzése után ![\inline \left ({\frac{1}{4}} \right )^k](<http://latex.codecogs.com/gif.latex?\inline&space;\left&space;({\frac{1}{4}}&space;\right&space;)^k>) annak valószínűsége, hogy nem prímre leltünk.
