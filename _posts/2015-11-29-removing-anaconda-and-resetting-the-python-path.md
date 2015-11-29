---
layout: post
title: Removing Anaconda and resetting the Python path
---

Installing Anaconda on a linux machine comes with a price, you loose the default python path and use 
Anaconda's python path instead. While this does not in itself create any problems, it does create a problem when you have 
to remove Anaconda because now you don't have a python path set!

Removing Anaconda is easy. Just locate the `anaconda3` directory and remove it by typing `rm -rf anaconda3/`

Now comes the tricky part. Since we removed the directory, typing `python` on the terminal will not work because the
path the does not exist now. So in order to use the default python installation, type `hash -d python`

Congratulations! You have successfully removed Anaconda and reset the python path as well :)