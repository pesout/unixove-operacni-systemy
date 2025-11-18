# UOS cvičení 3 a 4

## Find a xargs

```bash
# Vyhledá všechny soubory se jménem "a..." a vyhledá v nich řetězec "test"
find /etc -type f -name "a*" 2>/dev/null | xargs grep -n "test" 2>/dev/null

# Smazat všechny prázdné
find /tmp -empty | xargs rm -f

# Alternativně bez xargs
find /tmp -empty -delete
```

## Editory a spouštění skriptů

```bash
nano skript.sh  # editace skriptu v editoru nano
vim skript.sh   # editace skriptu v editoru vim
chmod u+x skript.sh  # přidá uživateli právo spouštět

# Spustí daný skript z aktuálního adresáře (díky explicitní cestě NEhledá v $PATH)
./skript.sh

# Spustí v aktuálním shellu (může např. přistupovat k lokálně definovaným proměnným)
source skript.sh
```

### Editor vim

- `i` – zapnout vkládací režim
- `Esc` – zapnout prohlížecí režim
- `:q!` – ukončit bez ohledu na změny
- `:wq` – uložit změny a ukončit
- `Shift + z z` – uložit změny a ukončit
- `u` – undo
- `yy` – zkopírovat řádek, - `p` – vložit

... [a mnoho dalšího](https://vim.rtorr.com/lang/cs)

## Inicializační skripty

Typicky obsahují aliasy, proměnné a funkce. Skripty se spustí při inicializaci shellu a pomáhají nastavit prostředí k práci. Uživatelé si je mohou upravit podle svých potřeb.

- `.bashrc` – pro interaktivní (ne-login) shell
- `.bash_profile` nebo `.profile` – login shell; často jen "source ~/.bashrc"

## Aliasy

```bash
# Nastaví alias (zkratku) pro spuštění příkazu
alias ll='ls -lah' ## "ll" bude dělat to samé, jako "ls -lah"

# Výpis všech existujících aliasů
alias

# Zruší alias
unalias ll
```

## Argumenty skriptů a proměnné

### Argumenty
- `$1`, `$2` – první a druhý argument
- `$#` – počet argumentů
- `$@` – všechny argumenty (doporučené s uvozovkami)

### Proměnné vs. proměnné prostředí

```bash
A=5         # jen v aktuálním shellu
export A=5  # i pro podprocesy (skripty, programy)
```

## Operátory/oddělovače příkazů

```bash
;     # oddělí příkazy: spustí se postupně
|     # pipe: stdout vlevo -> stdin vpravo
&     # spustí na pozadí
&&    # další příkaz jen když předchozí uspěl (exit 0)
||    # další příkaz jen když předchozí selhal (exit != 0)

# Pokud se nepodaří přečíst soubor, vytvoř ho
cat soubor.txt || touch soubor.txt
```

## Subshell a aritmetické prostředí

```bash
# Subshell: změny (cd, proměnné) se nepropíší ven
(command)

# Vypíše text a aktuální datum
echo "Dnes je $(date)" 

 # Aritmetická evaluace
((expression))

# Vypíše "1"
I=0; ((I++)); echo "$I"

# Vypíše "20"
echo $((10+10))
```

## Příkaz test a podmínka

Příkaz `test` vyhodnocuje logické výrazy, porovnává hodnoty, kontroluje typy souborů apod Výsledek je vrácen jako návratová hodnota `$?`.

Lze volat pomocí `test`, případně jako `[ ... ]`. Rozšířený test `[[ ... ]]` umožňuje AND/OR jako `&&` a `||` a má má `=~` pro regex.

```bash
test -f soubor.txt && echo "soubor existuje"
[ -d /etc ] && echo "je to složka"

name="alice42"
if [[ -n $name && $name =~ ^[a-z]+[0-9]+$ ]]; then
  echo "OK"
else
  echo "INVALID"
fi
```

Podmínka tedy pracuje s příkazem a jeho návratovou hodnotou, na což je `test` velmi vhodný.

## Příklady skriptů

### 1) Kontrola přihlášeného uživatele

```bash
#!/bin/bash
if [[ -z "$1" ]]; then
  echo "Error: no user provided"
  exit 2
fi

COUNT=$(who | cut -d " " -f 1 | grep -cw "$1")

if [[ $COUNT -eq 0 ]]; then 
  echo "User $1 is not logged in"
else
  echo "User $1 is present"
fi
```

### 2) Ukaž přihlášené uivatele a aktualizuj každou sekundu

```bash
#! /bin/bash
while true; do
  echo "Prihlaseni uzivatele $(date), celkem $(who | wc -l):"
  who
  sleep 1
  clear
done
```

### 3) Jednoduché for cykly

Čísla od 1 do 10:

```bash
#! /bin/bash
for i in {1..10}; do
  echo "Cislo $i"
done
```

Soubory a složky v aktuálním adresáři:

```bash
for i in *; do
  [[ -d "$i" ]] && echo "SLOZKA: $i"
  [[ -f "$i" ]] && echo "SOUBOR: $i"
done
```

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
