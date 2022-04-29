Title: Python 3 virtuális környezet kialakítása Ubuntu alatt
Published: 2018-01-20 11:52
Author: molnardenes
Tags: [virtualenv, ubuntu]
---

Ubuntu alatt az alapértelmezett Python verzió a 2-es. Célszerű ezt az alapértelmezést nem változtatni, mert több alkalmatás is függ ettől.

Ha kiadjuk az mkvirtualenv parancsot, akkor a virtuális környezetben is a Python2 lesz számunkra elérhető. Ahhoz, hogy Python3-at tudjunk használni, a következőképpen kell eljárnunk:

```bash
which python3
```

A which parancs segítségével megkapjuk, hogy hová van telepítve a Python 3 verziónk: */usr/bin/python3*.

Ha ez megvan, akkor az mkvirtualenv parancsot a következő paraméterezéssel kiadva a 3-as Python verzió lesz elérhető a virtuális környezetünkben:

```bash
mkvirtualenv --python=/usr/bin/python3 virtualis_kornyezet_neve
```

Ha azt szeretnénk, hogy minden alkalommal, amikor virtuális környezetet hozunk létre, a Python3 legyen az alapértelmezett, akkor a *VIRTUALENV_PYTHON* változóval tudjuk ezt beállítani, mégpedig úgy, hogy a terminálban kiadjuk a következő parancsot:

```bash
export VIRTUALENV_PYTHON=/usr/bin/python3
```
