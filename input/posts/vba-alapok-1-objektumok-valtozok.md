Title: VBA alapok 1 objektumok, változók
Published: 2014-10-26 23:29
Author: molnardenes
Tags: [objektumok]
---

A **VBA** (**Visual Basic for Applications**) a Visual Basic
egyszerűsített változata, tartalmaz egy integrált fejlesztői
környezetet, amely be van építve a legtöbb Microsoft Office
alkalmazásba. A nyelvet alapvetően arra tervezték, hogy más
alkalmazásokhoz kiegészítő funkcionalitást biztosítson, mint például
makrók rögzítése és futtatása valamint varázslók készítése.

A vírusok elleni védekezés nevében a makrók alapesetben le vannak tiltva
az Office-ban, csakúgy, mint a Fejlesztőeszközök menü, ahol
hozzáférhetünk a makrórögzítőhöz és a VBA fejlesztőkörnyezetéhez.
Elérhetővé úgy tudjuk tenni, hogy a Fájl menüpont Beállítások
alpontjában kiválasztjuk a Menüszalag testreszabását, majd ott a Fő
lapokat választva tudjuk aktiválni a Fejlesztőeszközök menüpontot.

![Fejlesztőeszközök
engedélyezése](/assets/images/vba01.png)

Ha ezzel megvagyunk, akkor a Fejlesztőeszközök lapon a Kód csoportban,
kattintsunk a Makróvédelem gombra! Ott a Makróbeállítások csoportban
válasszuk az Összes makró engedélyezését. Ezzel készen is állunk a
makrókkal való munkára.

------------------------------------------------------------------------

![VBE
ablakok](/assets/images/vba02.png)

A Visual Basic Editort az **ALT-F11** billentyűkombinációval tudjuk
megnyitni. Megnyitása után egy többablakos elrendezéssel találkozunk.
Vegyük sorba, hogy melyik ablak mire szolgál:

**Project Explorer:** A projekt ablakban találhatjuk a megnyitott dokumentumaink elemeit. Az
elemek tárolása fa szerkezetű. Minden egyes VBA Projekt egy-egy
munkafüzetet jelent, amelyen belül megtaláljuk a hozzátartozó
munkalapokat és a ThisWorkbook objektumot, ami az egész munkafüzetet
együtt jelenti. Ha esetleg nem látható, vagy véletlenül becsuktuk, akkor
a CTRL-R segítségével újranyithatjuk.

**Code:** Ebben az ablakban készíthetjük el és szerkeszthetjük a kódunkat. F7-tel nyitható
meg, ha becsuktuk.

**Immediate:** A program futási eredményeit láthatjuk ebben az ablakban, CTRL-G-vel
nyitható meg.

**Watches:** A program nyomkövetésére és hibakeresésre használható.

------------------------------------------------------------------------

### Objektumok ##

Egy Excel alkalmazás objektumokból áll. Ilyen objektum például egy
munkafüzet, a munkafüzet egy lapja, a cellák, a különböző ábrák, stb.
Ezeknek az objektumoknak vannak tulajdonságaik és metódusaik.
Az általunk kiadott utasítások az Excel objektummodellen keresztül
hajtódnak végre, ami nem más, mint egy hatalmas lista az Excelhez
kapcsolódó objektumokról, mint például a fentebb elsoroltak.

Ebben a bejegyzésben a **Range** és a **Cells** objektumokkal fogunk
foglalkozni. A tartomány, **Range** talán a leggyakrabban használt
objektum a VBA-ban. Hivatkozhat egyetlen cellára, vagy akár egy nagyobb,
több cellából álló tartományra is. A **Cells** objektum egyetlen cellát
jelölhet. Használata: *Cells(sor, oszlop)*. Még egy speciális
objektummal kell megismerkednünk, ami nem más, mint az éppen aktuális
cella. Erre az **ActiveCell** kulcsszóval hivatkozhatunk. Az általunk
kiválasztott cellát a **.Select** metódussal tudjuk kijelölni:

```vb
Cells(1,1).Select
Cells(1, "A").Select
Range("A5:B10").Select
Range(Cells(5,1), Cells(10,2)).Select
Range(ActiveCell, "B6").Select
```

Egy adott cellának könnyen adhatunk értéket a **.Value** tulajdonságán
keresztül, ami tulajdonképpen el is hagyható akár. Ha azt szeretnénk,
hogy a felhasználó által kijelölt területtel kapcsolatban végezzen
műveletet a makrónk, akkor lesz hasznunkra a **Selection** objektum. Ez
a felhasználó által kijelölt cellákat jelenti. A példánkban az A1:B5
tartomány minden cellájába írjuk be, hogy a VBA milyen szuper.

