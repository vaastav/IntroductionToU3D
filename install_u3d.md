# How to embed U3D objects in PDF

This article focuses on how to embed U3D objects in a PDF file

## Requirements

+ MikTeX: For generating PDF files that contain U3D objects [Windows]
+ MacTeX: For generating PDF files that contain U3D objects [OSX]
+ Adobe Acrobat: v7.0 or higher. For viewing PDF files with U3D objects in it.

### MikTeX

This is for making LaTeX files which will be used to embed U3D files in a PDF on windows OS

Download Link: [MikTex](http://miktex.org/download)

#### How to Install

Double click the .exe file and follow the instructions on the screen.

#### The media9 package

Embedding U3D objects requires either the movie15 or the media9 packages.

**NOTE: The movie15 package is obsolete and is superseded by media9. So this tutorial uses media9 package.** 



The media9 package requires the media9.sty file which can be downloaded [here](https://www.ctan.org/tex-archive/macros/latex/contrib/media9?lang=en).

**Install Instructions**

1. Go to the folder in C:/ where MikTeX was installed. ( It will probably be "C:\MikTeX 2.9" or "C:\Program Filex (x86)\MikTeX2.9" )

2. In the installation folder, go to tex/latex/ and create a new folder called media9

3. Copy the media9.sty file which you had previously downloaded and paste it in the media9 folder

4. Go back to the installation folder and travel miktex/bin

5. Double click on mo.exe

6. Click on Refresh FNDB button and once it completes, click OK

7. After you have completed all the above steps, you will have media9 package installed and ready to use for embedding your favorite U3D objects

### MacTeX

This is for making LaTeX files which will be used to embed U3D files in a PDF on OSX

### Adobe Acrobat

This is for reading the 3D PDF files

Download Link: [Adobe Acrobat](https://get.adobe.com/reader/)

## How to embed U3D objects

For embedding U3D objects you would need to write a .tex file.

This is what the tex file looks like

```
\documentclass[a4paper]{article}
\usepackage{media9}
\usepackage[english]{babel}
\begin{document}
\includemedia[
	label = Object,
	activate = pageopen,
	width = 1\linewidth,
	height = 1\linewidth,
	3Dmenu,
	3Dtoolbar,
	3Dviews = Views.vws,
]{}{Object.U3D}
\end{document}
```
