# startcomputer
Startcomputer voor de WAW

## Proces flow

START
 ↓
ORANGE (20s)
 ↓
5 min signaal
 ↓
KLASSE 1
  - 4 min
  - 1 min
  - piezo 10s
  - START (lang signaal)
 ↓
KLASSE 2
 ↓

# Startprocedure / Wedstrijdstart Controller

## Doel

Deze functie verzorgt een volledig geautomatiseerde startprocedure voor wedstrijdstarts, inclusief:

* Oranje vlag fase
* Hoornsignalen
* Piezo aftellen
* Klassecycli
* Hard reset functionaliteit
* Veilige timer-afhandeling

De controller is ontworpen voor betrouwbare langdurige werking op echte hardware binnen Node-RED.

---

# Functionaliteit

## START ON

Wanneer:

```js
msg.payload === true
```

wordt ontvangen:

1. Het systeem wordt actief gezet
2. Oranje vlag wordt gehesen
3. Na ingestelde tijd:

   * Oranje vlag neer
   * Korte hoorn
   * Start eerste klassecyclus
4. Daarna lopen de klassecycli automatisch door

---

## START OFF

Wanneer:

```js
msg.payload === false
```

wordt ontvangen:

* Alle timers worden direct gestopt
* Lopende processen worden beëindigd
* Klassetelling wordt gereset
* Hoorn en piezo worden uitgezet
* Oranje vlag wordt neergehaald
* Het systeem keert terug naar veilige ruststand

Dit voorkomt achterblijvende timers of ongewenste outputs.

---

# Timer Management

Alle `setTimeout()` timers worden centraal geregistreerd via:

```js
addTimer()
```

Hierdoor kunnen bij een STOP of redeploy alle timers veilig worden verwijderd met:

```js
clearAllTimers()
```

Voordelen:

* Geen memory leaks
* Geen achterblijvende callbacks
* Geen onverwachte signalen na STOP
* Veilige herstarts

---

# Hoornbesturing

De hoornbesturing is overlap-veilig opgebouwd.

## Probleem dat wordt opgelost

Meerdere processen kunnen tegelijk de hoorn activeren:

* Startsignalen
* Waarschuwingen
* Handmatige bediening

Met een normale aan/uit besturing zou een eerdere timeout de hoorn kunnen uitschakelen terwijl een andere functie deze nog nodig heeft.

## Oplossing

Een interne reference counter (`hornCount`) bewaakt hoeveel processen de hoorn actief gebruiken.

### Werking

* `hornOn()` verhoogt teller
* `hornOff()` verlaagt teller
* Pas bij teller = 0 gaat de hoorn werkelijk uit

Hierdoor kunnen meerdere signalen veilig overlappen.

---

# Piezo Functionaliteit

De piezo wordt gebruikt voor de laatste 10 seconden vóór de start.

De functie stuurt enkel:

```js
piezo(true)
piezo(false)
```

Het daadwerkelijke knipperen gebeurt in een aparte Node-RED functie:

```txt
PIEZO BLINKER
```

Dit zorgt voor duidelijke scheiding tussen:

* timinglogica
* hardware-output
* blink-patronen

---

# Klasse Scheduler

Elke klassecyclus wordt volledig automatisch gepland via:

```js
scheduleClass(classNumber)
```

Per klasse worden automatisch ingepland:

| Moment                 | Actie       |
| ---------------------- | ----------- |
| 4 minuten voor start   | Korte hoorn |
| 1 minuut voor start    | Korte hoorn |
| 10 seconden voor start | Piezo aan   |
| Startmoment            | Lange hoorn |

Na het startmoment wordt automatisch de volgende klasse gestart.

---

# Configuratie

Alle tijden zijn centraal configureerbaar:

```js
const ORANGE_TIME
const T5
const T4
const T1
const PIEZO_BEFORE
```

Hoornlengtes:

```js
const HORN_SHORT
const HORN_LONG
```

Hierdoor kan het systeem eenvoudig aangepast worden aan:

* wedstrijdreglementen
* testmodus
* debug-snelheden

---

# Status Logging

Alle belangrijke gebeurtenissen worden doorgestuurd via output 4:

Voorbeelden:

```txt
START ON
ORANJE VLAG OP
KLASSE 1 CYCLUS GESTART
HOORN AAN KLASSE 1 - 1 MIN
PIEZO AAN
```

Dit maakt debugging en monitoring eenvoudig.

---

# Veiligheid

De controller bevat meerdere beveiligingen:

* Dubbele START wordt geblokkeerd
* STOP forceert volledige reset
* Timers worden centraal beheerd
* Outputs worden expliciet uitgezet
* Overlappende hoornsignalen zijn veilig afgehandeld
* Scheduler stopt direct bij `running === false`

---

# Outputs

| Output   | Functie        |
| -------- | -------------- |
| Output 1 | Oranje vlag    |
| Output 2 | Hoorn          |
| Output 3 | Piezo          |
| Output 4 | Status logging |

---

# Toepassing

Geschikt voor:

* Zeilwedstrijden
* Startprocedures
* Trainingsstarts
* Testopstellingen
* Geautomatiseerde wedstrijdsystemen

De code is ontworpen voor stabiele werking op embedded Node-RED systemen en industriële I/O hardware.


# Install nodered
https://nodered.org/docs/getting-started/windows
