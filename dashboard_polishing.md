<details>
<summary>INDIKATOR</summary>

![enter image description here](https://raw.githubusercontent.com/hrubyf/AGOL/refs/heads/master/indikator.png)

```javascript
var  reference = 4.7;
var  wert = $datapoint.AVG_S1;

if (IsEmpty(wert)) {
	return {
		topText: 'Arbeitslosigkeit (nach SGB II)',
		middleText: '-',
		bottomText: 'Keine Daten'
};
}

var  difference = wert - reference;
var  diffText = "";
var  akzentFarbe = "#ffffff"; // Standard: Weiß

// Farb- und Richtungssymbolik für den Hauptwert
if (difference > 0.05) {
	akzentFarbe = "#fc8181"; // Rötlich (höhere Arbeitslosigkeit)
	diffText = "▲ +" + Text(difference, "#,##0.0") + " %";
} else  if (difference < -0.05) {
	akzentFarbe = "#68d391"; // Grünlich (niedrigere Arbeitslosigkeit)
	diffText = "▼ " + Text(difference, "#,##0.0") + " %";
} else {
	diffText = "±0,0 %";
}

return {
	topText: 'Arbeitslosigkeit (nach SGB II)',
	topTextColor: '#e2e8f0',
	topTextOutlineColor: '',
	topTextWeight: 'normal',
	topTextMaxHeight: 25,

// 1. Nur der konkrete Bezirkswert färbt sich ein:
	middleText: Text(wert, '#,##0.0') + ' %',
	middleTextColor: akzentFarbe,
	middleTextOutlineColor: '',
	middleTextWeight: 'bold',
	middleTextMaxHeight: 45,

// 2. Unten: Bleibt komplett in neutralem Grau, Differenz mit klarem Pfeil:
	bottomText: 'berlinweit ' + Text(reference, '#,##0.0') + ' % (' + diffText + ')',
	bottomTextColor: '#a0aec0',
	bottomTextOutlineColor: '',
	bottomTextWeight: 'normal',
	bottomTextMaxHeight: 25
};
```
</details>


<details>
<summary>LISTE (bullet points)</summary>

![enter image description here](https://raw.githubusercontent.com/hrubyf/AGOL/refs/heads/master/liste_1.png)

Erweiterte Formatierung
```javascript
// MSS-Statusfarben zuordnen
var  c = "#73b273"; // Standard/Mittel

if ($datapoint.SK == "sehr niedrig") {
	c = "#b23838"; // Rot
} else  if ($datapoint.SK == "niedrig") {
	c = "#e69838"; // Orange/Ocker
} else  if ($datapoint.SK == "hoch") {
	c = "#388238"; // Dunkelgrün
}

return {
	textColor: '',
	backgroundColor: '',
	separatorColor: '',
	// WICHTIG: Hier wird die Variable an das HTML weitergereicht:
	attributes: {
	statusFarbe: c
	}
}
```

Quellcode
```html
<p>
    <span style="color:{expression/statusFarbe};"><span style="font-size:1.2em;vertical-align:middle;">●</span></span> <strong>{field/Name}</strong>
</p>
<p style="color:#a0aec0;font-size:0.85em;margin-top:2px;">
    Status: {field/SK} | SGB II: {field/S1} %
</p>
```
</details>


<details>
<summary>LISTE (waffle chart)</summary>

![enter image description here](https://raw.githubusercontent.com/hrubyf/AGOL/refs/heads/master/liste_2.png)

Erweiterte Formatierung
```javascript
// 1. Sicheres Abgreifen des Indikatorwerts
var  rawVal = $datapoint["S4"];
var  quote = Number(DefaultValue(rawVal, 0));

// Auf ganze Punkte von 0 bis 100 beschränken
var  anzahlAktiv = Floor(Min(100, Max(0, quote)));

// 2. Akzentfarbe ermitteln
var  statusWert = DefaultValue($datapoint["SK"], "");
var  aktiveFarbe = "#fc8181"; // Standard: Rot

if (statusWert == "sehr niedrig") {
	aktiveFarbe = "#b23838";
} else  if (statusWert == "niedrig") {
	aktiveFarbe = "#e69838";
} else  if (statusWert == "mittel") {
	aktiveFarbe = "#73b273";
} else  if (statusWert == "hoch") {
	aktiveFarbe = "#388238";
}

var  inaktiveFarbe = "#334155"; // Dunkelgrau für inaktive Kacheln

// 3. 10x10 SVG-Raster generieren
var  svgRects = "";
var  kachelIndex = 0;

for (var  r  =  0;  r  <  10;  r++) {
	var  yPos = r * 6;
	for (var  c  =  0;  c  <  10;  c++) {
		var  xPos = c * 6;
		kachelIndex += 1;

		var  f = IIf(kachelIndex <= anzahlAktiv, aktiveFarbe, inaktiveFarbe);
		svgRects += "<rect x='" + Text(xPos) + "' y='" + Text(yPos) + "' width='4' height='4' rx='1' fill='" + f + "' />";
		}
}

var  svgCode = "<svg viewBox='0 0 58 58' width='58' height='58' style='display:block;'>" + svgRects + "</svg>";

// 4. Rückgabe für das Listen-Profil
return {
	textColor: "",
	backgroundColor: "",
	separatorColor: "",
	attributes: {
			waffleChart: svgCode
	}
};
```

Quellcode
```html
<div style="align-items:center;border-bottom:1px solid #2d3748;display:flex;justify-content:space-between;padding:6px 0;">
    <div style="flex:1;padding-right:12px;">
        <div style="color:#f8fafc;font-size:0.95em;margin-bottom:2px;">
            <strong>{field/Name}</strong>
        </div>
        <div style="color:#94a3b8;font-size:0.8em;">
            Status: <span style="color:#cbd5e1;">{field/SK}</span>
        </div>
        <div style="color:#f8fafc;font-size:0.85em;margin-top:2px;">
            <strong>Kinderarmut: {field/S4} %</strong>
        </div>
    </div>
    <div style="flex-shrink:0;">
        {expression/waffleChart}
    </div>
</div>
```
</details>


<details>
<summary>Liste (Piktogrammreihe)</summary>

![enter image description here](https://raw.githubusercontent.com/hrubyf/AGOL/refs/heads/master/liste_3.png)

Erweiterte Formatierung
```javascript
// Zu visualisierende Quote (z. B. S4: Kinderarmut oder S1: Arbeitslosigkeit)
var  quote = $datapoint.S4;

// Umrechnung auf eine 10er-Basis (z. B. 23.4% -> 2 Personen aktiv)
// Bei z. B. 100er-Basis teilt man durch 1, hier runden wir auf ganze Personen von 10:
var  aktiveIcons = Round(quote / 10.0, 0);
 
// Farben definieren
var  farbeAktiv = "#f87171"; // Warmes Korallenrot für betroffene Personen
var  farbeInaktiv = "#334155"; // Dunkles Schiefergrau für den Hintergrund/Rest
  
// SVG-Pfad eines schlanken Personen-Piktogramms (Kopf + Körper)
var  iconPath = "M9 4a3 3 0 1 0 0-6 3 3 0 0 0 0 6zm-7 14c0-2.8 3.1-5 7-5s7 2.2 7 5v1H2v-1z";
  
// Dynamische SVG-Zeile aufbauen (10 Personen nebeneinander)
var  svgIcons = '<svg viewBox="0 0 200 24" width="100%" height="22" preserveAspectRatio="xMinYMid meet" xmlns="http://www.w3.org/2000/svg">';
  
for (var  i  =  0;  i  <  10;  i++) {
	// Jedes Icon um 20 Einheiten nach rechts verschieben
	var  xOffset = i * 20;
	var  aktuelleFarbe = farbeInaktiv;

	if (i < aktiveIcons) {
		aktuelleFarbe = farbeAktiv;
	}

// Gruppe mit Verschiebung und Personen-Pfad
	svgIcons += '<g transform="translate(' + Text(xOffset) + ', 2)">';
	svgIcons += '<path d="' + iconPath + '" fill="' + aktuelleFarbe + '" />';
	svgIcons += '</g>';
}
 
svgIcons += '</svg>';
  
return {
	attributes: {
		pictogramChart: svgIcons,
		anzahlText: Text(aktiveIcons) + " von 10 Kindern",
		quoteFormatiert: Text(quote, "#,##0.0") + " %"
	}
};
```

Quellcode
```html
<div style="border-bottom:1px solid #2d3748;margin-bottom:8px;padding-bottom:6px;">
    <div style="align-items:baseline;display:flex;font-size:0.9em;justify-content:space-between;margin-bottom:4px;">
        <strong>{field/Name}</strong> <span style="color:#cbd5e1;"><span style="font-size:0.85em;">{expression/quoteFormatiert} </span></span><span style="color:#94a3b8;"><span style="font-size:0.9em;margin-left:4px;">({expression/anzahlText})</span></span><span style="color:#cbd5e1;"><span style="font-size:0.85em;">&nbsp;</span></span>
    </div>
    <div style="padding:2px 0;width:100%;">
        {expression/pictogramChart}
    </div>
</div>
```
</details>


