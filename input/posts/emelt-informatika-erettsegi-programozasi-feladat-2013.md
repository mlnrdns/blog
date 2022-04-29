Title: Emelt informatika érettségi programozási feladat 2013
Published: 2014-02-02 01:20
Author: molnardenes
Tags: [erettsegi]
---

Az informatikai érettségi programozási feladataiból álló sorozat második
részében a 2013. májusi érettségit nézzük meg közelebbről.

Az **első feladatban** be kell olvasnunk a megadott fájl tartalmát.
Ehhez célszerű a with kulcsszót használni, ez ugyanis biztosítja a fájl
bezárását, ha megfeledkeznénk róla.

```python
print('1. feladat')
print('----------')
jeloltLista = []
try:
    with open('érettségi2013.txt', 'r') as fajl:
        for sor in fajl:
            sor = sor.strip('\n').split(' ')
            valasztoKerulet = sor[0]
            szavazatSzama = sor[1]
            jeloltVezetekneve = sor[2]
            jeloltKeresztneve = sor[3]
            partNeve = sor[4]
            jelolt = [valasztoKerulet, szavazatSzama, jeloltVezetekneve, jeloltKeresztneve, partNeve]
            jeloltLista.append(jelolt)
        print('Fájl beolvasva.')
except IOError:
    print('Fájl nem található')
```

Végigolvassuk a fájl sorait, majd a **strip** metódussal eltávolítjuk a
sorvége jeleket, és a **split** metódussal feldaraboljuk a szóközök
mentén. Az egyes elemeket elnevezzük és egy lista típusú jelölt változót
hozunk létre. Ezután ezekből a listákból létrehozzunk egy listát,
amelyben a jelölteket tároljuk az összes jellemző adatukkal. Ezt az
egészet berakjuk egy try blokkba, hogy ellenőrizni tudjuk, meg van-e a
fájl (ez opcionális, nyugodtan elhagyható).

A **második feladat** meglehetősen egyszerű, itt az induló jelöltek
számát kell megadnunk, ami nem más, mint a jelölteket tartalmazó lista
mérete, azaz:

```python
print('2. feladat')
print('----------')
indulokSzama = len(jeloltLista)
print('A helyhatósági választáson {0} jelölt indult.'.format(indulokSzama))
```

A **harmadik feladatban** a bekért adatok alapján meg kell találnunk a
jelöltet a listában -feltéve, hogy létezik-, és megadni a szavazatainak
számát. Ez sem jelenthet különösebb gondot. Egész egyszerűen
végigiterálunk a jelöltek listáján, és a bekért adatokat
összehasonlítjuk a lista megfelelő elemeivel. Célszerű bevezetni egy
bool változót, amiben eltároljuk, hogy volt-e találatunk vagy sem:

```python
print('3. feladat')
print('----------')
vezeteknev = input('Adja meg a jelölt vezetéknevét: ')
keresztnev = input('Adja meg a jelölt keresztnevét: ')
szavazat = 0
talalat = False
for jel in jeloltLista:
    if vezeteknev == jel[2]:
        if keresztnev == jel[3]:
            talalat = True
            szavazat = jel[1]
if talalat:
    print('A keresett jelölt {0} szavazatot kapott.'.format(szavazat))
else:
    print('Ilyen nevű jelölt nincs a listában.')
```

A **negyedik feladatban** a részvételi arányt kell megadnunk. Ezt úgy
számítjuk ki, hogy a szavazók számát elosztjuk a jogosultak számával,
majd az értéket szorozzuk 100-zal. Két tizedesjegyre a **round**
függvénnyel kerekítjük. Itt egy kis trükkhöz kell folyamodni, ugyanis a
beolvasott érték típusa alapértelmezetten string lesz, ezért szükséges
**castolnunk**, vagyis egésszé alakítanunk az **int()** függvénnyel:

```python
print('4. feladat')
print('----------')
osszesSzavazat = 0
szavazasraJogosultak = 12345
for jel in jeloltLista:
    osszesSzavazat += int(jel[1])
reszveteliArany = round(osszesSzavazat / szavazasraJogosultak * 100, 2)
print(
    'A választáson {0} állampolgár, a szavazásra jogosultak {1}%-a vett részt.'.format(osszesSzavazat, reszveteliArany))
```

Az **ötödik feladat** semmilyen érdekességet nem tartogat.
Végigiterálunk ismét a lista elemein, megvizsgáljuk, hogy az egyes
listát utolsó eleme alapján melyik pártra leadott szavazatot számoljuk,
és itt is használjuk a castolást. Amúgy tényleg semmi extra:

