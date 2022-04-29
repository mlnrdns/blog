Title: VBA alapok 2 elágazások és ciklusok
Published: 2014-11-06 21:37
Author: molnardenes
Tags: [alapok]
---

A **VBA** sorozat második részében az elágazások és ciklusok
bemutatására kerül sor. A bejegyzés első felében az elágazásokkal
ismerkedünk meg. Az elágazás feltétele egy logikai kifejezés, aminek az
igaz/hamis értékétől függ, hogy a program melyik utasítást hajtja végre.

![elagazas](/assets/images/elagazas.png)

Ahogyan az ábrán is látszik, amennyiben a feltételünk igaznak bizonyul,
az igaz ágban lévő kódunk fog lefutni, amennyiben hamis, úgy a hamis ág
hajtódik majd végre. Ennek a hamis ágnak a megléte nem feltétlenül
szükséges, erre csak akkor van szükség, ha hamis érték esetén is
szeretnék, ha végrehajtana egy utasítást a programunk. Lássunk erre egy
példát:

```vb
Sub ifElseIf()

    Dim number As Integer
    number = Cells(1, 2)

    If number Mod 2 = 0 And number Mod 3 = 0 Then
        Cells(2, 2) = "Osztható mindkettővel"
    ElseIf number Mod 2 = 0 And number Mod 3 <> 0 Then
        Cells(2, 2) = "Osztható kettővel, de hárommal nem"
    ElseIf number Mod 2 <> 0 And number Mod 3 = 0 Then
        Cells(2, 2) = "Osztható hárommal, de kettővel nem"
    Else
        Cells(2, 2) = "Se kettővel, se hárommal nem osztható"
    End If

End Sub
```

Láthatjuk tehát, hogy egy feltételes elágazás a következő módon épül
fel: **If** <feltétel> **Then**  <végrehajtandó kód="">
**ElseIf** <végrehajtandó kód="">  **Else** <végrehajtandó
kód> **End If**. Az ElseIf és Else ág, valamint az End If elhagyható
bizonyos esetekben. Ha csak annyi vizsgáltunk volna, hogy páros-e a
szám, akkor megadhattuk volna így is:

```vb
If number Mod 2 = 0 Then Cells(1, 2) = "Páros"\
```

A példákban használt **Mod** a modulus operátor, amely egy osztási
művelet maradék adja vissza. Esetünkben azt vizsgáljuk, hogy páros-e a
szám, vagyis 2-vel osztva 0-át ad-e maradékul.

Az elágazások használata során érdemes megismerkedni a **Select
Case**-zel is, ami egy többirányú elágazás egy változó értékei alapján.
Nézzünk egy példát, ami alapján azonnal érthetővé is válik a dolog:

```vb
Sub evszak()
    Select Case Month(VBA.Date)
        Case 1 To 2: Cells(3, 1) = "Tél"
        Case 3 To 5: Cells(3, 1) = "Tavasz"
        Case 6 To 8: Cells(3, 1) = "Nyár"
        Case 9 To 11: Cells(3, 1) = "Ősz"
        Case 12: Cells(3, 1) = "Tél"
    End Select
End Sub
```

A példaprogram az aktuális hónap száma alapján megmondja, hogy milyen
évszak van éppen.

------------------------------------------------------------------------


Az elágazások után ismerkedjünk meg a ciklusokkal, amik akkor hasznosak,
ha ugyanazt a tevékenységet egymás után többször is el kell végezni. Ha
például több ezer értékünk van egy oszlopban és szeretnénk megjelölni
valahogy a páros számokat köztük, akkor kézzel szinte lehetetlen
vállalkozás lenne, és nem is volna túl praktikus vagy időbarát. Ilyenkor
nagy hasznát vesszük majd a ciklusoknak, amiknek VBA-ban a következő
típusait különböztetjük meg:

-   **Számlálós ciklusok:**
    -   **For ... Next:** meghatározott számú alkalommal végrehajtja a
        ciklust
    -   **For ... Each ... Next:** egy gyűjtemény összes elemén
        végigmegy
-   **Feltételes ciklusok:**
    -   **Do ... While:** végrehajtja a ciklust, ha a feltétel igaz, és
        addig fut, amíg hamissá nem válik
    -   **Do ... Until:** végrehajta a ciklust, ha a feltétel hamis, és
        addig fut, amíg igazzá nem válik
    -   **Do ... Loop ... While:** ld. do .. while annyi különbséggel,
        hogy ez legalább egyszer lefut
    -   **Do ... Loop ... Until:** ld. do ... until annyi különbséggel,
        hogy ez legalább egyszer lefut

