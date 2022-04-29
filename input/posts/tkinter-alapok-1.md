Title: Tkinter alapok 1
Published: 2014-01-12 23:37
Author: molnardenes
Tags: [GUI, tkinter]
---

A következő bejegyzés egy grafikus felületekről (GUI) szóló sorozat
bevezető része. Pythonhoz több grafikus könyvtár is létezik, például
wxPython, pyQT, pyGTK, PySide, vagy a bejegyzésben ismertetett
**tkinter**. Ez utóbbival szemben sok kritikát fogalmaztak meg, többek
között, hogy nem elég szép, ennek ellenére a GUI-kkal való ismerkedésre
talán ez a legalkalmasabb. A szépség pedig relatív amúgy is.

Nagy előnye a tkinternek, hogy alapból a Python installálásával
megkapjuk, csupán annyi dolgunk van, hogy importáljuk: from tkinter
import *. Alapvetően nem szerencsés az ilyesfajta importálási mód,
mivel függvénynevek összeütközéséhez vezethet, a tkinternél viszont ezek
a nevek főként gui-specifikusak, így nem kell tartani a névütközéstől.

Lássunk is hozzá az első grafikus felületű programunkhoz, ami nem lesz
más, mint egy egyszerű gyökkereső program. Egy korábbi
bejegyzésben bemutatott Newton-Raphson módszerrel fogjuk kiszámítani egy
szövegdobozba beírt szám négyzetgyökét. Elsőként a teljes kódot nézzük
meg majd menjünk végig rajta részenként:

```python
from tkinter import *
from tkinter import ttk

def newton(*args):
    try:
        x = float(eredetiSzam.get())
        tipp = x / 2.0
        while abs(tipp * tipp - x) >= 0.001:
            tipp = tipp - (((tipp ** 2) - x)/(2 * tipp))
            szamNegyzetgyoke.set(tipp)
    except ValueError:
        pass

foablak = Tk()
foablak.title("Négyzetgyök")

fokeret = ttk.Frame(foablak, padding="3 3 12 12")
fokeret.grid(column=0, row=0, sticky=(N, W, E, S))

eredetiSzam = StringVar()
szamNegyzetgyoke = StringVar()

eredetiSzam_szovegdoboz = ttk.Entry(fokeret, width=7, textvariable=eredetiSzam)
eredetiSzam_szovegdoboz.grid(column=2, row=1, sticky=(W, E), padx=5, pady=5)

ttk.Label(fokeret, textvariable=szamNegyzetgyoke).grid(column=2, row=2, sticky=(W, E), padx=5, pady=5)
ttk.Button(fokeret, text="OK", command=newton).grid(column=2, row=3, sticky=W)

ttk.Label(fokeret, text="Az eredeti szám: ").grid(column=1, row=1, sticky=W, padx=5, pady=5)
ttk.Label(fokeret, text="A négyzetgyöke:").grid(column=1, row=2, sticky=E, padx=5, pady=5)

eredetiSzam_szovegdoboz.focus()
foablak.bind('<Return>', newton)

foablak.mainloop()
```

Az első két sorban beimportáljuk a szükséges modulokat. A tkinter
egyértelmű, a ttk pedig az ún. "tk themed widget set", ami lényegesen
szebb widgeteket tartalmaz. A következőkben látszólag semmi újdonság
nincs, az a korábban létrehozott gyökkereső függvényünk. Ugyanakkor az
eredeti számot tartalmazó globális változónk hozzá van rendelve a kódban
egy szövegdobozhoz a **textvariable** opcióval.

Ez azt eredményezi, hogy akárhányszor csak változik az adott szövegdoboz
értéke, automatikusan megváltozik a hozzá kapcsolt változó is.

![tkinter01](/assets/images/tkinter01.png)

Ezután létrehozzuk az ablakot, és elnevezzük, majd egy ún. **frame**-et
hozunk létre, ami tartalmazni fogja az összes **widget**ünket. Létrehozunk
két változót, melyek tkinterben **StringVar**, **IntVar** és **DoubleVar** lehetnek.
Ezek rendelkeznek a **.get** és **.set** metódussal, azaz kiolvashatjuk és be is
állíthatjuk az értéküket. Ezeket a változókat rendeljük hozzá egy
szövegdobozhoz és egy címkéhez. Ha ezek értéke megváltozik, akkor a két
globális változó is, és viszont.

Az eredeti számot tartalmazó szövegdobozt a ttk.**Entry** objektummal
készítjük el, és megadjuk neki, hogy a főkeret része legyen. Ezután a
**.grid** segítségével elhelyezzük a főkereten, mégpedig úgy, hogy megadjuk,
hogy melyik sor, melyik oszlopába kerüljön. A mi ablakunk most három
sorból és két oszlopból áll, ha jól megnézzük az elosztást. A **sticky**
opcióval horgonyokat hozunk létre, azaz, ha változik is az ablak mérete,
mi a megadod helyre "ragasztottuk" az adott objektumot, így az nem fog
onnan "elvándorolni". A gomb létrehozásánál megadjuk, hogy a
kattintáskor hajtsa végre a newton függvényt (**command=newton**).

A végén hozzárendeljük az *Enter* billentyűhöz is a newton függvényt,
amennyiben a kurzor a szövegdobozban van. Az utolsó sorban pedig
elindítja a loop-ot a program, ami szükséges a futáshoz. Az
eseményvezérelt programok lényege ugye, hogy készenléti állapotban
futnak mindaddig, míg valamilyen esemény (kattintás, billentyű
lenyomása, stb) be nem következik.

A sorozat további részeiben megismerkedünk a többi widgettel, menükkel,
és megtanulunk komplexebb interfészeket is létrehozni.
