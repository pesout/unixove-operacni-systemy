# UOS cvičení 5

## Inode

- **unikátní identifikátor souboru/adresáře** ve filesystému
- jméno souboru = jen položka v adresáři, která ukazuje na inode
- výpis obsahu adresáře včetně inodes: `ls -li`

## Linky (odkazy)

### Pevný link (hard link)

- lze vytvořit pomocí `ln puvodni.txt novy.txt`
- **další jméno pro stejný soubor**
- ukazuje na **stejný inode jako původní soubor**
- když smažu původní soubor, hard link zůstane
- nefunguje na složky, aby se zamezilo cyklům

### Symbolický link (symlink)

- lze vytvořit pomocí `ln -s puvodni.txt novy.txt`
- **symlink je jiný soubor s vlastním inode**
- obsahuje **textovou cestu k cíli**
- když smažu originál, odkaz zůstane, ale je rozbitý
- může mířit na adresář, soubor i na neexistující cestu

## Procesy

- **Proces** = jedna konkrétní běžící instance programu v systému (má unikátní PID).

```bash
ps      # vypíše procesy aktuálního terminálu (aktuální shell)
ps x    # procesy daného uživatele bez TTY filtru
ps u    # podrobnější info (CPU, paměť, start, příkaz)
ps aux  # "všechno": všechny procesy všech uživatelů
ps aux | grep název  # hledání procesu podle názvu

top     # interaktivní přehled běžících procesů, CPU, RAM (q = quit)
htop    # hezčí verze top

pstree  # zobrazí procesy jako strom (rodič–potomek)
```

### Jobs a signály

- **Job** = úloha spravovaná shellem, jeden program nebo celá pipeline

```bash
./skript.sh    # spustí úlohu v popředí
./skript.sh &  # spustí úlohu v pozadí
```

Úlohu v popředí lze zastavit (stopnout) pomocí `CTRL+Z`.

```bash
bg          # přenese stopnutý proces do pozadí
jobs        # seznam úloh
fg %1       # přenese úlohu 1 do popředí

kill %1     # pošle úloze 1 signál, že se má ukončit (SIGTERM)
kill -9 %1  # hned zabije úlohu 1 (SIGKILL)
kill PID    # zabije proces, PID lze zjistit např pomocí "ps aux"
kill -l     # vypíše seznam signálů a jejich čísla

# "trap" – odchytávání signálů v shell skriptu
trap 'echo "Chycen SIGINT";' INT   # při Ctrl+C se místo ukončení provede echo

# "nohup" – spustí příkaz tak, aby běžel i po odhlášení z terminálu
nohup ./skript.sh  >>success.log  2>/dev/null & # 

```

## Základní síťové příkazy

```bash
ping pef.czu.cz       # posílá ICMP pakety, test dostupnosti hosta
ping -c 4 pef.czu.cz  # pošle 4 pakety a skončí

ifconfig  # vypíše síťová rozhraní

# cesta k serveru (hvězdičky znamenají, že nepřišla odpověď, např. timeout, firewall)
traceroute pef.czu.cz       # cesta bude spíše kratší
traceroute ioranahotel.com  # cesta bude spíše delší

whois czu.cz  # informace o vlastníkovi domény, technických kontaktech apod.
dig czu.cz  # DNS konfigurace

nmap pef.czu.cz       # otevřené porty
nmap -p 80 localhost  # zkontroluje, zda má host otevřený port 80

# "curl" – nástroj pro přenos dat ze serveru a na server
# podporuje mnoho protokolů, viz "man curl"  
curl "https://www.wikipedia.org"     # přenese a vypíše data ze serveru
curl -I "https://www.wikipedia.org"  # vypsat pouze hlavičky
```

Přehled dalších síťových příkazů naleznete např. [zde](https://www.geeksforgeeks.org/linux-unix/linux-network-commands-cheat-sheet).