```vb
Range("A1:B5").Value = "a VBA szuper"
Selection.Value = "a VBA szuper"
ActiveCell.Value = "a VBA szuper"
```

Ha lenyomjuk az **F5**-öt és megnézzük a munkafüzetünket, akkor
láthatjuk, hogy a megadott tartományba bekerült a szöveg.

Az eddigi példákban végig abszolút hivatkozásokat használtunk.
Lehetőségünk van azonban természetesen relatív hivatkozások használatára
is. Ezt az **.Offset** metódus segítségével tudjuk megtenni. Használata:
*.Offset(sor, oszlop)*. Nézzünk rá egy-két példát:

```vb
ActiveCell.Offset(, 2).Value = "kettővel jobbra"
ActiveCell.Offset(1, -1).Value = "egy sorral lejjebb, eggyel balra"
```

------------------------------------------------------------------------

### Változók ##

A változós deklarálása a következő módon történik: **Dim**
*változó_neve* **As ***típus. *Az értékadás egyszerűen az **=** jel
használatával történik. A legfontosabb adattípusok a következők:

-   **Byte:** egész szám 0 és 255 között
-   **Integer:** egész szám -32.768 és 32.767 között
-   **Long:** egész szám –2.147.483.648 és –2.147.483.647 között
-   **Single** és **Double:** lebegőpontos szám 6, illetve 14 tizedesjegy pontossággal
-   **Boolean:** logikai változó, értéke igaz vagy hamis
-   **Published:** dátum
-   **String:** szöveg
-   **Variant:** az értékadáskor kapja meg a típusát is, ami így akár minden értékadásnál megváltozhat

Nézzünk egy példát a használatára. Az A1 és A2 cellába írjunk be egy
egész számot. Ezután lépjünk be a Visual Basic Editorba az **ALT-F11**
használatával. Majd hozzunk létre két egész változót, a-t és b-t,
amelyeknek az értékét olvassuk be az A1 és A2-es cellákból, majd pedig
az A3-as cella legyen a felette lévő kettő összege:

```vb
Dim a, b As Integer
a = Cells(1, 1)
b = Cells(2, 1)
Cells(3, 1) = a + b
```

Ha lefuttatjuk **F5**-tel, akkor láthatjuk, hogy az A3-as cellában
megkaptuk a fölötte lévő két cella összegét.

A változókkal kapcsolatban még fontos megjegyezni az **Option Explicit**
utasítást. Ennek használatával kötelezővé válik a változók deklarálása
használat előtt. Ha a fordító futás közben esetleg egy olyan változót
találna, ami nem lett korábban deklarálva, akkor hibát jelez, és
megjelöli a hibás sort. Ha azt szeretnénk, hogy ez mindig be legyen
kapcsolva, és ne nekünk kelljen minden alkalommal beírni még a kód
előtt, akkor a Tools ➪ Options ➪ Require Variable Declaration elé
tegyünk egy pipát.

Megjegyzéseket a **'** karakter, illetve a **Rem** kulcsszó után
helyezhetünk el a programkódban, és a sor végéig tart. A többsoros
megjegyzések készítésére nincs nyelvi eszköz, de a környezet lehetővé
teszi, hogy egy kijelölt kódrészt megjegyzéssé, vagy megjegyzésből kóddá
alakítsunk.

------------------------------------------------------------------------

### Eljárások és függvények ##

Mivel a VBA egy objektumorientált nyelv (bár az öröklődést nem támogatja, de a kompozíció megvalósítható), utasításainkat eljárásokba és
függvényekbe szervezhetjük. Minden egyes eljárást a **Sub** utasítással
kell kezdenünk, majd a makró nevét kell megadnunk és végül zárójelek
között a futáshoz szükséges paraméterlistát. Az eljárásokat az **End Sub** utasítással zárjuk. Az eljárásokat könnyedén futtathatjuk az
**Alt-F8** használatával elérhető makróindítóval.

Excelben könnyedén készíthetünk saját függvényeket. Minden egyes
függvényt a **Function** utasítással kell kezdenünk, majd a függvény
nevét kell megadnunk végül zárójelek között a futáshoz szükséges
paraméterlistát. A függvényeket az **End Function** utasítással zárjuk.
Minden függvénynek kötelezően tartalmaznia kell legalább egy olyan
értékadó utasítást, amelynek bal oldalán a függvény neve szerepel.

Ezeknek a részletes bemutatására a későbbi bejegyzésekben kerül majd
sor.
