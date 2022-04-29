Title: Tkinter alapok 2
Published: 2014-01-18 18:40
Author: molnardenes
Tags: [GUI, tkinter]
---

Az előző bejegyzésből kimaradt egy fontos megjegyzés, ezt most pótolom. 
A grafikus programjainkat Windows alatt célszerű **.pyw**
kiterjesztéssel elmenteni, ugyanis így biztosítjuk, hogy a Windows a
pythonw.exe értelmezőt használja a futtatáshoz. Ezzel pedig azt tudjuk
kiküszöbölni, hogy a konzolablak felugorjon grafikus programok
futtatásánál is.

Ebben a bejegyzésben egy telefonszámokat kezelő programot fogunk írni.
Hozzáadhatunk majd új számot, módosíthatjuk, törölhetjük és
megjeleníthetjük a neveket és telefonszámokat.

![Telefonkönyv](/assets/images/tkinter02.png)

Mivel ez egy kicsivel nagyobb program, mint az előző, célszerű objektumorientáltan elkészíteni
Nyilván még itt sem égetően szükséges, de
nagyobb lélegzetvételű, több ablakot tartalmazó grafikus felületek
esetén már megkerülhetetlen, úgyhogy nem árt ilyen módon elkészíteni. A
mostani példánkban a főablakunkat a tk.Frame-ből származtatjuk, így egy
privát névteret tudunk biztosítani minden függvényünknek. A procedurális
megközelítéssel szemben további nagy előnye az objektumorientált
programunknak, hogy nem szükséges fentről lefelé kódolnunk, vagyis nem
fontos, hogy előbb definiáljuk a függvényünket, mint ahogy használnánk.
A mostani megközelítésben ráérünk később is létrehozni a
függvénydefiníciókat, ugyanis a főablakot lényegében csak az utolsó
lépésben hozzuk létre. Ha több ablakot szeretnénk használni a
programunkban, akkor azok mindegyikét célszerű külön-külön osztályként
definiálni. Nagyobb programoknál lehetőségünk lesz így arra is, hogy
különválasszuk a guit a vezérléstől. Nézzük is a kódot részletekben:

```python
from tkinter import *
from tkinter import ttk

class Foablak(Frame):
    def __init__(self, parent):
        super().__init__(parent)
        self.parent = parent
        self.grid(row=0, column=0)
        self.telefonkonyv = []
        self.nev = StringVar();
        self.telszam = StringVar();
```

A főablakot tartalmazó osztályunkat a Frame-ből származtatjuk, a
konstruktorban beállítjuk a gyermekosztályunkat és a **super()**
használatával "megtartunk egy másolatot" a szülőből is (a super()
használata, jelentősége önálló bejegyzést igényel, a későbbiekben 
biztosan szentelek neki egy posztot). Létrehozunk egy listát, amiben
majd tárolni fogjuk a neveket és telefonszámokat, amiknek létrehozunk
egy-egy StringVart.

```python
        self.nevLabel = ttk.Label(self, text="Név: ")
        self.nevEntry = ttk.Entry(self, textvariable=self.nev)
        self.telszamLabel = ttk.Label(self, text="Telefon: ")
        self.telszamEntry = ttk.Entry(self, textvariable=self.telszam)
        self.nevLabel.grid(row=0, column=0, padx=2, pady=2, sticky=W)
        self.nevEntry.grid(row=0, column=1, padx=2, pady=2, sticky=EW)
        self.telszamLabel.grid(row=1, column=0, padx=2, pady=2, sticky=W)
        self.telszamEntry.grid(row=1, column=1, padx=2, pady=2, sticky=EW)
```

A következőkben a név és telefonszám megadásához szükséges widgeteket
hozzuk létre és állítjuk be. Az előző bejegyzésben megismert címke
(**label**) és szövegdoboz (**entry**) használata már ismert. Újdonságot jelent
a **padx** és **pady** tulajdonság, ami egyfajta keretet, margót jelent, vagyis
az egyenlőségjel után megadott pixelnyi helyett hagy a widget megadott
oldalainál.

A következő részben a középső ikonokat tartalmazó rész kódját nézzük meg
részletesen. Ezek tulajdonképpen egyszerű gombok (**button**), de képeket
rendeltünk hozzájuk:

```python
        menuFrame = Frame(self.parent)
        self.ujKep = PhotoImage(file="add.gif")
        ujSzamButton = ttk.Button(menuFrame, image=self.ujKep, command=self.hozzaad)
        ujSzamButton.grid(row=2, column=0, padx=2, pady=2, sticky=W)
        self.torolKep = PhotoImage(file="remove.gif")
        torolButton = ttk.Button(menuFrame, image=self.torolKep, command=self.torol)
        torolButton.grid(row=2, column=1, padx=2, pady=2, sticky=W)
        self.felulirKep = PhotoImage(file="update.gif")
        felulirButton = ttk.Button(menuFrame, image=self.felulirKep, command=self.felulir)
        felulirButton.grid(row=2, column=2, padx=2, pady=2, sticky=W)
        self.toltKep = PhotoImage(file="load.gif")
        toltButton = ttk.Button(menuFrame, image=self.toltKep, command=self.tolt)
        toltButton.grid(row=2, column=3, padx=2, pady=2, sticky=W)
        menuFrame.grid(row=1, column=0)
```

