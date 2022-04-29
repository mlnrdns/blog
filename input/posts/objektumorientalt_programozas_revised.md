Title: Objektumorientáltság Pythonban -  1. rész alapok
Published: 2016-10-09 22:30
Author: molnardenes
Tags: [OOP]
---

Egy korábbi bejegyzésben már igyekeztem bemutatni az **objektumorientált programozás** alapjait, de nem igazán vagyok ennyi idő után elégedett az eredménnyel, ezért egy újabb bejegyzésben nekifutok újra ennek a dolognak.

Elsősorban kezdőknek szánom a cikket, akik hallottak már valamennyit az OOP-ról, classokról, öröklődésről, stb., de nem igazán sikerült megérteniük a lényegét.

*A cikkben sokat felhasználtam Al Sweigart és Jeff Knupp hasonló anyagaiból (angolul érdemes őket olvasni, nagyon sok hasznos infó van a blogjaikon).*

Rögtön az elején szögezzünk le két alapvetést, ami az OOP-tól függetlenül is igazság:

- **a kód duplikálása rossz dolog**
- **a kód mindig változni fog**

Az apró kis egyszer-kétszer használandó programokon kívül szinte minden kódot rendszeresen karban kell tartani: hibákat javítani vagy új feature-öket hozzáadni.

A jó program egyik legfontosabb ismérve, hogy olvasható és könnyen karbantartható.

Ha egy kódrészletet több helyre is átmásolsz a programodban, akkor a későbbiekben ezt az összes olyan helyen módosítanod kell, ahová beraktad. Ha egy-egy helyen kifelejted a módosítást, mert már hónapokkal később nem emlékszel, hogy hová másoltad még be, akkor nem fogod tudni mindenhol kijavítani a hibát, vagy megfelelően implementálni egy új feature-t.

A függvények nagy segítséget nyújtanak a kód duplikálásnak elkerülésében. Egyszer kell csak megírnod a függvényt, és később a programban elég csak ezt meghívni, ahol szeretnénk, hogy lefusson. Ha módosítani kell rajta, akkor azt egy helyen tudjuk megtenni, és mindenhol ez a frissített függvény fog lefutni.

Hogy jobban megértsük a dolog lényegét, vegyünk alapul egy szerepjátékos példát. Nézzük meg ezt a karakterlapot: 

![magus](/assets/images/magus_karakterlap.png)

Egy M.A.G.U.S. karakter tulajdonképpen egész számok és szövegek halmaza. A különböző fizikai és szellemi tulajdonságait számok reprezentálják, míg faját, kasztját, képességeinek megnevezését szövegek. Anélkül, hogy objektumorienált szemléletet használnák, a következőképpen létre tudjuk hozni a karakterünket:

```python
name = 'Dlorna Garrap'
strength = 13
health = 16
inventory = { 'arany': 40, 'kulcs': 1 }

print('A híres utazó, %s jelenlegi életereje %s.', % (name, health))   
```

A fenti változónevek elég általánosak, ha már szörnyeket is szeretnénk a játékhoz adni, célszerű átnevezni a játékos változóit is:

```python
hero_name = 'Dlorna Garrap'
hero_strength = 13
hero_health = 16
hero_inventory = { 'arany': 40, 'kulcs': 1 }

monster_name = 'Goblin'
monster_strength = 11
monster_health = 9
monster_inventory = { 'arany': 4, 'tőr': 1 }

print('A híres utazó, %s jelenlegi életereje %s.', % (hero_name, hero_health))   
```

Ez mind szép és jó, de mi a helyzet akkor, ha már több szörnyet szeretnél a játékodban? Ekkor jön az, hogy  a változóneveidben elkezded számozni a lényeket: monster1, monster2, stb. Könnyen belátható, hogy ez nem túl szerencsés mearanyás.

Megpróbálkozhatsz ilyenkor listák létrehozásával:

```python
monster_name = ['Goblin', 'Sárkány', 'Goblin']
monster_strength = [20, 80, 16]
monster_health = [20, 300, 18]
monster_inventory = [{'arany': 12, 'tőr': 1}, {'arany': 890, 'mágikus amulett': 1}, {'arany': 15, 'tőr': 1}]
```

Így az első goblin értékei a listák 0. indexénél lesznek, a sárkány értékei az 1. indexnél, míg a második goblin értékei a 2. indexnél.

