Title: Virtuális környezet kialakítása Python projektekhez
Published: 2016-10-12 10:54
Author: molnardenes
Tags: [virtualenv, pip]
---

Amennyiben egy rugalmas, jól karbantartható fejlesztői környezetet szeretnénk magunknak kialakítani Windows alatt is, elengedhetetlen, hogy megismerkedjünk a **virtualenv** és a **virtualenvwrapper** (ennek windowsos verziója a **virtualenvwrapper-win**) használatával.

A virtualenv lehetővé teszi számunkra, hogy különböző Python verziókat és különböző csomagokat használjunk ugyanazon a gépen. Én szinte minden egyes projekt esetén külön környezetet hozok létre. Ez azért szerencsés, mert tegyük fel, hogy van egy A és B projektünk, és mindkettő használja a C projektet. Az A azonban a C 1.2-es verzióját, a B pedig 1.6-ost. A Python nem tud verziószám alapján különbséget tenni a csomagok között, ezért virtuális környezet használata nélkül nem tudnánk egyszerre mindkét verzióját használni a C projektnek.

## A virtualenv telepítése ##
Csomagok telepítéséhez leginkább a **pip**et ajánlom. Amennyiben Python **3.4.** vagy újabb, illetve **2.7.9.** vagy újabb verziót használunk, akkor a pip már része lesz az aktuális kiadásnak. Korábbi verziójú Pythonok esetén használjuk a pip [hivatalos telepítőjét](https://bootstrap.pypa.io/get-pip.py "pip installer"). Letöltés után adjuk ki a következő parancsot:

```shell
python get-pip.py
```

Ha ezzel megvagyunk, telepítsük a **pip** segítségével a **virtualenv**et:

```shell
pip install virtualenv
```

Globálisan tulajdonképpen elégséges a pip és a virtualenv telepítése. Az összes többi csomagot javasolt az egyes virtuális környezetekbe telepíteni.

## Virtuális környezet létrehozása ##
Hozzunk létre egy könyvtárat a projektünknek, majd lépjünk bele az új könyvtárunkba és adjuk ki a következő parancsot:

```shell
virtualenv test_env
```

Ha jól csináltuk, a következőket kell látnunk:

```shell
Using base prefix 'PYTHON_ELÉRÉSI_ÚTVONALA'
New python executable in PROJEKTÜNK_ELÉRÉSI_ÚTVONALA\test_env\Scri
pts\python.exe
Installing setuptools, pip, wheel...done.
```

Ahhoz, hogy innentől ezt a környezetet tudjuk használni, szükségünk van még egy lépésre: aktiválnunk kell a **test_env**et: A **test_env\Scripts** mappában találjató az **activate.bat** állomány, amit, ha lefuttatunk, élesedik a környezet. Ezt látni fogjuk, ugyanis a parancssorban megjelenik a környezet neve:

```shell
(test_env) PROJEKTÜNK_ELÉRÉSI_ÚTVONALA\test_env\Scripts>
```

Amennyiben már nincs szükségünk a környezetre, akkor a Scripts mappában található **deactivate.bat** állomány futtatásával kiléphetünk a virtuális környezetből.

## Csomagok telepítése a virtuális környezetben ##
Ha aktiváltunk egy környezetet és onnan kiadjuk a pip telepítő parancsot, akkor az adott csomag ebbe a környezetbe telepítődik és nem globálisan. Ha kialakítottunk egy saját környezetet a projekthez, akkor a **pip freeze** paranccsal kilistázhatjuk az adott környezetbe telepített csomagok listáját. Ha ezt a listát beírjuk egy **requirements.txt** állományba, akkor bármikor egy sornyi paranccsal a teljes környezetet reprodukálni tudjuk egy másik gépen is akár:

```shell
pip freeze > requirements.txt
```

Majd az új helyen:

```shell
pip install -r requirements.txt
```

## A virtualenvwrapper használata ##
A virtuális környezetek kialakítását és használatát sokban egyszerűsíti a **virtualenvwrapper** csomag. Mire is jó ez?
- a virtuális környezeteinket egy helyen tudjuk tárolni
- a környezetek létrehozása, törlése, másolása sokkal egyszerűbbé válik
- egyszerűen válthatunk a környezeteink között

Első lépésben telepítsük a **virtualenvwrapper**t globálisan:

```shell
pip install virtualenvwrapper-win
```

Új virtuális környezetet a következő paranccsal tudunk létrehozni:

```shell
mkvirtualenv test_env
```

Lehetőségünk van arra hogy hozzárendeljünk egy projektkönyvtárat az adott környezethez. Hozzunk létre egy könyvtárat a teszt projektünknek, majd rendeljük egymáshoz a tesztkörnyezetet és a tesztkönyvtárat. Lépjünk be a tezstkönyvtárunkba és adjuk ki a következő parancsot:

```shell
PROJEKTÜNK_ELÉRÉSI_ÚTVONALA\setprojectdir .
```

A következő alkalommal, amikor élesítjük a virtuális környezetünket, automatikusan ebbe a könyvtárba fog belépni. 

Ha szeretnénk kilépni az éppen használt környezetből, akkor a **deactivate** parancs kiadásával tehetjük ezt meg.

Ha egy meglévő környezetbe szeretnénk belépni, nincs más dolgunk, mint kiadni a **workon** parancsot:

```shell
workon test_env
```

Ha csak önmagában adjuk ki a **workon**t, akkor kilistázza az összes elérhető virtuális környezetet.
