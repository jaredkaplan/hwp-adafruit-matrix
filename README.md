Adafruit LED 8x8 Matrix 
=======================

This library provides an interface to the Adafruit LED 8x8 Matrix.

* [Product information and data sheet](https://learn.adafruit.com/adafruit-led-backpack/1-2-8x8-matrix)

Getting Started
---------------

This library can be imported into Kinoma Studio and added to an application's build path settings. You can then use the AdafruitMatrix module to create a virtual display and a matrix instance that renders any 8x8 region of the display.

The following objects can be created using the AdafruitMatrix module in this library:

* [MonoColorMatrix](#monocolormatrix)
* [BiColorMatrix](#bicolormatrix)
* [MatrixPixelmapDisplay](#matrixpixelmapdisplay)
* [MatrixCanvasDisplay](#matrixcanvasdisplay)
* [MatrixDisplayPixel](#matrixdisplaypixel)

Example
-------

The following example demonstrates how to instantiate a new MonoColorMatrix object and turn on all of the LED's in the matrix:

```javascript
// get a reference to the AdafruitMatrix module
var AdafruitMatrix = require( "AdafruitMatrix" );

// create a new MonoColorMatrix instance and specify the
// I2C data and clock pins on the device
var matrix = new AdafruitMatrix.MonoColorMatrix( 27, 29 );

// initialize the matrix
matrix.init();

// get the reference to the matrix display and set the
// pixel state of all the display pixels to ON by calling
// the fill function with default parameters
matrix.display.fill();

// draw the matrix state
matrix.draw();
```

The matrix display provides an interface for setting individual pixels of the matrix. Convenience methods for filling and clearing rectangular regions of the display also exist. The following example iterates over the the display rows and columns and changes the state of each individual pixel before drawing the matrix.

```javascript
// iterate over each row of the display
for( var row = 0; row < matrix.display.height; row++ )
{
    // for each column in the display set the pixel with an alternating 
    // state, creating a checkerboard pattern on the matrix
    for( var col = 0; col < matrix.display.width; col++ )
        matrix.display.setPixel( col, row, (col + row) % 2 );
}

// draw the matrix state
matrix.draw();
```

A display can be larger than the dimensions of the actual matrix. The matrix itself is an 8x8 view into the display, and can be moved around to create animation effects. If you are connecting multiple matrix devices, each device can view different parts of the same display. The following example demonstrates how you create a custom size display, and use the functions in the Matrix interface to change the view offset:

```javascript
// create a 16x8 display
var display = new AdafruitMatrix.MatrixPixelmapDisplay( 16, 8 );

// create a new MonoColorMatrix instance
var matrix = new AdafruitMatrix.MonoColorMatrix( 27, 29, null, display );

// initialize the matrix
matrix.init();

// create a filled rectangle with a 4x4 hole in the center
// on the left side of the display
display.fillRect( 0, 0, 8, 8 );
display.clearRect( 2, 2, 4, 4 );

// fill a 4x4 rectangle in the center of the right side
// of the display
display.fillRect( 10, 2, 4, 4 );

// set the matrix display offset
matrix.setDisplayOffset( 4, 0 );

// draw the matrix state
matrix.draw();
```

A MatrixCanvasDisplay can be used in place of the default pixelmap display if more sophisticated drawing functions are necessary, or if you want to render an image file or texture into the display. The MatrixCanvasDisplay provides a Canvas 2D object that is used for drawing, and then is mapped into matrix pixels when needed. The following example demonstrates how to create and use a MatrixCanvasDisplay:

```javascript
// create a new matrix canvas display
var display = new AdafruitMatrix.MatrixCanvasDisplay();

// create a new MonoColorMatrix instance
var matrix = new AdafruitMatrix.MonoColorMatrix( 27, 29, null, display );

// initialize the matrix
matrix.init();

// begin drawing in the display and get a reference 
// to the Canvas 2D context
var ctx = display.beginDrawing();

// create a texture from an icon file
var texture = new Texture( "icon.png" );

// draw the texture in the canvas
ctx.drawImage( texture, 0, 0 );

// end drawing
display.endDrawing();

// update the matrix
matrix.draw();
```

API Reference
-------------

The following reference describes the object interfaces defined in the AdafruitMatrix module.

MonoColorMatrix
---------------

The MonoColorMatrix object interface is used when controlling a single color LED matrix, such as the following:

* [Adafruit - 1.2" 8x8 Bright Square Pure Green LED Matrix + Backpack](https://www.adafruit.com/products/1856)

Creating a new instance of a MonoColorMatrix object:

```javascript
var matrix = new AdafruitMatrix.MonoColorMatrix( sda, scl, addr, display, config );
```

Initializing the matrix object:

```javascript
matrix.init();
```

Updating the state of the LED's on the matrix:

```javascript
matrix.draw();
```

Modifying the offset of the matrix region in the display:

```javascript
matrix.setDisplayOffset( x, y );
```

BiColorMatrix
-------------

The BiColorMatrix object interface is used when controlling a two color LED matrix, such as the following:

* [Adafruit - Bicolor LED Square Pixel Matrix with I2C Backpack](https://www.adafruit.com/products/902)

Creating a new instance of a BiColorMatrix object:

```javascript
var matrix = new AdafruitMatrix.BiColorMatrix( sda, scl, addr, display, config );
```

Initializing the matrix object:

```javascript
matrix.init();
```

Updating the state of the LED's on the matrix:

```javascript
matrix.draw();
```

Modifying the offset of the matrix region in the display:

```javascript
matrix.setDisplayOffset( x, y );
```

MatrixPixelmapDisplay
---------------------

The MatrixPixelmapDisplay object interface is the default display used with a matrix object. The pixelmap provides an interface for manipulating individual pixels, and also has methods for filling and clearing rectangular regions of the display.

Creating a new instance of a MatrixPixelmapDisplay object:

```javascript
var display = new AdafruitMatrix.MatrixPixelmapDisplay( width, height );
```

> By default the display's width and height are initialized to 8, creating an 8x8 pixel area for the matrix. The width and height parameters can be specified to create a display area that is larger than the actual matrix dimensions.

Getting a pixel value from the display:

```javascript
var p = display.getPixel( x, y );
```

> This method returns a [MatrixDisplayPixel](#matrixdisplaypixel) object that contains the pixel's color, state, and brightness values. The pixel object returned can be manipulated to change the state of the display.

Setting a pixel value in the display:

```javascript
display.setPixel( x, y, state, color );
```

>The state parameter must be set to one of the following constants defined in the AdafruitMatrix module:
>
>* ON
>* OFF
>
>The color parameter is used for bi-color matrix displays, and must be one of the following constants defined in the AdafruitMatrix module:
>
>* COLOR_1
>* COLOR_2
>* COLOR_3
>
>The actual color that is displayed depends on the matrix. When specifying COLOR_1 or COLOR_2 it will correspond to turning on one of the two LED's for each pixel. Specifying COLOR_3 will turn both LED's on causing the third color to be displayed.

Filling the entire display region:

```javascript
display.fill( color );
```

>The color parameter is optional, and is used with bi-colored matrix displays. The color must be one of the three color constants defined in the AdafruitMatrix module. If no color is specified and a bi-color matrix is being used to render the display, then the COLOR_1 constant will be used as a default.

To fill a specific region of the display:

```javascript
display.fillRect( x, y, width, height, color );
```

Clearing the entire display region:

```javascript
display.clear();
```

To clear a specific region of the display:

```javascript
display.clearRect( x, y, width, height );
```

MatrixCanvasDisplay
-------------------

The MatrixCanvasDisplay object interface can be used as a drop in replacement for the default pixelmap display. It supports all of the same methods that the pixelmap display does for manipulating individual pixels, but it is backed by Canvas 2D object. The Canvas 2D object can be used to draw an image from a file or texture, and can be drawn into using the Canvas 2D drawing interface.

Creating a new instance of a MatrixCanvasDisplay object:

```javascript
var display = new AdafruitMatrix.MatrixCanvasDisplay( width, height, clearColor, buffered );
```

>The clearColor parameter is used to specify what color to draw into the Canvas object when the display's clear method is called. The buffered parameter can also be specified to make manipulating individual pixels more efficient. If the display will mostly be drawn using the Canvas 2D interface, then buffering may have a negative impact on performance. Buffering is turned off by default.

The following methods are implemented in the MatrixCanvasDisplay, and have the same effect as the methods specified in the [MatrixPixelmapDisplay](#matrixpixelmapdisplay) interface:

* getPixel
* setPixel
* fill
* fillRect
* clear
* clearRect

To get the Canvas 2D rendering context and begin drawing to the display:

```javascript
var ctx = display.beginDrawing();
```

>This method returns a Canvas 2D rendering context which can be drawn into using the [Canvas API](http://www.w3schools.com/tags/ref_canvas.asp).

To finish drawing in the display's Canvas 2D context:

```javascript
display.endDrawing();
```

>When the display is buffered, this method will copy the canvas's pixel data into a mirrored pixelmap display that is used for manipulating individual pixel data.

MatrixDisplayPixel
------------------

When getting pixel data from the matrix display using the getPixel method, a MatrixDisplayPixel object is returned. The object can be modified, which changes the pixel data stored in the display.

Setting the pixel state and color:

```javascript
p.set( state, color );
```

>The state parameter must be set to one of the following constants defined in the AdafruitMatrix module:
>
>* ON
>* OFF
>
>The color parameter is used for bi-color matrix displays, and must be one of the following constants defined in the AdafruitMatrix module:
>
>* COLOR_1
>* COLOR_2
>* COLOR_3

To toggle the state of the pixel:

```javascript
p.toggle();
```

Using getters to get the individual RGB values of the pixel:

```javascript
var r = p.r;
var g = p.g;
var b = p.b;
```
