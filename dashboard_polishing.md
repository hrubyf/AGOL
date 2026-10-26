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

