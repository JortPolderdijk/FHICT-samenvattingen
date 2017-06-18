# Samenvatting IRT - industriële regeltechniek

DISCLAIMER: kan fouten bevatten. Graag bij het vinden van een fout deze repo clonen, de fout aanpassen en een pull request sturen. Alvast bedankt!

---

## Vakinhoud

Informatie over de inhoud van het vak zijn afkomstig uit het blokboek vanaf de portal. Het gaat in dit geval over de versie van 2017-06-18.

### Thema

Deze cursus behandelt de basisbeginselen van industriële regeltechniek. Het is gericht aan 2e-jaars studenten in semester 4, en geeft handvatten voor het ontwerpen van regelingen zoals bedoeld in de proftaak van dit semester.### Leerdoelen

######  Introduction

+ Introductie in simulatiesoftware zoals SciLab en/of Matlab
+ Voorbeelden van regelsystemen kunnen noemen
+ Het verschil kunnen uitleggen tussen een open-loop en een closed-loop proces of systeem
+ Het verschil kunnen uitleggen tussen statisch en dynamisch gedrag van een systeem of een proces
+ In eigen woorden kunnen beschijven wat de rol is van een feedback control system

######  Transfer-Functions

+ Kan in eigen woorden vertellen wat een overdrachtsfunctie is
+ Kent de systeemreductieregels waaronder: somregel, productregel, loopvereenvoudiging
+ Kan en complex (samengesteld) systeem bestaande uit individuele overdrachtsfuncties vereenvoudingen tot een enkelvoudige equivalente overdrachtsfunctie door toepassing van systeemreductieregels.


######  S-domain-and-simplification-rules

+ Kan 1e-orde overdrachtsfuncties toepassen in het Laplace-domein
+ Kan in eigen woorden uitleggen wat de termen Eindwaarde, Versterking, Tijdconstante en Insteltijd inhouden
+ Kan de bovengenoemde termen aflezen uit de standaardvorm voor 1e-orde overdrachten
+ Kan 1e-orde systemen simuleren in een wetenschappelijk simulatie software pakket (Scilab/Matlab)

######  1st-order-standard-form-and-Laplace

+ Kan reductieregels toepassen op overdrachtsfuncties in het s-domain
+ Kan standaardvorm voor 1e-orde overdrachten afleiden uit een willekeurige 1e-orde overdracht
+ Kan uit de standaardvorm de parameters K, \tau, Ts en de eindwaarde afleiden
+ Kan uit een gegeven lineaire differentiaalvergelijking in het tijddomein door middel van een laplacetransformatie en overdrachtsfunctie afleiden in het s-domain (=laplace-domein)
+ Kan met behulp van rekenen en simulatie het gedrag van 1e-orde processen en systemen analyseren

######  Controllers-Proportional

+ Kan uitleggen wat een P-regelaar is
+ Kan een P-regelaar toepassen bij een 1e-orde proces om zo het proces te regelen
+ Kan een met een P-regelaar geregeld 1e-orde analyseren door middel van rekenen en simulatie.
+ Kan analyseren en quantificeren wat de invloed is van een P-regelaar op de tijdconstante, versterking, eindwaarde en insteltijd.
+ Kan uitleggen wat de fysische grenzen zijn van een proces met betrekking tot e theorie
+ Kan onderscheid maken tussen eenheidsfeedback en feedback door middel van een sensor en dit verwoorden en toepassen

###### Controllers-PI-and-PID

+ Begrijpt en kan met eigen woorden uitleggen wat bedoelt wordt met integreren en differentieren
+ Kan uitleggen en demonstreren wat de invloed is van een PI en een PID regelaar op en 1e-orde te regelen proces
+ Kan deze invloed in een simulatie of berekening inzichtelijk maken
+ Kan uitleggen wanneer een geregeld proces een 1e-orde process is en wanneer niet. 
+ Kan een PID en een PI regelaar opsplitsen in een equivalent diagram bestaande uit individuele P, I en D componenten
+ Kan beschijven wat bedoeld wordt met eindfout (steady state error) en overshoot


## Rekenregels

Omdat het makkelijk is om een aantal rekenregels te kennen zodat ze toegepast kunnen worden, hier een klein overzichtje:

