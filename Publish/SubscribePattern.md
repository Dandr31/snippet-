# Understanding the [Publish/Subscribe Pattern](https://msdn.microsoft.com/en-us/magazine/hh201955.aspx#author) for Greater JavaScript Scalability
    Addy Osmani | May 18, 2011
Design patterns are an invaluable tool for modern JavaScript developers, providing us with generalized reusable solutions to commonly occurring problems in application design that we can apply to our own problems. In addition, they provide the community with a common vocabulary we can use to discuss our designs, which can often be a non-trivial process.

Today, we'll be exploring a design pattern so common to JavaScript development, you've probably used it before, whether you were aware of it or not. It's called the Observer pattern and is often referred to and implemented nowadays as the publisher/subscriber (pub/sub) pattern.
### The Observer (Pub/Sub) pattern

The general idea behind the Observer pattern is the promotion of loose coupling (or decoupling as it's also referred as). Rather than single objects calling on the methods of other objects, an object instead    subscribes to a specific task or activity of another object and is notified when it occurs. Observers are also called Subscribers and we refer to the object being observed as the Publisher (or the subject).  Publishers notify subscribers when events occur.

When objects are no longer interested in being notified by the subject they are registered with, they can unregister (or unsubscribe) themselves. The subject will then in turn remove them from the observer collection. 

It's often useful to refer back to published definitions of design patterns that are language agnostic to get a broader sense of their usage and advantages over time. The definition of the observer pattern provided in the Gang of Four book, Design Patterns: Elements of Reusable Object-Oriented Software, is:

'One or more observers are interested in the state of a subject and register their interest with the subject by attaching themselves. When something changes in our subject that the observer may be interested in, a notify message is sent which calls the update method in each observer. When the observer is no longer interested in the subject's state, they can simply detach themselves.'

Basically, the pattern describes subjects and observers forming a publish-subscribe relationship. Unlimited numbers of objects may observe events in the subject by registering themselves. Once   registered to particular events, the subject will notify all observers when the event has been fired. 
### Advantages 

Arguably, the largest benefit of using pub/sub is the ability to break down our applications into smaller, more loosely coupled modules, which can also improve general manageability. 

Pub/sub is also a pattern that encourages us to think hard about the relationships between different parts of your application, identifying what layers need to observe or listen for behaviour and which need to   push notifications regarding behaviour occurring to other parts of our apps.

Whilst it may not always be the best solution to every problem, it remains one of the best tools for designing decoupled systems and should be considered an important tool in any JavaScript developer's utility belt. 
### Disadvantages

Consequently, some of the issues with the pub/sub pattern actually stem from its main benefit. By decoupling publishers from subscribers, it can sometimes become difficult to obtain guarantees that particular parts of our applications are functioning as we may expect.

For example, publishers may make an assumption that one or more subscribers are listening to them. Say that we're using such an assumption to log or output errors regarding some application process. If the subscriber performing the logging crashes (or for some reason fails to function), the publisher won't have a way of seeing this due to the decoupled nature of the system. 
### Implementations

One of the benefits of design patterns is that once we understand the basics behind how a particular pattern works, being able to interpret an implementation of it becomes significantly more straightforward. Luckily, popular JavaScript libraries such as dojo and YUI already have utilities that can assist in easily implementing your own pub/sub system. 

For those wishing to use the pub/sub pattern with vanilla JavaScript (or another library) AmplifyJS includes a clean, library-agnostic implementation of pub/sub that can be used with any library or toolkit. ScriptJunkie also has a tutorial on how to get started with Amplify's pub/sub that was recently published. You can of course also write your own implementation from scratch or also check out either PubSubJS or OpenAjaxHub, both of which are also library-agnostic.

jQuery developers have quite a few options for pub/sub (in addition to Amplify) and can opt to use one of the many well-developed implementations ranging from Peter Higgins's jQuery plugin to Ben Alman's (optimized) gist on GitHub. Links to just a few of these can be found below.

    Ben Alman's gist https://gist.github.com/661855 (recommended)
    Rick Waldron's jQuery-core style take on the above https://gist.github.com/705311
    Peter Higgins' plugin (http://github.com/phiggins42/bloody-jquery-plugins/blob/master/pubsub.js).
    Ben Truyman's gist https://gist.github.com/826794

A note regarding using jQuery's custom events for pub/sub: although custom events may assist you in achieving an observer-like implementation, they have a few downsides such as:

1. The amount of unnecessary overhead involved in their usage and

2. They heavily tie everything to the DOM, which isn't the best idea if you’re just looking for a lightweight pub/sub system. Where possible, I'd recommend using a dedicated pub/sub implementation instead. If interested, here’s a jsPerf test demonstrating how a non-custom-events solution such as PubSubJS is faster than custom events.
### Tutorial

So that we are able to get an appreciation for how many of the vanilla JavaScript implementations of the Observer pattern might work, let's take a walk through of a trimmed down version of Morgan Roderick's PubSubJS, which I've put together below. This demonstrates the core concepts of subscribe, publish as well as the concept of unsubscribing.

I've opted to base our examples on this code as it sticks closely to both the method signatures and approach of implementation I would expect to see in a JavaScript version of the original observer pattern.

Note: if you're interested in another compact pub/sub implementation (0.45KB minified), you may also find my 'pubsubz' project over on GitHub useful.

Sample Pub/Sub implementation 
```js
var PubSub = {};

(function(p){

    "use strict";

    

    var topics = {},

          lastUid = -1;

    

    var publish = function( topic , data){

        if ( !topics.hasOwnProperty( topic ) ){

            return false;

        }

        

        var notify = function(){

            var subscribers = topics[topic],

                throwException = function(e){

                return function(){

                    throw e;

                };

            }; 

            

            for ( var i = 0, j = subscribers.length; i < j; i++ ){

                try {

                    subscribers[i].func( topic, data );

                } catch( e ){

                    setTimeout( throwException(e), 0);

                }

            }

        };

        

        setTimeout( notify , 0 );

        return true;

    };

    

    /**

     *  Publishes the topic, passing the data to it's subscribers

     *  @topic (String): The topic to publish

     *  @data: The data to pass to subscribers

    **/

    p.publish = function( topic, data ){

        return publish( topic, data, false );

    };

    

    /**

     *  Subscribes the passed function to the passed topic. 

     *  Every returned token is unique and should be stored if you need to unsubscribe

     *  @topic (String): The topic to subscribe to

     *  @func (Function): The function to call when a new topic is published

    **/

    p.subscribe = function( topic, func ){

        // topic is not registered yet

        if ( !topics.hasOwnProperty( topic ) ){

            topics[topic] = [];

        }

        var token = (++lastUid).toString();

        topics[topic].push( { token : token, func : func } );

        // return token for unsubscribing

        return token;

    };

    /**

     *  Unsubscribes a specific subscriber from a specific topic using the unique token

     *  @token (String): The token of the function to unsubscribe

    **/

    p.unsubscribe = function( token ){

        for ( var m in topics ){

            if ( topics.hasOwnProperty( m ) ){

                for ( var i = 0, j = topics[m].length; i < j; i++ ){

                    if ( topics[m][i].token === token ){

                        topics[m].splice( i, 1 );

                        return token;

                    }

                }

            }

        }

        return false;

    };

}(PubSub));
```
## Example 1: Basic use of publishers and subscribers

This could then be easily used as follows:
```js
// a sample subscriber (or observer)

var testSubscriber = function( topics , data ){

    console.log( topics + ": " + data );

};



// add the function to the list of subscribers to a particular topic

// maintain the token (subscription instance) to enable unsubscription later

var testSubscription = PubSub.subscribe( 'example1', testSubscriber );



// publish a topic or message asyncronously

PubSub.publish( 'example1', 'hello scriptjunkie!' );

PubSub.publish( 'example1', ['test','a','b','c'] );

PubSub.publish( 'example1', [{'color':'blue'},{'text':'hello'}] );



// unsubscribe from further topics

setTimeout(function(){

    PubSub.unsubscribe( testSubscription );

}, 0);



// test that we've fully unsubscribed

PubSub.publish( 'example1', 'hello again!' );
```
Real-time stock market application

Next, let's imagine we have a web application responsible for displaying real-time stock information. 

The application might have a grid for displaying the stock stats and a counter for displaying the last point of update, as well as an underlying data model. When the data model changes, the application will need to update the grid and counter. In this scenario, our subject is the data model and the observers are the grid and counter. 

When the observers receive notification that the model itself has changed, they can update themselves accordingly.
## Example 2: UI notifications using pub/sub

In the following example, we limit our usage of pub/sub to that of a notification system. Our subscriber is listening to the topic 'dataUpdated' to find out when new stock information is available. It then triggers 'gridUpdate' which goes on to call hypothetical methods that pull in the latest cached data object and re-render our UI components. 

Note: the Mediator pattern is occasionally used to provide a level of communication between UI components without requiring that they communicate with each other directly. For example, rather than tightly coupling our applications, we can have widgets/components publish a topic when something interesting happens. A mediator can then subscribe to that topic and call the relevant methods on other components.
```js
var grid = {

    refreshData: function(){

        console.log('retrieved latest data from data cache');

        console.log('updated grid component');

    },

    updateCounter: function(){

        console.log('data last updated at: ' + getCurrentTime());

    }    

};



//a very basic mediator

var gridUpdate = function(topics, data){ 

      grid.refreshData();

      grid.updateCounter();

}



var dataSubscription = PubSub.subscribe( 'dataUpdated', gridUpdate );

      

PubSub.publish( 'dataUpdated', 'new stock data available!' );



PubSub.publish( 'dataUpdated', 'new stock data available!' );



function getCurrentTime(){

    var date = new Date(),

          m = date.getMonth() + 1,

          d = date.getDate(),

          y = date.getFullYear(),

          t = date.toLocaleTimeString().toLowerCase(),

         return (m + '/' + d + '/' + y + ' ' + t);   

}
```
Whilst there's nothing terribly wrong with this, there are more optimal ways that we can utilize pub/sub to our advantage.
## Example 3: Taking notifications further

Rather than just notifying our subscribers that new data is available, why not actually push the new data through to gridUpdate when we publish a new notification from a publisher. In this next example, our publisher will notify subscribers with the actual data that's been updated as well as a timestamp from the data-source of when the new data was added.

In addition to avoiding data having to be read from a cached store, this also avoids client-side calculation of the current time whenever a new data entry gets published.
```js
var grid = {

    addEntry: function(data){

        if (data !== 'undefined') {

           console.log('Entry:' 

                       + data.title 

                       + ' Changenet / %' 

                       + data.changenet 

                       + '/' + data.percentage + ' % added');

        }

    },

    updateCounter: function(timestamp){

        console.log('grid last updated at: ' + timestamp);

    }    

};



var gridUpdate = function(topics, data){ 

        grid.addEntry(data);

        grid.updateCounter(data.timestamp);

}



var gridSubscription = PubSub.subscribe( 'dataUpdated', gridUpdate );

PubSub.publish('dataUpdated',   { title: "Microsoft shares", changenet: 4, percentage: 33, timestamp: '17:34:12'  });

PubSub.publish('dataUpdated',   { title: "Dell shares", changenet: 10, percentage: 20, timestamp: '17:35:16'  });
```
##Example 4: Decoupling applications using jQuery & Ben Alman's pub/sub implementation

In the following movie ratings example, we'll be using Ben Alman's jQuery implementation of pub/sub to demonstrate how we can decouple a user interface. Notice how submitting a rating only has the effect of publishing the fact that new user and rating data is available. 

It's left up to the subscribers to those topics to then delegate what happens with that data. In our case we're pushing that new data into existing arrays and then rendering them using the jQuery.tmpl plugin.

HTML
```html
<script id="userTemplate" type="text/x-jquery-tmpl">

    <li>${user}</li>

</script>



<script id="ratingsTemplate" type="text/x-jquery-tmpl">

    <li><strong>${movie}</strong> was rated ${rating}/5</li>

</script>



<div id="container">

    <div class="sampleForm">

        <p>

            <label for="twitter_handle">Twitter handle:</label>

            <input type="text" id="twitter_handle" />

        </p>

        <p>

            <label for="movie_seen">Name a movie you've seen this year:</label>

            <input type="text" id="movie_seen" />

        </p>

        <p>

            <label for="movie_rating">Rate the movie you saw:</label>

            <select id="movie_rating">

                  <option value="1">1</option>

                   <option value="2">2</option>

                   <option value="3">3</option>

                   <option value="4">4</option>

                   <option value="5" selected>5</option>

           </select>

         </p>

         <p>

             <button id="add">Submit rating</button>

         </p>

     </div>

 

     <div class="summaryTable">

         <div id="users"><h3>Recent users</h3></div>

         <div id="ratings"><h3>Recent movies rated</h3></div>

     </div>

     

 </div>
 ```
 JavaScript
 ```js
 (function($) {



var movieList = [],

    userList  = [];

    

/* subscribers */

$.subscribe( "/new/user", function(userName){

    if(userName.length){

        userList.push({user: userName});

        $( "#userTemplate" ).tmpl( userList ).appendTo( "#users" );

   }

});



$.subscribe( "/new/rating", function(movieTitle, userRating){

    if(movieTitle.length){

      movieList.push({ movie: movieTitle, rating: userRating});

      $( "#ratingsTemplate" ).tmpl( movieList ).appendTo( "#ratings" );

    }

});





$('#add').bind('click', function(){

        var strUser    = $("#twitter_handle").val(),

            strMovie = $("#movie_seen").val(),

            strRating = $("#movie_rating").val();

        

        $.publish('/new/user',  strUser  );

        $.publish('/new/rating',  [ strMovie, strRating] );

    

});



})(jQuery);
```
## Example 5: Decoupling an Ajax-based jQuery application 

In our final example, we're going to take a practical look at how decoupling our code using pub/sub early on in the development process can save us some potentially painful refactoring later on. This is something Rebecca Murphey touched on in her pub/sub screencast and is another reason why pub/sub is favoured by so many developers in the community.

Quite often in Ajax-heavy applications, once we've received a response to a request we want to achieve more than just one unique action. One could simply add all of their post-request logic into a success callback, but there are drawbacks to this approach,

Highly coupled applications sometimes increase the effort required to reuse functionality due to the increased inter-function/code dependency. What this means is that although keeping our post-request logic hardcoded in a callback might be fine if we're just trying to grab a result set once, it's not as appropriate when we want to make further Ajax-calls to the same data source (and different end-behaviour) without rewriting parts of the code multiple times. Rather than having to go back through each layer that calls the same data-source and generalizing them later on, we can use pub/sub from the start and save time.

Using pub/sub, we can also easily separate application-wide notifications regarding different events down to whatever level of granularity you're comfortable with, something which can be less elegantly done using other patterns.  

Notice how in our sample below, one topic notification is made when a user indicates they want to make a search query and another is made when the request returns and actual data is available for consumption. It's left up to the subscribers to then decide how to use knowledge of these events (or the data returned). The benefits of this are that, if we wanted, we could have 10 different subscribers utilizing the data returned in different ways but as far as the Ajax-layer is concerned, it doesn't care. Its sole duty is to request and return data then pass it on to whoever wants to use it. This separation of concerns can make the overall design of your code a little cleaner.

HTML
```html
<form id="flickrSearch">

    <input type="text" name="tag" id="query"/>

    <input type="submit" name="submit" value="submit"/>

</form>

    

<div id="lastQuery"></div>

<div id="searchResults"></div>

    

<script id="resultTemplate" type="text/x-jquery-tmpl">

     {{each(i, items) items}}

             <li><p><img src="${items.media.m}"/></p></li>

     {{/each}}

</script>
```
JavaScript
```js
(function($) {

    $('#flickrSearch').submit(function(e){

        e.preventDefault();

        var tags = $(this).find('#query').val();

        if(!tags){return;}

        $.publish('/search/tags', [ $.trim(tags) ]);



    });

    

    $.subscribe('/search/tags', function(tags){

        $.getJSON('http://api.flickr.com/services/feeds/photos_public.gne?jsoncallback=?', 

                   { tags: tags, tagmode: 'any', format: 'json'},

        function(data){

            if(!data.items.length){ return; }

            $.publish('/search/resultSet', [ data ]);                 

        });

    });

    

    $.subscribe('/search/tags', function(tags){

        $('#searchResults').html('<p>Searched for:<strong>' + tags + '</strong></p>');

    });

    

    $.subscribe('/search/resultSet', function(results){

        var holder = $('#searchResults');

        holder.html();

        $('#resultTemplate').tmpl(results).appendTo(holder);

    });

    

    

})(jQuery);
```
## Conclusions

The Observer pattern is useful for decoupling a number of different scenarios in application design and if you haven't been using it, I recommend picking up one of the pre-written implementations mentioned today and just giving it a try out. It's one of the easier design patterns to get started with but also one of the most powerful.

As an aside, I've previously written about design patterns in my creative-commons licensed book 'Essential JavaScript Design Patterns for Beginners', which I recommend checking out if you're new to patterns and would like to learn more about how they can be used in JavaScript.

 