Az ilyen kódok rengeteg hibalehetőséget rejtenek magukban: mi történik akkor, ha a hős megöli az első goblint, de a killMonster() függvényt hibásan írtuk meg, ugyanis mindenhonnan töröljük az első goblin értékeit, de az inventory-ból véletlenül elfelejtjük?


```python
def kill_monster(monster_index):
    del monster_name[monster_index]
    del monster_health[monster_index]    
    # az inventory törlése nem szerepel a függvényben!

kill_monster(0)
```

A listán ezután a következő módon néz ki:

```python
monster_name = ['Sárkány', 'Goblin']
monster_strength = [80, 16]
monster_health = [300, 18]
monster_inventory = [{'arany': 12, 'tőr': 1}, {'arany': 890, 'mágikus amulett': 1}, {'arany': 15, 'tőr': 1}]
```

Észrevehetjük, hogy ezután a megölt goblin készlete lesz a sárkány készlete, ami pedig korábban a sárkányé volt, az a második gobliné lesz. Láthatjuk, hogy elég gyorsan kiszaladtak a kezünkből a dolgok.

Próbálkozhatnánk tovább: egy szörny adatai egy dictionary-ban lennének, és ezekből készülne egy lista. De ekkor például az inventory már egy dictionary lenne, amely egy dictionary-ben található, amely egy listában található. És ekkor még nem beszéltünk arról, hogy mi van akkor, ha az inventory-ban van egy hátizsák, amelynek vannak "rekeszei", amelyekben találhatók különböző tárgyak :)

Ilyenkor jön számunkra kapóra az objektumorientált programozás, amelynek segítségével létre tudunk hozni egy új adattípust.

## Osztályok ##

Az osztályokra tekinthetünk úgy, hogy azok nem mások, mint tervrajzok, amelyek alapján új objektumokat hozhatunk létre.

A fenti példákban szereplő hős és a szörnyek ugyanazokkal a tulajdonságokkal rendelkeznek (erő, életerő, készlet), ezért egy általános karakter osztályt hozzunk létre először:

```python
class Character():
    def __init__(self, name, strength, health, inventory):
        self.name = name
        self.strength = strength
        self.health = health
        self.inventory = inventory

hero = Character('Dlorna Garrap', 13, 16, {})
monsters = []
monsters.append(Character('Goblin', 11, 16, {'arany': 12, 'tőr': 1}))
monsters.append(Character('Sárkány', 30, 20, {'arany': 40, 'mágikus amulett': 1}))
```

Látható, hogy a kódunk mennyivel rövidebb lett, hiszen ugyanaz a kódrészlet használható a hős és a szörnyek létrehozásához is.

Nézzük meg részletesebben a fenti kódot:

```python
class Character():
```

A **class** kulcsszóval definiálhatunk egy új osztályt, hasonlóan a **def**hez, amellyel új függvényt tudunk létrehozni.

```python
class Character():
    def __init__(self, name, strength, health, inventory):
```

A fenti egy metódust definiál a Character osztályunkhoz. A metódus tulajdonképpen egy olyan függvény, amelyet egy osztályon belül definiáltunk.

A példában éppen egy speciális metódus szerepel. Az **__init__()** *(kettős alsóvonás előtte és utána is)* tulajdonképpen egyfajta **konstruktor**ként viselkedik. A Dive into Python szerint úgy néz ki, mint egy konstruktor (ez az első metódus, amelyet egy osztályban definiálunk), úgy viselkedik, mint egy konstruktor (ez az első kódrészlet, amely lefut egy újonnan létrehozott példányban), és még úgyis hangzik, mint eg konstruktor (az *init* egy konstruktorszerű viselkedést feltételez). Ugyanakkor mégsem az, hiszen az objektum már létezik, amikor az __init__() meghívásra kerül. Ebben a bejegyzésben is, és a későbbiekben is én konstruktorként hivatkozom az initre.

Amennyiben egy osztályban nem hozunk létre konstruktort, a Python gondoskodik erről helyettünk, és létrehoz egy olyan __init__() metódust, amely nem csinál semmit.

Egy-egy új objektum inicializálását a konstruktorban tudjuk elvégezni.

Pythonban a metódusok első paramétere a **self**.

## Self ##

De mi is az a **self**? Nem más, mint maga a példány. Ezzel jelezzük a programunknak, hogy az adott változó magához az objektumpéldányhoz tartozik, és nem csak szimpla helyi változók a metóduson belül. Az adott objektum változóit **mezők**nek vagy **adattag**oknak is nevezzük.