#### Breuken
<table class="tg">
  <tr>
    <td class="tg-yw4l">Vereenvoudigen (wegstrepen)</th>
    <td class="tg-yw4l"><img src="https://latex.codecogs.com/png.latex?\frac{3a}{ab}&space;=&space;\frac{3}{b}" title="\frac{3a}{ab} = \frac{3}{b}" /></th>
  </tr>
  <tr>
    <td class="tg-yw4l">Vermenigvuldigen</td>
    <td class="tg-yw4l"><img src="https://latex.codecogs.com/png.latex?\frac{a}{b}&space;*&space;\frac{c}{d}&space;=&space;\frac{ac}{bd}" title="\frac{a}{b} * \frac{c}{d} = \frac{ac}{bd}" /></td>
  </tr>
  <tr>
    <td class="tg-yw4l">Delen door een breuk (vermenigvuldigen met het omgekeerde)</td>
    <td class="tg-yw4l"><img src="https://latex.codecogs.com/png.latex?\frac{1&plus;\frac{1}{a}}{\frac{2}{a}}&space;=&space;(1&space;&plus;&space;\frac{1}{a})&space;*&space;\frac{a}{2}&space;=&space;\frac{a}{2}&space;&plus;&space;\frac{1}{2}&space;=&space;\frac{a&space;&plus;&space;1}{2}" title="\frac{1+\frac{1}{a}}{\frac{2}{a}} = (1 + \frac{1}{a}) * \frac{a}{2} = \frac{a}{2} + \frac{1}{2} = \frac{a + 1}{2}" />
    </td>
  </tr>
  <tr>
  	<td></td>
  	<td><img src="https://latex.codecogs.com/png.latex?H_s&space;=&space;\frac{6}{s&space;&plus;&space;4}" title="H_s = \frac{6}{s + 4}" /><br>
  	<img src="https://latex.codecogs.com/png.latex?H_s&space;=&space;\frac{\frac{6}{s&space;&plus;&space;4}}{4}&space;=&space;\frac{\frac{6}{4}}{\frac{s}{4}&space;&plus;&space;\frac{4}{4}}&space;=&space;\frac{1,5}{\frac{s}{4}&space;&plus;&space;1}" title="H_s = \frac{\frac{6}{s + 4}}{4} = \frac{\frac{6}{4}}{\frac{s}{4} + \frac{4}{4}} = \frac{1,5}{\frac{s}{4} + 1}" /></td>
  </tr>
</table>


## Introductie

Regeltechnieken kom je overal tegen, denk aan HVAC, huishoudelijke apparaten, voertuigen. Hier kun je simpele processen hebben (zoals het aansturen van een motor) maar je kunt ook veel complexere systemen bijregelen.

**Feedback control**

*Feedback control gaat over het bijregelen van een bepaald gedrag door middel van feedback input.*

**Open-loop en closed-loop control**

Je hebt twee soorten systemen. Bij een open-loop systeem heb je niet te maken met *feedback*. Zie onderstaande afbeelding ter referentie.