```python
print('5. feladat')
print('----------')
gyepSzavazat = 0
hepSzavazat = 0
tiszSzavazat = 0
zepSzavazat = 0
fuggetlenSzavazat = 0

for jel in jeloltLista:
    if jel[4] == 'ZEP':
        zepSzavazat += int(jel[1])
    elif jel[4] == 'GYEP':
        gyepSzavazat += int(jel[1])
    elif jel[4] == 'HEP':
        hepSzavazat += int(jel[1])
    elif jel[4] == 'TISZ':
        tiszSzavazat += int(jel[1])
    else:
        fuggetlenSzavazat += int(jel[1])

gyepArany = round(gyepSzavazat / osszesSzavazat * 100, 2)
hepArany = round(hepSzavazat / osszesSzavazat * 100, 2)
tiszArany = round(tiszSzavazat / osszesSzavazat * 100, 2)
zepArany = round(zepSzavazat / osszesSzavazat * 100, 2)
fuggetlenArany = round(fuggetlenSzavazat / osszesSzavazat * 100, 2)

print('Zöldségevők Pártja = {0}%'.format(zepArany))
print('Gyümölcsevők Pártja = {0}%'.format(gyepArany))
print('Tejivók Szövetsége = {0}%'.format(tiszArany))
print('Húsevők Pártja = {0}%'.format(hepArany))
print('Függetlenek = {0}%'.format(fuggetlenArany))
```

A **hatodik feladat** a legmagasabb szavazatszámot elért jelöltet kell
megadnunk, és azt, hogy melyik párt színeiben indult. Itt elsőként
végigiterálunk a listán, és meghatározzuk a legmagasabb szavazatszámot.
Ezt a maximumkiválasztás tételével tudjuk, azaz bevezetünk egy maximum
változót, és ahogy iterálunk végig az elemeken, amint egy maximumnál
nagyobbat találunk, az lesz az új maximum. Ha ezzel megvagyunk, akkor
újra végigiterálunk a listán, és megnézzük, hogy melyik elemek egyeznek
meg a maximummal, és azokat kiírjuk:

```python
print('6. feladat')
print('----------')
maxSzavazat = 0

for jel in jeloltLista:
    if int(jel[1]) > maxSzavazat:
        maxSzavazat = int(jel[1])

for jel in jeloltLista:
    if int(jel[1]) == maxSzavazat:
        if (jel[4] == '-'):
            print("{0} {1} --> független".format(jel[2], jel[3]))
        else:
            print("{0} {1} --> {2}".format(jel[2], jel[3], jel[4]))
```

A **hetedik feladat** tartogat némi kis trükköt, de természetesen ez sem
különösebben vészes. Bevezetünk egy változót, amiben az adott kerület
legmagasabb szavazatszámát tároljuk el. Majd a beépített **range()**
függvény használatával végigmegyünk az egyes kerületeken. A range 0 és
max között fog végigmenni, a mi példánkban 0 és 8 között ugye. A 0-ra
érdemes figyelni, ugyanis ilyen kerületünk nem lesz, ezért majd az éppen
aktuális értékhez mindig hozzá kell adnunk 1-et. Ezen a cikluson belül
két másik ciklust fogunk létrehozni. Az elsőben meghatározzuk az adott
kerület legmagasabb szavazatát, a másikban pedig végigmegyünk a
jelölteken, és ha egy jelölt szavazatszáma megegyezik a maximummal és a
kerületszáma megegyezik a range aktuális száma + 1-gyel, akkor kiírjuk a
fájlba a jelölt adatait. A maximum szavazat értékét pedig lenullázzuk,
hogy a következő kerületnél is tiszta lappal induljunk (gondoljunk arra,
hogy mi lenne, ha a következő kerületben csak kisebb számú szavazatok
lennének, és nem állítanánk vissza 0-ra a számlálót). És ezt
végigjátsszuk még 8-szor:

```python
print('7. feladat')
print('----------')
keruletMaxSzavazat = 0
with open ('kepviselok.txt','w') as output:
    for valasztoKeruletSzama in range(8):
        for jel in jeloltLista:
            if int(jel[0]) == valasztoKeruletSzama+1:
                if int(jel[1]) > keruletMaxSzavazat:
                    keruletMaxSzavazat = int(jel[1])

        for jel in jeloltLista:
            if keruletMaxSzavazat == int(jel[1]) and valasztoKeruletSzama+1 == int(jel[0]):
                if jel[4] == '-':
                    output.write('{0} {1} {2} független\n'.format(jel[0], jel[2], jel[3]))
                else:
                    output.write('{0} {1} {2} {3}\n'.format(jel[0], jel[2], jel[3], jel[4]))
        keruletMaxSzavazat = 0
```
