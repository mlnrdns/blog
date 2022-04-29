Title: Git repository-k mozgatása Team Project-ek között
Published: 2017-03-04 06:56
Author: molnardenes
Tags: [git]
---

Időnként előfordulhat, hogy olyan helyzet áll elő, ami miatt az egyik repository-nkat át kell mozgatni egy másik team project alá. A leginkább neuralgikus pont a history, amit semmiképpen sem lenne szerencsés elveszítenünk áthelyezés közben. Ebben a bejegyzésben röviden arról lesz szó, hogy hogyan tudjuk ezt megtenni.

Első lépésként hozzunk létre az új helyen egy üres repository-t. Ha ezzel megvagyunk, akkor másoljuk ki a **Clone URL**-jét.

Tükrözzük az eredeti repót: indítsuk egy a Git command line-t, és lépjünk be az eredeti repository könyvtárába, majd adjuk ki a következő parancsot:

```bash
git clone --mirror https://azujrepository.com/DefaultCollection/User/_git/UjRepositoryNeve

```

Pusholjuk ki a lokális változtatásokat a célrepository-ba:

```bash
git push --mirror https://azujrepository.com/DefaultCollection/User/_git/UjRepositoryNeve
```

Mind a clone, mind a push esetén azért hasznájuk a **--mirror** opciót, hogy biztosítsuk, hogy az összes branch és minden attribútum lemásolódik az új repository-nkba.

Utolsó lépésként szükséges beállítani, hogy ezután a pushok már az új repository-ra vonatkozzanak, vagyis szükséges a remote repository-k URL-jének beállítása. Ezt a git remote set-url paranccsal tudjuk megtenni:

```bash
git remote set-url origin https://azujrepository.com/DefaultCollection/User/_git/UjRepositoryNeve
```

Ha mindent jól csináltunk, akkor ezentúl az új repository-ban elérhető a teljes history, az új pushok pedig ide töltik fel a változtatásokat.
