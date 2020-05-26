# Beschreibung Prolongieren-API

Die Prolongieren-API dient dazu einen bestehenden Classic-Vorgang für eine Anschlussfinanzierung nach BaufiSmart zu übertragen.
Die API erzeugt einen neuen BaufiSmart-Vorgang ohne Antrag. Der Abschluss und die Berechnung der Anschlussfinanzierung findet dann in BaufiSmart und nicht über die API statt. Dafür werden in BaufiSmart die Vorschläge berechnet und anschließend der Anschlussfinanzierungsvorschlag angenommen.
Einen bestehenden BaufiSmart Vorgang prolongiert man am besten in demselben Vorgang, in dem bereits die erste Finanzierung stattgefunden hat. In BaufiSmart ist ein Vorgang eine Klammer um die Finanzierungen der Antragsteller. Die initiale Finanzierung und eventuell folgende Kapitalbeschaffungen und Anschlussfinanzierungen können in diesem Vorgang stattfinden.

# Prolongation von BaufiSmart oder Classic Vorgängen
Es wird ein Endpunkt bereitgestellt, der anhand der mitgelieferten Parameter erkennt, ob es sich um einen BaufiSmart oder um einen Classic-Vorgang handelt.
Dementsprechend wird der Aufruf für die Prolongation an den passenden Service weitergeleitet, damit die Anschlussfinanzierung dann in BaufiSmart abgeschlossen werden kann.
Für Classic-Vorgänge wird in diesem Prozess ein BaufiSmart-Vorgang erzeugt.

Bei Erfolg wird der Prolongations-Vorgang in BaufiSmart geöffnet.

Da der Prozess der Vorbereitung des Vorgangs einige Zeit in Anspruch nimmt, wird während dieser Zeit ein Warte-Fenster angezeigt.

Es ist möglich, dass das Anlegen eines BaufiSmart Vorgangs nicht durchgeführt werden kann (Fehlerfall). In diesem Fall wird eine Fehlermeldung angezeigt. In diesem Fall sollte Kontakt zu Europace aufgenommen werden.

## Aufruf

Der Service ist erreichbar unter

```
GET https://www.europace2.de/anschlussfinanzierung/<vorgangsNummer>/<datenKontext>/<quelle>
```

Als Header muss X-Authentication mit Berechtigungs-JWT-Token des aktuellen Nutzers gesetzt sein.

## Parameter

Folgende Parameter werden als Pfad-Parameter übergeben.

#### vorgangsnummer

BaufiSmart-Vorgangsnummer bzw. Classic-Fallnummer

#### datenKontext

ECHTGESCHAEFT bzw. TEST_MODUS

#### quelle

Als BaufiSmart-Quellen werden akzeptiert: 'ep2', 'baufismart' und 'bs'.

Als Classic-Quellen werden akzeptiert: 'classic', 'omc' und 'epc'.

## Nutzung der API in No-Browser-Clients

Wird die API über einen Client angebunden, in dem kein Browser-Redirect stattfinden kann,
kann oben genannte URL mit dem zusätzlichen Query-Parameter `noBrowserRedirect=true` aufgerufen werden.

```
GET https://www.europace2.de/anschlussfinanzierung/<vorgangsNummer>/<datenKontext>/<quelle>?noBrowserRedirect=true
```

Bei diesem Aufruf wird ein Status-JSON mit folgendem Format zurückgegeben:

```
{
    "status": "in Progress",
    "vorgangsNummer": "<vorgangsnummer>"
}
```

Über den Aufruf von

```
GET https://www.europace2.de/anschlussfinanzierung/<vorgangsNummer>/progress?noBrowserRedirect=true
```

kann der aktuelle Status abgefragt werden. Ist die Prolongation abgeschlossen, wird ein JSON-Objekt mit folgendem Format zurückgegeben:

```
{
    "status": "done",
    "vorgangsNummer": "<alte vorgangsnummer>",
    "newVorgangsNummer": "<neue vorgangsnummer>"
}
```

# Prolongation von BaufiSmart Vorgängen - Direkter Aufruf

Für die Prolongation von BaufiSmart-Vorgängen wird der Aufruf auf folgende API weitergeleitet. Diese API kann nach der folgenden Beschreibung auch direkt angesprochen werden. 

## Dokumentation

Um einen bestehenden BaufiSmart Vorgang zu prolongieren, muss zunächst ein HTTP POST Request geschickt werden:
 
POST auf https://www.europace2.de/baufiSmart/anschlussfinanzierung/{vorgangId}

```
Response:
200 OK

{
"teilVorgangId":"{teilvorgangId}",
"webUrl":"/baufiSmart/vorgangBearbeiten.html?teilvorgang={teilvorgangId}",
"vorgangsNummer":"{vorgangId}"
}
```

## Fehler
Wenn ein Fehler auftritt, wird die Fehlerseite als Json ausgeliefert.
Mögliche Ursachen können sein:
- Der Vorgang ist gesperrt (Statuscode 423),
- der Vorgang wurde nicht gefunden (Statuscode 404),
- der Vorgang konnte nicht wieder aktiviert werden (Statuscode 502).

## Hinweis
Achtung: Der Vorgang wird für die Prolongation vorbereitet, so dass die Daten entsprechend verändert werden.
Wenn der Vorgang nicht prolongiert werden kann, wird der Datensatz nicht verändert. In diesem Fall wird ein 200 OK und die Json-Response geliefert.

## Nutzungsbedingungen
Die APIs werden unter folgenden [Nutzungsbedingungen](https://developer.europace.de/terms/) zur Verfügung gestellt

