<details>
<summary>INDIKATOR</summary>

![enter image description here](https://raw.githubusercontent.com/hrubyf/AGOL/refs/heads/master/indikator.png?token=GHSAT0AAAAAAENZ7S4EBAZVYYYVJALEDWZY2W7EYAQ)

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

![enter image description here](https://raw.githubusercontent.com/hrubyf/AGOL/refs/heads/master/liste_1.png?token=GHSAT0AAAAAAENZ7S4FUPLNB6CMBOBWOPD62W7FFXQ)

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
