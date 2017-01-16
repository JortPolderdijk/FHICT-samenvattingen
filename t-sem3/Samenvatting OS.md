# Samenvatting ES3 - Operating system

-- Hier komt een table of contents --

DISCLAIMER: kan fouten bevatten.

##Vakinhoud
Informatie over de inhoud van het vak zijn afkomstig uit het blokboek vanaf de portal. Het gaat in dit geval over de versie van 2016-06-29.
### Thema
De volgende thema's zijn gekoppeld aan dit vak:

* Hoe werkt het veelgebruikte embedded Linux operating systeem?
* Hoe laat je software programma's communiceren via en met een operating systeem?

### Leerdoelen
De volgende leerdoelen zijn gekoppeld aan dit vak:

* de student heeft:
	* laten zien te kunnen navigeren in het filesysteem
	* laten zien dat hij het Linux filesysteem begrijpt en dat hij daarin de toegankelijkheid van files kan instellen
	* toegelicht waar een embedded Linux distributie uit bestaat, hoe het filesysteem is ingedeeld en hoe het OS opstart
	* goede afwegingen gemaakt en gedocumenteerd bij het kiezen voor *threads* of *processen*
	* *shared memory* op zinvolle manier gebruikt en heeft daarbij op zinvolle manier gebruik gemaakt van synchronisatie primitieven
	* *message queues*, de *socket API* en de *POSIX library* op zinvolle en correcte manier gebruikt
	* alleen gebruik gemaakt van *portable datatypes*
	* *QEMU* gebruikt om het embedded platform te emuleren
* de student heeft de relevante theorie toegepast, waaronder: de juiste OS begrippen, de rol van Busybox en hoe het werkt en wat bedoeld wordt met embedded Linux
* met behulp van basis shell commando's zijn problemen opgelost
* een eigen embedded Linux distributie is middels Buildroot gebouwd, diverse onderdelen van Buildroot zijn zelf geconfigureerd en het resultaat is op een embedded platform geïnstalleerd
* een eigen applicatie is gebouwd en geïnstalleerd met een eigen Makefile
* eigen applicaties zijn gecrosscompileerd met een zelf gebouwde cross-toolchain, een van die
applicaties stuurt vanuit userspace een USB device aan, een andere past het CGI mechanisme toe
* de Busybox configuratie is aangepast, Busybox is gebouwd en geïnstalleerd op het
embedded platform
* het netwerk van het embedded platform is geconfigureerd en een webserver is geïnstalleerd

## Operating system
Een operating systeem is een programma of een geheel aan samenwerkende programma's dat na het opstarten van een computer in het geheugen geladen wordt en hardware aanstuurt.

**Taken van een OS:**

* Zorgen voor een abstractielaag tussen proces en hardware: proces hoeft niet te weten hoe hij bijvoorbeeld het geheugen uit moet lezen.
	* Processing power (CPU)
	* Memory (RAM)
	* Files (Disk)
	* Network
	* I/O (f.e. keyboard, mouse, display, ports)
* GUI, commands, libraries

### Processen
Processen voeren een functie uit.

#### Single-process
Een voorbeeld van een CPU met een single process is de ATMega328P die in een Arduino zit. Deze kan maar één process tegerlijkertijd runnen.

#### Multi-process
Minder low-level devices hebben vaak een multi-process CPU. Deze kan om gaan met meerdere processen tegelijkertijd. Processen worden opgestart door het operating system en communiceren met elkaar via IPC (**Inter-Process Communication**).

**Voordelen:**

- In-line with problem analysis: vergelijkbaar met de echte wereld, daar gebeurt ook alles tegelijk.
- Distributed / multicore execution: makkelijk te verdelen over cores van een processor.
- Mix of programming languages
- Reuse / substitution: proces kan vanuit verschillende processen aangeroepen worden, daarom is het makkelijk om code te hergebruiken.

**Nadelen:**

- Operating System (overhead) needed
- Inter-Process Communication complexity

#### Process scheduling
De *scheduler* is eigenlijk de core van het operating systeem. Het verdeelt alle beschikbare processor time. Tevens kan het reageren op diverse interrupts, zoals: system timer interrupt, I/O interrupt en system calls.

