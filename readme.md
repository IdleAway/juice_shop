# Jucieshop notes

## Install VScode
[Download visualstudio](https://code.visualstudio.com/Download) .deb
```bash
sudo dpkg -i /home/$USER/Download/code_1.79.2-1686734195_amd64.deb
```

## Install docker

Kali/Parrot:
```bash
sudo apt update && sudo apt upgrade
sudo apt install -y docker.io
sudo systemctl start docker.io
sudo usermod -aG docker $USER
```

## Setup Juiceshop

[Docker Hub Juiceshop](https://hub.docker.com/r/bkimminich/juice-shop)

```bash
docker pull bkimminich/juice-shop
docker run --rm -p 3000:3000 bkimminich/juice-shop
```

[Hat alles geklappt? - Shoplink](http://localhost:3000/)

## Burp Addons
### PwnFox colored proxy tabs
<br>

#### Firefox

---

[Addon](https://addons.mozilla.org/de/firefox/addon/pwnfox/)

Linksklick auf den Fuchs:
- "Enabled" checkbox setzen
- "Proxify only container" tabs checkbox setzen

<br>

#### Burpsuite
---
[PwnFox.jar (git-repo -> releases)](https://github.com/yeswehack/PwnFox)

Im Reiter `Extensions` im oberen Teil `Burp extensions` auf den Button `Add` klicken. Im folgenden Kontextmenü die `Extension file (.jar)` auswählen (/home/$USER/Downloads/PwnFox.jar) und mit `Next` bestätigen.

## Juice-Shop
### Einstieg

Um alles zu Dokumentieren was wir tun legen wir uns ein neues Verzeichniss an:
```bash
mkdir juiceshop && cd juiceshop
```
Burp starten ein mit `Temoprary project` fortfahren. Firefox starten, rechtsklick auf `Neuen Tab` und einen farbigen Tab starten um den Proxy zu benutzen.

Zum guten Ton gehört es sich zunächst über portentielle "Terms of Contidion" (ToC) und die `Privacy policy` zu informieren, damit man auch weiß mit wem man es zu tun hat x).

*Aufgabe1*
1. Registriere einen neuen Account
2. Informiere dich über die `Privacy policy`

*Aufgabe2*
1. Finde den Link zu den ToC?
2. Schränken die ToC den Test ein?
3. Gibt der Link Informationen preis?
4. Was ist das Problem?

### Fuzzing

Die Analyse des Pfades der ToC-Datei ergab, dass der Endpunkt `/ftp` den Inhalt eines Ordners bereitstellt. Die Vermutung liegt nahe, dass es noch andere Endpunkte geben könnte, die ebenfalls Dateien aus dem Dateisystem des Server bereitstellen.

Um nach validen Endpunkten auf einem Server zu suchen kann `fuzzing` verwendet werden. Beim Fuzzing kann ein Parameter in der Anfrage dynamisch ausgetauscht werden. Die Datengrundlage für den ausgetauschten Parameter bildet meist eine Wörterliste. Solche Listen sind meist thematisch geordnet. Für alles folgende werden die Listen von [SecLists](https://github.com/danielmiessler/SecLists) verwendet.

Mit dem Tool `ffuf`(Fuzz faster you fool) und der `common.txt` werden nach weiteren Endpunkten gesucht.

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u http://localhost:3000/FUZZ

#Gefiltert nach nicht 404 Status Code, nicht 0 oder 4987 Response Size, colored
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u http://localhost:3000/FUZZ -fc 404 -fs 0,1987 -c

#Output als html Datei
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u http://localhost:3000/FUZZ -fc 404 -fs 0,1987 -c -o /home/$USER/juiceshop/endpunkte.html -of html
```

*Aufgabe 3*
1. Finde das versteckte Scoreboard (BurgerMenü 'Help getting started')


### FTP Endpunkt

*Aufgabe 4*
1. Welche Dateien können runtergeladen werden?
2. Wie können die anderen Dateien trotzdem runter geladen werden?

*Rabbithole Easter Egg*
1. Löse das Easter Egg
2. Das Ei im Ei

*Regarding your incident...*
1. ?!?!

### Shop

1. Suchfunktion
1. Feedbackfunktionen
1. Warenkorb

### Scoreboard

Im Scoreboard wird der Fortschritt über die gefundenen und ausstehenden Sicherheitslücken festgehalten. Manche bieten eine "Fix" Herausforderung, indem der anfällige Code behandelt wird.

*Aufgabe 5*
1. Coding Challenge: Score Board
2. Coding Challenge: Confidential Document 
3. Coding Challenge: DOM XSS
4. Coding Challenge: Exposed Metrics
5. Coding Challenge: Outdated Allowlist

<hr>

## Apendix

### Install SecLists
Kali:
```bash
apt -y install seclists
```
From Git:
```Bash
cd /usr/share && git clone https://github.com/danielmiessler/SecLists.git
```