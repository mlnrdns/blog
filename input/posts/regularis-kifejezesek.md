Title: Reguláris kifejezések
Published: 2014-01-10 19:51
Author: molnardenes
Tags: [regex]
---

A következő bejegyzés egy korábbi lényeges átdolgozása. A bejegyzés első
verzióját éreztem a blog leggyengébb bejegyzésének, ezért jelentősen
átdolgoztam. A **reguláris kifejezések** (regex, regexp) olyan, bizonyos
szintaktikai szabályoknak megfelelő stringek, amelyekkel meghatározható
stringek egy halmaza. A mindennapokban gyakran találkozunk velük
különböző weboldalakon, többször is, mint gondolnánk. Bankkártyaszámok,
telefonszámok, címek, emailcímek, születési dátumok mind reguláris
kifejezésekkel vannak ellenőrizve.

Mielőtt rátérnék példák tárgyalására, nézzük meg, hogy mik a
legfontosabb minták:

- **bármely karakter**: bármely karakter önmagát jelenti, vagyis egy önállóan álló, módosító operátor nélkül a betű egyezik minden a betűvel
- **. (pont)**: bármilyen karakter. A k.r kifejezésnek például megfelel a kér és a kar is, vagy éppen a kár.
- **[....]**: a kapcsos zárójelben szereplő bármely karakterrel megegyező karakter. A k[aá]r kifejezésnek megfelel például a kar vagy a kár, de a kér nem. A kötőjellel tartományt is megadhatunk, a [0-9] megfelel bármely számjegynek.
- **[^...]**: a kapcsos zárójelben szereplő karakterek egyikével sem egyező karakter. A k[^é]r kifejezésnek nem megoldása a kér, de a kár vagy kar igen.
- **?**: A megelőző minta 0 vagy 1 alkalommal fordul elő. A hajó.? kifejezés igaz a hajó és a hajót szavakra is.
- **+**: A megelőző minta 1 vagy annál több alkalommal fordul elő. A haj.+ó kifejezésnek megfelel a hajlandó, a hajó viszont nem.
- **\***: A megelőző minta 0 vagy több alkalommal fordul elő. A haj.\*ó kifejezésnek már mind a hajlandó, mind a hajó megfelel.
- **{m,n}**: A megelőző minta minimum m, de maximum n alkalommal fordul elő. A {2} pontosan két előfordulás, a legalább kettő jelölése {2,}, 2 és 4 előfordulás közötti jele {2,4}, a legfeljebb 10 előfordulást pedig {,10}-zel jelöljük. A p.{,3}j akkor igaz, ha maximum 3 karaktert kell helyettesíteni, például pej vagy paraj, a párbaj esetén már 4 karakterről van szó, így az nem akad fenn rajta.
- **^**: Azt jelezhetjük vele, hogy a kifejezést a minta elején értelmezzük. A ^harc kifejezésnek megfelel a harcos, a szabadságharc viszont nem.
- **$**: A minta végét jelenti. A da$ mintának megfelel minden da-ra végződő szó, például pagoda, kaloda.
- **(...)**: Kifejezéseket csoportosíthatjuk vele. Például a (halász)?hajó kifejezésre illeszkedik a halászhajó és a hajó is. Nagy hasznát vesszük a vagylagos egyezésnél is:
- **|**: Vagylagos egyezés. Két lehetőség közül bármelyikkel való egyezésnél érvényes. Például ka(loda|tona) kifejezés esetén igaz a kaloda és katona is.
- **w**: Egy betűkarakter, azaz számjegy, betű vagy alsóvonás. Ekvivalens a következővel: [A-Za-z0-9_].
- **W**: Egy nem betűkarakter. Ekvivalens: [^A-Za-z0-9_].
- **s**: Egy whitespace karakter. Ekvivalens: [ trnf].
- **S**: Nem whitespace karakter. Ekvivalens: [^ trnf].
- **d**: Egy számjegy.
- **D**: Egy nem számjegy.

Pythonban az alapkönyvtár részeként megtaláljuk a reguláris
kifejezéseket kezelő **re** modult. A modul importálásával minden
reguláris kifejezésekkel kapcsolatos függvényhez hozzáférünk. A regexek
hasonlóképpen néznek ki, mint a stringek, tehát '' vagy "" közé
kerülnek, de, hogy a szimpla stringektől megkülönböztessük őket, ezért
egy kis **r**-t az idézőjelek elé írunk. Például az *r"[0-9]"* nem egy
egyszerű string, hanem egy reguláris kifejezés, ami egy 0 és 9 közötti
számot jelent, azaz 0 és 9 között bármelyik szám megfeleltethető ennek a
kifejezésnek.