**States**

Een process heeft drie states waarin hij kan zijn, namelijk: Ready, Running, Blocked.

* Running: het proces draait op de CPU
* Ready: het proces is klaar om te draaien, maar CPU is bezet
* Blocked: het proces wacht op een event zoals een interrupt

![states of process](https://github.com/JortPolderdijk/FHICT-samenvattingen/raw/master/t-sem3/assets/OS/Picture1.png)

**Context switches**

Bij een context switch zet het OS de Program Counter weer terug, herstelt hij alle registers en laat hij de CPU weten waar hij moet draaien.

### Scheduling Algorithm
Er zijn meerdere algoritmes om te bepalen welk proces aan de beurt is. Er zijn een aantal criteria om een proces te stoppen en een ander proces te starten.

| To stop the running process                           | To select a new process               |
|-------------------------------------------------------|---------------------------------------|
| 1. wacht tot proces voltooid is                       | 1. first come first serve (
)      |
| 2. proces geeft zelf aan dat het mag                  | 2. highest priority                   |
| 3. proces met hogere prioriteit of eind van tijdsslot | 3. worst turnaround time              |
| 4. proces is blocked door resource request            | 4. shortest (expected) remaining time |
|                                                       | 5. earliest deadline                  |


**First come first served (FIFO)**

Dit is het simpelste algoritme om te bepalen wie er aan de beurt is. Een context switch vind plaats door het eindigen van een process, het blocken door een resource request of door een process met een hogere prioriteit.

**Round Robin (RR)**

RR is eigenlijk FIFO maar dan met time slicing.

### Programma <-> processen
#### Program
* Bestand met machine code en data

#### Process
* Een *instantie van een programma*, draaiend op een computer
	* Code area
	* Data area (stack, heap)
	* Program Counter (keeps track of the progress)

### POSIX
POSIX staat voor **Portable Operating System Interface** en is een familie aan standaarden en APIs voor operating systemen. *POSIX defineert welke systemcalls er allemaal moeten zijn en waar ze aan moeten voldoen (veel unix afgeleiden).*

## Embedded Linux
Embedded Linux is een customized Linux distributie met een small footprint (RAM, Flash). Daarnaast is hij customized (kernel options, drivers).

Voor embedded controllers is het belangrijk dat er real-time support aanwezig is.

### BuildRoot
BuilRoot is een **set of makefiles** and patches wat het makkelijk maakt om een compleet embedded Linux systeem te bouwen.

Door de commando's `make source` en `make` kan een image gebouwd worden.

**Configureren**

* `make menuconfig`: overall configuration (toolchain, kernel, packages, etc.)
* `make busybox-menuconfig`: BusyBox Configuration
* `make linux-menuconfig`: Linux Kernel customization

#### BusyBox
BusyBox is het Zwitsers zakmes van Embedded Linux en bestaat uit één executable die vele Linux utilities (zoals init, wget, httpd, chmod) bevat. Het kleine bestand (< 500Kb) zorgt er voor dat hij goed bruikbaar is op embedded systemen. De BusyBox binary gebruikt `argv` om uit te lezen welke applet hij moet uivoeren.

**Hoe te gebruiken?**

* `busybox --list`: haal alle applets op binnen BusyBox. Deze zijn vervolgens uit te voeren door het volgende te doen: `busybox ifconfig`.
* `ln -s busybox ifconfig` maakt een symbolic link aan voor ifconfig. Hierdoor kan nu `ifconfig` uitgevoerd worden.
* `busybox --install` maakt voor alle applets een symbolic link.

#### Network
Het is ook belangrijk om een goed netwerk in te kunnen stellen. Zonder netwerk kunnen boot messages alleen via HDMI of RS232 (serial) bekeken worden op een Raspberry Pi. Vandaar dat het belangrijk is om via internet te communiceren. Via het commando `dmesg` kunnen berichten van het booten gelezen worden.

**/etc/network/interfaces**

Er zijn twee manieren om je Linux te koppelen aan het internet. Via een statisch ip of via een dynamisch ip.

Statisch IP-adres:
```auto eth0
iface eth0 inet static
adresss 10.0.0.42
netmask 255.255.255.0
gateway 10.0.0.1
```
Dynamisch IP-adres:
```auto eth0
iface eth0 inet dhcp
```

Voor wifi, gebruik de tool [wpa_supplicant](https://wiki.archlinux.org/index.php/WPA_supplicant).

**Gebruik maken van het netwerk**
Remote login: `ssh root@10.0.0.42`
Copying files to target: `scp helloworld root@10.0.0.42:/bin`

#### Embedded webserver
De httpd (HTTP deamon) kan HTML of text naar de browser toesturen. Deze files kan hij vanaf de disk lezen, of hij start CGI scripts om deze content te kunnen generen.
CGI scripts kunnen zijn: Shells (bash), of Binaries (C/C++).

Een CGI programma is altijd bereikbaar via http://localhost/**cgi-bin/**

Alle output gaat via `stdout`. De input ligt aan het request-type. Bij een GET-request komt hij via het enviroment binnen en bij een POST request is het de `stdin`.
GET: `qs = getenv("QUERY_STRING");`
POST: `qslength = getenv("CONTENT_LENGTH");`

![CGI](https://github.com/JortPolderdijk/FHICT-samenvattingen/raw/master/t-sem3/assets/OS/Picture2.png)

## Linux Distribution

![CGI](https://github.com/JortPolderdijk/FHICT-samenvattingen/raw/master/t-sem3/assets/OS/Picture3.png)

Een Linux distributie bestaat uit de volgende drie dingen:

* Bootloader
	* alternatieven: grub, syslinux, u-boot
* Kernel
	* Gemaakt voor specifieke CPU architectuur
	* Met drivers en opties voor die drivers
* Root Filesystem

| Path  |   |
|-------|---|
| /dev/ |  handles to devices |
| /etc/ |  configuration files & scripts |
| /bin/ |  set of binaries |
| /lib/ |  set of libraries |
| /sbin/ | set of system binaries (like init and shutdown) |

### Storage organisation
![Storage](https://github.com/JortPolderdijk/FHICT-samenvattingen/raw/master/t-sem3/assets/OS/Picture4.png)
De storage bestaat uit blocks (sectoren) van 512 bytes. Het allereerste block bevat het MBR.

**Master Boot Record (MBR)**
Het Master Boot Record bestaat uit drie areas:
- Het bootloader programma (stage 1)
- De partitietabel
- Het MBR kenmerk (`0x55, 0xAA`)

### BOOTING
![Booting](https://github.com/JortPolderdijk/FHICT-samenvattingen/raw/master/t-sem3/assets/OS/Picture5.png)

### ROOT FILE SYSTEM
Een aantal belangrijke commando's om te weten:
Schrijf naar SD-kaart: `dd`

## USB

### GPIO
Belangrijk om te onthouden: alles in Linux is een file. Zo ook de GPIO pinnen. De pinnen kunnen binnen linux niet direct vanaf de user aangesproken worden. Hier is root toegang voor nodig.
De pinnen zijn toegankelijk via SYSFD: `/sys/class/gpio/...`. Maar gelukkig zijn er genoeg libraries die het vieze werk al voor ons doen.

### USB
In een USB 'netwerk' is altijd maar één host aanwezig. Deze host kan maximaal 127 devices aansturen. De host pollt naar alle devices, wijst deze een uniek nummer aan (dit noem je **enumeratie**) en moet altijd het initiatief nemen om iets te doen.

#### Pipes & endpoints
Een pipe is een logische link tussen host en device. Een endpoint is een bron of eindbestemming van data.
OUT: host -> device IN: device -> host (op USB device)

Er zijn verschillende typen endpoints.

| Type                  | Beschrijving                       | Vertraging  | Herhaling | Foutcorrectie | Voorbeeld                     |
|-----------------------|------------------------------------|-------------|-----------|---------------|-------------------------------|
| Control endpoint      | Besturingsverzoek aan het apparaat |             | Max 3x    |               | SET_INTERFACE                 |
| Interrupt endpoint    | Kleine hoeveelheid data            | 1ms - 32 ms | Ja        |               | - Muis                        |
| Bulk endpoint         | Middelmatige hoeveelheid data      |             | Ja        | Ja            | - Scanner data                |
| Isochronous endpoints | Grote hoeveelheid data           |             | Nee       | Nee           | - Audio stream & Video stream |

#### Device descriptor
Om met een device te kunnen communiceren moet je een aantal gegevens hebben. Je kan deze ophalen met behulp van commando `lsusb -v`. Vervolgens heb je de interface descriptors nodig (idVendor en idProduct) en het adres van de endpoints welke je aan wilt spreken.

#### LibUsb
Je kunt [libusb](http://libusb.info/) gebruiken om makkelijker met usb devices te kunnen communiceren.
Om libusb te kunnen gebruiken moet je het volgende toevoegen aan je script: `#include <libusb-1.0/libusb.h>`. Daarnaast moet je compilen met de `-lusb-1.0` flag.

## Compilation
### Cross-compilation
Om te kunnen cross-compileren moet je gebruik maken van een toolchain. Bij BuildRoot wordt er met de standaard instellingen ook al één voor je aangemaakt die je kunt gebruiken. Een voorbeeld van het cross-compileren is: `arm-linux-gcc -o myprog myprog1.c -lusb-1.0 -lrt –lpthread`

### Makefile
Omdat het wel makkelijk is om bestanden automatisch te kunnen laten bouwen, kunnen we een Makefile maken. `make` bouwt de eerste target uit een Makefile. `make <targetname>` bouwt de gekozen target.

```makefile
target: prerequisites
	recipe
```
**Voorbeeld van een Makefile**

```makefile
# Phony targets (i.e. not really files)
.PHONY: all clean

all: helloworld

helloworld: helloworld.c
	gcc -o helloworld helloworld.c
	@echo Helloworld is made
clean:
	rm -f helloworld helloworld.o
```

`.PHONY` is gebruikt om er voor te zorgen dat deze target altijd uitvoerd kan worden en er niet wordt gekeken of de files wel aangepast zijn.

**Aantal handigheidjes**

|                 | Description                            |
|-----------------|----------------------------------------|
| $(SOURCES)      | gebruik variabel SOURCES               |
| $(SOURCES:.c=.o) | vervang .c naar .o in variabel SOURCES |
|                 |                                        |
| $@              | Name of target                         |
| $^              | All prerequisites                      |
| $<              | First prerequisite                     |
| CC              | C compiler                             |
| CXX             | C++ compiler                           |

Het make commando doet alleen iets als één van de bronbestanden ook daadwerkelijk nieuwer is als wat hij al gecompileerd heeft.

## Forking, threading & sockets

### Forking
Bij `fork()` cloned een process zichzelf. En gaat verder vanaf de regel waarop `fork()` is uitgevoerd. Dit commando geeft 0 terug als het een child process is en een childId als het een parent process is.

Bij `execve()` vervangt een process zichzelf met een geclonede variant.

### Multi-threading
![Threading](https://github.com/JortPolderdijk/FHICT-samenvattingen/raw/master/t-sem3/assets/OS/Picture6.png)

**Waarom threads of forks?**

* Wachttijd benutten
* Multi-cores benutten

#### POSIX threads
Om POSIX threads te kunnen gebruiken moet je compilen met de flags `-lrt -phtread`.

| POSIX function   |                                                         |
|------------------|---------------------------------------------------------|
| pthread_create() | Maakt en start thread met opgegeven functie             |
| pthread_join()   | Blijft hangen totdat de meegegeven threadid is voltooid |
| pthread_exit()   | Hiermee kan een thread zichzelf afsluiten               |

**Voorbeeld**

```c++
#include <pthread.h>
#include <stdio.h>

void *PrintHello(void *m)
{
   printf("%s\n", m);
   pthread_exit(NULL);
}

int main (int argc, char *argv[])
{
   pthread_t thread0, thread1;
   pthread_create(&thread0, NULL, PrintHello, (void *)"hello");
   pthread_create(&thread1, NULL, PrintHello, (void *)"world");

   pthread_join(thread0, NULL);
   pthread_join(thread1, NULL);

   pthread_exit(NULL);
}
```

### Sockets
Er zijn verschillende socket types, namelijk:

* Stream socket interface
* Datagram socket interface
* Raw socket interface

Sockets kunnen gebruikt worden als filedescriptors. Je maakt ze zo aan:

```c++
s = socket(family, type, protocol);
```
Hierbij moet je de volgende tabel gebruiken:
![Sockets](https://github.com/JortPolderdijk/FHICT-samenvattingen/raw/master/t-sem3/assets/OS/Picture7.png)

## Shared memory & semaphores

### Shared Memory
Met behulp van shared memory kunnen meerdere processen bij een stuk geheugen.

**Voorbeeld**

```c++
int main(){
    int shm_fd = 0;
    void *vaddr = NULL;

    shm_fd = shm_open("my_shm", O_CREAT | O_RDWR, 0666);
    ftruncate(shm_fd , SIZE) ;
    vaddr = mmap(0, SIZE, PROT_WRITE, MAP_SHARED, shm_fd, 0);
    mlock(vaddr, SIZE) ;

    /*** Shared memory is ready for use ***/

    munmap(vaddr, SIZE);
    close(shm_fd);
    shm_unlink("my_shm");

    return 0;
}
```

**Critical section**
Een critical section is een stuk code wat door maar één thread tegelijk bewerkt mag worden omdat er een gedeelde resource wordt gebruikt.

#### Mutual Exclusion
ME zorgt ervoor dat als een process in een critical section bevindt, dat hij de enige is die daar in zit. Dit voorkomt bijvoorbeeld dat twee processen tegelijkertijd in het zelfde stukje geheugen lezen en/of schrijven.
Bij het ontwerpen van een mutual execution algoritme moet met de volgende problemen rekening worden gehouden:

* deadlock
* livelock
* starvation

### Semaphores
Een voordeel van een semaphore is dat je geen busy-waiting hebt. Een nadeel is wel dat het OS semaphores wel moet ondersteunen.

```c++
#include <semaphore.h>

sem_init
sem_open
sem_post / sem_wait
sem_close
sem_unlink
```

## Producer / Consumer & Message Queues
Producer en Consumer probleem gaat over het hebben van een buffer met behulp van semaphores.

### Producer / Consumer

![PC](https://github.com/JortPolderdijk/FHICT-samenvattingen/raw/master/t-sem3/assets/OS/Picture8.png)
Als je een buffer hebt om iets door te geven aan een ander process, wil je wel precies weten waar je in de buffer zit. Met behulp van een **bounded buffer** kunnen we dit probleem oplossen.

`n = 0 (in - out), s = 1, e = sizeof(buffer)`
*Producer*

```c++
while (true)
{
   sem_wait(e);
   sem_wait(s);
   append();
   sem_post(s);
   sem_post(n);
}
```

*Consumer*

```c++
while (true)
{
   sem_wait(n);
   sem_wait(s);
   take();
   sem_post(s);
   sem_post(e);
}
```

### Message Queues
Een makkelijkere oplossing voor dit probleem is door gebruik te maken van een message queue.

**Voordelen**

* Niet nodig om shared memory aan te maken
* Geen queue administratie nodig
* Geen semaphoren nodig
* Voor gealloceerde message buffers
* Berichten met prioriteit
* Send and receive functie zijn synchronous (mogelijkheid om een timeout in te stellen)
* Asynchronous delivery notifications

| Function                 |                                                 |
|--------------------------|-------------------------------------------------|
| mq_open()                | Open a (blocking or non-blocking) message queue |
| mq_send() / mq_receive() | Send / Receive message                          |
| mq_close()               | Close the queue                                 |
| mq_unlink()              | Destroy the queue when all have unlinked        |

*De naam van een Message Queue moet altijd met een / beginnen. Bijvoorbeeld: `/mijn_mq`.*

## Deamon
Een deamon is een programma wat losgekoppeld is van de terminal. Dit kan op twee manieren:

- Hij kan zichzelf loskoppelen door te *forken*. 
- Hij kan zichzelf loskoppelen door `deamon(0, 0);`


# Vragen & antwoorden

| Vraag | Antwoord |
|------| ------|
| Wat is een proces? | Een proces voert een functie binnen een programma uit. |
|Wat zijn de voor- en nadelen van het opdelen van je applicatie in meerdere processen?|
|Wat is en wat doet een operating system?
|Welke voorbeelden van schaarse resources ken je?
|Wat doet een scheduler in een operating system?
|Wat is POSIX?
|Welke scheduling algorithmes kent POSIX en wat is het verschil tussen die algorithmes?
|Wat maakt een operating system geschikt voor real-time toepassingen?
|
|Van welke architectuur is Linux afgeleid? | Unix
|Welke betekenis heeft de extensie van een file in Linux? | Geen betekenis.
|Met welk Linux commando kan ik opvragen in welke directory ik sta? | `pwd` (print working directory)
|Als ik nu in de directory `/home/eric/src` sta, in welke directory sta ik dan na uitvoeren van het commando `cd ../../../usr/local/lib` ? | `/usr/local/lib`
|Wat houdt het begrip mounting in?
|Wie mag de file 'fooscript' aanpassen (schrijven) na het uitvoeren van het commando 'chmod 664 fooscript' en wie mag deze file executeren?
|Hoe kan ik het commando 'rm fooscript' uitvoeren als superuser?
|Hoe kan ik in een (1) commandoregel het commando 'ls' uitvoeren, de output doorgeven aan het commando 'sort' en vervolgens de output wegschrijven naar een file met de naam 'lijst'? | `ls | sort > lijst` Het probleem is alleen dat het bestand `lijst` ook zelf weggeschreven wordt.| 
|Wat doet het volgende commando: 'echo "foo bar" >> myfile' ? | Voegt `foo bar` toe aan het einde van het bestand `myfile`. |
|
|Wat wordt bedoeld met de kreet "Embedded Linux"? Anders gezegd: In hoeverre is het anders dan de Linux op je laptop?
|Wat wordt er allemaal door BuildRoot geproduceerd?
|Met welk commando kun je de BuildRoot configuratie aanpassen? Hoe de BusyBox configuratie? Hoe de Linux kernel configuratie?
|Waarom wordt BusyBox gebruikt in plaats van losse Linux binaries?
|Hoe weet BusyBox welke binary ("applet") hij moet nabootsen?
|Wat moet je doen om een nieuwe applet aan BusyBox toe te voegen?
|Wat wordt bedoeld met het begrip "Host" en het begrip "Target"?
|In welke file staat de Linux netwerk configuratie? | in `/etc/network/interfaces`
|Wat kun je doen met het commando "ssh" en wat met het commando "scp"?
|Wat doet een HTTP Daemon (httpd)?
|Hoe worden CGI-programma's opgestart?
|Wat is de Query String in de volgende URL? `http://10.0.0.42/cgi-bin/mycgiprog?name=John+Doe&age=21` | `name=John+Doe&age=21`
|Hoe wordt een Query String doorgegeven aan het CGI programma? | Via een enviroment variabel, namelijk `QUERY_STRING`. POST-request gaat via `stdin`.
|Waar stuurt een CGI-programma zijn output naar toe? | naar `stdout`
|
|Uit welke 3 elementen bestaat een Linux distributie?
|Op welke punten is een EmbeddedLinux distributie verschillend van een gewone Linux distributie?
|Hoe is een SD-kaart ingedeeld?
|Waar staat de afkorting MBR voor? Waar staat hij op de SD kaart en elke informatie staat er in?
|Beschrijf wat er achtereenvolgens gebeurt als Linux boot.
|Hoe kun je een programma tijdens booten automatisch laten opstarten?
|Met welk Linux commando kan ik na het booten de kernel log messages bekijken?
|Wat zijn de belangrijkste directories in het root filesystem en wat staat er in welke directory?
|Met welk Linux commando kun je een image vanaf je disk, via een USB kaartlezer/schrijver, op een (micro)SD kaartje schrijven? (Het omgekeerde kan overigens ook.)
|
|Wat gebeurt er tijdens "enumeratie"? Wie neemt het initatief?
|Als ik de knoppen van een device wil uitlezen, moet ik dan een IN of een OUT endpoint gebruiken?
|Benoem tenminste 3 endpoint types.
|Met welk Linux commando kun je de Device Descriptor van een USB device opvragen? | `lsusb -v` |
|Hoe wordt een USB device geidentificeerd?
|Wat moet je allemaal doen om de "libusb" library te gebruiken vanuit je eigen applicatie?
|Hoe werkt GPIO bij Embedded Linux?
|
|Wat wordt bedoeld met "crosscompilatie"?
|Wat is het nut van make en Makefiles?
|Hoe ziet een Makefile regel (rule) er in algemene vorm uit?| `target: prerequisites /n /t recipe` |
|Waarvoor wordt het target .PHONY gebruikt?
|Wat doet de volgende regel in een Makefile?  `OBJECTS = $(SOURCES:.c=.o)`
|Construeer een Makefile rule voor een target "install" die via scp het programma "myprog" installeert in directory /bin op een target met ip-adres 192.168.1.10. Wat gebeurt er als je "make install" uitvoert en het target "myprog" is nog niet gebouwd?
|Als je het commando "make" twee maal uitvoert zal hij in het algemeen de tweede keer minder doen dan de eerste keer. Hoe werkt dat?
|Welke target wordt gebouwd als je het commando "make" geeft zonder een expliciet target. 
|
|Bij forking is een child een kloon van de parent. Hoe weten parent en child dan wie wie is?
|Hoeveel threads heeft een proces als het net gestart is?
|Welke functie wordt door de eerste thread van een proces uitgevoerd?
|Hoe kan een thread wachten tot een thread die hij gecreeerd heeft klaar is?
|Met welke POSIX library moet je een applicatie linken als je threads gebruikt? Hoe doe je dat?
|Via welke API moet je de TCP/IP stack gebruiken?
|Waar is de TCP/IP stack geimplementeerd in Linux?
|
|Wat is shared memory en waar kan ik het vinden in Linux?
|Als een proces een share memory unlinked, wat betekent dit dan? Wat merken andere processen die dit zelfde shared memory gebruiken hier van?
|Wat is een Critical Section en wat is mutual exclusion?
|Wat is een voor- en wat het een nadeel van semaforen?
|Welke initiele waarde moet ik een semafoor geven wanneer die gebruikt wordt voor het implementeren van mutual exclusion?
|Wat gebeurt er met een proces als het geblokkeerd wordt in een sem_wait operatie?
|
|Hoeveel semaforen heb je nodig als je 1 producer en 1 consumer hebt? Hoeveel als je meerdere producers en/of consumers hebt?
|Als je semaforen gebruikt om producer/consumer te implementeren, welke initiele waardes moet je de gebruikte semaforen dan geven en waarom?
|Wat zijn voordelen van het gebruik van MessageQueues versus een zelfgemaakt producer/consumer implementatie?
|Waar bevinden MessageQueues zich in Linux? | MQs bevinden zich in de map `/dev/shm`|
|
|Wat is in Linux het wezenlijke verschil tussen het opstarten van een nieuwe thread en het forken van een nieuw proces?
|Wat is een daemon?
|Hoe kun je een verzameling data in Shared Memory of parameters in een Message bundelen in een datastructuur? Waarom is dat handig?
|Op welke 2 verschillende manieren kun je messages over een TCP/IP verbinding versturen? Wat zijn de voor- en nadelen?
|Hoe maak je in een embedded linux omgeving applicaties portable? Waarom is dat belangrijk?
|Wat draag POSIX bij aan portability?
|Hoe zorg je bij het compileren dat een programma met een of meer POSIX libraries wordt gelinkt?
|Wat is het nut van Portable Datatypes?
|Welke essentiele rollen vervult de C-library in een Linux systeem?
|Waarom zijn er speciale Embedded C-libraries?
|Wat is QEMU en wat kun je er mee?