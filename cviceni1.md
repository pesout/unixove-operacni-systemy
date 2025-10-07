# UOS cvičení 1

## Připojení / odhlášení

```bash
ssh <uzivatel>@<server> -p <port> # Připojí se přes SSH na server
exit                              # Ukončí aktuální shell/session (odhlášení)
logout                            # Alternativa k exit
sudo reboot                       # Restartuje systém (vyžaduje sudo)
```

## Uživatelé a přepnutí identity

```bash
sudo adduser <uzivatel> # Vytvoří uživatele interaktivně (heslo, HOME, shell)
sudo useradd <uzivatel> # Nízká úroveň: vytvoří účet bez průvodce (bez HOME)
su <uzivatel>           # Přepne identitu na daného uživatele (zadá se jeho heslo)
```

## Syntaxe a mezery

```bash
ls -l -a /etc/cron.d # Správný zápis: ppřepínače (-l -a) a argument (cesta)
ls -la /etc/cron.d   # Zkrácený zápis: sloučené přepínače
ls /etc/cron.d -al   # Změna pořadí přepínačů a argumentů většinou nevadí
ls -a-l /etc/cron.d  # ŠPATNĚ – Pomlčka mezi `-a-l` nesmí být
ls - a /etc/cron.d   # ŠPATNĚ – Mezera mezi pomlčkou a přepínačem nesmí být
```

Unix je case-sensitivní – `ls` není totéž co `LS`.

## Prohlížení souborů a umístění

```bash
ls                 # Vypíše obsah aktuální složky
ls -l              # Dlouhý formát (práva, vlastník, velikost, čas)
ls -a              # Zahrne skryté položky (tečkové soubory)
ls -al /etc/cron.d # Kombinace přepínačů + explicitní cesta
ls -l --inode      # Dlouhý formát + zobrazení inode
pwd                # Vypíše aktuální pracovní adresář (cestu)
```

## Navigace a práce s adresáři

```bash
cd                 # Přesun do domovského adresáře ($HOME)
cd /etc            # Přesun do zadané absolutní cesty
cd ..              # O úroveň výš (rodič)
mkdir Music        # Vytvoří adresář Music
rmdir Music        # Smaže prázdný adresář Music
rm -r Music        # Smaže adresář rekurzivně včetně obsahu
```

Význam zkratek adresářů: `~` (HOME), `.` (aktuální), `..` (nadřazený), `/` (kořenový)

## Práce se soubory

```bash
touch dokument.txt     # Vytvoří prázdný soubor dokument.txt
rm dokument.txt        # Smaže soubor dokument.txt
mv dokument.txt Music  # Přesune soubor dokument.txt do adresáře Music
cp dokument.txt Music  # Zkopíruje soubor dokument.txt do adresáře Music
```

## Proměnné a příkazová substituce

```bash
echo "Hello world!" # Vytiskne text do terminálu
echo $HOME          # Vypíše hodnotu proměnné prostředí HOME
echo $USER          # Vypíše uživatelské jméno aktuálního uživatele
ls /home/$(whoami)  # Substituce $(...) vloží výstup whoami jako argument
ls $(pwd)           # Předá aktuální cestu (výstup pwd) příkazu ls
```

## Kde leží binárky

```bash
ls /bin /sbin /usr/bin /usr/sbin # Typická umístění systémových nástrojů
which ls                         # Zjistí, odkud se spouští ls
whereis ls                       # Najde binárku/zdrojáky/manuál pro ls
```

## Historie příkazů

```bash
history             # Vypíše historii zadaných příkazů s čísly
!4                  # Spustí znovu příkaz s číslem 4 z historie
!!                  # Zopakuje úplně poslední příkaz
cat ~/.bash_history # Vypíše obsah souboru s historií
clear               # Vyčistí obrazovku
```

## Nápověda a návratový kód

```bash
man ls  # Otevře manuálovou stránku pro ls
echo $? # Vypíše návratový kód posledního příkazu (0=OK, nenula=chyba)
```

## Zástupné znaky a expanze

```bash
cp /usr/include/*std* .    # Zkopíruje soubory obsahující "std" do aktuální složky
mv std* ~/libs             # Přesune vše začínající "std" do ~/libs
mv *.h ~/libs/unix         # Přesune všechny hlavičkové soubory .h do cílové složky
mv file??.txt ./velke      # Přesune soubory "fileXX.txt" (přesně 2 znaky místo "??")
mv file[1-5].txt ./cisla   # Přesune soubory s čísly 1–5 v názvu
cp file{1,3,5}.txt ./liche # Zkopíruje konkrétní soubory 1, 3 a 5
touch file{1..5}.txt       # Vytvoří file1.txt až file5.txt
mkdir myfiles_{odd,even}   # Vytvoří složky myfiles_odd a myfiles_even
touch when\? where\? why\? # Vytvoří soubory s otazníkem v názvu (escapování "?")
```

## Tipy pro práci v terminálu

- **TAB** – doplňování pro rychlejší a přesnější psaní cest, názvů souborů atd.
- **CTRL + C** – ukončení běžícího příkazu
- **CTRL + L** – vyčistit obrazovku
- **CTRL + A** – přesun kurzoru na začátek
- **CTRL + E** – přesun kurzoru na konec
- **CTRL + U** – smazat text před kurzorem
- **CTRL + K** – smazat text za kurzorem
- **CTRL + W** – smazat slovo před kurzorem
- **CTRL + T** – vzájemně přehodit dva znaky vlevo od kurzoru
















