Title: Python-programozás 3 Adattípusok - bool, string
Published: 2012-12-01 00:43
Author: molnardenes
Tags: [datatypes, alapok]
---

Az első bejegyzésben foglalkoztunk két **numerikus adattípussal**,
nevezetesen az **integerrel** és a **floattal**. Annyi kiegészítést
teszek a hozzá, hogy ezek **immutábilisek**, vagyis létrejöttük után nem
változhat az értékük. E kettő mellett a numerikus adattípusok közé
soroljuk az úgynevezett **booleant**, vagy **bool** típust, aminek a
visszatérési értéke a logikai igaz vagy hamis. Nézzünk rá néhány példát:

```python
>>> 9 > 3
True
>>> 6 + 5 == 11
True
>>> 4 * 2 < 2
False
>>> 5 == 6
False
>>> x = 3.8
>>> y = 4.8
>>> x <= y
True
>>> 4 != 2 + 2
False
```

Mint ahogyan az integer és a float típusokkal használtunk operátorokat,
ugyanígy a boolean típusnak is megvannak a saját operátorai. Ezek a
következők: **not**, **or**, **and**. Néhány példát nézzük rájuk:

```python
>>> not (9 > 3)
False
>>> not not (9 > 3)
True
>>> x = 60
>>> y = 50
>>> (x >= 60) and (y <= 50)
True
>>> (x >= 60) and (y <= 30)
False
>>> (x >= 60) or (y <= 30)
True
>>> not x >= 60 or y <= 50
True
>>> not (x >= 60 or y <= 50)
False
>>> (not x >= 60) or y <= 50
True
```

A három operátor precedenciális sorrendje a következő: 1. not, 2. and,
3. or. Most pedig nézzük meg mindhárom operátor igazságtáblázatát:

A NOT operátor igazságtáblázata

  kifejezés|NOT kifejezés
  :-----------:|:---------------:
  True        | False
  False       | True

Az AND operátor igazságtáblázata

   egyik kifejezés| másik kifejezés|egyik AND másik kifejezés|
  ----------------|----------------|--------------------------|
   True|True|True                    
   True|False|False                   
   False|True   |False                   
   False|False  |False                   

   Az OR operátor igazságtáblázata

  |egyik kifejezés|másik kifejezés|egyik OR másik kifejezés
  |---------------|---------------|------------------------
  |True           |True           |True                    
  |True           |False          |True                    
  |False          |True           |True                    
  |False          |False          |False                   

------------------------------------------------------------------------

A numerikus adatok után nézzük meg az első alfanumerikus adattípusunkat,
a **string** (**karakterlánc**) típust. A Pythonban minden olyan adat,
amit aposztrófok vagy idézőjelek határolnak.

```python
>>> text1 = "Szia!"
>>> text2 = 'Egy Petőfi-sorral válaszolok neked:'
>>> text3 = '"Azért a víz az úr!" - ezt ne feledd!'
>>> print(text1,text2,text3)
Szia! Egy Petőfi-sorral válaszolok neked: "Azért a víz az úr!" - ezt ne
feledd!
```

A fenti példákban megfigyelhettük, hogy érdemes aposztrófokat használni
abban az esetben, ha a szövegben idézőjelet használunk, ugyanakkor
használjuk inkább az idézőjelet, ha aposztróf fordul elő a szövegben. Mi
van akkor, ha aposztrófot használok olyan esetben, ha a szövegünk is
tartalmaz aposztrófot? Nézzük:

```python
>>> text4 = ""Azért a víz az úr!" - ezt ne feledd!"
SyntaxError: invalid syntax
```

A fentiekben látható, hogy szintaktikai hibát kapunk. Az ilyen esetek
elkerülésére szolgál, az úgynevezett **escape-karakter**, a
(**backslash**).

```python
>>> text4 = ""Azért a víz az úr!" - ezt ne feledd!"
>>> print(text4)
"Azért a víz az úr!" - ezt ne feledd!
```

