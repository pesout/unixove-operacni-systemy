# UOS cvičení 4

## Práva

Ve výstupu příkazu `ls -l` jsou na začátku řádky:
```
-rwxrwxrwx  1 stepan stepan    27 lis 19 12:39 soubor.txt
```

První znak je typ (ne právo) – *pomlčka* (soubor), `d` (adresář), `c` (znakové zařízení), `l` (link) apod.

Následující znaky – `rwxrwxrwx` – jsou práva **vlastníka**, **skupiny vlastníka** a **ostatních** (3x3 znaky).

### Práva na soubory

- `r` (čtení, Read)
- `w` (zápis, Write)
- `x` (spuštění, eXecute)

### Práva na složky

- `r` (seznam souborů)
- `w` (přidat obsah, přejmenovat a smazat obsažené soubory a složky)
- `x` (vstup do adresáře, může být součástí cesty)

**Typické konfigurace**

- `rwx` (vše povoleno)
- `r-x` (read-only adresář)
- `--x` (adresář s "tajným" obsahem, používám pouze známé cesty)

### Chmod pro práva "rwx"

Příkaz `chmod` slouží k nastavení práv na soubor/složku.

**Symbolický zápis**

```bash
chmod u+x soubor.txt         # Přidá vlastníkovi právo "x"
chmod g-w soubor.txt         # Odebere skupině právo "w"
chmod u+rwx,o-wx soubor.txt  # Přidá vlastníkovi práva "rwx" a ostatním odebere "wx"
chmod u=rwx,g=rw soubor.txt  # Nastaví práva vlastníka na "rwx" a práva skupiny na "rw"
```

**Oktalový zápis**

Poziční, **číslice 0 až 7**, v příkazu `chmod ABC soubor.txt` jsou `A` práva vlastníka, `B` práva skupiny a `C` práva ostatních.

Číslice se dají vypočítat sčítáním podle klíče: `r = 4`, `w = 2` a `x = 1`. 

```bash
chmod 654 soubor.txt  # Vlastník "rw-", skupina "r-x", ostatní "r--"
chmod 321 soubor.txt  # Vlastník "-wx", skupina "-w-", ostatní "--x"
chmod 777 soubor.txt  # Plná práva, všichni "rwx"
```

### S-bit (setuid / setgid)

**Spuštění s právy vlastníka (setuid) nebo skupiny vlastníka (setgid).**

Pomocí symbolického zápisu se přidá jako `chmod u+s soubor.txt`, případně `chmod u+s soubor.txt`.

Dává smysl jen v případě, že ten, kdo skript spouští, má zároveň právo "x".

Práva pak vypadají třeba takto: `rwSrwsrwx` – malé `s` je tehdy, pokud je na stejné pozici zároveň právo `x`. Velké `S` v tomto příkladu tedy znamená, že samotný vlastník nemá právo spouštět (není to zrovna typická situace).

### T-bit (sticky bit)

Má-li uživatel do adresáře právo zápisu, může tam soubory/složky vytvářet, přejmenovávat i mazat. Nastavený **sticky bit** to omezí – **uživatelé mohou mazat a přesouvat pouze položky, které vlastní** (nikoli soubory ostatních uživatelů).

Typicky pro sdílené adresáře, např. `/tmp`.

Pomocí symbolického zápisu se přidá jako `chmod +t slozka`. Práva pak vypadají např. takto `rwxrwxrwt`.

### S-bit a t-bit – oktalový zápis

Jedna **číslice 0 až 7**, lze vypočítat sčítáním podle klíče: `setuid = 4`, `setgid = 2` a `sticky = 1`.

V rámci příkazu `chmod` se tato číslice uvádí před oktalovým zápisem `rwx` práv.

```bash
chmod 1777 slozka      # Plná práva pro všechny + sticky bit
chmod 4775 soubor.txt  # Plná práva pro vlastníka a skupinu (ostatní "r-x") + setuid
chmod 6775 soubor.txt  # Plná práva pro vlastníka a skupinu (ostatní "r-x") + setuid + setgid
```

### Maska přístupových práv

Oktalově se vypíše pomocí `umask` a symbolicky `umask -S`. Definuje, jaká práva seberu při vytváření souboru/složky.

Nastavuje se oktalovým zápisem jako `umask WXYZ` – pozičně to funguje stejně jako u `chmod`.

Např. `umask 0027` při vytváření souboru/složky sebere skupině právo na zápis a ostatním sebere všechna práva.

Programy při vytváření souborů/složek některá práva nemusí vůbec nastavovat. Například při vytváření souborů se typicky nenastavuje právo `x`, takže z tohoto pohledu má stejný efekt `umask 0002` a `umask 0003` (ale při vytváření složek to význam má).

Speciální bity (setuid/setgid/sticky) programy při vytváření v praxi nenastavují skoro nikdy, takže typická `umask` často začíná nulou.
