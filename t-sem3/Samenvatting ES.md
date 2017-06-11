# Samenvatting ES3 - Embedded Controllers

-- Hier komt een table of contents --

DISCLAIMER: kan fouten bevatten.

##Vakinhoud
Informatie over de inhoud van het vak zijn afkomstig uit het blokboek vanaf de portal. Het gaat in dit geval over de versie van 2016-06-29.
### Thema
De volgende thema's zijn gekoppeld aan dit vak:

* Hoe stuur je een microcontroller low-level aan als je zelf een driver moet maken?

### Leerdoelen
De volgende leerdoelen zijn gekoppeld aan dit vak:

* Na het succesvol afronden van het onderdeel Embedded Controller kan de student:
	* een datasheet van een microcontroller gebruiken
	* uitleggen hoe een CPU werkt en kan enkele assembler instructies gebruiken
	* een programma maken waarin hardware in een microcontroller via registers aangestuurd
wordt
	* uitleggen wat een interrupt is en wat diens invloed op de werking van de CPU is en kan een
interrupt service routine maken
	* uitleggen hoe het memory model van een microcontroller eruit ziet en de verschillende
memory secties benoemen
	* de assembler listing en memory dump van een programma relateren aan de source code
	* met verschillende meetapparaten aan een embedded controller meten en kan hieruit zinnige
conclusies trekken

## GPIO
De ATmega328P beschikt over de volgende GPIO features:

* 3 ports (**PORTB, PORTC, PORTD**)
* 8 pins per port
* diverse functionaliteiten:
	* GPIO output
	* GPIO input (internal pull-up resistor)
	* GPIO input (external pull-up resistor)
	* alternative function (I2C, SPI, UART, ADC..)

In een Atmega zit een pull-up resistor tussen de 20kΩ en de 50kΩ. Deze kan softwarematig worden ingeschakeld.

![schematics](/Users/school/Documents/FHICT-samenvattingen/t-sem3/assets/ES/Picture1.png)

DDRx = Data Direction Register
PORTx = Output
PINx = Input

*Waarbij x B, C of D kan zijn. A is niet bestemd voor GPIO.*

| DDR | PORT | PIN |                         |
|-----|------|-----|-------------------------|
| 0   | 0    | 0/1 | Lees uit zonder pull-up |
| 0   | 1    | 0/1 | Lees uit met pull-up    |
| 1   | 0    | 0   | Schrijf 0               |
| 1   | 1    | 0   | Schrijf 1               |



**Hoe programmeer je dit?**
Setting a bit: `DDRB |= _BV(DDB2);`
Clearing a bit: `PORTB &= ~_BV(PORTB2);`

Een voorbeeld ter verduidelijking:

Setting:

| Binary   |              |
|----------|--------------|
| 10011**0**01 | DDRB        |
| 00000**1**00 | _BV(DDB2) |
| 10011**1**01 | OR          |

Clearing:

| Binary   |              |
|----------|--------------|
| 01101**1**11 | PORTB        |
| 11111**0**11 | ~_BV(PORTB5) |
| 01101**0**11 | AND          |

## Timers

### Interrupts
In plaats van het constant pollen van bijvoorbeeld een button, kunnen we ook gebruik maken van interrupts binnen de Atmega. 
**Voordelen**: efficient gebruik van CPU resources & snelle reactie op events.
**Nadelen**: moeilijker te gebruiken en race condition (als de interrupt komt op een moment waarop je hem niet verwacht)

#### In gebruik nemen van interrupts
1. install ISR (interrupt service routine)
2. enable interrupt
3. enable global interrupt
4. run main loop and wait for interrupt

**1. Install ISR**

```c++
ISR(PCINT0_vect) // PCINT0 = name of interrupt vector
{
	// the interrupt code goes here..
}
```
**2. Enable interrupt**

Om wiring te besparen hebben ze er voor gekozen om de 24 pinnen te multiplexen. Dit resulteert er in dat er drie interrupts zijn: PCINT0, PCINT1 en PCINT2. Stel dat er een interrupt komt op PCINT0, dan moet deze alsnog weten welke pin deze heeft verzoorzaakt.

De mask (PCMSKx) verzorgt voor elke pin een enable switch. Deze PCMSKx an worden enabled via het PCICR register.

**3. Enable global interrupt**

Het globale register om interrupts aan te zetten is SREG. Gebruik hiervoor geen binary operators maar de in gebouwde functies van de Arduino library: `cli()` (clear) en `sei()` (set).

#### Goed gebruik van interrupts
* ISR kort en simpel houden.
* Geen tijd verspillen, geen delay of serial print
* Vermijd loops
* Interrupts niet opnieuw aanzetten in een interrupts
* Variabelen die tussen de interrupt en main programma worden gedeeld `volatile` maken zodat de compiler forceert de memory altijd uit het geheugen te lezen.

### Timers
**Voordelen:** accuraat en weinig CPU resources
**Nadelen:** complex

Een timer is een stukje hardware wat gescheden is van de CPU. De Atmega328P heeft 3 timers. Een timer telt van 0x0000 naar 0xFFFF en doet dit elke keer opnieuw.

#### Prescaler
Een prescaler telt het aantal pulses en genereert een nieuwe pulse na bijvoorbeeld 256 pulses. De nieuwe clock gaat dan 256 keer zo langzaam. `TCCR1B` is het register voor de prescaler. `TCNT1` is de timer en als deze tot 0xFFFF is gelopen geneereert deze de interrupt `TIMER1_OVF_vect`.

