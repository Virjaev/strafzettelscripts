# strafzettelscripts
ESX Strafzettel

Ein realistisches Strafzettel-System für FiveM ESX. Polizeibeamte und Mechaniker können mit einem Mini-Drucker Strafzettel für Fahrzeuge in ihrer Nähe ausstellen. Offene Strafzettel erscheinen beim Einsteigen in das betroffene Fahrzeug.

## Funktionen

- Nutzbares Item `minidrucker` für `police` und `mechanic`
- Auswahl aller Fahrzeuge in der Nähe
- Eingabe von Vergehen und Betrag
- Druckanimation mit ausfahrendem Papier
- Papier-Design für den Strafzettel
- Direkte Zahlung oder Zahlung später
- Offene Strafzettel werden nacheinander angezeigt
- Erinnerung beim Fahren
- Fälligkeit nach drei Stunden
- Automatische Zahlung mit 50 % Zuschlag nach Ablauf
- Bankkonto darf ins Minus gehen
- Bezahlte Strafzettel gehen an das Firmenkonto des ausstellenden Jobs

## Abhängigkeiten

Diese Resources müssen vor dieser Resource gestartet werden:

```cfg
ensure oxmysql
ensure es_extended
ensure esx_addonaccount
ensure esx_society
ensure virjaev_strafzettel
```

> Wenn dein Ressourcenordner anders heißt, musst du in der letzten Zeile den tatsächlichen Ordnernamen verwenden.

## Installation

1. Den gesamten Resource-Ordner in den `resources`-Ordner deines Servers kopieren.
2. Die Datei `sql.sql` in deine Datenbank importieren.
3. In der `server.cfg` die Resource nach den Abhängigkeiten starten.
4. Prüfen, ob das Item `minidrucker` in deinem Inventar existiert.
5. Den Server bzw. die Resource neu starten.

```cfg
restart virjaev_strafzettel
```

## Datenbank-Update für Firmenkonten

Wenn du die Version mit Firmenkonten nachträglich installierst, führe diesen Befehl **einmalig** in deiner Datenbank aus:

```sql
ALTER TABLE esx_vehicle_fines
ADD COLUMN issuer_job VARCHAR(50) NULL AFTER issuer_name;
```

Neue Strafzettel speichern damit automatisch den Job des Ausstellers. Das Geld geht bei `police` an `society_police`, bei `mechanic` an `society_mechanic`.

## Konfiguration

Die wichtigsten Einstellungen sind in `config.lua`:

```lua
Config.PrinterItem = 'minidrucker'

Config.AllowedJobs = {
    police = true,
    mechanic = true
}

Config.SearchRadius = 18.0
Config.FineDueHours = 3
Config.LateFeeMultiplier = 1.50
```

### Weitere Jobs erlauben

Zum Beispiel für Sheriff:

```lua
Config.AllowedJobs = {
    police = true,
    mechanic = true,
    sheriff = true
}
```

Damit die Einnahmen funktionieren, muss ein Konto `society_sheriff` über `esx_society` / `esx_addonaccount` vorhanden sein.

## Verwendung im Spiel

1. Als Police oder Mechanic den `minidrucker` im Inventar verwenden.
2. Fahrzeug auswählen.
3. Vergehen und Betrag eintragen.
4. Auf **Drucken** drücken.
5. Der Halter sieht den Strafzettel beim Einsteigen in sein Fahrzeug.

## Fehlerhilfe

### Beim Einsteigen erscheint kein Strafzettel

- Die SQL-Datei muss importiert sein.
- Das Fahrzeug muss in `owned_vehicles` eingetragen sein.
- Das Kennzeichen in `owned_vehicles` muss mit dem Kennzeichen des Fahrzeugs übereinstimmen.
- Die vollständige aktuelle `client.lua` verwenden. Dort darf im Client **nicht** `os.time()` stehen.

### `society_police` oder `society_mechanic` wurde nicht gefunden

- Stelle sicher, dass `esx_addonaccount` und `esx_society` gestartet sind.
- Prüfe in deiner Datenbank, ob die entsprechenden Society-Konten angelegt wurden.

### Der Drucker lässt sich nicht benutzen

- Prüfe, ob der Spieler den Job `police` oder `mechanic` hat.
- Prüfe, ob der Item-Name exakt `minidrucker` ist.
- Ändere den Item-Namen bei Bedarf in `config.lua`.
