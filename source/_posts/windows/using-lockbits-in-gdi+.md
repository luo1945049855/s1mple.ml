---
title:  "Using LockBits in GDI+"
date:   2020-09-09
categories: Windows
---

Understanding how to use LockBits is essential for creating high performance GDI+ applications. Usually, GDI+ is thought of as a low performance graphics API. While arguments can be made for this, if you use GDI+ properly, you can achieve great performance.

The easiest way to access pixel data is to use GetPixel and SetPixel member functions of the Bitmap class. Unfortunately, using these functions has a great amount of overhead. When working with large images, doing any type of processing with GetPixel and SetPixel is simply too slow and not acceptable for modern applications. LockBits is specifically used to make the data in a bitmap directly available to you, the programmer. The workflow is very simple:

1. Use LockBits to obtain a raw array of data
2. Perform image processing using the standard array of data
3. Use UnlockBits to commit the new data back into the bitmap

## Calling LockBits

When calling LockBits, you can specify a specific rectangular portion of the bitmap you want to manipulate, the read/write mode, the pixel format you would like the data to be in.

// We must use LockBits to get the raw data. GetPixel and SetPixel are simply too slow

```cpp
BitmapData bitmapData;
pBitmap->LockBits(
    &Rect(0,0,pBitmap->GetWidth(), pBitmap->GetHeight()), 
    ImageLockModeWrite,
    PixelFormat32bppARGB, &bitmapData);
```

## The format of the pixel data

It is important for you to realize how the data is formatted. The data is located in the Scan0 member variable in the BitmapData class. This is a standard, one dimensional array which stores information for a 2D image. Therefore, that 2D information is packed into a 1D array. In order to gain access to a specific pixel, you will need to know the X and Y coordinate, along with the Stride.

![LockBits](/images/LockBits.jpg?raw=true"LockBits")

As you can see in the image above, not all data elements are used. Furthermore, the Scan0 array is of the type void. Before accessing data, it is a good idea to keep another pointer around with the same element size as that in the format you picked when calling the LockBits function. For example, in the code above, a 32 bits per pixel format was selected. Therefore, it would be a good idea to create a pointer of the type int, which is 32 bits for most compilers.

```cpp
unsigned int *pRawBitmapOrig = (unsigned int*)bitmapData.Scan0;   // for easy access and indexing

unsigned int curColor = pRawBitmapCopy[curY * bitmapData.Stride / 4 + curX];
int b = curColor & 0xff;
int g = (curColor & 0xff00) >> 8;
int r = (curColor & 0xff0000) >> 16;
int a = (curColor & 0xff000000) >> 24;
```

As you can see in the above code, casting the Scan0 array to another pointer with 32 bits per data element makes accessing the pixel data very straightforward.

## Unlocking the bits

As soon as you’re finished manipulating the image data, you’ll want to call UnlockBits. This function essentially commits the data in the Scan0 array back into the bitmap, and also does memory management if any memory had to be allocated in the first place with LockBits.

```cpp
pBitmap->UnlockBits(&bitmapData);
```

## Conclusion

Using LockBits and Unlock bits can make simple functions such as image convolution perform 20 times as fast when compared to using GetPixel and SetPixel. Just remember that if you’re editing even a moderately small number of pixels, it might be a good idea to take route described in this tutorial instead of having to make thousands or even millions of functions calls.

## Reference

[Using LockBits in GDI+](http://supercomputingblog.com/graphics/using-lockbits-in-gdi/)