Néhány példával nézzük meg a fontosabbakat:

```vb
Sub forNext()
    Dim counter As Integer

    For counter = 1 To 6
        Worksheets.Add
    Next counter
End Sub
```

A fenti egyszerű For ... Next ciklus létrehoz 6 lapot a munkafüzetben.
Alapértelmezetten egyesével halad a ciklus, de a **Step** használatával
ezen változtathatunk, ahol negatív számot is megadhatunk, így visszafelé
is tudunk számolni:

```vb
Sub forNextBackwards()
    Dim number As Integer

    For number = 10 To 1 Step -1
        Cells(number, 1) = number
    Next number
End Sub
```

A következő ciklus, amit megnézzük, a **For ... Each** ciklus lesz. Ez
szintén számlálós ciklus, hasonlóan az előzőhöz, de itt nem a konkrét
számon van a hangsúly, ugyanis itt annyi történik, hogy egy adott
gyűjtemény összes elemén végigmegy függetlenül a darabszámtól. Ez
olyankor lehet hasznos, ha például szeretnénk beolvasni egy mappa összes
fájljából az adatokat, de nem tudjuk, hogy konkrétan hány darab fájl
van. Ennek a ciklusnak hála, erre nem is lesz szükségünk. Ha elrejtettük
a munkafüzeteinket, akkor csak egyesével lehet újra láthatóvá tenni
azokat az Excelben. Írjunk erre egy makrót, amivel egyszerre mindet
megtudjuk majd jeleníteni újra:

```vb
Sub unHideWorksheets()
    Dim ws As Worksheet
    For Each ws In Worksheets
        ws.Visible = xlSheetVisible
    Next ws
End Sub
```

Mi a teendő akkor, ha csak egy bizonyos fület szeretnénk elrejteni, már
ha létezik olyan. Ekkor szintén a fenti módszer a követendő, vagyis
végig kell iterálnunk az összes fülön, viszont, ha megtaláltuk, amit
kell, akkor onnantól fölösleges lenne már tovább folytatni az iterációt.
Erre is lehetőségünk nyílik az **Exit For** utasításnak köszönhetően:

```vb
Sub hideWorksheetByName()
    Dim ws As Worksheet
    For Each ws In Worksheets
        If ws.Name = "titkos" Then
            ws.Visible = xlSheetHidden
            Exit For
        End If
    Next ws
End Sub
```

A számlálós ciklusok után nézzünk meg két feltételes ciklus is. Elsőként
a **Do ... While** looppal foglalkozzunk kicsit. Az ún. **guess and
check** algoritmussal próbáljuk megtalálni egy szám egész köbgyökét. Ha
nem járunk sikerrel, akkor írjuk ki, hogy nincs egész köbgyöke a
számnak. A módszer a következő:

1.  Találgatással próbáljuk megkeresni egy x szám köbgyökét, mégpedig
    úgy, hogy először megvizsgáljuk a 0^3 értékét, majd 1^3, 2^3, és
    így tovább egészen addig, amíg
2.  el nem érünk egy k számhoz, amely esetében k^3 > x

Lássuk a kódot:

```vb
Sub kobgyok()
    Dim number, result As Integer
    number = Cells(1, 2)
    result = 0

    Do While result ^ 3 < Abs(number)
        result = result + 1
    Loop

    If result ^ 3 <> Abs(number) Then
        Cells(5, 2) = "Nincs egész köbgyöke"
    Else
        If number < 0 Then
            result = -result
        End If
        Cells(5, 2) = result
    End If

End Sub
```

Ugyanez a fenti példa **Do ... Until** használatával. Remélhetőleg így
érthető a kettő közötti különbség:

```vb
Sub kobgyok()
    Dim number, result As Integer
    number = Cells(1, 2)
    result = 0

    Do Until result ^ 3 >= Abs(number)
        result = result + 1
    Loop

    If result ^ 3 <> Abs(number) Then
        Cells(5, 2) = "Nincs egész köbgyöke"
    Else
        If number < 0 Then
            result = -result
        End If
        Cells(5, 2) = result
    End If

End Sub
```
