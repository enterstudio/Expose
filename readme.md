## Expose 2.0

A simple static site generator for photoessays

### Intro

If you're into photography, you probably have folders of images and videos like this:

![a bunch of images](http://jack.works/exposeimages/folder.jpg)

Expose is a Bash script that turns those images into photoessays similar to [jack.ventures](http://jack.ventures) or [jack.works](http://jack.works) (my personal blogs)

If you're not a fan of that look, a [Medium-style theme](http://jack.ventures/sample/inner-mongolia) is also included

tested on Windows/Cygwin, OSX, and should be fine on Linux

### Installation

The only dependency is Imagemagick. For videos FFmpeg is also required.

download the repo and alias the script
	
	alias expose=/script/location/expose.sh

for permanent use add this line to your ~/.profiles, ~/.bashrc etc depending on system

### Basic usage

	cd ~/folderofimages
	expose

The script operates on your current working directory, and outputs a _site directory.
Configuration and settings can be edited in the expose.sh file itself

### Flags

	expose -d
	expose -f

The d flag enables draft mode, where only a single low resolution is encoded. This can be used for a quick preview or for layout purposes.
The f flag overwrites the current _site. Note that images/videos are not overwritten, only HTML. This is meant for updating the text content of the site.

### Adding text

The text associated with each image is read from any text file with the same filename as the image, eg:

![images and text files](http://jack.works/exposeimages/imagetext.jpg)

### Sorting

Images are sorted by alphabetical order. To arbitrarily order images, add a numerical prefix

### Organization

You can put images in folders to organize them. The folders can be nested any number of times, and are also sorted alphabetically. The folder structure is used to generate a nested html menu.

To arbitrarily order folders, add a numerical prefix to the folder name. Any numerical prefixes are stripped from the url.

![folders](http://jack.works/exposeimages/folders.jpg)

### Text metadata

YAML in the text file is read and made available to the theme. The variables depend on the theme used.

### Theme-1 specific options

	---
	top: 30
	left: 5
	width: 30
	height: 20
	textcolor: #ffffff
	---

![content dimensions](http://jack.works/exposeimages/dimensionvariables.jpg)

The units are in percentages

	---
	top: 12
	left: 50
	width: 40
	height: 30
	polygon:[{"x":5, "y":0},{"x":100, "y":0},{"x":100, "y":100},{"x":7, "y":55}, {"x":0, "y":16}]
	textcolor: #ff9518
	---

Use a polygon to wrap text around shapes. The polygon is defined by 3 or more points in a JSON blob. Units are again in percentages.

![content polygon](http://jack.works/exposeimages/polygon.jpg)

### Theme-2 specific options

	---
	width: 32.5
	---

In theme-2 the width variable acts on the image rather than the content. You can use this to tile images in a row:

![changing width](http://jack.works/exposeimages/widthoption.jpg)

Note that in this theme the text goes above its associated image, except the first image which is used as a masthead.

### Metadata file

If you want certain variables to apply to an entire gallery, place a metadata.txt (this is configurable) file in the gallery directory. eg. in metadata.txt:
	
	textcolor: #ff0000	

To give all images in a gallery red text. Metadata file parameters are overriden by metadata in individual posts.
	
### Advanced usage

### Video options

Since we're using FFMpeg for video, we can leverage its filter framework for quick effects. This also saves a re-encode from a video editor workflow. Not all the FFmpeg options are applicable, but here are a few I found useful:

	---
	video-options: -ss 10 -t 5
	---
	
This will cut the video 10 seconds from the start, with a duration of 5 seconds.

	---
	video-filters: lut3d=file=fuji3510.cube
	---
	
If you're like me and shoot video in log profile, doing post work can be a pain. I like to globally apply a film print emulation LUT for a consistent look. Note that FFmpeg will look for the LUT file in the working directory you started the script in.
Notably FFmpeg does not support .look LUTs, so you'll have to convert them to one of .cube .3dl .dat or .m3d

![3d LUT](http://jack.works/exposeimages/lut3d.jpg)

	---
	video-filters: deshake,unsharp=6:6:3,lutyuv="u=128:v=128"
	---

Applies stabilization to the video and a slight sharpen filter, then converts to grayscale. Separate filters with commas.

A full list of FFmpeg filters can be [found here](https://ffmpeg.org/ffmpeg-filters.html#Video-Filters)

### Image options

Similar to videos, we can leverage the image editing features of Imagemagick.

	---
	image-options: -gravity Center -crop 100%x75%+0+0
	---

If you have a camera that shoots 4:3, but you want to crop down to 16:9. This will crop the image equally from top and bottom for the correct aspect ratio.

	---
	image-options: -sharpen 0x1.5
	---

Sharpens the image with a 1.5 pixel radius

	---
	image-options: -hald-clut transform.png
	---

Imagemagick does not read LUTs natively, but will accept a Hald color lookup image. This image can be created in photoshop or other graphics package by applying your LUT to the Hald identity CLUT.

	---
	image-options: -colorspace Gray -sigmoidal-contrast 5,50%
	---

Convert to a black-and-white image. Typically you would want to enhance contrast as well, which can be done by the sigmoidal contrast modifier. The first number controls contrast intensity.

A full list of Imagemagick options can be [found here](http://www.imagemagick.org/script/command-line-options.php)

### Image sequences

Timelapse and stop-motion are a great way to add motion to a scene. If your folder contains the key word "imagesequence" (this is configurable), the images in the folder will be converted to a video. Video options and filters may be applied to image sequences.

![Image sequence](http://jack.works/exposeimages/imagesequence.jpg)

By default the video is encoded at 24fps.

