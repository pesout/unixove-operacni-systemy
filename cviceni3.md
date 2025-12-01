# UOS cvičení 3

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
