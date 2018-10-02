# QDBMP - Quick n' Dirty BMP Library

## Overview

QDBMP (Quick n' Dirty BMP) is a minimalistic cross-platform C library for handling BMP image files. The library is extremely lightweight - it consists of just two small files and has no dependencies other than the standard C library. It is intended to provide small applications with basic image I/O capabilities by utilizing the widely used BMP image format.

The library is written entirely in C, and is highly portable. It explicitly handles endianness issues and uses no OS-specific code.

The library supports the following BMP variants:

* Uncompressed 32 BPP (alpha values are ignored)
* Uncompressed 24 BPP
* Uncompressed 8 BPP (indexed color)

QDBMP is free and open source software, distributed under the MIT license.

### Usage
Add `qdbmp.h` and `qdbmp.c` to your project. Include `qdbmp.h` in your code. 

### Sample Program
```c
/* Creates a negative image of the input bitmap file */
#include "qdbmp.h"
#include <stdio.h>

int main( int argc, char* argv[] )
{
    BMP*    bmp;
    UCHAR   r, g, b;
    UINT    width, height;
    UINT    x, y;

    if ( argc != 3 )
    {
        fprintf( stderr, "Usage: %s <input file> <output file>\n", argv[ 0 ] );
        return 0;
    }

    /* Read an image file */
    bmp = BMP_ReadFile( argv[ 1 ] );
    BMP_CHECK_ERROR( stderr, -1 );  /* If an error has occurred, notify and exit */

    /* Get image's dimensions */
    width = BMP_GetWidth( bmp );
    height = BMP_GetHeight( bmp );

    /* Iterate through all the image's pixels */
    for ( x = 0 ; x < width ; ++x )
    {
        for ( y = 0 ; y < height ; ++y )
        {
            /* Get pixel's RGB values */
            BMP_GetPixelRGB( bmp, x, y, &r, &g, &b );

            /* Invert RGB values */
            BMP_SetPixelRGB( bmp, x, y, 255 - r, 255 - g, 255 - b );
        }
    }

    /* Save result */
    BMP_WriteFile( bmp, argv[ 2 ] );
    BMP_CHECK_ERROR( stderr, -2 );

    /* Free all memory allocated for the image */
    BMP_Free( bmp );

    return 0;
}
```

## API

### Types

`BMP` - Bitmap object instance  
`UINT` - Unsigned long integer (DWORD)  
`USHORT` - Unsigned short integer (WORD)  
`UCHAR` - Unsigned char  


### Error Handling

QDBMP's functions do not return values indicating success. Instead, each library operation sets an internal flag that may be queried using `BMP_GetError()`. If the operation was successful the function returns `BMP_OK`. Otherwise, it returns an error code describing what went wrong. `BMP_GetErrorDescription()` may be used to get a textual description of the last error that has occurred.

The suggested style is checking for an error immediately after every error prone operation. The `BMP_CHECK_ERROR` macro may be used to simplify this process. To illustrate:

```c
/* Perform a BMP operation */
bmp = BMP_ReadFile( "MyImage.bmp" );

/* Method 1: Manually check for an error */
if ( BMP_GetError() != BMP_OK )
{
    /* Print error info */
    printf( "An error has occurred: %s (code %d)\n", BMP_GetErrorDescription(), BMP_GetError() );
}

/* Method 2: Check for an error using the macro */
BMP_CHECK_ERROR( stdout, -1 );
```

### Functions

#### BMP_Create
`BMP* BMP_Create( UINT width, UINT height, USHORT depth )`  
Creates a blank BMP image with the specified dimensions and bit depth. Supported depth values are 8, 24 and 32.

#### BMP_Free
`void BMP_Destroy( BMP* bmp )`  
Frees all memory used by the BMP image.

#### BMP_ReadFile
`BMP* BMP_ReadFile( const char* filename )`  
Reads the specified BMP image file.

#### BMP_WriteFile
`void BMP_WriteFile( BMP* bmp, const char* filename )`  
Writes the BMP image to the specified file.

#### BMP_GetWidth
`UINT BMP_GetWidth( BMP* bmp )`  
Returns the image's width.

#### BMP_GetHeight
`UINT BMP_GetHeight( BMP* bmp )`  
Returns the image's height.

#### BMP_GetDepth
`USHORT BMP_GetDepth( BMP* bmp )`  
Returns the image's color depth (number of bits per pixel).

#### BMP_GetPixelRGB
`void BMP_GetPixelRGB( BMP* bmp, UINT x, UINT y, UCHAR* r, UCHAR* g, UCHAR* b )`  
Populates the arguments with the specified pixel's RGB values.

#### BMP_SetPixelRGB
`void BMP_SetPixelRGB( BMP* bmp, UINT x, UINT y, UCHAR r, UCHAR g, UCHAR b )`  
Sets the specified pixel's RGB values.

#### BMP_GetPixelIndex
`void BMP_GetPixelIndex( BMP* bmp, UINT x, UINT y, UCHAR* val )`  
Gets the specified pixel's color index in the palette.

#### BMP_SetPixelIndex
`void BMP_SetPixelIndex( BMP* bmp, UINT x, UINT y, UCHAR val )`  
Sets the specified pixel's color index in the palette.

#### BMP_GetPaletteColor
`void BMP_GetPaletteColor( BMP* bmp, UCHAR index, UCHAR* r, UCHAR* g, UCHAR* b )`  
Populates the arguments with the RGB values for the specified palette index.

### BMP_SetPaletteColor
`void BMP_SetPaletteColor( BMP* bmp, UCHAR index, UCHAR r, UCHAR g, UCHAR b )`  
Sets the RGB values for the specified palette index.

### BMP_GetError
`BMP_STATUS BMP_GetError()`  
Returns the last error code.

### BMP_GetErrorDescription
`const char* BMP_GetErrorDescription()`  
Returns a textual description of the last error.

### Macros
#### BMP_CHECK_ERROR
`BMP_CHECK_ERROR( output_file, return_value )`  
May be used after each BMP operation to check for an error. If an error has occurred, its description is printed to the specified output file and the specified value is returned.

## Contact
QDBMP was developed by Chai Braudo (braudo@gmail.com). 
