---
title: "PHP: Create Thumbnails on the Fly"
date: 2017-10-15T17:49:50+02:00
draft: true
---
Sometimes it might be usefull, to create thumbnails on your server. Most of the time you have your images right on your server, but when you want to handle an image from an url it becomes more difficult. Here is how you can create a thumbnail directly from an url and display it.

For that you can use this Thumbnail function. You can pass an url, the width + height of the final image and if you provide a filename it will be saved as a file, otherways the image will be returned so you can easily display it.

# How to use the function
~~~php
$url = "http://short1.link/ag2q80";

$image = Thumbnail($url);

if(exif_imagetype($url) == IMAGETYPE_PNG) {
	header( "Content-type: image/png" );
	imagepng($image);
}
else {
	header( "Content-type: image/jpeg" );
	imagejpeg($image);
}
~~~
Basically you can add this code to display the image directly to the function

# The function
~~~php
function Thumbnail($url, $width = 150, $height = 150, $filename = false) {

	// download and create gd image
	$image = ImageCreateFromString(file_get_contents($url));

	// calculate resized ratio
	// Note: if $height is set to TRUE then we automatically calculate the height based on the ratio
	//$height = $height === true ? (ImageSY($image) * $width / ImageSX($image)) : $height;

	$w = ImageSX($image);
	$h = ImageSY($image);

    if($w > $h) {
            $new_height =   $height;
            $new_width  =   floor($w * ($new_height / $h));
            $crop_x     =   ceil(($w - $h) / 2);
            $crop_y     =   0;
    }
    else {
            $new_width  =   $width;
            $new_height =   floor( $h * ( $new_width / $w ));
            $crop_x     =   0;
            $crop_y     =   ceil(($h - $w) / 2);
    }

	// create image 
	$output = ImageCreateTrueColor($width, $height);

	//support transparent png images
	if(exif_imagetype($url) == IMAGETYPE_PNG) {
		imagealphablending($output, false);
		imagesavealpha($output,true);
		$transparent = imagecolorallocatealpha($output, 255, 255, 255, 127);
		imagefilledrectangle($output, 0, 0, $new_width, $new_height, $transparent);
	}

	ImageCopyResampled($output, $image, 0, 0, $crop_x, $crop_y, $new_width, $new_height, $w, $h);

	// save image
	if($filename) {
		if(exif_imagetype($url) == IMAGETYPE_PNG) {
			imagepng($output, $filename);
		}
		else {
			ImageJPEG($output, $filename, 70); 
		}
	}
	else {
		return $output;
	}
}
~~~