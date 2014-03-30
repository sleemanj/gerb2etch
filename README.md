gerb2etch
=========

Bash script to convert gerber (and drill) files into a PDF containing artwork suitable for DIY PCB etching.

Provided with gerber files exported from your favourite PCB design tool, this script uses various tools to create a PDF file containing positive or negative artwork complete with frames and corner markings which you can use in various DIY pcb creating.

Low resolution example of generated artwork
-------------------------------------------
Show below is what is generated in the PDF for a copper and mask, silk is also available.  This is a low resolution screenshot, you can set arbitrary resolution (I wouldn't go higher than 1200 dpi unless you have a fast computer and time to spare).  This eample has a negative copper and positive mask, you can specifiy the negativity of any layer.

![Alt text](/example-output.jpg "Exmple")

Requirements
------------
*  bash
*  gerbv
*  imagemagick
*  grep
*  bc


For debian/ubuntu etc..

     sudo apt-get install bash gerbv imagemagick grep bc

should about cover it.

For windows users, you're largely on your own, but cygwin has bash, grep, bc and imagemagick:
http://cygwin.com/install.html

Gerbv has a windows version:
http://sourceforge.net/projects/gerbv/files/gerbv/gerbv-2.6.1/

For mac users, you're largely on your own, but it looks like Fink has everything you need:
http://www.finkproject.org/
so after installing fink probably the same apt-get install as above?


Installation
------------
Get the requirements above.
Put gerb2etch somewhere make it executable

Quick start
------------

*  export gerbers from your favourite tool into a directory
*  open a shell/console
*  cd into the directory with the gerbers
*  run gerb2etch
*  open the generated "artwork.pdf" file in your PDF viewer of choice
*  See gerb2etch -h
  
  
Tips
-----------

Use a marker pen to colour one corner of the edge your blank pcb, always put this corner into the marked corner on the artwork, then you can never get it around the wrong way.

The corner markings have a long edge and a short edge, especially for square PCB's you should make your marker colouring of the PCB edge longer on the long side, again now you can't get it rotated when you expose the other side.

Provided they will fit, the bottom and top images (for each of copper, mask and silk) will be placed side to side with the "alignment corners" facing each other.  This way you could use a "T" shaped ruler between them to help with your alignment (if you are doing a double sided board with a two-step exposure) - simply set the separation distance (-s) to the width of the ruler so it will cover the gap precisely and have one edge line up to each side's artwork.

A 1mm padding is put arund the PCB because usually home-etching needs a bit more lee-way especially at the edges, so cut your PCB 2mm wider and 2mm higher than your gerber specifies, you can set this padding amount with option -p


Advanced Usage
--------------

Usage: gerb2etch [options] [layer] [layer] ...
 
Options
-------
    -r Resolution in dpi, eg "-r 1200"   
    -u Short hand for negative acting UV exposure (eg dry-film), negates copper, sets layer output to copper and mask
    -t Short hand for toner transfer, negates mask (but doesn't output by default), sets layer output to copper only
    -z Rotate artwork (portrait becomes landscape, landscape becomes portrait)
   
    -n Specifically set the layers to negate, eg -n "copper mask" makes copper and mask negative
       Definition of negative
       ----------------------
         copper: traces will be transparent
         mask:   pads will be transparent
         silk:   text will be transparent
   
    -p Padding in mm from edge of artworks to edge of alignment frames, always transparent, eg "-p 1"
    -b Frame border width in mm, eg "-b 0.25"
    -s Separation distance between artwork frames in mm, eg "-s 30.4"

    -x Do not pre-trim the artwork, this means that the border added by gerbv will be kept in the artwork.
   
Layers   
-------
   Available layers are copper mask silk top bottom
   
   Eg, to get just the copper artworks
   
      : gerb2etch [options] copper
   
   For just the bottom artworks
   
      : gerb2etch [options] bottom
   
   For just the copper on the bottom
   
      : gerb2etch [options] copper bottom
   
   For just the copper and silk on the top
   
      : gerb2etch  [options] copper silk top
   
   
