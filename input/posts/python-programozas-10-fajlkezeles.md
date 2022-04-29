Title: Python-programozás 10 Fájlkezelés
Published: 2013-03-14 21:47
Author: molnardenes
Tags: [files]
---


Az eddigi programjaink eddig csak nagyon kevés adatot kezeltek, melyek
minden alkalommal kódolhatók voltak a programtestbe (listákként
például). Nagyobb tömegű adat kezelésére ez az eljárás azonban
alkalmatlan. Egyrészt a program olvashatósága jelentős mértékben romlana
több nagyon hosszú lista létrehozásával, másrészt bármiféle
adatmódosítás a forráskód megnyitásával járna. Ezeken túl a más
programokkal való adatcsere is lényegében lehetetlenné válna. Egy idő
után tehát szükségessé válik az adatok és az azokat kezelő programok
szétválasztása.

Mielőtt bármit is tudnánk kezdeni a fájlokkal, meg kell nyitnunk azokat.
Erre a Pythonnak van egy beépített függvénye, az open. Ennek a
szintaxisa a következő: *open (fájlnév, mód)*. Egy fájlt háromféle módon
nyithatunk meg. Első lehetőség, hogy egy már meglévő fájlhoz hozzá
szeretnénk adni adatokat, akkor a mód az '*a*' lesz, ha csak olvasásra
nyitnánk meg a fájlt, akkor '*r*' a mód, ha pedig egy új fájlt hoznánk
létre, akkor a '*w*'. A fájlokat használat után be kell zárnunk, ezt a
*close* utasítással tudjuk megtenni. Célszerű a fájlnevet egy változóba
elmenteni, így a későbbiekben egyszerűbb lesz rá hivatkozni. Ha a
fájlunk nincs egy könyvtárban a programmal, akkor teljes elérési utat
adjunk meg!

Megnyitás után, lehetőségünk van a fájl tartalmának beolvasására. Ezt
négyféleképpen tehetjük meg:

-   readline(): Beolvassa a következő sort a fájlból, beleértve a
    sortörés karaktert (n) is. Üres stringet ('') ad vissza, ha nincs
    több sor a fájlban. Akkor ajánlott használni, ha csak a fájl egy
    részével szeretnénk foglalkozni.
-   *for* sor *in* fajl: Minden sort beolvas egyesével. Ezt akkor
    célszerű alkalmazni, ha soronként szeretnénk a fájllal foglalkozni.
-   readlines(): A fájl minden sorát beolvassa egy *listába*. A sorok
    tartalmazzák a sortörés karaktert is. Ez a módszer akkor a legjobb
    választás, ha az egyes sorokhoz az indexük alapján
    szeretnénk hozzáférni.
-   read(): Egy *stringbe* olvassa be a fájl adatait egyben.

Nézzünk meg néhány példaprogramot a különböző lehetőségekre! Minden
esetben a szöveges fájl teljes tartalmát be fogjuk olvastatni. Elsőként
ezt a **readline()**-nal tesszük meg:

```python
fajl = open ('fájlnév.kit', 'r')
teljes_szoveg = ''

sor = fajl.readline()
while sor != "":
    teljes_szoveg = teljes_szoveg + sor
    sor = fajl.readline()

print(teljes_szoveg)
fajl.close()
```

A while ciklusban sorról sorra olvassuk be a fájlt egészen addig, míg a
beolvasás egy üres sort nem ad vissza. Mivel minden sor legalább egy
sorvége karaktert tartalmaz, egyetlenegyet kivéve, mégpedig az utolsó
sort, ami üres.

Most a **for ciklussal** fogjuk beolvasni soronként a teljes fájlt.
Nézzük:

```python
fajl = open ('fájlnév.kit', 'r')
teljes_szoveg = ''

for sor in fajl:
    teljes_szoveg = teljes_szoveg + sor

print(teljes_szoveg)
fajl.close()
```

A **read()** segítségével egyetlen hatalmas stringbe olvashatjuk be a
fájl szövegét:

```python
fajl = open ('fájlnév.kit', 'r')
teljes_szoveg = fajl.read()

print(teljes_szoveg)
fajl.close()
```

Időnként nagy segítségünkre van, ha a fájl szövegét egy listába olvassuk
be, így index alapján férünk hozzá az egyes sorokhoz, és for ciklussal
is végigiterálhatunk rajta. Erre jó a **readlines()** módszer. Előnye,
hogy gyorsabb, mint a readline(), ami soronként olvassa be a tartalmat.

```python
fajl = open ('fájlnév.kit', 'r')
teljes_szoveg = ''

fajl_lista = fajl.readlines()
for sor in fajl_lista:
    teljes_szoveg = teljes_szoveg + sor

print(teljes_szoveg)
fajl.close()
```

------------------------------------------------------------------------

Előfordulhat az is, hogy mi magunk szeretnénk eltárolni bizonyos
adatokat egy fájlban, hogy később is fel tudjuk használni. Erre is
lehetőségünk van, mégpedig a **write()** metódus segítségével. Mielőtt
írni tudnánk a fájlba, szükséges azt megnyitni írásra (*'w'*), vagy
hozzáírásra (*'a'*). Ha csak írásra nyitjuk meg a fájlt, akkor felülírja
az esetlegesen létezőt. Ezután a write szintaxisa a következőképpen néz
ki: *fajnev.write(str)*. Nézzünk rá egy példát, amiben bekérjük a
felhasználó nevét és életkorát, majd pedig kiírjuk egy fájlba. Arra
figyeljünk oda, hogy a sorvége karaktereket nem fűzi hozzá a sorokhoz,
azt magunknak kell hozzátenni. Nézzük:

```python
nev = input ('Kérem a nevet: ')
kor = input ('Most az életkort: ')

fajl = open ('fájlnév.kit','w')
fajl.write(nev + '\n')
fajl.write(kor)
fajl.close()
```

Végezetül annyit fűznék még hozzá a fájlokhoz, hogy nagyon fontos, hogy
ne keverjük össze az általunk létrehozott fájlobjektummal (objektumokról
majd a későbbiekben lesz szó részletesen) a fájl nevét! A fenti
példákban végig *fajl* néven hivatkoztam az objektumunkra, míg maga a
fájnév *fájlnév.kit* néven szerepelt.