A kényelmesebb elrendezés értelmében hozzunk létre egy újabb **frame**-et,
amiben majd kipakolhatjuk a buttonjainkat. Ezt azért célszerű megtenni,
mert a **tkinter** úgy hozza létre az egyes sorokat és oszlopokat, hogy azok
hossza/magassága mindig a leghosszabb/legmagasabb elemnek felel meg.
Ezért, hogy kedvünk szerint helyezhessük mi el a különböző widgeteket
szerencsés lesz, a gui minden részegységét külön frame-ekbe szervezni.
Ezután betöltjük a képeket változókba, majd megadjuk ezeket a változókat
a button objektumunk egy tulajdonságaként. Emellett a megfelelő
parancsokat is hozzájuk rendeljük, ezeket azonban majd csak később írjuk
meg. A képekkel kapcsolatban fontos megjegyezni, hogy csak **.gif**
kiterjesztésűek lehetnek és csak valós méretben jeleníthetők meg
(ugyanakkor megjegyzem, hogy ez nem szentírás természetesen, ugyanis a
**PIL** (Python Imaging Library) használatával lesz lehetőség
méretezésre és többféle kiterjesztés megnyitására is.

```python
        scrollFrame = Frame(self.parent)
        scrollbar = Scrollbar(scrollFrame, orient=VERTICAL)
        self.listBox = Listbox(scrollFrame, yscrollcommand=scrollbar.set)
        self.listBox.grid(row=1, column=0, sticky=NSEW)
        scrollbar["command"] = self.listBox.yview
        scrollbar.grid(row=1, column=1, sticky=NS)
        scrollFrame.grid(row=2, column=0)

        parent.bind("<Escape>", self.kilep)
```

Ezzel a grafikus felület elkészítésének a végére is értünk. Létrehozunk
egy **scrollbar**t és egy **listbox**ot egy új frame-ben. A scrollbart
függőlegesre állítjuk és összekapcsoljuk a listboxszal. Utolsó lépésként
az ESC billentyűhöz hozzárendeljük a kilep függvényünket.

```python
    def frissit(self):
        self.telefonkonyv.sort()
        self.listBox.delete(0,END)
        self.nevEntry.delete(0,END)
        self.telszamEntry.delete(0,END)
        for nev, telszam in self.telefonkonyv:
            self.listBox.insert(END, nev)

    def kilep(self, event=None):
        self.parent.destroy()

    def hozzaad(self):
        self.telefonkonyv.append ([self.nev.get(), self.telszam.get()])
        self.frissit()

    def torol(self):
        del self.telefonkonyv[int(self.listBox.curselection()[0])]
        self.frissit()

    def felulir(self):
        self.telefonkonyv[int(self.listBox.curselection()[0])] = [self.nev.get(), self.telszam.get()]
        self.frissit()

    def tolt(self):
        nev, telszam = self.telefonkonyv[int(self.listBox.curselection()[0])]
        self.nev.set(nev)
        self.telszam.set(telszam)
```

Az osztályunkban szükséges még a **metódus**ok definiálása. Nagyobb
programoknál célszerű lehet ezeket külön osztályba tenni, de ilyen rövid
esetekben ez talán nem szükséges. Minden metódusnak van legalább egy
argumentuma, mégpedig a **self**, ami ugye mindig az aktuális objektumot
jelöli. A frissít metódusban sorba rendezzük a beépített **sort**
függvénnyel a telefonkönyvet, majd minden értéket törlünk a
szövegdobozokból és a listboxból (**delete** metódus). Ezután pedig
elemenként kihelyezzük a telefonkönyv neveit (**insert** metódus). Ezt
minden tevékenység után lefordítjuk, így mindig egy frissített lista áll
majd rendelkezésünkre. A listbox éppen aktuálisan kijelölt elemére a
**curselectionnel** hivatkozhatunk.

```python
alkalmazas = Tk()
alkalmazas.title("Telefonkönyv")
ablak = Foablak(alkalmazas)
alkalmazas.mainloop()
```

Miután definiáltuk a főablak osztályt, hozzáfoghatunk a program futását
biztosító kód megírásához. Elsőként létrehozunk egy objektumot, ami
magát az egész programot jelképezi. Ezután nevet adunk a programunknak,
és létrehozunk egy objektumot a főablak osztályból, aminek adunk egy
szülőt, ami nem más lesz, mint az imént létrehozott alkalmazas
elnevezésű objektum. Végül pedig hozzáadunk egy eseményloopot, amivel
láthatóvá válik az ablak, és vár a felhasználó utasításaira.

Az alkalmazás továbbfejleszthető nyugodtan, lehet például reguláris
kifejezésekkel ellenőrizni, hogy érvényes telefonszámot adtunk-e meg,
lehet figyelni, hogy egy név vagy szám nem ismétlődik-e, stb.