![loop](http://engineering.electrical-equipment.org/wp-content/uploads/2014/03/Difference-between-Open-Loop-Closed-Loop-Systems-5.jpg)

**Statisch en dynamisch gedrag**

Statisch gedrag wil zeggen dat de uitgang niet veranderd over de tijd. Je zou het kunnen zien als een `static` in c++. Deze veranderd namelijk ook niet.

Dynamisch gedrag veranderd wel naarmate er meer tijd verstrijkt (zie afbeelding hieronder). Het gedrag kunnen we benaderen met een *transfer functie* en deze heeft bepaalde *karakteristieken*.


## Transfer functie

Een transfer functie is een functie die beschrijft wat een input (X) doet met een output (Y).

![redux](https://github.com/JortPolderdijk/FHICT-samenvattingen/raw/master/t-sem4/assets/IRT/1.png)

![redux](https://github.com/JortPolderdijk/FHICT-samenvattingen/raw/master/t-sem4/assets/IRT/2.png)

**Closed-loop transferfunctie bepalen**

Een belangrijke formule om te onthouden is:
<img src="https://latex.codecogs.com/png.latex?H_t&space;=&space;\frac{Y}{X}&space;=&space;\frac{H_{straight}}{1&space;&plus;&space;H_{feedbackloop}}&space;=&space;\frac{H_1}{1&space;&plus;&space;H_1&space;*&space;H_2}" title="H_t = \frac{Y}{X} = \frac{H_{straight}}{1 + H_{feedbackloop}} = \frac{H_1}{1 + H_1 * H_2}" />

<img src="https://latex.codecogs.com/png.latex?H_{feedbackloop}&space;=&space;H_1&space;*&space;H_2" title="H_{feedbackloop} = H_1 * H_2" /> (want je doet de feedback keer de transfer functie van de input).

Hierbij is het belangrijk om te weten dat `H_straight` de transferfunctie is van het systeem zonder de feedback loop. `H_feedbackloop` is de feedback transferfunctie van het systeem. `H_t` is de totale transferfunctie (ofwel de vervangende transfer functie, ook wel *reduction*).

**Open-loop transferfunctie bepalen**

Mocht je een open-loop systeem hebben waarvan je de transferfunctie moet bepalen, dan kunnen we zeggen dat <img src="https://latex.codecogs.com/png.latex?H_{straight}&space;=&space;0" title="H_{straight} = 0" />

Je krijgt dan dus de volgende transfer functie:
<img src="https://latex.codecogs.com/png.latex?H_t&space;=&space;\frac{Y}{X}&space;=&space;\frac{H_{straight}}{1&space;&plus;&space;H_{feedbackloop}}&space;=&space;\frac{H_1}{1&space;&plus;&space;H_1&space;*&space;0}&space;=&space;\frac{H_1}{1&space;&plus;&space;0}&space;=&space;H_1" title="H_t = \frac{Y}{X} = \frac{H_{straight}}{1 + H_{feedbackloop}} = \frac{H_1}{1 + H_1 * 0} = \frac{H_1}{1 + 0} = H_1" />

Heel verassend dat de transferfunctie dan gewoon `H_1` blijkt te zijn.

**Twee transferfuncties 😨**

Als we twee transferfuncties (bijvoorbeeld `H_1` en `H_2`) hebben, kunnen we deze gewoon bij elkaar optellen:

![summation](https://github.com/JortPolderdijk/FHICT-samenvattingen/raw/master/t-sem4/assets/IRT/3.png)

<img src="https://latex.codecogs.com/png.latex?H_t&space;=&space;H_1&space;&plus;&space;H_2" title="H_t = H_1 + H_2" />

**Een opamp 🎸**

Een opamp (operationele versterker), is een elektronische met een zeer hoge versterkingsfactor (theoretisch `∞`, maar nu even <img src="https://latex.codecogs.com/png.latex?10^5" title="10^5" />).

<img src="https://latex.codecogs.com/png.latex?R_1" title="R_1" /> kan in sommige gevallen vervangen zijn door een capacitor. Gebruik dan de formule <img src="https://latex.codecogs.com/png.latex?R&space;=&space;\frac{1}{C&space;*&space;s}" title="R = \frac{1}{C * s}" /> om de capacitor om te rekenen naar een weerstandwaarde (de s is afkomstig uit een laplace transformatie, de capacitors uitgang is namelijk afhankelijk van de tijd). 

![opamp](https://github.com/JortPolderdijk/FHICT-samenvattingen/raw/master/t-sem4/assets/IRT/4.png)

Om de transferfunctie te kunnen berekenen, zetten we dit om naar een feedback control diagram, waarbij <img src="https://latex.codecogs.com/png.latex?R_1" title="R_1" /> en <img src="https://latex.codecogs.com/png.latex?R_2" title="R_2" /> de feedback is.

![opamp](https://github.com/JortPolderdijk/FHICT-samenvattingen/raw/master/t-sem4/assets/IRT/5.png)

<img src="https://latex.codecogs.com/png.latex?H_1&space;=&space;A_o" title="H_1 = A_o" />

<img src="https://latex.codecogs.com/png.latex?H_{2}&space;=&space;\frac{R_1}{R_2&space;&plus;&space;R_1}&space;*&space;\frac{R_1}{R_1}&space;=&space;\frac{1}{R_2&space;&plus;&space;1}" title="H_{feedbackloop} = \frac{R_1}{R_2 + R_1} * \frac{R_1}{R_1} = \frac{1}{R_2 + 1}" />

<img src="https://latex.codecogs.com/png.latex?H_t&space;=&space;\frac{Y}{X}&space;=&space;\frac{H_{straight}}{1&space;&plus;&space;H_{feedbackloop}}&space;=&space;\frac{H_1}{1&space;&plus;&space;H_1&space;*&space;H_2}&space;=&space;\frac{A_o}{1&space;&plus;&space;A_o&space;*&space;\frac{1}{R_2&space;&plus;&space;1}}&space;=&space;\frac{A_o}{1&space;&plus;&space;\frac{A_o}{R_2&space;&plus;&space;1}}&space;*&space;\frac{R_2&space;&plus;&space;1}{R_2&space;&plus;&space;1}&space;=&space;\frac{A_o&space;*&space;(R_2&space;&plus;&space;1)}{(R_2&space;&plus;&space;1)&space;&plus;&space;A_o}" title="H_t = \frac{Y}{X} = \frac{H_{straight}}{1 + H_{feedbackloop}} = \frac{H_1}{1 + H_1 * H_2} = \frac{A_o}{1 + A_o * \frac{1}{R_2 + 1}} = \frac{A_o}{1 + \frac{A_o}{R_2 + 1}} * \frac{R_2 + 1}{R_2 + 1} = \frac{A_o * (R_2 + 1)}{(R_2 + 1) + A_o}" />

## Het S-domein (Laplace)

Omdat dat het rekenen met integralen en differentialen nogal ingewikkeld is, kunnen we het Laplace-domein introduceren. We gaan dan van het tijdsdomein (<img src="https://latex.codecogs.com/png.latex?\inline&space;t&space;=&space;3&space;s" title="t = 3 s" />) naar het frequentiedomein.

### Order van feedback systemen

Om te bepalen welke order ons systeem heeft, moeten we kijken naar de <img src="https://latex.codecogs.com/png.latex?\inline&space;s" title="s" /> in de transferfunctie ___na een Laplace transformatie___.

<table>
	<tr>
		<td>0-order</td>
		<td><em>Geen s</em></td>
	</tr>
	<tr>
		<td>1st-order</td>
		<td><img src="https://latex.codecogs.com/png.latex?s" title="s" /></td>
	</tr>
	<tr>
		<td>2nd-order</td>
		<td><img src="https://latex.codecogs.com/png.latex?s^2" title="s^2" /></td>
	</tr>
	<tr>
		<td>3th-order</td>
		<td><img src="https://latex.codecogs.com/png.latex?s^3" title="s^3" /></td>
	</tr>
		<tr>
		<td>4th-order</td>
		<td><img src="https://latex.codecogs.com/png.latex?s^4" title="s^4" /></td>
	</tr>
</table>

**Algemene vorm**

De algemene vorm van een transfer functie is: <img src="https://latex.codecogs.com/png.latex?H(s)&space;=&space;\frac{K}{\tau&space;s&space;&plus;&space;1}" title="H(s) = \frac{K}{\tau s + 1}" />

### Karakteristieken van een functie (fantastic four)

<table>
	<thead>
		<tr>
			<th></th>
			<th></th>
			<th>Symbool</th>
			<th>Beschrijving</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>Final value</td>
			<td>Eindwaarde</td>
			<td><img src="https://latex.codecogs.com/png.latex?\inline&space;Y(s&space;\rightarrow&space;0)" title="Y(s \rightarrow 0)" /></td>
			<td>De uiteindelijke output waarde die wordt bereikt. Hiervoor moet je heel ver in de tijd kijken.</td>
		</tr>
		<tr>
			<td>Gain</td>
			<td>Versterking</td>
			<td><img src="https://latex.codecogs.com/png.latex?\inline&space;K" title="K" /></td>
			<td>De uiteindelijke ratio tussen de output (Y) en de input (X) <em>(nadat de final value is bereikt)</em>.</td>
		</tr>
		<tr>
			<td>Time constant</td>
			<td>Tijdsconstante</td>
			<td><img src="https://latex.codecogs.com/png.latex?\inline&space;\tau" title="\tau" /></td>
			<td>De tijd die nodig is om 63% te bereiken van de final value.</td>
		</tr>
		<tr>
			<td>Settle time</td>
			<td>Insteltijd</td>
			<td><img src="https://latex.codecogs.com/png.latex?\inline&space;T_s" title="T_s" /></td>
			<td>De tijd die nodig is om de final value binnen 2% afwijking te bereiken (<img src="https://latex.codecogs.com/png.latex?\inline&space;=&space;4&space;*&space;\tau" title="= 4 * \tau" />)</td>
		</tr>
	</tbody>
</table>

**Berekenen vanaf standaard vorm**

<table>
	<thead>
		<tr>
			<th></th>
			<th>Standaardvorm</th>
			<th>Hoe bepalen</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>Final value</td>
			<td><img src="https://latex.codecogs.com/png.latex?\frac{K}{\tau&space;s&space;&plus;&space;1}" title="\frac{K}{\tau s + 1}" /></td>
			<td>Maak <img src="https://latex.codecogs.com/png.latex?\inline&space;s&space;=&space;0" title="s = 0" /></td>
		</tr>
		<tr>
			<td>Gain</td>
			<td><img src="https://latex.codecogs.com/png.latex?\frac{K}{\tau&space;s&space;&plus;&space;1}" title="\frac{K}{\tau s + 1}" /></td>
			<td>Zorg voor standaardvorm en lees <img src="https://latex.codecogs.com/png.latex?\inline&space;K" title="K" /> dan uit.</td>
		</tr>
		<tr>
			<td>Time constant</td>
			<td><img src="https://latex.codecogs.com/png.latex?\frac{K}{\tau&space;s&space;&plus;&space;1}" title="\frac{K}{\tau s + 1}" /></td>
			<td>Zorg voor standaardvorm en lees <img src="https://latex.codecogs.com/png.latex?\inline&space;s" title="s" /> dan uit.</td>
		</tr>
		<tr>
			<td>Settle time</td>
			<td><img src="https://latex.codecogs.com/png.latex?\frac{K}{\tau&space;s&space;&plus;&space;1}" title="\frac{K}{\tau s + 1}" /></td>
			<td><img src="https://latex.codecogs.com/png.latex?\inline&space;T_s&space;=&space;\tau&space;*&space;4" title="T_s = \tau * 4" /></td>
		</tr>
	</tbody>
</table>

### Transformeren naar S-domein

<table>
	<thead>
		<tr>
			<th></th>
			<th>T-domein</th>
			<th>S-domein</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td></td>
			<td><img src="https://latex.codecogs.com/png.latex?\inline&space;y(t)" title="y(t)" /></td>
			<td><img src="https://latex.codecogs.com/png.latex?\inline&space;Y(s)" title="Y(s)" /></td>
		</tr>
		<tr>
			<td>Differentiatie</td>
			<td><img src="https://latex.codecogs.com/png.latex?\frac{dy}{dt}Y(t)" title="\frac{dy}{dt}Y(t)" /></td>
			<td><img src="https://latex.codecogs.com/png.latex?sY(s)" title="sY(s)" /></td>
		</tr>
		<tr>
			<td>Integratie</td>
			<td><img src="https://latex.codecogs.com/png.latex?\int&space;y(t)\frac{dy}{dt}t" title="\int y(t)\frac{dy}{dt}t" /></td>
			<td><img src="https://latex.codecogs.com/png.latex?\frac{1}{s}Y(s)" title="\frac{1}{s}Y(s)" /></td>
		</tr>
	</tbody>
</table>

**Voorbeeld**

* <img src="https://latex.codecogs.com/png.latex?5\frac{d^3y}{dt^3}&space;&plus;&space;ay&space;=&space;x" title="5\frac{d^3y}{dt^3} + ay = x" />
* Omzetten naar S-domein: <img src="https://latex.codecogs.com/png.latex?5s^3Y&space;&plus;&space;aY&space;=&space;X" title="5s^3Y + aY = X" />
* Transfer functie bepalen: <img src="https://latex.codecogs.com/png.latex?H(s)&space;=&space;\frac{Y(s)}{X(s)}&space;=&space;\frac{1}{5s^3&space;&plus;&space;a}" title="H(s) = \frac{Y(s)}{X(s)} = \frac{1}{5s^3 + a}" />
* Omdat er <img src="https://latex.codecogs.com/png.latex?\inline&space;s^3" title="s^3" /> in de transfer functie staat, is dit een 3th-order systeem.

#### Andere notatie

<table>
	<thead>
		<tr>
			<th>Order</th>
			<th>T-domein</th>
			<th>S-domein</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>1</td>
			<td><img src="https://latex.codecogs.com/png.latex?\dot{y}" title="\dot{y}" /></td>
			<td><img src="https://latex.codecogs.com/png.latex?sY(s)" title="sY(s)" /></td>
		</tr>
		<tr>
			<td>2</td>
			<td><img src="https://latex.codecogs.com/png.latex?\ddot{y}" title="\ddot{y}" /></td>
			<td><img src="https://latex.codecogs.com/png.latex?s^2Y(s)" title="s^2Y(s)" /></td>
		</tr>
		<tr>
			<td>3</td>
			<td><img src="https://latex.codecogs.com/png.latex?\dddot{y}" title="\dddot{y}" /></td>
			<td><img src="https://latex.codecogs.com/png.latex?s^3Y(s)" title="s^3Y(s)" /></td>
		</tr>
	</tbody>
</table>