# UOS cvičení 2

## Vstup, výstupy a roury (pipes)

- **stdin** – standardní vstup
- **stdout** – standardní výstup (1)
- **stderr** – chybový výstup (2)

```bash
# Pipes: sort + uniq (uniq potřebuje seřazený vstup)
cat data.txt | sort | uniq -c | sort -nr
# => spočítá výskyty a seřadí je od nejvyšších
# => roura (pipe) předá výstup

# STDIN/STDOUT/STDERR:
ls /etc/hosts /neexistuje /root >ok.txt 2>err.txt
# 1> stdout do ok.txt, 2> stderr do err.txt

ls /etc/hosts /neexistuje /root 2>/dev/null | wc -l
# pipe nese jen stdout; chyby zahodíme

ls /etc/hosts /neexistuje /root 2>&1 | grep -E 'denied|file'
# sloučíme stderr do stdout
```

## Číslování, začátky a konce souborů

```bash
nl /etc/passwd
# nl očísluje řádky

head -n 5 /etc/passwd     # nebo: head -5
# head vytiskne prvních N řádků

tail -n 5 /etc/passwd     # posledních N řádků
tail -n 5 -f /var/log/syslog
# -f sleduje soubor v reálném čase (logy)
```

## Převod znaků, počítání, výběr sloupců

```bash
cat vstup.txt | tr 'abc' 'xyz'
# tr nahrazuje znaky z první množiny znaky z druhé (a->x, b->y, c->z)

tr '[:lower:]' '[:upper:]' < text.txt
# převod na VELKÁ písmena

wc -l text.txt   # počet řádků
wc -w text.txt   # počet slov
wc -c text.txt   # počet bajtů/znaků

cut -c2-10 soubor.txt
# výběr znaků podle pozic (2. až 10. včetně)

cut -d ':' -f 1,3,5 /etc/passwd
# -d nastaví oddělovač, -f vybere pole 1,3,5

echo "$(who)" | cut -d " " -f 1,4
# příkazová substituce a výběr 1. a 4. pole

paste -d "--" uid.txt jmeno.txt shell.txt
# horizontální sloučení souborů se separátorem "--"
```

## Regulární výrazy

```bash
# Kotvy a znaky
^        # začátek řádku
$        # konec řádku
.        # libovolný znak (kromě \n)
[abc]    # kterýkoli ze znaků a,b,c
[^abc]   # negace: cokoli kromě a,b,c
[a-z]    # rozsah

# Kvantifikátory
*        # 0..∞ výskytů (chamtivé)
+        # 1..∞
?        # 0 nebo 1
{m}      # přesně m
{m,}     # m..∞
{m,n}    # m..n

# Praktické vzory
'^[[:space:]]*$'       # prázdný (nebo whitespace) řádek
'^[0-9]+$'             # celý řádek jen číslice
'^[A-Za-z0-9._-]+$'    # "bezpečný" název (jen vybrané znaky)
'\b([0-9]{3}-){2}[0-9]{3}\b'  # jednoduchý vzor pro číslo ve formátu 123-456-789
```

## Grep

```bash
grep "pattern" soubor.txt        # pattern může být regulární výraz  
grep -v "pattern" soubor.txt     # -v negace (řádky, které NEodpovídají)
grep -i "pattern" soubor.txt     # -i ignoruje velikost písmen
grep -c "pattern" soubor.txt     # -c pouze počet shod
grep -n "pattern" soubor.txt     # -n s čísly řádků
grep -e "foo" -e "bar" soubor.txt # -e více vzorů (OR)
```

## Sed

```bash
sed 's/:/---/' /etc/passwd
# s/A/B/ nahradí první výskyt A za B na řádku
# lze použít regulární výraz 

sed 's/:/---/g' /etc/passwd
# s příznakem g (global) pro všechny výskyty na řádku

sed 's/^/user> /g' passwd-file.txt
# vloží "user> " na začátek každého řádku (kotva ^)
```

## Awk (programovatelný filtr)

```bash
# Základy: oddělovač a výběr polí
awk -F ':' '{print $1, $3, $NF}' /etc/passwd   # první, třetí a poslední pole

# Filtrace: jen řádky s podmínkou
awk -F',' '$3 > 100 {print $1, $3}' data.csv   # třetí pole > 100

# Sumarizace a agregace
awk -F',' '{sum+=$3} END{print "Součet:", sum}' data.csv
```
