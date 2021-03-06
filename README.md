## Softether Serveri Seadistamine Arch
Antud õpetuses käsitleme Softether VPN serveri laadimist, käivitamist ning seadistamist. Õpetus on mõeldud Arch distrodele.
### Käivitusfailide laadimine
1. Uuendame süsteemi
```bash
pacman -Syu
```
2. Laeme Softether käivitusfailid.
* Softether veebilehelt - https://www.softether-download.com/en.aspx?product=softether

2.1 Alternatiiv meetod läbi lynx-i.

Laeme lynx-i paketi
```bash
pacman -S lynx
```
Lynxi käivitamine
```bash
lynx http://www.softether-download.com/files/softether/
```
Noole klahvidega liikudes vali versioon lähtuvalt oma süüstemist.

Vajuta ENTER valitud failile.

Vajuta ENTER Softether VPN Server.

Vajuta D, et laadida fail kettale.

Peale edukat laadimist kettale vajuta Q, et väljuda.

3. Esmane seadistus.
Navigeeri kausta, kus asub laetud fail
```bash
cd <KAUSTA ASUKOHT>
```
Paki fail lahti
```bash
tar xzvf <SOFTETHER FAIL.tar.gz>
```
Softether vajab töötamiseks järgnevaid katalooge (make, gccbinutils (gcc), libc (glibc), zlib, openssl, readline, and ncurses). Laeme need
```bash
pacman -S base-devel
```
Navigeerime lahtipakitud kausta
```bash
cd vpnserver
```
Teeme programmi käivitatavaks
```bash
make
```
Liigume ühe kausta võrra tagasi, liigutame lahtipakitud kausta suvalisse kohta (antud juhul /usr/local) ning navigeerime loodud asukohta.
```bash
cd ..
mv vpnserver /usr/local
cd /usr/local/vpnserver/
```
Kausta kaitsmiseks muudame juurdepääsuõigusi
```bash
chmod 600 *
chmod 700 vpnserver
chmod 700 vpncmd
```
Loome teenuse käivitus faili
```bash
nano /etc/init.d/vpnserver
```
Sisestame faili antud tekstti ning salvestame
```bash
#!/bin/sh
# chkconfig: 2345 99 01
# description: SoftEther VPN Server
DAEMON=/usr/local/vpnserver/vpnserver
LOCK=/var/lock/subsys/vpnserver
test -x $DAEMON || exit 0
case "$1" in
start)
$DAEMON start
touch $LOCK
;;
stop)
$DAEMON stop
rm $LOCK
;;
restart)
$DAEMON stop
sleep 3
$DAEMON start
;;
*)
echo "Usage: $0 {start|stop|restart}"
exit 1
esac
exit 0
```
Loome antud kausta, kui seda pole
```bash
mkdir /var/lock/subsys
```
Muudame juurdepääsuõigusi ning käivitame teenuse
```bash
chmod 755 /etc/init.d/vpnserver && /etc/init.d/vpnserver start
```
Kontrollime, kas teenus käivitus
```bash
sudo systemctl status vpnserver
```
Kontrollime, kas VPN server on töökorras
```bash
cd /usr/local/vpnserver
./vpncmd
```
Peale viimase käsu sisestamist avaneb Softether seadistamise käsurida.

Vali nr. 3 ning vajuta ENTER.

Kirjuta antud käsk terminali
```bash
check
```
Antud käsk kontrollib, kas süsteem on võimaline haldama Softetheri VPN serverit ja klient aplikatsiooni.

Kui kõik kontroll punktid on läbitud, võime jätkata.

### Administraatori parooli vahetamine
Avame uuesti seadistamiseks Softetheri käsurea
```bash
./vpncmd
```
Vali nr. 1 "Management of VPN Server or VPN Bridge", vajuta ENTER, et ühendada localhost serveriga ning vajuta veel ENTER, et suunduda administraatori seadidistus keskkonda.

Järgnevalt sisesta käsk
```bash
ServerPasswordSet
```
Pärast uue parooli sisestamist on administraatori parool edukalt seadistatud.

### Virtuaalse Hubi loomine

Et teha võimalikuks Softetheri kasutamine peab esmalt looma virtuaalse Hubi käsuga. Antud juhul on hubi nimeks sisestatud VPN
```bash
HubCreate VPN
```
Hubi saab valida käsuga
```bash
Hub VPN
```
### SecureNAT seadistamine ning local bridge
Softether hubidesse saab ühendada läbi SecureNAT või local bridge kaudu. Lubades ühenduse local bridge kaudu peab ka seadistama DHCP serveri ip aadresside määramiseks.

Local bridge ja SecureNAT kasutamine korraga pole soovitatud.

SecureNAT koosneb DHCP serveri ja VirtualNAT funktsioonidest.

Vpncmd kaudu saame administraatorina aktiveerida SecureNAT-i käsuga
```bash
SecureNatEnable
```
