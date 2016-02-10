---
layout: post
title: Installing OpenCV on Ubuntu
comments: true
---

I was sitting on my laptop browsing on facebook when i saw a post on Microsoft Hololens. That was my first encounter
with augmented reality and i was totally blown away by it. I saw several videos on youtube on [Hololens](https://www.youtube.com/watch?v=qym11JnFQBM)
as well as some videos of [Magic Leap](https://www.youtube.com/watch?v=kw0-JRa9n94) and i saw myself hooked up to it.
I decided that day that i wanted my career to be in Augmented Reality but the problem was i didn't know anything about it.

A little digging revealed that i need to master Computer Vision first. So i signed up for a course on
[Udacity](https://www.udacity.com/course/introduction-to-computer-vision--ud810). Although the instructors in this
course are great, they are teaching in the content in MATLAB. The problem with MATLAB is that it is way too easy to get
started with it but it doesn't give an in depth understanding of the concepts. Also, its not that versatile. So i 
took to learning [OpenCV](http://opencv.org/) and the first challenge was to install OpenCV on my system.

My system has Ubuntu 14.04 LTS so i needed to follow the steps for my specific OS. I googled a bit and found this 
[post](http://milq.github.io/install-opencv-ubuntu-debian/). While this post has all the steps you need to install 
OpenCV on your system, there are a few extra steps that i followed.

When i followed the steps and installed OpenCV, the example programs were running fine but i wasn't able to compile 
OpenCV programs. An error kept coming up

```  
CMake Warning at /home/zishan/opencv-3.1.0/cmake/OpenCVConfig.cmake:166 (message):
  Found OpenCV Windows Pack but it has no binaries compatible with your
  configuration.

  You should manually point CMake variable OpenCV_DIR to your build of OpenCV
  library.
Call Stack (most recent call first):
  CMakeLists.txt:5 (find_package)


CMake Error at CMakeLists.txt:5 (find_package):
  Found package configuration file:

    /home/zishan/opencv-3.1.0/cmake/OpenCVConfig.cmake

  but it set OpenCV_FOUND to FALSE so package "OpenCV" is considered to be
  NOT FOUND.


-- Configuring incomplete, errors occurred!

```

It was time to start googling again for a solution. I found the problem described on a post in 
[Stack Overflow](http://stackoverflow.com/questions/8711109/could-not-find-module-findopencv-cmake-error-in-configuration-process)
and scrolling down a bit, one answer said that we need to fill the OpenCV_DIR variable with the path of opencv 
(containing the OpenCVConfig.cmake file).
 
`export OpenCV_DIR=<path_of_opencv>`
 
Since my OpenCV files were in my home folder, i filled the OpenCV_DIR variable as follows
 
`export OpenCV_DIR='/home/zishan/opencv-3.1.0/build'`
 
Now when i build the project again with `cmake .. && make`, the compilation was successful and i got an executable.
 
Great, so now we are able to compile to OpenCV programs as well. Hold on, the post isn't over yet. I didn't quite like
the long process of compiling OpenCV programs. There is too much redundancy like creating a `CMakeLists.txt` file for
every single program that i create with the following content:
 
```
cmake_minimum_required(VERSION 2.8)
project( ProgramName )
find_package( OpenCV REQUIRED )
add_executable( ProgramName ProgramName.cpp )
target_link_libraries( ProgramName ${OpenCV_LIBS} )
```
 
Whenever there is some redundancy, there is a program in need. So i created a small script in Python to do this work 
for me. I created a file make.py in my development/opencv folder where i plan to have all the OpenCV programs. The 
contents of make.py are as follows:

```
import sys
import os

def make_it(name):
	text = "cmake_minimum_required(VERSION 2.8.12)\n"
	text += "project(" + name + ")\n"
	text += "find_package(OpenCV REQUIRED)\n"
	text += "include_directories(${OpenCV_INCLUDE_DIRS})\n"
	text += "add_executable(" + name + " " + name + ".cpp)\n"
	text += "target_link_libraries(" + name + " ${OpenCV_LIBS})\n"
	with open("CMakeLists.txt", 'w') as f:
		f.write(text)

	# Check if the folder already exists. If it does, then remove the folder
	if os.path.isdir(name):
		ans = input("'" + name+"' already exsists. Would you like to remove it? (y/n): ")
		if ans == 'y':
			os.system("rm -r " + name)
			os.system("mkdir " + name + " && cd " + name + " && cmake .. && make")
		else:
			print("Cannot continue. Please remove the directory named '" + name + "' first")
	else:
		os.system("mkdir " + name + " && cd " + name + " && cmake .. && make")


if __name__ == '__main__':
	name = sys.argv[1]
	make_it(name)
```

What this script does is that it creates an appropriate `CMakeLists.txt` file and also compiles the OpenCV program
for us. To run the script, type `python make.py name_of_program`. The script will create a folder named name_of_program
and compile the program as well. To run the program type `name_of_program/name_of_program` or type `cd name_of_program
&& ./name_of_program`.

Note that if there are errors in the program and you need to recompile the program, then you don't need to run the
script again and only need to compile the program. First go the folder by typing `cd name_of_program` and then type
`cmake .. && make`

I have just begun my journey with OpenCV and this is the first among many more posts coming up in the future. Feel free
to comment below for any suggestions or doubts.

[Update]  
Check out my new post: [Getting started with OpenCV Part 1](http://zishanahmad.github.io/blog/2016/02/07/getting-started-with-opencv-1/)
