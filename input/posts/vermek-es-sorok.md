Title: Vermek és sorok
Published: 2013-05-11 02:46
Author: molnardenes
Tags: [adatszerkezet, algoritmus, OOP]
---

Az előző bejegyzésben a bináris keresőfákkal foglalkoztunk. Most egy
újabb adatszerkezettel ismerkedünk meg, a veremmel. A **verem**
(**stack**) adatszerkezet egy olyan LIFO (last in, first out)
adatszerkezet, amelyben az új elemet mindig a lista elejére helyezhetjük
el, illetve elemet is csak a lista tetejéről érhetünk el. A verem
adatszerkezetben először mindig a legutoljára eltárolt elemhez férhetünk
hozzá, miután ezt kivettük (töröltük), jöhet az utolsó előttinek
eltárolt elem és így tovább.

A verembe behelyezett elemek kivétele tehát fordított sorrendben
történik. Ez egy nagyon haszos tulajdonsága a veremnek, hiszen ezt
kihasználva könnyen megfordíthatjuk egy lista elemeinek sorrendjét:

![Stack](/assets/images/stack01.png)

Nincs más hátra, mint a verem implementálása Pythonban. A verem egy
**absztrakt adattípus**. Az absztrakt adattípus egy leírás, amely
absztrakt adatok halmazát és a rajtuk végezhető műveleteket adja meg nem
törődve azok konkrét realizálásával. Ha ezt végiggondoljuk, akkor
azonnal beugrik az objektum fogalma. Szükségünk lesz tehát egy verem
osztályra, és annak metódusaira, amelyek azokat a műveleteket fogják
tartalmazni, amelyeket a vermekkel végezhetünk. Ezek pedig a következők:
egy elem hozzáadása (push), egy elem kivétele (pop), egy elem értékének
visszaadása anélkül, hogy eltávolítanánk a veremből (peek). Ezek mellett
szeretnénk tudni, hogy üres-e (isEmpty) éppen a verem, és ha nem, akkor
pedig hány elemet tartalmaz. Ezek után lássuk a kódot:

```python
class Stack:
     def __init__(self):
         self.elements = []

     def isEmpty(self):
         return self.elements == []

     def push(self, element):
         self.elements.append(element)

     def pop(self):
         return self.elements.pop()

     def peek(self):
         return self.elements[len(self.elements)-1]

     def size(self):
         return len(self.elements)
```

------------------------------------------------------------------------

Nézzünk most egy gyakorlati problémát, amit kiválóan meg tudunk oldani
verem használatával. Egy tizes számrendszerbeli számot könnyedén át
tudunk váltani verem használatával kettes, nyolcas vagy tizenhatos
számrendszerbeli számmá.

Ezt a következőképpen tudjuk megtenni. A 10-es számrendszerbeli
számunkat osszuk el azzal a számmal, amilyen számrendszerbe szeretnénk
átváltani (tehát 2-vel, 8-cal vagy 16-tal). Az osztás maradékát elrakjuk
a verembe. A kapott eredményt elosztjuk ismét az alappal, majd a
maradékot elrakjuk a verembe. Ezt addig folytatjuk, míg 0-át nem kapunk.
A végeredményhez pedig a maradékok visszafelé olvasásával jutunk, azaz a
maradékok fordított sorrendben történő felhasználásával. Erre pedig van
egy nagyon hasznos adatszerkezetünk, mégpedig a verem. A 16-os
számrendszernél a 0 és 9 közötti számokon kívül az abécé első hat betűje
is helyett kapott, az A = 10, B = 11, C = 12, D = 13, E = 14, F = 15. A
számrendszerváltóhoz szükségünk lesz az előbb létrehozott *Verem*
osztályra. Nézzük:

```python
def convertDecimal(decimal, newBase):
    numbers = "0123456789ABCDEF"

    remainderStack = Stack()

    while decimal > 0:
        remainder = decimal % newBase
        remainderStack.push(remainder)
        decimal = decimal // newBase

    newText = ""
    while not remainderStack.isEmpty():
        newText = newText + numbers[remainderStack.pop()]

    return newText
```

Az eddigiek alapján a kódnak világosnak kell lennie. Amire érdemes
figyelni, hogy a végeredményt stringként kell visszaadnunk, hiszen
hexadecimális átváltás esetén nem biztos, hogy csak számokat kapunk
eredményként.

------------------------------------------------------------------------

A verem mellett egy másik absztrakt adattípus a **sor**. A sor egy olyan
adatszerkezet, amelybe az egymás után beírt adatokat a beírás
sorrendjéből vehetjük ki (FIFO – first in, first out). Ez az adattípus
olyan esetekben használható jól, amikor két nem egyforma sebességgel
működő rendszer között adatátvitelt akarunk megvalósítani. Vagy olyan
esetekben is jól jön, amikor egy irodában egy nyomtató van, és egyszerre
többen szeretnének nyomtatni. Ilyenkor az fogja elsőként megkapni a
kinyomtatott anyagot, aki elsőként elküldte azt a nyomtatóra. A sor
adatszerkezetnek két utasítása van. A *berak* utasítás egy elemet betesz
a sorba, a *kivesz* utasítás kiveszi a sor következő elemét. A sor
implementációja a következőképpen néz ki:

```python
class Queue:
    def __init__(self):
        self.elements = []

    def isEmpty(self):
        return self.elements == []

    def put(self, element):
        self.elements.insert(0,element)

    def get(self):
        return self.elements.pop()

    def size(self):
        return len(self.elements)  
```

------------------------------------------------------------------------

A **kétvégű sor** a verem és a sor tulajdonságait is magában hordozza. A
kétvégű sorok esetén akár az elején, akár a végén betehetünk új elemet,
vagy akár ki is vehetünk egyet. Az eddigi metódusok itt is
megtalálhatók, de nem egy berakási és egy kivételi, hanem két berakási
és két kivételi metódust kell alkalmaznunk:

```python
class DeQueue:
    def __init__(self):
        self.elements = []

    def isEmpty(self):
        return self.elements == []

    def appendLeft(self, element):
        self.elements.append(element)

    def append(self, element):
        self.elements.insert(0,element)

    def popLeft(self):
        return self.elements.pop()

    def pop(self):
        return self.elements.pop(0)

    def size(self):
        return len(self.elements)
```

A kétvégű sor segítségével könnyen megállapíthatjuk egy adott szóról,
hogy palindrom-e. A rekurzív algoritmusokról szóló bejegyzésben
már megismertünk egy módszert a palindrom megállapításával kapcsolatban.
A kétvégű sorok esetén az algoritmus nem más, mint, hogy felbontjuk
betűire a szót, és berakjuk elölről a sorba. Majd kiveszünk egy
karaktert elölről és hátulról is, ezeket összehasonlítjuk. Ha ez a kettő
egyezik, folytatjuk egészen addig, míg az egyezés fennáll. Ha 0 vagy 1
karakter maradt (ha páros számú karakterből állt a szó 0, ha
páratlanból, akkor 1), akkor a szó palindrom. A kódunk pedig így néz ki:

```python
def isPalindrom(word):
    characterDeque = DeQueue()

    for character in word:
		characterDeque.appendLeft(character)        

    isTheSame = True

    while characterDeque.size() > 1 and isTheSame:
        first = characterDeque.popLeft()
        last = characterDeque.pop()
        if first != last:
            isTheSame = False

    return isTheSame
```
