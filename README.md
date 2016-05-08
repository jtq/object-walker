# Object Walker

Simple object walker with a per-value callback.

## Basic example

Walk an object tree and spit out all simple values and their paths.

    var walker = require('object-walker');

    var obj = {
		a: 0,
		b: 0xFF0000,
		c: -10.1,
		d: {
			a: 10
		}
	};

    walker.walk(obj, function(val, path) {
      console.log(path, val);
    });

## More complex example: tweening system

Define an `original` object with properties and a `target` object with target values for some or all of the original object's properties.  Then define a "tweening" function that will calculate the values of the object at any arbitrary point along the spectrum from `original` to `target`.

    var walker = require('object-walker');

    var from = {
		top: 10,
		left: 25,
		scale: 1,
		rotation: 0
	};

	var to = {
		top: 100,
		left: 0,
		scale: 1.5,
		rotation: 90
	};

	// Given a "from" object, a "to" object and a "progress" parameter (0 -> 1), calculate the values of the "from" object's properties at that point
	function tween(from, to, progress) {

		var result = JSON.parse(JSON.stringify(from));	// Quick hacky object deep-clone

		walker.walk(from, function(val, path) {
			var diff = walker.get(to, path) - walker.get(from, path);
			var newval = walker.get(from, path) + (diff * progress);
			walker.set(result, path, newval);
		});

		return result;
	}

	console.log("start", from);
	for(var i=1; i<=10; i++) {
		console.log(i, tween(from, to, i/10));
	}