## A konstruktor meghívása ##

```python
hero = Character('Dlorna Garrap', 13, 16, {})
monsters = []
monsters.append(Character('Goblin', 11, 16, {'arany': 12, 'tőr': 1}))
monsters.append(Character('Sárkány', 30, 20, {'arany': 40, 'mágikus amulett': 1}))
```

A konstruktor meghívása Pythonban ugyanúgy történik, mintha egy függvényt hívnánk meg. A fenti példában a **Character()** meghívja a **Character** osztály **__init__()** konstruktorát. A **hero** változóban létrejön egy példány a **Character** osztályból. Ahogy látható emellett létrehozunk néhány szörnyet is a szörnylistában.

Ha egy másik programozó meglátja a kódunkban a **Character()**-t, tudni fogja, hogy nincs más dolga, mint megkeresni a **class Character()** részt a kódunkban, és máris ismertté válik számára, hogy mi is az a **Character**, milyen tulajdonságai és metódusai vannak.

## Metódusok ##

A metódusok nagyon hasznosak abban az esetben, ha olyan kódot írunk, amely magára az objektumra van hatással.

Ha például szeretnénk, ha hogy a karakter életereje csökkenjen, akkor írhatnánk a következőt:

```python
hero = Character('Dlorna Garrap', 13, 16, {})
hero.health -= 6
```

Könnyen beláthatjuk, hogy ez nem túl szerencsés, ennél azért lényegesen komplexebb a dolog. Vegyük csak példának azt, hogy nem lenne rossz megnézni, hogy ezzel az életerővesztéssel meghalt-e a karakterünk:

```python
hero = Character('Dlorna Garrap', 13, 16, {})
hero.health -= 6
if hero.health < 0:
    print(hero.name + ' meghalt!')
```

Ezzel a fenti kóddal az a probléma, hogy minden olyan esetben meg kell vizsgálni, hogy elfogyott-e az életereje, amikor valamennyit veszít abból. Ugyanakkor tudjuk, hogy a kódismétlés rossz dolog. Ha nem objektumorientáltan akarnánk megoldani, akkor szerencsés lenne egy függvényt írni erre:

```python
def take_damage(character_object, damage_amount):
    character_object.health = self.health - damage_amount
    if character_object.health < 0:
        print(character_object.name + ' meghalt!')

hero = Character('Dlorna Garrap', 13, 16, {})
take_damage(hero, 6)
```

Ez már egész jó megoldásnak tűnik, könnyen módosítható, ha szeretnénk páncélok és különböző fegyverek hatását is figyelembe venni. Ugyanakkor, ha a programunk jelentős mértékben megnövekedik, többszáz függvényünk lesz, akkor már biztos, hogy tudni fogjuk azonnal, hogy ez a take_damage függvény a Character osztályunkhoz kapcsolódik.

A megoldás, hogy ezt a függvényt a Character osztály egy metódusává alakítjuk:

```python
class Character():
    # ... kódrészlet ....

    def take_damage(self, damage_amount):
        self.health = self.health - damage_amount
        if self.health < 0:
            print(self.name + ' meghalt!')

    # ... kódrészlet ....
```

Ahogy a programunkban egyre több osztály jelenik meg, mindegyikben egyre több metódus és változó, egyre inkább látni fogjuk, hogy mennyire sokat segít a kód rendszerezésében az objektumorientált szemlélet.

## Publikus és privát metódusok ##

A metódusok és az adattagok lehetnek publikusak vagy privátnak jelöltek. A publikus metódusok meghívhatók, a publikus adattagok módosíthatók bármely kódrészlet által függetlenül attól, hogy azok az adott osztályon belül, vagy kívül helyezkednek el. A privát metódusok csak osztályon belül hívhatók meg, a privát adattagok csak osztályon belül módosíthatók.

Vannak programnyelvek (Java, C#, stb), ahol a védelmi szintek a fordítóprogram által biztosítottak. Pythonban nem létezik ilyen szinten a privát és publikus koncepciója. Minden metódus és adattag **publikus**. Ugyanakkor a szokás, hogy az alsóvonallal kezdődő metódusok privátnak tekintendők. Semmi sem akadályoz meg minket abban, hogy ennek ellenére az osztályon kívülről is módosítsuk, de nem mondhatjuk, hogy nem lettünk figyelmeztetve, hogy ez nem ajánlott dolog.

A következő bejegyzésben az öröklődéssel ismerkedünk majd részletesebben.
