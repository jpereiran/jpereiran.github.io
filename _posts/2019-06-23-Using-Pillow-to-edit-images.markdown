---
title:  "Using Pillow to edit images"
date:   2019-06-23 20:15:16
categories: articles
abstract: Have you ever run into a problem or task where you needed to quickly edit a lot of images? Well, with the help of Python and the library Pillow [...]
---

Have you ever run into a problem or task where you needed to quickly edit a lot of images? Well, since where I work sometimes we need to do some editting on images, I found that with the help of Python and the library Pillow you can actually manipulate images in a easy and fast way.

### Introduction

Python Imaging Library (PIL) is a free library that allows the manipulation of images directly from Python. It supports a variety of formats, including the most commonly used such as GIF, JPEG, PNG, BMP and many others.

However, since the library supports only up to version 2.7 of Python, Alex Clark in collaboration with other programmers has developed Pillow, a friendly fork of PIL which aims to maintain a stable library that adapts to new technologies and have Python 3.x support.

### Getting started

First, be sure that you have Pillow installed in your computer. If not, you can easilly install it using pip:

```python
pip install Pillow
``` 

### Opening an Image

In order to work with an image, we need to open it. To do this, we use the open function and then the save function:

``` python
from PIL import Image

# Open an image
im = Image.open("test_image.png")

# Show a representation of the image in BMP/PPM format
im.show()

# Save the image
im.save("test_image.png")
```
Once we opened our image, we are able to retrieve its attributes and to work with it.

<img src="{{ site.baseurl }}/images/posts/pillow/2019_06_23_1.png" title="Test image opened">

### Basic functions (Size, Rotate, Resize, Transpose, Thumbnail)

Then, we can get the size of our image and work with it to change its size, do different rotations and mirrorings on the image or even make a thumbnail of it.

```python
from PIL import Image

# Open an image
im = Image.open("test_image.png")

# Get the size of the image
width, height = im.size
print(width, height)

#Rotate the image x degrees without changing its size
x = 90
im = im.rotate(x,expand=False) 
width, height = im.size
print(width, height)

#Rotate the image x degrees changing its size
x = 90
im = im.rotate(x,expand=True)
width, height = im.size
print(width, height)

#Resize the image to a new size in integers
img = img.resize((125, 240)) 

#Transpose/Mirror the image
img = img.transpose(Image.FLIP_LEFT_RIGHT)
img = img.transpose(Image.FLIP_TOP_BOTTOM)

#Create a thumbnail of the image (keeping the size ratio)
img.thumbnail((50, 50)) 
img.show()
```
With these functions we are able to get the original size of our image and rotate, mirror and resize it.

### Effects and Filters

Also you can add several predifened effects and filters to your images to change its appearance.

```python
from PIL import Image, ImageFilter, ImageChops, ImageEnhance, ImageOps

# Open an image
im = Image.open("test_image.png")

# Invert colors
invert_image = ImageChops.invert(im)
invert_image.show()

# Grayscale
gray_image = ImageOps.grayscale(im)
gray_image.show()

# Mirroring
mirror_image = ImageOps.mirror(im)
mirror_image.show()

# Enhance the image (brightness, contrast, sharpness) a +/- value
bri_image = ImageEnhance.Brightness(im).enhance(5)
bri_image.show()

con_image = ImageEnhance.Contrast(im).enhance(5)
con_image.show()

shp_image = ImageEnhance.Sharpness(im).enhance(-5)
shp_image.show()

# Apply some filters (blur, contour, sharpen, detail)
blur_image = im.filter(ImageFilter.BLUR)
blur_image.show()

cont_image = im.filter(ImageFilter.CONTOUR)
cont_image.show()

sharp_image = im.filter(ImageFilter.SHARPEN)
sharp_image.show()

det_image = im.filter(ImageFilter.DETAIL)
det_image.show()
```
And here it is how the results of each example look like:

<img src="{{ site.baseurl }}/images/posts/pillow/2019_06_23_2.jpg" title="Test image opened">

Oredered from left to right and top to bottom, the first one is the inverted and the last is the one with the detail filter.

### Cropping the transparent parts of a PNG

One of the most helpful things that I found in this library it is the ability to crop the transparent parts of PNG images dinamically. With this example, you can crop all the sides of an image except its upper side.

```python
from PIL import Image, ImageOps

# Open an image 
im = Image.open("test_image_2.png")
im.load()

# Get its attributes
imageSize = im.size
imageBox = im.getbbox()
imageComponents = im.split()

# Check the components of the image to see if it is RGB
if len(imageComponents) == 4:
	# Make a RGB version
	rgbImage = Image.new("RGB", imageSize, (0,0,0))
	rgbImage.paste(im, mask=imageComponents[3])
	croppedBox = rgbImage.getbbox()
# In case it is already a RGB image
else: 
	croppedBox = im.getbbox()

# Get the dimensions I wanna change (4-tuple with the left, upper, right, and lower pixel)
croppedBox = croppedBox[:1] + (0,) + croppedBox[2:]

# Crop and save the new image
cropped=im.crop(croppedBox)
cropped.save("cropped_image.png") 
```

The full code and images used in these examples can be found in the [blog's github](https://github.com/jpereiran/jpereiran-blog/tree/master/code/pillow).

### Conclusion
If you ever need to work with different images in Python and manipulate its attributes, among the different options that Python gives you, in my opinion Pillow is the answer due to its simplicity. However, there are other options like OpenCV or even Numpy and Scipy that can also achieve some of the functions that Pillow gives you but these libraries are more focused on other functionalities and other kind of tasks. 

Hopefully these examples can show you how to use this library to speed up your work and as an inspiration to achieve different results without doing all the manual work that is commonly used when working with images.

The full documentation and a more complete tutorial of the library can be found in [https://pillow.readthedocs.io/en/stable/index.html](https://pillow.readthedocs.io/en/stable/index.html)