Nézzünk is erre egy példát. Használjuk a **re.findall()** függvényt, ami
egy szöveg és egy reguláris kifejezés alapján visszaad egy listát
azokkal az értékekkel, amelyek a szövegből megfelelnek a reguláris
kifejezésnek. Ha ez nem teljesen világos most, akkor a példa után
azonnal az lesz. Egy szövegből próbáljuk kikeresni az emailcímeket:

```python
import re
szoveg = '''Kérdés esetén forduljon a rendszergazdához a következő címen:
         rendszergazda@molnardenes.com, vagy az ügyeletvezetőhöz az
         ugyelet@ugyeletes.info címen!'''
emailek = re.findall(r'[\w\.-]+@[\w\.-]+\.[A-Za-z]{2,4}', szoveg)
for email in emailek:
    print(email)
```

Nézzük meg részletesen a fenti kifejezést! Az első kapcsos zárójelben
[w.-] lévő kifejezés ugye tartalmazhat bármilyen karaktert, pontot
(pont rakása esetén, amikor pont karakterként kell értelmezni valóban,
akkor használjunk escape karaktert!) vagy kötőjelet. Az ezt követő +
jelzi, hogy az előbbi legalább egyszer vagy annál többször ismétlődjön.
Ezután jön a @ jel, majd egy újra megismételjük a kukac előtti részt,
ami után pont következik (szintén használjuk az escape karaktert!),
végül pedig valamilyen betű következik minimum 2, maximum 4 alkalommal.

A következő metódus, amivel érdemes megismerkedni, a **re.search()**,
ami egy reguláris kifejezés előfordulását keresi egy stringben. Ha a
keresés sikeres, a metódus a MatchObject egy példányával tér vissza,
ellenben ha nem, akkor None értékkel. Emiatt a metódus után célszerű egy
feltételes elágazásban megvizsgálni, hogy volt-e találat. A következő
példában megállapítjuk néhány rendszámról, hogy az egy taxi rendszáma-e
(a Wikipedia alapján a taxik rendszáma EAA-xxx és EDZ-xxx között van):

```python
import re
rendszamok = ["KKV-555", "EAA-125", "PVC-632", "EDE-667", "AFI-937", "EDZ-122"]
taxi = "^E[A-D][A-Z]-[0-9]{3}"
for rendszam in rendszamok:
    if re.search(taxi, rendszam):
        print(rendszam + " egy taxi rendszáma")
    else:
        print(rendszam + " nem taxihoz tartozó rendszám")
```

A rendszámok listában tároljuk a rendszámokat, melyek közül néhány egy
taxi rendszáma. A taxi nevű reguláris kifejezés a következőképpen néz
ki: első helyen egy E betűnek kell állnia, ezt egy A és D közötti betű
követi, majd pedig egy A és Z közötti. Ezután kötőjel következik, majd
végül három 0 és 9 közé eső szám.

A **re.sub()** függvény használatával egy adott mintát cserélhetünk le
egy másikra egy szövegben. A példánkban az emailcímek domainjét
cseréljük le:

```python
import re
szoveg = 'Jelentkezni lehet a jelentkezes@tv.hu vagy a szerkeszto@gmail.com címen.'
print(re.sub(r'@([\w\.-]+\.[A-Za-z]{2,4})', r'@molnardenes.com', szoveg))
```

Nagyon hasznos lehet a későbbiekben a **re.compile()** függvény, aminek
a segítségével egy mintát objektummá alakíthatunk. Ezeknek pedig
lehetnek metódusaik. Nézzünk erre is egy példát! Kérjünk be egy
számlaszámot, és ellenőrizzük, hogy érvényes-e. A számlaszám 16 vagy 24
számot tartalmaz nyolcas blokkokban, és vagy kötőjel választja el őket,
vagy semmi. Tehát számokon és kötőjeleken kívül más karaktert nem
tartalmazhat:

```python
import re
szamlaszam = re.compile(r'^[0-9]{8}([ -]?[0-9]{8}){1,2}$')
bekeres = input('Add meg a számlaszámod: ')
while not szamlaszam.search(bekeres):
    bekeres = input('Add meg a számlaszámod újra: ')
```
