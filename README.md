gerb2etch
=========

Bash script to convert gerber (and drill) files into a PDF containing artwork suitable for DIY PCB etching.

Provided with gerber files exported from your favourite PCB design tool, this script uses various tools to create a PDF file containing positive or negative artwork complete with frames and corner markings which you can use in various DIY pcb creating.

Low resolution example of generated artwork
-------------------------------------------
Show below is what is generated in the PDF for a copper and mask, silk is also available.  This is a low resolution screenshot, you can set arbitrary resolution (I wouldn't go higher than 1200 dpi unless you have a fast computer and time to spare).  This eample has a negative copper and positive mask, you can specifiy the negativity of any layer.

![Alt text](/example-output.jpg "Example")

Requirements
------------
*  bash
*  gerbv
*  imagemagick
*  grep
*  bc
*  for drill shrinking and via unburying, sed is required also


For debian/ubuntu etc..

     sudo apt-get install bash gerbv imagemagick grep bc sed

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

The script will look for the gerber layers using the below file names...

|Layer|Name Your Files|
|---|---|
|Copper Top | *.gtl ; or Top.gbr|
|Silk Top  | *.gto ; or TopSilk.gbr|
|Mask Top |*.gts ; or TopMask.gbr|
|Copper Bottom | *.gbl ; or Bottom.gbr|
|Silk Bottom | *.gbo ; or BottomSilk.gbr|
|Mask Bottom | *.gbs ; or BottomMask.gbr|
|Board Outline |*.gm1 ; or *.oln ; or Edges.gbr ; or BoardOutline.gbr|
|Drill | *.drl ; or drill.txt ; or Holes.gbr|
  
  
Tips
-----------

Use a marker pen to colour one corner of the edge your blank pcb, always put this corner into the marked corner on the artwork, then you can never get it around the wrong way.

The corner markings have a long edge and a short edge, especially for square PCB's you should make your marker colouring of the PCB edge longer on the long side, again now you can't get it rotated when you expose the other side.

Provided they will fit, the bottom and top images (for each of copper, mask and silk) will be placed side to side with the "alignment corners" facing each other.  This way you could use a "T" shaped ruler between them to help with your alignment (if you are doing a double sided board with a two-step exposure) - simply set the separation distance (-s) to the width of the ruler so it will cover the gap precisely and have one edge line up to each side's artwork.

A 1mm padding is put arund the PCB because usually home-etching needs a bit more lee-way especially at the edges, so cut your PCB 2mm wider and 2mm higher than your gerber specifies, you can set this padding amount with option -p

Printers may (usually do) have a small scaling error when you print, to counteract this you can use the -x and/or -y options to apply a scaling to the artwork.  For example, if you print what should be a 25mm horizontal line but  your printer prints out a 24mm long one, then you could use -x 1.042 (24 times 1.042 = 25.008, close enough).

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
   
    -d Shrink drill holes by $SHRINK_PERCENT% (but no smaller than $SHRINK_MIN_METRIC mm)
      Note that the pads do not shrink, just the etched holes, useful for better centering of drills
      especially in larger holes.
   
    -dd As for -d, but also completely remove any holes that are *already* smaller than $SHRINK_MIN_METRIC mm
       This is useful when you have small vias inside pads of 0 ohm SMD resistors so that you can use either
       a top-trace between vias when professionally manufactured, or a 0 ohm "resistor" on the bottom
       when you are etching your own and thus don't need to drill the small vias.       
   
    -V Do not untent vias.  By default all drill holes will be masked with a minimum circular mask of $UNBURY_DIAM_METRIC mm
      This will have the effect of masking ALL holes even if the solder mask gerber doesn't include them (tented vias).
      A side effect is that all through hole pad masks will be a minimum diameter of $UNBURY_DIAM_METRIC mm
      For home-etched boards you probably want all vias exposed so you can solder wire/rivets through them after your
      soldermask is applied.  If you are electroplating however you might want to turn this off and so tent the plated
      vias under soldermask as a professional board house would do (assuming you have specified tent vias of course).
   
    -D Include holes in the solder mask pads, by default the masks do not have holes so that slight mis-alignment is less 
      of a problem.
   
    -X Do not pre-trim the artwork, this means that the border added by gerbv will be kept in the artwork.
   
    -x Scale the X dimension by a given factor, eg "-x 1.05" makes the X dimension 5% longer
   
    -y Scale the Y dimension by a given factor, eg "-y 0.95" makes the Y dimension 5% shorter
    
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
   
   
