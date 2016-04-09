var Transform = require('stream').Transform;
var inherits = require('util').inherits;
var fileSystem = require('fs');
var underscore = require('underscore');
var program = require('commander');

// For Node 0.8 users
if (!Transform) {
    Transform = require('readable-stream/transform');
}

//Constructor logic includes Internal state logic. PatternMatch needs to consider it because it has to parse chunks that get transformed

function PatternMatch(
    this.pattern = pattern;
//Switching on object mode so when stream reads sensordata it emits single pattern match.
    Transform.call(
	this,
	{
	    objectMode: true
	}
	);

}

// Extend the Transform class.

inherits(PatternMatch, Transform);


//Transform classes require that we implement a single method call _transform and optionally implement a method called _flush. Your assignment will implement both.

PatternMatch.prototype._transform = function (chunk, encoding, getNextChunk){
    var data = chunk.toString();
    var lines = data.split(this.pattern);
    var length = lines.length;
    if (length == 1) {
	this.characters = (this.characters || "") + lines[0];
	}
    else {
	this.characters = lines[length - 1];
	lines.splice(length - 1);
	var that = this;
	underscore.each(lines, function(line) {
	    that.push(line);
	    })
	getNextChunk();    
}

//After stream has been read and transformed, the _flush is called. It is a great place to push values to output stream and clean up existing data

PatternMatch.prototype._flush = function (flushCompleted) {
    flushCompleted();
}

// That wraps up patternMatch module.
// Program module is for taking command line arguments
program
    .option('-p, --pattern <pattern>', 'Input Pattern such as . ,')
    .parse(process.argv);

// Create an input stream from the file system
var inputStream = fileSystem.createReadStream( "input-sensor.txt" );

// Create a Pattern Matching stream that will run through the input and find matches for the given pattern at the command line - "." and ",".
console.log('-------------------Input-------------------');
var patternStream = inputStream.pipe(new PatternMatch(program.pattern));

// Read matches from the stream.
var streamMatches = [];
patternStream.on('readable', function() {
    var chunk;
    while((chunk = patternStream.read()) !== null) {
	streamMatches.push(chunk);
	}
});

patternStream.on('end', function() {
    console.log('-------------------Output-------------------');
    console.log(streamMatches);
});
