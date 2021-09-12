# pandemie-rohdaten

## Hintergrund

Marcel Barz hat in einem Video ("Die Pandemie in Rohdaten") dargelegt, dass die Corona-Pandemie nicht in den deutschen Sterbezahlen ersichtlich ist. Da die Quellen des Videos leider nicht verfügbar sind, soll dieses Repository die Berechnungen bereitstellen um die Ergebnisse im Video nachzuvollziehen.

### Verwendete Tools

Um die Daten zu bereinigen und aus den Rohdaten zu erzeugen wurde [Python Pandas](https://pandas.pydata.org/) im [Jupyter Notebook](https://jupyter.org/) verwendet, beides gängige Tools um Daten zu analysieren. Beide müssen entsprechend installiert werden, um das Projekt lokal ausführen zu können.

## Repository Struktur

### Asset Ordner

Im Ordner "assets" sind alle Dateien, die aus externen Quellen (statistisches Bundesamt) heruntergeladen wurden. 

Der Ordner enthält die folgenden Dateien:

- sterbefaelle_2000-2015.csv
Quelle: https://www.destatis.de/DE/Themen/Gesellschaft-Umwelt/Bevoelkerung/Sterbefaelle-Lebenserwartung/Tabellen/sonderauswertung-sterbefaelle-endgueltige-daten.html (05. September 2021)
Erzeugung der Datei: Die heruntegeladene xlsx-Datei enthält mehrere Reiter. Der Reiter "D_2000-2015_Monate_AG_Ins" wurde geöffnet und als CSV Datei abgespeihert.

- sterbefaelle_2016-2021.csv
Quelle: https://www.destatis.de/DE/Themen/Gesellschaft-Umwelt/Bevoelkerung/Sterbefaelle-Lebenserwartung/Tabellen/sonderauswertung-sterbefaelle.html?nn=209016 (05. September 2021). Erzeugung der Datei: Die heruntegeladene xlsx-Datei enthält mehrere Reiter. Der Reiter "D_2016-2021_Monate_AG_Ins" wurde geöffnet und als CSV Datei abgespeihert.

- bevoelkerung_2011.csv
Quelle: https://www.destatis.de/DE/Themen/Gesellschaft-Umwelt/Bevoelkerung/Bevoelkerungsstand/Tabellen/liste-altersgruppen.html (05. September 2021). Erzeugung der Datei: Manuelles Kopieren der Daten aus der Seite in eine neue CSV Datei.

### Source Ordner

Der Ordner "source" enthält ein Jupyter Notebook, das die Dateien aus dem assets-Ordner einliest, verarbeitet und die Dateien für den output-Ordner erzeugt.

### Output Ordner

Dieser Ordner enthält zwei Dateien. Einerseits die vom Jupyter Notebook erzeugte Datei "relative_sterbedaten.csv", sowie die aufbereitete Datei "relative_sterbedaten.ods", die neben den reinen Daten auch eine visuelle Hervorhebung und ein Ranking der Sterberaten enthält. Die Hervorhebung, sowie das Ranking wurde manuell ergänzt.


## Vorgehen

Das Vorgehen ist analog zum Video. Neben dem Bereinigen der Daten (Schaltjahr herausrechnen) und dem Aufbereiten der Daten (relative Sterbedaten berechnen, indem man die Sterbezahlen durch Bevölkerungsstand teilt) wird auch die finale Tabelle (manuell) erstellt, in der das Ranking ersichtlich wird.

### Abweichungen zum Video

Abgesehen von fehlenden Datengrundlagen, die das Nachstellen des Videos erschweren (siehe Kapitel "Herausforderungen") gibt es auch Abweichungen zum Video

#### Altesrgruppen

Da die Rohdaten eine granularere Aufteilung der Altersgruppen bereitstellt wird auf eine gröbere Gruppenstruktur (wie im Video) verzichtet, da dies die Daten bereits mit einer unschärfe belegt.


## Herausforderungen

Während dem Nachstellen gab es einige Herausforderungen, auf die in diesem Kapitel eingegangen wird. Falls für ein Problem eine Lösung gefunden wurde, wird diese ebenfalls in diesem Kapitel erörtert.

### Keine Tagesbezogenen Daten per Altersgruppe

#### Problem 

Um die Schaltjahre zu korrigieren benötigt man die tagesbezogenen Daten per Altersgruppe für den jewieligen 29. Februar. Da die Daten für die Altersgruppen nur maximal mit einer Auflösung auf Wochenbasis existieren, kann die konkrete Zahl nicht ermittelt werden.

#### Lösung

Basierend auf den Ansatz im Video, dass der Schalttag nicht aus der Reihe tanzt, werden die Februar-Werte jedes Schaltjahrs korrigiert, indem man 28/29tel des Wertes verwendet (= einen durchschnittlichen Tag entfernt).

Dies passiet in der Funktion "fix_leapyear"

### Unterschiedliche Altersgruppen für Sterberaten und Bevölkerungsstand

#### Problem 

Die Altersgruppen der Sterberaten sind viel feingranularer als die Altersgruppen für den Bevölkerungsstand. Dies hat zur Folge, dass man beide Werte nicht direkt vergleichen kann. 

#### Lösung

Eine mögliche Lösung wäre, die feingranularen Altersgruppen in die groben Bevölkerungsstand-Altersgruppen umzurechnen. Dies hat den Nachteil, dass man nicht die feine Aufteilung wie im Video erreicht. Zusätzlich gleichen sich die Grenzen der Altersgruppen nicht, somit kann man diese Umrechnung auch nicht mit 100% Sicherheit unternehmen.

Eine zweite Lösung, die auch in der Berechnung verfolgt wird, ist, die groben Altersgruppen in die feingranularen Altersgruppen aufzuteilen. Im ersten Schritt erfolgt dies durch reine gleichverteilung, dh. der Wert einer Altersgruppe (zb. "40 bis 60") wird entsprechend geteilt (durch 4) um der feinen Granularität der Sterberaten-Altersgruppen zu entsprechen.
Dieser Ansatz birgt eine große Gefahr: Man geht davon aus, dass innerhalb der Altersgruppe die Sterberaten gleichverteilt sind, was in der Realität nicht der Fall sein wird. Dadurch berechnet man falsche Werte (der Wert für 40-45 entspricht dem Wert 55-60, wobei letzerer vermutlich in der Realität höher sein wird). Da der Ansatz für alle Jahre gleich verwendet wird, fällt die Unschärfe in allen Jahren gleich aus, was die Daten weiterhin vergleichbar macht. Diese Anpassung erfolgt in der Funktion "fill_year_column"


## Feedback

Feedback ist ausdrücklich erwünscht! Auch Pull-Requests, die die Berechnung verbessern/korrigieren. Ich arbeite erst seit kurzen mit Pandas, entsprechend gehe ich auch davon aus, dass die ein oder andere Berechnung einfacher umzusetzen ist.

Kontakt: erbsenzaehler.jr@gmx.de

Auf negatives Feedback aufgrund des Videos oder dessen Inhalte bitte ich zu verzichten, mir geht es rein um eine sachliche Analyse des Themas.