#### Modus
Een timer kan diverse modus hebben. Bijvoorbeeld CTC (Clear Timer on Compare). In register `OCR1A` kan dan de waarde geschreven worden waarop de timer weer bij 0 moet starten. Door het register `TCCR1A` aan te passen kan deze modus enabled worden. 

**Voordeel:** precisie timers maken

### Summary

|                |                                                                                                   |
|----------------|---------------------------------------------------------------------------------------------------|
| cli()          | disable interrupts                                                                                |
| sei()          | set interrupts                                                                                    |
| *interrupts*     |                                                                                                   |
| PC             | Pin Change                                                                                        |
| PC MSK{0,1,2}  | Pin Change MaSK register <br><br> 0 = pin {0, 7} <br> 1 = pin {8, 14} <br> 2 = pin {16, 23}                         |
| PC IFR         | Pin Change Interrupt Flag Register                                                                |
| PC ICR         | Pin Change Interrupt Control Register <br><br> 3 flags/bits for the 3 PCMSKx ports                        |
| *timers*         |                                                                                                   |
| TCCR{0,1}{A,B} | Timer/Counter {0,1} Control Register {A, B}                                                       |
| OCR{0,1}{A,B}  | Output Compare {0,1} Register{A,B}                                                                |
| TIMSK{0,1,2}   | Timer/counter{0,1,2} interrupt MaSK register <br><br> Overflow interrupt<br> Output Compare Match Channel A/B |


## Low Power
Er zijn een aantal manieren om het verbruik van een CPU naar beneden te brengen. Onder andere:

* Functies die je niet gebruikt uitzetten
* De clock naar beneden schalen (je moet dan wel je eigen `delay()` schrijven.

### Stappen
1. Turn off peripherals (like ADC and the timer)
2. Slow down system clock
3. Power down mode
4. Wake up (by watchdog)

**1. Turn off peripherals**

```c++
ADCSRA = 0x00; // Turns off ADC
ACSR = 0x80; // Disables ACD
DIDR0 = 0x3F; // The digital input buffer on the ADC pins are disabled.
DIDR1 = 0x03; // The digital input buffer on the AIN1/0 pin is disabled.
PRR = 0xFF; // Power reduction
```

**2. Slow down system clock**

F.e.

```c++
for(int i = 0; i < 8; i++) // loop through prescalers
{
	CLKPR = 0x80; // Unlock the prescaler
	CLKPR = i; // Set the prescaler
	
	digitalWrite(LEDPIN, HIGH);
	mydelay(pow(2, i)); // clockDivisionFactor = 2 ^ prescaler
	
	digitalWrite(LEDPIN, LOW);
	mydelay(pow(2, i));
}
```

**3. Power down mode**
`set_sleep_mode(SLEEP_MODE_PWR_DOWN);` -> zie [hier](http://www.atmel.com/webdoc/GUID-EAD481FD-28E6-4CD5-87FB-5165E7687C12/index.html?GUID-3B54E66E-AE92-459F-99E6-EF5B8655258F)

**4. Watchdog**
De watchdog timer heeft zijn eigen 128kHz timer. Hierdoor is hij veel zuiniger. Deze timer draait buiten de chip.

```c++
void init()
{
	cli(); // Disable global interrupts
	wdt_reset(); // reset watchdog
	WDTCSR = _BV(WDCE) | _BV(WDE); // set up WDT interrupt
	WDTCSR = _BV(WDIE) | _BV(WDE) | _BV(WDP3); // Start WDT with 4s prescaler.
	sei(); // Enabled global interrupts
}

ISR(WDT_vect)
{
	...
}

void loop()
{

}
```

## CPU

### Architectures
Als we kijken naar de architectures die een CPU kan hebben, dan kan je onderscheid maken tussen drie veel gebruikte architecturen.

***Von Neumann architecture:*** code en data delen dezelfde bus
***Harvard architecture:*** code en data hebben eigen bus
***Modified Harvard architecture:*** aparte cache voor code en data maar gedeeld geheugen buiten chip.

### Load & store
`lds r2, $FF00;` Load r2 with the contents of data space location 0xFF00
`ldi r18, 0;` Load 0 into register r18
`sts $FF00,r2;` Write content of r2 to data space location 0xFF00

`ser r16;` Set all bits in r16 to one
`out _SFR_IO_ADDR(DDRB), r16;` set port B as output
`sbi _SFR_IO_ADDR(PORTB), 2;` set pin 2 of PORTB to high
`cbi _SFR_IO_ADDR(PORTB), 3;` set pin 3 of PORTB to low

### ALU, arithmetic and bitwise logical instructions
`adc r3, r1;` Add with carry high byte

`and r2, r3;` Bitwise AND r2 and r3. Result in r2

`muls r21,r20;` Multiply signed r21 and r20, result in r1r0
`movw r20, r0;` Copy result back in r21:r20

### Branch instructions
`jmp farplc;` Unconditional jump
`farplc: nop;` Jump destination (do nothing)

`cpi r26, $56;` Compare r26 with 0x56 carry flag in status register is set if abs(value) of 0x56 is larger than the absolute value of r26.
`brcs carry;` Branch if carry set
`carry: nop;` Branch destination


## Memory

![schematics](/Users/school/Documents/FHICT-samenvattingen/t-sem3/assets/ES/Picture2.png)

| Section |                                                          |
|---------|----------------------------------------------------------|
| data    | Initialized global variables                             |
| bss     | Uninitialized global variables (or with initial value 0) |
| heap    | Dynamically created objects (malloc, new)                |
| stack   | stack with actual calling tree                           |

### Stack

*Put value on stack (PUSH):* `SP---;` & `*SP = value;`
*Get value from stack (POP:* `value = *SP;` & `SP++`;
