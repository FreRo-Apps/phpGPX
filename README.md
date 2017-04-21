# phpGPX

[![MIT License](https://badges.frapsoft.com/os/mit/mit.svg?v=102)](https://github.com/Sibyx/phpGPX/blob/master/LICENSE)
[![Code Climate](https://codeclimate.com/github/Sibyx/phpGPX/badges/gpa.svg)](https://codeclimate.com/github/Sibyx/phpGPX)
[![Latest development](https://img.shields.io/packagist/vpre/sibyx/phpgpx.svg)](https://packagist.org/packages/sibyx/phpgpx)
[![Packagist downloads](https://img.shields.io/packagist/dm/sibyx/phpgpx.svg)](https://packagist.org/packages/sibyx/phpgpx)
[![Gitter](https://img.shields.io/gitter/room/phpgpx/phpgpx.svg)](https://gitter.im/phpGPX/Lobby)



Simple library written in PHP for reading and creating [GPX files](https://en.wikipedia.org/wiki/GPS_Exchange_Format).

Library is currently marked as Release Candidate but is already used in production on several project without any problems. [Documentation](https://sibyx.github.io/phpGPX/) is available using GitHub pages generated by Jekyll.

Contribution and feedback is welcome! Please check the issues for TODO. I will be happy every feature or pull request.

## Features

 - Full support of [official specification](http://www.topografix.com/GPX/1/1/).
 - Statistics calculation.
 - Extensions.
 - JSON & XML & PHP Array output.

### Supported Extensions
 - Garmin [TrackPointExtension](https://www8.garmin.com/xmlschemas/TrackPointExtensionv1.xsd): http://www.garmin.com/xmlschemas/TrackPointExtension/v1
 
### Stats calculation

 - Distance (m)
 - Average speed (m/s)
 - Average pace  (s/km)
 - Min / max altitude (m)
 - Start / end (DateTime object)
 - Duration (seconds)

## Examples

### Open GPX file and load basic stats
```php
<?php
use phpGPX\phpGPX;

$gpx = new phpGPX();
	
$file = $gpx->load('example.gpx');
	
foreach ($file->tracks as $track)
{
    // Statistics for whole track
    $track->stats->toArray();
    
    foreach ($track->segments as $segment)
    {
    	// Statistics for segment of track
    	$segment->stats->toArray();
    }
}
```

### Writing to file
```php
<?php
use phpGPX\phpGPX;

$gpx = new phpGPX();
	
$file = $gpx->load('example.gpx');

// XML
$file->save('output.gpx', phpGPX::XML_FORMAT);
	
//JSON
$file->save('output.json', phpGPX::JSON_FORMAT);
```

### Creating file from scratch
```php
<?php

use phpGPX\Models\GpxFile;
use phpGPX\Models\Link;
use phpGPX\Models\Metadata;
use phpGPX\Models\Point;
use phpGPX\Models\Segment;
use phpGPX\Models\Track;

require_once '/vendor/autoload.php';

$sample_data = [
	[
		'longitude' => 9.860624216140083,
		'latitude' => 54.9328621088893,
		'elevation' => 0,
		'time' => new \DateTime("+ 1 MINUTE")
	],
	[
		'latitude' => 54.83293237320851,
		'longitude' => 9.76092208681491,
		'elevation' => 10.0,
		'time' => new \DateTime("+ 2 MINUTE")
	],
	[
		'latitude' => 54.73327743521187,
		'longitude' => 9.66187816543752,
		'elevation' => 42.42,
		'time' => new \DateTime("+ 3 MINUTE")
	],
	[
		'latitude' => 54.63342326167919,
		'longitude' => 9.562439849679859,
		'elevation' => 12,
		'time' => new \DateTime("+ 4 MINUTE")
	]
];

// Creating sample link object for metadata
$link 							= new Link();
$link->href 					= "https://sibyx.github.io/phpgpx";
$link->text 					= 'phpGPX Docs';

// GpxFile contains data and handles serialization of objects
$gpx_file 						= new GpxFile();

// Creating sample Metadata object
$gpx_file->metadata 			= new Metadata();

// Time attribute is always \DateTime object!
$gpx_file->metadata->time 		= new \DateTime();

// Description of GPX file
$gpx_file->metadata->description = "My pretty awesome GPX file, created using phpGPX library!";

// Adding link created before to links array of metadata
// Metadata of GPX file can contain more than one link
$gpx_file->metadata->links[] 	= $link;

// Creating track
$track 							= new Track();

// Name of track
$track->name 					= sprintf("Some random points in logical order. Input array should be already ordered!");

// Type of data stored in track
$track->type 					= 'RUN';

// Source of GPS coordinates
$track->source 					= sprintf("MySpecificGarminDevice");

// Creating Track segment
$segment 						= new Segment();


foreach ($sample_data as $sample_point)
{
	// Creating trackpoint
	$point 						= new Point(Point::TRACKPOINT);
	$point->latitude 			= $sample_point['latitude'];
	$point->longitude 			= $sample_point['longitude'];
	$point->elevation 			= $sample_point['elevation'];
	$point->time 				= $sample_point['time'];

	$segment->points[] 			= $point;
}

// Add segment to segment array of track
$track->segments[] 				= $segment;

// Recalculate stats based on received data
$track->recalculateStats();

// Add track to file
$gpx_file->tracks[] 			= $track;

// GPX output
$gpx_file->save('CreatingFileFromScratchExample.gpx', \phpGPX\phpGPX::XML_FORMAT);

// Serialized data as JSON
$gpx_file->save('CreatingFileFromScratchExample.json', \phpGPX\phpGPX::JSON_FORMAT);

// Direct GPX output to browser

header("Content-Type: application/gpx+xml");
header("Content-Disposition: attachment; filename=CreatingFileFromScratchExample.gpx");

echo $gpx_file->toXML()->saveXML();
exit();
```

Currently supported output formats:

 - XML
 - JSON

## Installation

You can easily install phpGPX library with composer.

```
composer require sibyx/phpgpx
```
 
## Contributors
 
 - [Jakub Dubec](https://github.com/Sibyx) - Initial works, maintenance
 - [Lukasz Lewandowski](https://github.com/luklewluk)
 - [Robert Blackwell](https://github.com/robertblackwell)
  
I wrote this library as part of my job in [Backbone s.r.o.](https://www.backbone.sk/en/).

## License

This project is licensed under the terms of the MIT license.