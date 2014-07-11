
# Why hu.js

The general awkwardness of DOM functions is even worse for SVG than for HTML.

You don't want to create an element with

    var rect = document.createElementNS("http://www.w3.org/2000/svg", "rect");
	
And you'd prefer to have concise, chainable and convenient functions not too inspired by Java, and to have internal references to gradients managed for you.

And that might be about all you need. Because SVG is a very high level model. You don't usually need a higher model to draw shapes in your browser. You don't need functions hiding the logic of the SVG.

And you don't want to import a JavaScript file of 50 kb or more after minification for that. [hu.min.js](hu.min.js) weights 3 kb.

That's why you might find hu.js useful, assuming its limited set of features isn't too limited for you.


# Basic Example


    // Add to the body a new SVG element
    var svg = hu('<svg>', 'body').css({ width:100, height:100 });
    
    // Define a reusable radial gradient
    var grad = svg.rgrad(0.3, 0.3, 0.8, 'yellow', 'green');
    
    // Draw a circle using this gradient
    hu('<circle>', svg).attr({ cx:50, cy:50, r:50, fill:grad });

### Result :

![](http://dystroy.org/demos/hu.js/hu-sphere.png)

### SVG :

    <svg style="width: 100px; height: 100px;">
		<defs>
			<radialGradient id="ù1" cx="0.3" cy="0.3" r="0.8">
				<stop offset="0%" stop-color="yellow"></stop>
				<stop offset="100%" stop-color="green"></stop>
			</radialGradient>
		</defs>
		<circle cx="50" cy="50" r="50" fill="url(#ù1)"></circle>
	</svg>

# More complex examples

This still simple example have more shapes, event handling and animations :

**[Demonstration](http://dystroy.org/demos/hu.js/demo.html)**

See the source to see how it's done.

hu.js is used for all svg shape building in the open source [Miaou](http://dystroy.org/miaou/static/intro.html) platform. 

# API

## hu

The entry point of the library is the `hu` function. It's also aliased as `ù` which is obviously shorter and prettier but there's no problem in using `hu` if you're in a poor country where keyboards suffer from a deficiency in `ù` keys.

This function is used to

- create a svg element (and optionnaly append it to an existing html or svg element)
- find an existing svg element with a css selector
- wrap a provided svg element (and optionnaly append it to an existing html or svg element)

In all cases, there's always exactly one svg element in a resulting `Hu` object. The `hu` function returns `null` if the passed arguments make it impossible to create the element. If a provided selector (or nodelist, or jquery set, or array of elements) refers to more than one element, only the first one is considered.


#### Exemples
    
Create a `svg` element and adding it to `document.body` :

    var svg = hu('<svg>', document.body);
    
Just create an element that we'll append later :

    var group = hu('<g>');

Get an element we have in our group :

    var rect = hu('#rectId', group);
    
## attr & css

These very similar functions let you set attributes or style properties.

#### Examples

Get the value of an attribute

    var cx = circle.attr('cx');
    
Set a style property

    svg.css('background', '#2a4646');
    
Create a `line`, append it to a group and set a few attributes

    hu('<line>', group).attr({
		x1:10, y1:20, x2:30, y2:40,
		stroke:"green", strokeOpacity:0.6,
		strokeWidth:5, strokeLinecap:'round'
	});

## def

`def` creates a SVG node, adds it to the `defs` element of the closest element of type `svg`, and gives it an automatically generated id.

This is useful for shared resources like gradients.

#### Examples

Create a diagonal linear gradient with four steps of decreasing opacity, use it in a rectangle

    var grad = svg.def('<linearGradient>').attr({
		x1:0, y1:0, x2:1, y2:1
	}).stops(
		{offset:"0%", stopColor:"red", stopOpacity:1},
		{offset:"7%", stopColor:"green", stopOpacity:0.9},
		{offset:"20%", stopColor:"red", stopOpacity:0.2},
		{offset:"100%", stopColor:"green", stopOpacity:0}
	);
	ù('<rect>', svg).attr({x:0, y:0, width:100, height:20, fill:grad});

#### Using your own manual id instead of the automatically generated one

	var logo = svg.def('<g>').attr('id','logo');
	
## rgrad

`rgrad` is a shortcut to create a simple radial gradient

#### Example

    var grad = svg.rgrad(0.3, 0.3, 0.8, 'white', 'rgba(100,50,57,0.2)');

*Note : similar hgrad and vgrad functions might be available in the future as shortcuts for horizontal and vertical gradients. Just ask if you want them now.*

## animate

Animates one or more numerical style properties or attributes.

#### Example 

    var l = ù('<line>', svg).attr({
    	x1:100, y1:0, x2:200, y2:20,
    	stroke:'red', strokeOpacity:0.8,
    	strokeWidth:5, strokeLinecap:'round'
    }).animate({strokeOpacity:0, x2:300}, 1000, function(){
    	l.remove();
    });
