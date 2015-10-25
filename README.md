how to get every country's latlon: http://stackoverflow.com/questions/2702309/need-a-list-of-all-countries-in-the-world-with-a-longitude-and-latitude-coordin

a browser console javascript verion, run these code in http://ws.geonames.org/ 's console
```javascript
/* getCountryLatLon.js */
function importJquery() {
	var element = document.createElement("script");
	element.src = "https://ajax.googleapis.com/ajax/libs/jquery/2.1.3/jquery.min.js";
	document.body.appendChild(element);
	var tick = function () {
		if (typeof $ !== 'undefined' && typeof $.ajax !== 'undefined') {
			getCountryInfo();
		} else {
			setTimeout(tick, 500);
		}
	};
	tick();
}
var xmlDoc;
var txtSource;
var $xmlDoc;
var results = [];
function getCountryInfo () {
    $.ajax({
        url : "http://api.geonames.org/countryInfo?username=demo",
        dataType: "text",
        success : function (txt) {
					console.log('get countryInfo xml string');
					txtSource = txt;
					xmlDoc = $.parseXML(txt);
					$xmlDoc = $(xmlDoc);
					getCountryDetail();
        }
    });
};

function getCountryDetail () {
	var countries = $xmlDoc.find('country');
	var limit = countries.length;
	var processCountry = function (index) {
		if (index >= limit) {
			console.log('countryCode,countryName,lat,lng');
			console.log(results.map(function (d) {
				return d.countryCode + ',' + d.countryName + ',' + d.lat + ',' + d.lng;
			}).join('\n'));
			return;
		}
		var country = countries[index];
		var countryCode = $(country).find('countryCode').html();
		var countryName = $(country).find('countryName').html();
		var link = 'http://ws.geonames.org/search?country=' + countryCode +
				'&name=' + countryName + '&maxRows=1&username=demo';
		console.log(index + ' ' + link);
		$.ajax({
			url : link,
			dataType: "text",
			success : function (txt) {
				console.log('get ' + index + ' ' + link + ' done');
				var c = $($.parseXML(txt));
				results[index] = {
					countryCode: c.find('countryCode').html(),
					countryName: c.find('countryName').html(),
					lat: c.find('lat').html(),
					lng: c.find('lng').html(),
					//continentName: c.find('continentName').html()
				};
				processCountry(index + 1);
			}
		});
	};
	processCountry(0);
};

importJquery();

```

the result is in countryinfo.csv.