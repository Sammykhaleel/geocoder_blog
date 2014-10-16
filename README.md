The idea of this application is to allow Administrator/user to store and find nearby locations based on the categories like education, employment, healthcare, sport and benefits. Live example <a href="http://sammy-geocoder.herokuapp.com/" target="blank">Here</a> and the source code <a href ="https://github.com/Sammykhaleel/geocoder_blog" target="blank">source code</a>

For the purpose of this application i will create one category to get an idea of using:

-Gmaps4rails

-Geocoder

<h2>Create locations app</h2>
First we need to have controllers, models, views and DB i'm using scaffold if you like to have in depth view of how to create these you can follow this <a href="http://guides.rubyonrails.org/getting_started.html" target="blank">blog</a>.
<pre>rails new geocoder</pre>
generate scaffold
<pre>rails g scaffold location latitude:float longitude:float address:string site:string</pre>
Notice here to use Geocoder we need to set latitude to float and longitude to float to use later on.
before we install Geocoder let's change the routes to set location to be the home page at http://localhost:3000/ we can do that by going to the config/routes folder and type the following
<pre>root 'locations#index'</pre>
Install Geocoder:
<pre>gem install geocoder</pre>
Or, if you're using Rails/Bundler, add this to your Gemfile:
<pre>gem "geocoder"</pre>
and run at the command prompt:
<pre>bundle install</pre>
Now in our controller we set the method index as below:
<pre>def index
    
    if params[:search].present?
     @locations = Location.near(params[:search], 50) 
    
    else
     @locations = Location.all
    end 
end</pre>
The first argument is to check if we have any stored location in our search box that we like to search for (we will create search box later in our view).
if we do have location in our data base then we need to sort it as nearby as we can see we called the near method on Location and we set it to 50 miles, you can change the miles as you like.
Otherwise we want to show all the locations that we have in our database in the index page.

Inside the model we can include geocoder
<pre>geocoded_by :address
after_validation :geocode, :if =&gt; :address_changed?</pre>
We use the validation to check if the address changed because we don't need to check for location every time.
And in the view we create the search box.
<pre>&lt;%= form_tag locations_path, :method =&gt; :get do%&gt;
  &lt;%= text_field_tag :search, params[:search], type: "text",placeholder: "Search"%&gt;
  &lt;%= submit_tag "Search Nearby", :name =&gt; nil, class: "alert button expand"%&gt;
&lt;%end%&gt;</pre>
Now before we setup google maps go a head and try it out by running rails server and create few locations then search nearby location by typing zip or city name you will see the location list refreshed based on the nearest one.

<a href="https://rubyboard.files.wordpress.com/2014/10/screen-shot-2014-10-16-at-12-16-27-am.png"><img class="alignnone  wp-image-202" src="https://rubyboard.files.wordpress.com/2014/10/screen-shot-2014-10-16-at-12-16-27-am.png?w=300" alt="Screen Shot 2014-10-16 at 12.16.27 AM" width="552" height="146" /></a>
<h3>Integrate Gmaps4rails</h3>
First in the Gemfile we include google map
<pre>gem 'gmaps4rails'</pre>
Then
<pre>bundle install</pre>
Now we need to add a div to show your map:

[code language="html"]
<div style="width: 800px;">
 <div id="map" style="width: 800px; height: 400px;">
 </div>
</div>
[/code]

Here we specify the hight and the width of our view.
Next we should not forgot calling the dependencies in the index.html.erb

[code language="html"]

<script src="//maps.google.com/maps/api/js?v=3.13&amp;sensor=false&amp;libraries=geometry" type="text/javascript"></script><script src="//google-maps-utility-library-v3.googlecode.com/svn/tags/markerclustererplus/2.0.14/src/markerclusterer_packed.js" type="text/javascript"></script>

[/code]

Now we can require <a href="http://underscorejs.org/underscore.js">underscorejs.org/</a>
the only thing you need to do is to copy the source code and place it under vendor/javascript create new file and call it underscore.js and past the source code in it.

Next step we need to require Gmaps4rails and underscore in assets/javascript/application.js
<pre>//= require underscore
//= require gmaps/google
</pre>
<h3>displaying the locations on the map</h3>
Again we need to go back to our controller and create a hash for the locations  and add this code inside the index method under the if statement:
<pre>@hash = Gmaps4rails.build_markers(@locations) do |location, marker|
      marker.lat location.latitude
      marker.lng location.longitude
      marker.infowindow location.site
     end
</pre>
The final result will look like this

<a href="https://rubyboard.files.wordpress.com/2014/10/screen-shot-2014-10-16-at-6-31-27-pm.png"><img class="alignnone  wp-image-203" src="https://rubyboard.files.wordpress.com/2014/10/screen-shot-2014-10-16-at-6-31-27-pm.png?w=300" alt="Screen Shot 2014-10-16 at 6.31.27 PM" width="525" height="207" /></a>

Here we get latitude and longitude on each location and the site.
Almost there, we need to add some javascript code in the views/locations/index to display the map
create script tag and add the following
[code language="javascript"]
<script>
handler = Gmaps.build('Google');
handler.buildMap({ provider: {}, internal: {id: 'map'}}, function(){
  markers = handler.addMarkers(&lt;%=raw @hash.to_json %&gt;);
  handler.bounds.extendWith(markers);
  handler.fitMapToBounds();
});
</script>
[/code]
now go ahead and try that if setup everything right you should see result like this:
<a href="https://rubyboard.files.wordpress.com/2014/10/screen-shot-2014-10-16-at-6-38-42-pm.png"><img class="alignnone  wp-image-204" src="https://rubyboard.files.wordpress.com/2014/10/screen-shot-2014-10-16-at-6-38-42-pm.png?w=300" alt="Screen Shot 2014-10-16 at 6.38.42 PM" width="654" height="441" /></a>

&nbsp;

&nbsp;

If the it's shows the map with blue color you may need to refresh the page to see the locations.

As you can in the live example <a href="http://sammy-geocoder.herokuapp.com/" target="blank">Here</a>
i used foundation and i added more categories its simple as we did it in this tutorial. 

Good luck 