Még két további lehetőségre hívom fel a figyelmet. Sorugrást a **n**
alkalmazásával érhetünk el. Egy karakterláncba könnyen szúrhatunk be
speciális karaktereket, vagy akár magát a backslasht is, sőt több soros
szöveget is eltárolhatunk, ha **"""**, azaz tripla idézőjelet
használunk.

Vegyük most sorra azokat az operátorokat, amelyeket a stringeken
alkalmazhatunk:

```python
>>> text1 = "Hozzál "
>>> text2 = "almát!"
>>> text = text1 + text2
>>> print(text)
Hozzál almát!
>>> szo = "Ho"
>>> szo * 3
'HoHoHo'
>>> szo * 3 + text
'HoHoHoHozzál almát!'
>>> 'a' + 5
Traceback (most recent call last):
File "<pyshell\#40>", line 1, in <module>
'a' + 5
TypeError: Can't convert 'int' object to str implicitly
```

A karakterláncok összefűzését **konkatenációnak** nevezzük. Furcsának
tűnhet, hogy ezt ugyanazzal az operátorral tesszük, amivel a számokat
adjuk össze. Ezt a jelenséget hívjuk úgy, hogy **operator overloading**,
vagy **operátor-felülbírálás**. Az ilyen esetekben az operátor az
adatunk típusától függően jár el, vagyis, ha két integer között
használjuk a + operátort, akkor azokat összeadja, ha azonban két string
között, akkor azokat összefűzi.

Az utolsó esetben, amikor egy stringet és egy integert akartunk
összeadni, típushibát kaptunk. Ahhoz, hogy össze tudjunk fűzni egy betűt
és egy számot, szükséges, hogy a számot is string típusúvá alakítsuk.
Erre használatos a str() függvényünk. Ha egy szám string típusban van
tárolva, akkor pedig az **int()** függvénnyel tudjuk integer, a
**float()** függvénnyel pedig float típusúra alakítani.

```python
>>> 'a' + str(5)
'a5'
```

------------------------------------------------------------------------

A karakterláncok úgynevezett **összetett adattípusok**, azaz egyszerűbb
entitásokat egyetlen struktúrában egyesít. Jelen esetben ezek az
egyszerűbb entitások a karakterek. Szükség van tehát olyan függvényekre,
amelyek segítségével hozzáférhetünk ezekhez a kisebb entitásokhoz. A
Python a stringeket **szekvenciáknak** tekinti. A szekvenciák elemek
rendezett együttesei. A stringben található minden egyes karakter
szekvenciabeli helye egyértelműen meghatározható egy index segítségével.
Az adott indexű karakterhez a következőképpen férhetünk hozzá:
**karakterlánc[x]**, ahol x annak a karakternek az indexe, amelyhez
hozzá akarunk férni.

```python
>>> text = 'Python-programozás'
>>> print (text[0])
P
>>> print (text[4])
o
>>> print (text[-1])
s
```

A fenti példában megfigyelhettünk valamit. Ezt jó lesz megjegyezni,
ugyanis ez majdnem minden programozási nyelv esetében így van (most
hirtelen egyetlen kivétel ugrik be, ez pedig a Visual Basic), azaz a
**számozás 0-tól indul**! Láthatjuk azt is, hogy az indexelés visszafelé
is működik, ez viszont nem nulláról, hanem **-1-ről indul**!

Lehetőségünk van arra is, hogy a szövegnek csak egy adott szakaszával
dolgozzunk, vagy szeletekre vágjuk. Ezt a következő formában tehetjük
meg: karakterlánc[x:y]. A kezdőkarakter indexe x, az utolsó karakter
indexe viszont y-1! Ezt mindig tartsuk észben! Az is elégséges, ha csak
az x, vagy az y értéket adjuk meg, ilyenkor az elejétől, vagy pedig a
végéig adja vissza a szeletünket.

```python
>>> text = 'Python-programozás'
>>> print (text[0:6])
Python
>>> print (text[:6])
Python
>>> print (text[7:])
programozás
>>> print (text[4:7])
on-
```

Még egy stringekkel kapcsolatos függvényt szeretnék bemutatni. Ez a
string hosszát adja meg. Használata: **len(karakterlánc)**.

```python
>>> text = 'Python-programozás'
>>> print (len(text))
18
>>> print (text[7:len(text)])
programozás
>>> print (text[7:len(text)-1])
programozá
```

A végére hagytam még egy operátort, amely alkalmazható stringekre. Ennek
visszatérési értéke egy logikai érték, igaz vagy hamis. Formája: x in y,
ahol az x stringet vizsgáljuk az y stringben. Ha x megtalálható y-ban,
akkor a visszatérési érték True, ellenkező esetben False.

```python
>>> text = 'Python-programozás'
>>> 'Python' in text
True
>>> 'VBA' in text
False
```

Végül megjegyzendő a stringekről, hogy csakúgy mint az integer vagy a
float típus, ez is immutábilis.
