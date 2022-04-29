Title: Fájlok ellenőrzése PowerShellben
Published: 2017-04-05 13:59
Author: molnardenes
Tags: [PowerShell, hash]
---


A mindennapi munka során gyakran előfordul, hogy adatokat kell egyik helyről a másikra másolni, és később képesnek kell lennünk arra, hogy bizonyítsuk, hogy a két adat teljesen megegyezik, semmiféle változtatás, módosítás nem történt benne.

Ennek az egyik legegyszerűbb módja az ellenőrző összegek generálása az eredeti, valamint a másolt helyen, majd ezeknek az ellenőrző eszközöknek az összehasonlítása.

Sokáig nem voltam tudatában annak, hogy a Windows 7-től kezdve már lehetséges third party alkalmazások használata nélkül is ellenőrző összegek generálása **PowerShell**ben. Ezek az ellenőrző összegek biztosítják, hogy egy fájlról teljes biztonsággal meg lehessen állapítani, hogy megegyezik-e egy másikkal vagy megváltozott másolás közben vagy után.

Az ellenőrző összeg generálása meglehetősen egyszerű:

![Python
shell](/assets/images/getfilehash1.png)

Az alapértelmezett algoritmus, amellyel a hashelés történik az **SHA256** algoritmus. Ezenfelül a következő algoritmusok használhatók:

- SHA1
- SHA256
- SHA384
- SHA512
- MACTripleDES
- MD5
- RIPEMD160

A megfelelő algoritmust az **Algorithm** paraméter megadásával történik:

```shell
Get-FileHash .\test_data.pdf -Algorithm MD5
```

Ha ezt szeretnénk esetleg egy szövegfájlba írni, akkor használjuk az **Out-File** cmdletet:

```shell
Get-FileHash .\test_data.pdf | Out-File test01.txt
```

Ezzel a test01.txt fájlba írtuk a fenti tartalmat.

A fenti példákban egyetlen fájlra hajtottuk végre az ellenőrzést. A **Get-ChildItem** segítségével egy könyvtár összes állományára legfuttathatjuk a hashelést, illetve a **recurse** paraméter átadásával rekurzívan az összes könyvtár összes állományára:

![Python
shell](/assets/images/getfilehash2.png)

Ha megtörtént a másolás, és szeretnénk később megbizonyosodni arról, hogy semmiféle változtatás nem történt az állományokban, akkor egyszerűen lefuttathatjuk a **Compare-Object** cmdletet, amelynek két paraméterként átadjuk az eredeti és a másolat hash-értékét:

```shell
Compare-Object (Get-ChildItem .\Temp | Get-FileHash).Hash (Get-ChildItem .\TempCopy | Get-FileHash).Hash
```

Ha nem kapunk vissza semmilyen üzenetet, akkor az eredeti és a másolat teljesen megegyezik, nem történt módosítás a két állományban egyáltalán. Ha erről azonban bizonyosak szeretnénk lenni, akkor használjuk az includeequal paramétert:


```shell
Compare-Object (Get-ChildItem .\Temp | Get-FileHash).Hash (Get-ChildItem .\TempCopy | Get-FileHash).Hash -includeequal
```


![Python
shell](/assets/images/getfilehash3.png)

Ha kimentettük a két hash-értéket egy-egy állományba, akkor lehetőségünk van ezeket összehasonlítani. Ilyenkor érdemes arra figyelni, hogy ne a két állományt hasonlítsuk össze, hanem a két állomány tartalmát:

```shell
Compare-Object -ReferenceObject (Get-Content test01.txt) -DifferenceObject (Get-Content test02.txt) -includeequal
```
