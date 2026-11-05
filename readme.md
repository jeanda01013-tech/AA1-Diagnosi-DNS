# PRÀCTICA DNS – TechCloud Solutions

- Alumne: Jean
- Sistema operatiu: Zorin OS
- Eines utilitzades: Terminal Linux, DIG i NSLOOKUP
- Domini analitzat: tecnocampus.cat

# 1. Introducció

L'objectiu d'aquesta pràctica és realitzar una auditoria de resolució de noms DNS utilitzant eines de línia d'ordres en un entorn Linux amb Zorin OS.

Durant la pràctica s'han realitzat consultes DNS amb les eines dig i nslookup, comprovant diferents tipus de registres, servidors autoritatius i respostes DNS.

També s'ha comprovat el funcionament del protocol mDNS mitjançant la resolució de noms de dispositius dins d'una xarxa local.

# 2. Configuració de la màquina virtual

S'ha utilitzat una màquina virtual amb Zorin OS configurada amb dues interfícies de xarxa:

enp0s3: adaptador en mode NAT per disposar de connexió a Internet.

enp0s8: adaptador en mode pont (Bridged) per permetre la comunicació amb altres dispositius de la xarxa local. 

![](./IMG/Cap%201.png)

```bash 
ip a
```
- A la captura s'observa que la interfície enp0s3 té l'adreça IP 10.0.2.15/24 i la interfície enp0s8 té l'adreça IP 192.169.6.1/24. Totes dues interfícies apareixen actives.
# 3. Diagnosi DNS amb DIG

## 3.1. Consulta del registre A

- S'ha executat l'ordre:

![](./IMG/Cap%202%20.png) 

```bash 
dig tecnocampus.cat A
```

L'objectiu d'aquesta consulta és obtenir les adreces IPv4 associades al domini.

Els resultats obtinguts han estat:

- 108.157.113.8

- 108.157.113.59

- 108.157.113.90

- 108.157.113.83

El TTL obtingut és de 60 segons. Això significa que la informació pot romandre emmagatzemada a la memòria cau durant 60 segons.
El servidor DNS que ha respost és 127.0.0.53, utilitzant el port 53.

## 3.2. Consulta dels servidors de noms NS
- S'ha executat:

```bash 
dig tecnocampus.cat NS
```

![](./IMG/Cap%203%20.png)

Aquesta consulta permet identificar els servidors de noms autoritatius del domini.

Els servidors obtinguts són:

ns-535.awsdns-02.net.
ns-1071.awsdns-05.org.
ns-130.awsdns-16.com.
ns-1689.awsdns-19.co.uk.

El TTL mostrat és de 86400 segons.

Aquests servidors són els encarregats de proporcionar informació DNS autoritativa sobre el domini.

## 3.3. Consulta del registre SOA

- S'ha executat:
```bash
dig tecnocampus.cat SOA
```
![](./IMG/Cap%204.png) 

El registre SOA (Start of Authority) conté informació administrativa sobre la zona DNS.

Les dades que cal identificar són:

- RNAME: correu electrònic de l'administrador de la zona DNS.

- SERIAL: número de sèrie que identifica la versió de la zona DNS.

El número de sèrie permet identificar les actualitzacions de la zona DNS i comprovar si ha canviat la seva informació.

## 3.4. Consulta de resolució inversa

- S'ha executat: 

```bash
dig -x 108.157.113.8
```
![](./IMG/Cap%205.png)

- S'ha utilitzat una de les adreces IP obtingudes en la primera consulta.

La resolució inversa permet obtenir el nom de domini associat a una adreça IP mitjançant els registres PTR.

Resultat obtingut:

- IP consultada: 108.157.113.8

- Nom de domini obtingut: server-108-157-113-8.mad56.r.cloudfront.net.

Aquesta consulta permet realitzar el procés contrari a una resolució DNS directa, passant d'una adreça IP a un nom de domini.

# 4. Comprovació DNS amb NSLOOKUP

## 4.1. Consulta bàsica no autoritativa

- S'ha obert l'eina en mode interactiu mitjançant:
```bash
nslookup
```
- Posteriorment, s'ha seleccionat el tipus de registre A:

```bash
set type=A
tecnocampus.cat
```
![](./IMG/Cap%206%20.png)

![](./IMG/Captura%20de%20pantalla%202026-11-05%20191824.png)

El servidor DNS utilitzat ha estat 127.0.0.53.

S'han obtingut les adreces IP següents:

- 108.157.113.90

- 108.157.113.83

- 108.157.113.59

- 108.157.113.8

La resposta apareix com a Non-authoritative answer perquè el servidor utilitzat actua com a resolvedor DNS i no és el servidor autoritatiu del domini.

Això significa que retorna informació obtinguda mitjançant un altre servidor DNS, en lloc de respondre directament des de la zona DNS autoritativa.

## 4.2. Consulta directa al servidor autoritatiu

- Primer, s'ha canviat el servidor DNS utilitzant l'ordre:
```bash
server ns-535.awsdns-02.net
```
![](/AA1-Diagnosi-DNS/IMG/Cap%206%20.png)

- El servidor seleccionat és ns-535.awsdns-02.net, la seva adreça IP és 205.251.194.23.

Després, s'ha realitzat novament la consulta:

```bash
tecnocampus.cat
```

![](/AA1-Diagnosi-DNS/IMG/Cap%208.png)

S'han obtingut les adreces IP següents:

- 18.172.213.22

- 18.172.213.97

- 18.172.213.8

- 18.172.213.110

En aquesta consulta s'ha contactat directament amb el servidor de noms seleccionat, en lloc d'utilitzar el resolvedor local 127.0.0.53.

En comparar les dues consultes, s'observa que les adreces IP obtingudes són diferents. Això pot ser degut al fet que el domini utilitza respostes DNS diferents segons el servidor consultat.

# 5. Resolució local mitjançant mDNS

## 5.1. Comprovació del hostname

- S'ha executat l'ordre:

```bash
hostname
```

![](/AA1-Diagnosi-DNS/IMG/Cap%209.png)

El nom de màquina (hostname) obtingut ha estat:

- jean-VirtualBox

Aquest nom identifica la màquina Zorin OS dins de la xarxa.

## 5.2. Prova de resolució mDNS

Des d'un altre equip amb Windows connectat a la mateixa xarxa s'ha executat:

```bash 
ping jean-VirtualBox.local
```
![](/AA1-Diagnosi-DNS/IMG/Cap%2010.png)

El resultat ha estat satisfactori. L'equip Windows ha aconseguit resoldre el nom jean-VirtualBox.local a l'adreça IP 192.169.6.1.

Els resultats de la prova han estat:

- Paquets enviats: 4.

- Paquets rebuts: 4.

- Paquets perduts: 0.

- Percentatge de pèrdua: 0 %.

- Temps mitjà: 156 ms.

Això demostra que s'ha aconseguit localitzar i comunicar amb la màquina Zorin OS mitjançant el seu nom local.