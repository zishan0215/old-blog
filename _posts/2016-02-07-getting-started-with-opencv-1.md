---
layout: post
title: Getting started with OpenCV Part 1
comments: true
---

In my [previous post](http://zishanahmad.github.io/blog/2016/02/05/installing-opencv-on-ubuntu/) i talked about how to 
install OpenCV on a Ubuntu system. If you haven't already installed OpenCV, i recommend you do that before moving
ahead.

[OpenCV](http://opencv.org) is an open-source BSD-licensed library that includes several hundreds of computer vision 
algorithms. The BSD licence allows one to create both academic and commercial applications that allows you to freely 
use, distribute, and adapt it.

OpenCVhas a modular structure, which means that the package includes several shared or static libraries. 
The following modules are available:

  * [Core Functionality](http://docs.opencv.org/3.1.0/d0/de1/group__core.html)
  * [Image Processing](http://docs.opencv.org/3.1.0/d7/dbd/group__imgproc.html)
  * video
  * calib3d
  * features2d
  * objdetect
  * highgui
  * videoio
  * gpu

Having understood what OpenCV is and what is has to offer, lets dig in and learn some basic concepts. The first thing
to keep in mind is that all classes and functions defined in the C++ API of OpenCV are defined within the namespace
`cv` so there are two ways to access them. The first is to precede the main function definition by:

```
using namespace cv;
```

The second method is to prefix all OpenCV class and function names by the namespace specification that is `cv::`.
In this tutorial series, i will use the latter method. 

The basic container to hold an image is `cv::Mat`.  

```
cv::Mat image;
```

The above definition creates an object of the cv::Mat class. By default, the size of the object is 0 by 0. To read an 
image in the image object, we use the `cv::imread` function.

```
image = cv::imread("img.jpg");
```

To check if the image has been correctly read or not, type

```
if(!image.data) {
    // image not created
    return -1;
}
```

Mat is basically a class with two data parts: the matrix header (containing information such as the size of the matrix, 
the method used for storing, at which address is the matrix stored, and so on) and a pointer to the matrix containing 
the pixel values (taking any dimensionality depending on the method chosen for storing) . The matrix header size is 
constant, however the size of the matrix itself may vary from image to image and usually is larger by orders of 
magnitude.

Before we go into the further details of the `cv::Mat` class, lets quickly make our first program. Create a file named
[imread.cpp](https://github.com/zishanAhmad/opencv/blob/master/imread.cpp) and add the  following contents:

```  
#include <iostream>
#include <opencv2/opencv.hpp>


int main(int argc, char** argv) {
    if (argc != 2) {
        std::cout<<"usage: ./imread <Image_Path>"<<std::endl;
        return -1;
    }

    cv::Mat image;
    image = cv::imread(argv[1]);

    if (!image.data) {
        std::cout<<"No image data"<<std::endl;
        return -1;
    }

    cv::namedWindow("Display Image", cv::WINDOW_AUTOSIZE);
    cv::imshow("Display Image", image);

    cv::waitKey(0);

    return 0;
}
```

I will use the same steps to compile this program as i described in my previous 
[post](http://zishanahmad.github.io/blog/2016/02/05/installing-opencv-on-ubuntu/). To compile the program, 
open the terminal and type

```
python make.py imread
```

The script will compile the program and a folder named imread will be created. We will need an image to run the program.
I have a folder named images and in that is an image `room.jpg`. To run the program, type the following command in the
terminal

```
imread/imread images/room.png
```

You will get an output like

![Running imread](/blog/assets/1.png)

Great! We just compiled and run a basic OpenCV program! Now lets go through the contents of the program. As discussed
above, to hold the image i defined a `cv::Mat` object named `image`. This object will hold the image and in order to do
that, the `imread()` function is used. `argv[1]` means that i pass the path of the image as a command line argument. To
show the image, a window needs to be created. The `namedWindow()` method is used for this purpose. Finally, the 
`imshow()` function is used to pass the image to the window.

Since there are a lot of functions in OpenCV, passing image objects to functions is a common task. Since image files can
become large and the image processing algorithms are computationally heavily, OpenCV uses a reference counting system 
instead of copying the image. The idea is that each Mat object has its own header, however the matrix may be shared 
between two instance of them by having their matrix pointers point to the same address. Moreover, the copy operators 
will only copy the headers and the pointer to the large matrix, not the data itself.

```
Mat A, C;                               // creates just the 
                                        // header parts
A = imread(argv[1], IMREAD_COLOR);      // here we'll know the 
                                        // method used (allocate matrix)
Mat B(A);                               // Use the copy constructor
C = A;                                  // Assignment operator
```

All the above objects, in the end, point to the same single data matrix. Their headers are different, however, and 
making a modification using any of them will affect all the other ones as well.

If you do not want the reference but instead want to copy the matrix itself too, then the `cv::Mat::clone()` and 
`cv::Mat::copyTo()` functions need to be used.

```
Mat F = A.clone();
Mat G;
A.copyTo(G);
```

When you do a `clone` or a `copyTo`, the images are independent of each other and changing one won't affect the other.

Till now we've seen how to read images. Now let's see how to modify and save images. The 
[program](https://github.com/zishanAhmad/opencv/blob/master/grayscale.cpp) below transforms an image from BGR to 
Grayscale format by using `cv::cvtColor`

```
#include <iostream>
#include <opencv2/opencv.hpp>

int main(int argc, char** argv) {
	if (argc != 2) {
        std::cout<<"usage: ./grayscale <Image_Path>"<<std::endl;
        return -1;
    }

    cv::Mat image;
    image = cv::imread(argv[1]);

    if (!image.data) {
        std::cout<<"No image data"<<std::endl;
        return -1;
    }

	cv::Mat gray_image;
	
	cv::cvtColor(image, gray_image, cv::COLOR_BGR2GRAY);
	
	cv::imwrite("../images/Gray_Image.jpg", gray_image);
	
	cv::namedWindow("Original image", cv::WINDOW_AUTOSIZE);
	cv::namedWindow("Gray image", cv::WINDOW_AUTOSIZE);
	
	cv::imshow("Original image", image);
	cv::imshow("Gray image", gray_image);
	
	cv::waitKey(0);
	
	return 0;
}
```

I saved this program as `grayscale.cpp` and then compiled it by typing in the terminal the following command

```
python make.py grayscale
```

To run the program, type

```
grayscale/grayscale images/room.jpg
```

The program produces an output as follows

![Grayscale image](/blog/assets/2.png)

In the above program, the image is read using the `cv::imread` function. In order to convert the image to grayscale,
i used the `cv::cvtColor` function

```
cvtColor(image, gray_image, COLOR_BGR2GRAY);
```

The `cv::cvtColor` takes three arguments: a source image, a destination image and an additional parameter that indicates
what kind of transformation will be performed.

Now that i have the grayscale image, the only thing left is to write the image and save it. To do that, the 
`cv::imwrite` function is used.

```
imwrite("../images/Gray_Image.jpg", gray_image);
```

Finally, the images are displayed first by creating the windows and then by passing the images to those windows.

```
cv::namedWindow("Original image", cv::WINDOW_AUTOSIZE);
cv::namedWindow("Gray image", cv::WINDOW_AUTOSIZE);

cv::imshow("Original image", image);
cv::imshow("Gray image", gray_image);
```

So in this tutorial, we learnt the basics of OpenCV, what it is, what is has to offer. Then we moved on the reading 
images and then modifying ans saving the new images. In the next tutorial, i will go through some more manipulation 
of images so that we get more comfortable with handling images.