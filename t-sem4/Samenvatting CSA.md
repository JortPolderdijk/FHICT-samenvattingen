# Samenvatting CSA - client-server architecturen semester 4

DISCLAIMER: kan fouten bevatten. Graag bij het vinden van een fout deze repo clonen, de fout aanpassen en een pull request sturen. Alvast bedankt!

---

## Vakinhoud

Informatie over de inhoud van het vak zijn afkomstig uit het blokboek vanaf de portal. Het gaat in dit geval over de versie van 2017-01-19.

### Thema
Deze cursus richt zich op specifieke concepten voor het maken van gedistribueerde client/server systemen. Concepten die hierbij aan de orde komen zijn o.a. client, server, addressing, binding, contracting, hosting, session, instance management, data contracts, callbacks en events.

### Leerdoelen

* Services in the history of programming* WCF Architecture: Endpoints, Addresses, Bindings and Contracts* Service Configuration (Programmatic and Administrative)* Metadata Exchange* Hosting* Server Side and Client Side Programming* Service Contracts and Data Contracts* Instance Management* Operation Patterns* Security

## Introductie

Diverse namen voor een applicatie die werkt op meerdere apparaten tegelijk, namelijk: *Distributed Applications*, *Client-Server Applications*, *Service Oriented Applications*.

Waarom distributeren:

* Data te delen
* Processorkracht te delen
* Gebruikers te verdelen

**Service Oriented Applications**

- Functionaliteiten zijn verdeeld (één service doet backend van een webshop en andere bijvoorbeeld frontend)
- Ziet er voor de user uit als één geheel (*transparency*)

**Client / server**

- Server serveert een service voor de client
- Client gebruikt een service van een server
- Asymmetric protocol: client start, server reageert

Voordelen:

- Server kan overal staan
- Platform maakt niet uit
- Message based communication (gemakkelijke koppeling)
- Gedeelde resources server

**Challenge: Boundaries**

Verschillende manieren om te willen communiceren.

- Communicatie tussen processen
- Communicatie tussen machines
- Communicatie over netwerken

**Challenge: Marshalling**

Marshalling: het in een andere volgorde lezen van bits

## Windows Communication Framework (WCF)

### Basics

Het belangrijkste aan WCF is het ABC, namelijk: *Address*, *Binding* en *Contract*.

**Address**

Waar kan ik de betreffende service vinden? Bijvoorbeeld:

- internet: ``http://google.com:8001/SuperSecretService``
- intranet: `net.tcp://192.168.0.30:12345/CalculatorService`
- localhost: `net.pipe://localhost/MyPipe`

**Binding**

Hoe gaan wij berichten delen?

- Text of binary
- Encrypted of niet
- Sessies
- 1-to-1 of broadcasten

**Contract**

Wat kan ik allemaal gebruiken/doen? Bijvoorbeeld:

```cs
[ServiceContract]interface ICalculator{    [OperationContract]    double Add(double n1, double n2);    . . .}
```

Om bijvoorbeeld marshalling te voorkomen moet dat *geserialized* worden. De meeste types en classes (zoals `enum` en `struct`) worden door .NET automatisch geserialized.

*Reflection*: de mogelijkheid van een programmeertaal om zijn eigen metadata te hebben. Zo kan je in een .dll/.exe de metadata van bepaalde types ophalen. WCF doet dit ook om types te serialiseren.

Je kunt in C# aangeven welke classes in het gedeelde DataContract mogen met het `[DataContract]` attribute. Vervolgens kun je members van die class meegeven met het `[DataMember]` attribute. Voorbeeld:

```cs
[DataContract]
public class BankClients // De clienten worden gedeeld via de service.
{
	[DataMember]
	public int Id { get; private set; } // Het ID van de clienten mag gedeeld worden om geld over te maken
	
	public double AmountMoney { get; set; } // De hoeveelheid geld wat ze hebben uiteraard niet.
	// Daarom zetten we hier geen [DataMember] voor. 
}
```

