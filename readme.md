#![citibike](http://citibikenyc.com/assets/images/header-logo.png) citibike Sinatra forms

## Objectives
1. create a form in HTML
2. using ERB population a select element with options from a JSON file
3. set values of options to data from the JSON file
3. create a route to respond to a post from the form
4. create instance variables from the params of the post to use in a map view
5. use embedded ruby to use the instance variables to plot points using javascript

## Instructions

### Get up and running
1. fork and then clone this repo: [https://github.com/ashleygwilliams/citibike-sinatra](https://github.com/ashleygwilliams/citibike-sinatra)
2. `bundle install`
3. `bundle exec shotgun`
4. `localhost:9393`

### Get familiar
This app is using the MultiJSON gem to load in data. If you look in `app.rb` you'll see that we are using something called a `before` filter to load the JSON into an instance variable called `@data`, which is a ruby hash of the data from the JSON file.

```ruby
#app.rb

  before do
    json = File.open("data/citibikenyc.json").read
    @data = MultiJson.load(json)
  end
```

The app has a single route, at `"\"` that will simple print out the contents of `@data`.
```ruby
#app.rb

  get '/' do
    erb :home
  end
```
```ruby
#home.erb

  <%= @data.inspect %>
```

Change the code in home.erb so that it prints a list of every station name, followed by it's longitude and latitude.

### Make a form

1. create a route that respond to a get request at `/form`
2. create a view, and update the route def so that `/form` will render that view
3. put a heading on the page with the text "Map my route!"
4. inside your view, create a `<form>` element with 2 `<select>` elements, one with `name="start"` and the other with `name="end"`. also create a submit button.
5. label each `<select>` element with the words "start:" and "end:" respectively 
6. inside the `<select>` elements, loop through the data hash and create an `<option>` element for each station with the `value` of the station name

you should now have a form that looks like this:
![form](http://content.screencast.com/users/ag_dubs/folders/Jing/media/abca7668-8e7f-4213-a8ac-ac0fa61613c8/00000047.png)

### Make the form work
1. your form should post to `/form`, so update your `<form>` element
2. create a route in `app.rb`
3. investigate your form's response by printing `params` to the browser when the form is submitted
4. once you feel comfortable with your form's response, change the response to a string that read "You chose ______ and _______" where the blanks contain the name of the station that the user chose

### MAP what?
the app you cloned also has a view called `map.erb`. this map uses [leaflet.js](http://leafletjs.com). the code in the view looks like this:

```html
<div id="map"></div>
<script type="text/javascript" charset="utf-8">
  var map = L.map('map').setView([40.7142, -74.0064], 13);
  L.tileLayer('http://tile.stamen.com/toner/{z}/{x}/{y}.png', {attribution: 'Stamen Toner'}, {
    attribution: 'Map data &copy; <a href="http://openstreetmap.org">OpenStreetMap</a> contributors, <a href="http://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, Imagery © <a href="http://cloudmade.com">CloudMade</a>',
    maxZoom: 18
  }).addTo(map);
</script>
```

basically, this code makes a `div` with `id=map`. the `<script>` contains javascript that makes a map and puts it into that div. 

1. create a route at `/map` and have it render this view. you should see a map, with no points. it'll look like this: 
![map](http://content.screencast.com/users/ag_dubs/folders/Jing/media/c544724f-1eea-433b-80fe-46079fb41d0f/00000048.png)

### Map the data
Let's connect our map to the data we receive from the form now. 

#### A. respond to the form post with the map view
1. change the action on your form to the `/map` route. 
2. change your map route to respond to a `post` instead of a `get`
3. test your app to ensure that everything still works

#### B. play with params again 
1. in your map route, create 2 instance variables and store the start and end station names in them
2. in `map.erb` create an unordered list and print the name of the start and end station name to the screen

#### C. from names to locations
so we're getting name data, but we actually want longitude and latitude data. where is that happening and what should we do to change it?

1. take a moment and look at your code. can you change the params that the form is sending to the map view? what are your options? play around for a second.

so, the params are determined by the `value` attribute of the `<select>` element. originally, we were just putting the station's name in there.. but we can change that.

2. change ther `value` of each `<select>` element to an array, where the first element is the latitude and the second element is the longitude of any given station. test this by printing the params to the map page. you'll want to see something like this:

![latlong](http://content.screencast.com/users/ag_dubs/folders/Jing/media/6283fefe-4e30-4e5b-b09c-89a3de24e330/00000050.png)

ooops. this data doesn't look right. we need to fix it. look at sarah's blog post for advice on how to do this:
[http://sarahduve.github.io/blog/2013/06/27/dont-forget-to-float/](http://sarahduve.github.io/blog/2013/06/27/dont-forget-to-float/)

#### D. embed the ruby in js
ok awesome, so now we have an array for the starting location and the ending location. the array contains the latitude and longitude for the selected station. this is rad because this is ALL we need to create markers and draw shapes and lines using the leaflet js mapping library.

you can add a marker to a leaflet map using the following code.
```javascript
L.marker([longitude, latitude]).addTo(map);
```
here:
- `L` is the leaflet library
- `.marker` is a method that creates a marker object. it takes one parameter, an array with 2 elements, `longitude` and `latitude`
- `.addTo` is a method that takes an objects and adds it to a map, which is passed as the parameter `map` (NOTE: that map was made earlier using this code `var map = L.map('map').setView([40.7142, -74.0064], 13);`)

so now:
1. add a marker for the start and end locations

in addition to markers, leaflet also allows you to draw polygons. a line is considered a polygon.

making a polygon is really similar to making a marker; except you use `.polygon` instead of `.marker` and you just simply pass it more points as parameters:
```javascript
L.polygon([
    [lat1, lng1],
    [lat2, lng2],
    [lat3, lng3]
]).addTo(map);
```
using that knowledge
2. create a line from the start location to the end location.


you now have something that looks like this:
![complete](http://content.screencast.com/users/ag_dubs/folders/Jing/media/ae7835ad-ddd7-425a-84d3-1e18bb693d1e/00000051.png)
