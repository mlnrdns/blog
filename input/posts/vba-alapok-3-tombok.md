Title: VBA alapok 3 tömbök
Published: 2014-11-07 23:38
Author: molnardenes
Tags: [adatszerkezet, arrays]
---

Excel használata során biztosan felmerül az igény arra, hogy egyszerre
nagy mennyiségű adatot tudjunk kezelni (egy egész sort vagy oszlopot
vagy esetleg egy tartományt). A **tömbök** nem mások, mint azonos típusú
adatok összetartozó sorozatai. Tömböket az eddig megismert módon
deklarálhatunk: Dim <név>(<elemszám>) As <típus>.
Értéket index alapján tudunk adni a tömb egyes elemeinek. Hozzunk létre
egy tömböt, ami az 5 kedvenc programozási nyelvünket tartalmazza, majd
iteráljunk végig ezen és írassuk ki az A1-A5 tartományba:

```vb
Sub firstArray()
    Dim line As Integer
    Dim languages(4) As String
    languages(0) = "VBA"
    languages(1) = "Java"
    languages(2) = "Python"
    languages(3) = "C#"
    languages(4) = "PHP"
    
    For line = 0 To 4
        Cells(line + 1, 1) = languages(line)
    Next    
End Sub
```

Feltűnhet, hogy a top 5 nyelvet listázzuk, mégis 4-et írtunk a tömb
indexéhez. Ennek az oka az, hogy alapértelmezetten a VBA 0-tól indexel,
vagyis az első elem a 0, a második az 1, a harmadik a 2, a negyedik a 3
és az ötödik a 4-es indexű elem. Ha ezen változtatni szeretnénk,
mégpedig, hogy 1-től indexeljen, akkor arra van lehetőség az **Option Base 1** utasítással, amit a kódunk legelején kell megadni, de ehelyett
inkább szokjuk meg, hogy a programozási nyelvekben általában 0-tól
történik az indexelés.

Lehetőségünk van többdimenziós tömbök létrehozására is. Nézzünk erre egy
példát! Csináljunk egy 10x10-es szorzótáblát:

```vb
Sub szorzotabla()
    Dim tomb(1 To 10, 1 To 10) As Integer

    For kulsoCiklus = 1 To 10
        For belsoCiklus = 1 To 10
            tomb(kulsoCiklus, belsoCiklus) = kulsoCiklus * belsoCiklus
        Next belsoCiklus
    Next kulsoCiklus

    For kulsoCiklus = 1 To 10
        For belsoCiklus = 1 To 10
            Cells(kulsoCiklus, belsoCiklus) = tomb(kulsoCiklus, belsoCiklus)
        Next belsoCiklus
    Next kulsoCiklus
End Sub
```

Ebben a példában megfigyelhetjük azt is, hogy hogyan tudunk ciklusokat
egymásba ágyazni. Első körben a tömbünket feltöltjük a szükséges
értékekkel, a második körben pedig végigiterálunk a tömb elemein és a
megfelelő cellákba elhelyezzük a megfelelő értékeket. A tömb
deklarációjánál most manuálisan adtam meg, hogy az indexelés 1-től 10-ig
menjen, hogy egyszerűbb dolgunk legyen a ciklusok használatában.

------------------------------------------------------------------------

A mindennapi munka során nagyon gyakran lesz szükségünk a tömbjeink
legkisebb és legnagyobb elérhető indexére. Erre használatos az
**LBound** és **UBound**:

```vb
Sub bounds()
    Dim i As Integer, target As Variant
    target = Array("A1", "B2", "C3", "D4", "E5")

    For i = LBound(target) To UBound(target)
        Range(target(i)).Value = i
    Next i
End Sub
```

Egy többdimenziós tömb esetén (pl: Dim matrix(1 To 10, 1 To 25) As
Integer) a következő módon kell lekérdezni: UBound(matrix, 1) és
Ubound(matrix, 2). Az első értéke 10, a másodiké 25 lesz. Tehát
**Ubound(<tömb neve>, <tömb dimenziója>)**.

------------------------------------------------------------------------

Felmerülhet a kérdés, hogy mi a helyzet akkor, ha a tömb mérete
folyamatosan változhat. Ennek kezelésére használhatjuk az úgynevezett
**dinamikus tömböket**. A dinamikus tömbök méretét futásidőben a
**ReDim** és a **Preserve** kulcsszavakkal változtathatjuk. A ReDim
Preserve utasítás tulajdonképpen a tömb felső határát növeli, megtartva
az addig belekerült értékeket is. Nézzünk erre is egy példát. Írassuk ki
azoknak a füleknek a nevét, amik éppen ki vannak jelölve (Ctrl + jobb
klikk a fülön):

```vb
Sub listSelectedSheets()
    Dim num As Integer
    Dim selected() As Variant
    Dim ws As Worksheet

    For Each ws In ActiveWindow.selectedSheets
        num = num + 1
        ReDim Preserve selected(num)
        selected(num) = ws.Name
    Next ws

    For num = 1 To UBound(selected)
        Cells(num, 1).Value = selected(num)
    Next num
End Sub
```