Een andere methode is om het attribuut `[Serializable]` bovenaan de class te gebruiken. *Let wel op: alles in die class is dan standaard geserialized!* Gebruik `[NonSerialized]` om members te verbergen. Voorbeeld:

```cs
[Serializable]
public class BankClients // De clienten worden gedeeld via de service.
{
	public int Id { get; private set; } // Het ID van de clienten mag gedeeld worden om geld over te maken
	
	[NonSerialized]
	public double AmountMoney { get; set; } // De hoeveelheid geld wat ze hebben uiteraard niet.
	// Daarom zetten we hier [NonSerialized] voor. 
}
```

**Tekenen**

Het is ook belangrijk om te weten hoe je services kunt tekenen. Let hier op de interfaces. Een voorbeeldje:
![voorbeeld](https://github.com/JortPolderdijk/FHICT-samenvattingen/blob/master/t-sem4/assets/CSA/Picture1.png)

**XML**

In WCF is XML een veelgebruikte data format. Het lijkt heel erg op HTML. 

```xml
	<book xmlns:books=“http://schemas.xmlbook.isbn.org”		title=“Fairytales” author=“Grimm”>			<chapter title=“Introduction”>				Bla, bla, bla ...					</chapter>			. . .	</book>
```

Namespace: `xmlns:books=“http://schemas.xmlbook.isbn.org”`
Attributes: `title=“Fairytales” author=“Grimm”` en `title=“Introduction”`
Elements: `<book></book>` en `<chapter></chapter>`

**Configuratie**

Je kunt op twee manieren configureren. In je code of in de `app.config` file.
In de code:

```cs
Type contract = typeof(ICalculator);WSHttpBinding binding = new WSHttpBinding();Uri address = new Uri("http://localhost:8000/calculatorservice");host.AddServiceEndpoint(contract, binding, address);```
Voordeel hiervan is je run-time een verbinding kunt maken (dus ook niet als het niet nodig is) en nadeel is dat er rebuilds nodig zijn op het moment dat het adres bijvoorbeeld veranderd.

In de `app.config`:

```xml
<endpoint	address="http://localhost:8000/calculatorservice"	binding="wsHttpBinding"	contract="MyCalculatorService.ICalculator"/>```
Voordelen hiervan zijn makkelijker te deployen, simpele veranderingen hoeven niet opnieuw gecompileerd te worden.

**Hosting**

Er zijn verschillende manieren waarop jouw services te hosten zijn. Een aantal voorbeelden hiervan zijn:

- Self-hosted
	- Programmatically: `host.AddServiceEndpoint(contract, binding, address);` en `host.Open();`
	- Administratively: `<endpoint address="" binding="wsHttpBinding" contract="MyCalculatorContract.ICalculator"/>`
- VS2013 test-host (dit is de tool in Visual Studio die automatisch jouw service host, zoals gebruikt bij WebshopService)
- dedicated hosting engine (bijvoorbeeld een IIS7 server)

### Instance management

Je hebt drie soorten instances, namelijk: `PerCall`, `PerSession` en `Singleton`. Instance management geeft aan wat de server moet doen het geheugen voor een bepaalde service. Het instellen van een `InstanceContextMode` gaat als volgt:

```cs
[ServiceBehavior(InstanceContextMode = InstanceContextMode.PerCall)]public class CService : IService{	. . . . .}
```

**PerCall**

- `[ServiceBehavior(InstanceContextMode = InstanceContextMode.PerCall)]`
- Het stuk geheugen bestaat alleen gedurende de call.
- Server schoont geheugen gelijk na een call op.
- Er zijn geen states, die moet je zelf opslaan en beheren.

**PerSession**

- `[ServiceBehavior(InstanceContextMode = InstanceContextMode.PerSession)]`
- Zolang er een client-server connectie is heeft de client zijn eigen stuk geheugen gekregen op de server.
- Pro: automatic state behaviour.
- Con: meer geheugen nodig dan bij PerCall.

**Singleton**

- `[ServiceBehavior(InstanceContextMode = InstanceContextMode.Single)]`
- Geheugen blijft er vanaf de eerste call (ongeacht welke client)
- Pro: komt meer overeen met de echte wereld (alles blijft opgeslagen)
- Con: Nog meer geheugen dan bij PerSession
- Con: Synchronizatie nodig tussen de clients
- Vaak gebruikt bij unieke objecten zoals een aan te sturen motor.

### Operations

Er zijn verschillende types van operations binnen WCF, namelijk: `Request - Reply` (standaard), `One-way`, `Callbacks` en `Events`.

**Request - Reply**

- De client neemt het iniatief (hallo server, geef me al jouw klanten).
- Het verzoek is blockend tot de server reageert (hoi client, hier zijn al mijn klanten).
- Er zijn hier geen extra attributen voor nodig.

**One-way**

- De server geeft geen reactie (hoi server, ik heb 200 euro).
- Return type moet `void` zijn (want er komt niks terug).
- Niet blockend, de client kan gelijk weer verder.
- Het is niet duidelijk of het bericht wel is aangekomen.

```cs
[OperationContract(IsOneWay = true)]
```

**Callbacks**

- De server neemt het initiatief om de client terug te roepen:
	- hee, server, ik wil 100 euro overmaken naar het goede doel.
	- hee, client, ik heb 100 euro overgemaakt naar de maker van deze samenvatting hoor.
- De server en client wisselen van rol.
- Binding moet een tweewegs communicatie zijn, bijvoorbeeld `WSDualHttpBinding`.

`Contract`

```cs
[ServiceContract (CallbackContract = typeof(ICallback))]interface IContract{	[OperationContract]	void DoSomething();}interface ICallback{	[OperationContract]	void OnCallback();}
```

`Client side`

```cs
Class CCallback: ICallback{	public void Callback()	{ ... }}ICallback callback = new CCallback();InstanceContext context = new InstanceContext(callback);CallbackClient proxy = new CallbackClient(context);
// De server moet weten welke context jij bent zodat hij niet per ongeluk de callback naar een andere client stuurt.
```

`Server side`

```cs
public void DoSomething (){
	// De server moet de context van het verzoek vervolgens aan de juiste callback koppelen.	ICallback this.callback = OperationContext.Current.		GetCallbackChannel<ICallback>();}

// Later kan je opgeslagen context als volgt gebruiken:
this.callback.OnCallback();

```

**Events**

Events zijn eigenlijk gewoon events en worden gebruikt als signaaltje om aan te geven dat er iets gebeurt is.

Bij events heb je te maken met het subscriben en eventueel unsubscriben.

![flowchart](https://github.com/JortPolderdijk/FHICT-samenvattingen/blob/master/t-sem4/assets/CSA/Picture2.png)

`Server side`

Je hebt een enum nodig met types van evenementen die er plaats kunnen vinden:

```cs
enum EventType
{
	NewDonation,
	NewParty
}
```

Vervolgens moet we aan de serverzijde ook aangeven wat de subscribe functie moet doen:

```cs
[ServiceContract(CallbackContract=typeof(IBankEvents))]
public interface IBankEventsContract
{
	[OperationContract]
	// Een client kan zich inschrijven
	void Subscribe(EventType mask);
	
	[OperationContract]
	// En hij kan zich ook weer uitschrijven.
	void Unsubscribe(EventType mask);
}
```

De implementatie aan de serverside zou er als volgt uit zien:

```cs
// De instancemode moet op PerCall staan.
[ServiceBehaviour(InstanceContextMode = InstanceContextMode.PerCall)]
class BankEvents : IBankEventsContract
{
	// Deze delegates zijn eigenlijk een soort placeholders voor een event (Action), welke we later toe gaan voegen.
	static Action donationEvent = delegate{};
	static Action partyEvent = delegate{};
	
	public void Subscribe(EventType mask)
	{
		// Hier doen we eigenlijk hetzelfde als bij callbacks. We slaan de callback channel op zodat we later die `callbacks` kunnen geven.
		IBankEvents subscriber = OperationContext.Current.GetCallbackChannel<IBankEvents>();
		
		// Moeilijke manier om te checken of een client wilt subscriben op het party event.
		if((mask & EventType.NewParty) == EventType.NewParty)
		{
			// Voeg het event toe aan onze delegate
			partyEvent += subscriber.OnParty;
		}
	}
	
	public void Unsubscribe(EventType mask)
	{
		/* zelfde verhaal als hierboven maar dan */
		partyEvent -= subscriber.OnParty;
	}
}
```

Het event kan je vervolgens aanroepen met `partyEvent();`.

`Client side`

```cs
interface IBankEvents
{
	[OperationContract(IsOneWay = true)]
	void OnDonation();
	
	[OperationContract(IsOneWay = true)]
	void OnParty();	
}
```

## Oefentoets #1

1. Een voetbal toernooi heeft een WCF service zodat client applicaties aan het eind van de dag de nieuwe wedstrijduitslagen kunnen ontvangen. De uitslagen wordt weergegeven door een lijst van de klasse “Result”. De klasse “Result” heeft properties met de naam van de thuisclub, de naam van de uitclub, de score van de thuisclub en de score van de uitclub. De service maakt gebruik van het event “void NewResults(List<Result> results)” om een client op de hoogte te stellen van een nieuwe uitslagen. Definieer een contract voor deze service. **(3 punten)**
2. Het endpoint voor deze service wordt geconfigureerd in de .config file. Geef aan hoe dit endpoint er uitziet. Hint: `<endpoint ......... />` **(1 punt)**
3. Een andere manier om een endpoint te definiëren is in de code. Geeft het stukje code dat dit zelfde endpoint definieert. Maak hierbij gebruik van self-hosting. **(1 punt)**
4. Geef drie vormen van hosting. Geef een voordeel en een nadeel van elke vorm van hosting. **(3 punten)**
5. Welke instancemode is voor deze service het meest geschikt en waarom? **(2 punten)**
6. Teken een diagram met een waarin de situatie gegeven wordt in het geval dat de service twee clients heeft. Teken hierin alle endpoints (dus ook events), proxies en classes die interfaces implementeren. **(2 punten)**
7. WCF kan automatisch het List type marshallen voor communicatie over een kanaal. **(2 punten)**
 * Naar welk formaat wordt de List geconverteerd?
 * Waarom wordt de List geconverteerd?
8. Er kunnen meerdere clients events van deze service ontvangen. Beschrijf hoe de service weet welke clients hij een event moet sturen. **(2 punten)**
9. Kan het gebruik van een OneWay Operation leiden tot een Exception?Als dit niet het geval is, leg dan uit waarom niet. Als dit wel het geval is, leg dan uit wat voor soorten Exceptions dit kunnen zijn. **(2 punten)**
10. Stel dat een service een business endpoint heeft met een bepaald adres, bepaalde binding en een bepaald contract. Wat gaat er mis in de volgende gevallen? **(2 punten)**
 * Het verkeerde endpoint adres wordt gebruikt.
 * De verkeerde endpoint binding wordt gebruikt.
 * Het verkeerde endpoint contract type wordt gebruikt.

**Score**

Punt = 1 + 0.45 * (aantal gescoorde punten)

[https://portal.fhict.nl/Studentenplein/LMC/1617vj/Technology/semester4/ds4-csa/Toets/OefenTentamen.pdf](https://portal.fhict.nl/Studentenplein/LMC/1617vj/Technology/semester4/ds4-csa/Toets/OefenTentamen.pdf)

