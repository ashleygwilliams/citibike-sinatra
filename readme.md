# FIS CitiBike Sandbox

## Up and Running

1. Clone repo
2. `bundle install`
3. `bundle exec shotgun`
4. open up `localhost:9393`

## data sets

### bikes
* `data/citibikenyc.json` is from <http://api.citybik.es/citibikenyc.json> which is a snapshot of stations info

### other
* `subway_entrances.json` is from <https://nycopendata.socrata.com/Transportation/Subway-Entrances/drex-xx56>

## da map

* map is using [leaflet.js](http://leafletjs.com/) 
* [a great tutorial to get your started](http://leafletjs.com/examples/quick-start.html)
* map is using tile layers from [stamen](http://maps.stamen.com/#toner/12/37.7706/-122.3782)

* how to add a point:

```js
var marker = L.marker([latitude, longitude]).addTo(map);
```
where latitude and longitude are the actual values from the json

HINT: you can embed ruby in javascript.

## interesting
* most popular subway entrances: <http://www.mta.info/nyct/facts/ffsubway.htm#stations>