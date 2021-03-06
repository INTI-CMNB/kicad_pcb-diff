# kicad_pcb-diff

This program generates a PDF file showing the changes between two KiCad PCB
files.

PCBs are plotted into PDF files, one for each layer. Then ImageMagick is used
to compare the layers and JPG files are generated. The JPGs are finally
assembled into one PDF file and the default PDF reader is invoked.
All the intermediate files are generated in the system temporal directory and
removed as soon as the job is finished. You can cache the layers PDFs
specifying a cache directory and keep the resulting diff images specifying an
output directory.

The default resolution for the images is 150 DPI. It can be increased for
better images, at the cost of (exponetially) longer execution times. You can
provide a smaller resolution for faster processing. For high resolution you
could need to configure the ImageMagick limits. Consult the 'identify -list
resource' command.

# Installation

## Dependencies

In order to run the scripts you need:
* Python 3.5 or newer
* KiCad 5.1 or newer
* Python3 wxWidgets (i.e. python3-wxgtk4.0)
* ImageMagick tools (i.e. imagemagick Debian package)
* pdftoppm tool (i.e. poppler-utils Debian package)
* xdg-open tool (i.e. xdg-utils Debian package)

In a Debian/Ubuntu system you'll get them running:

```$ sudo apt-get install python3 kicad python3-wxgtk4.0 imagemagick poppler-utils xdg-utils```

Note: if you are using Debian, or some derived distro like Ubuntu, you can find a Debian package in the releases section.

## Standalone use

1. As root run:
```
# make install
```

The scripts will be copied to */usr/local/bin*. If you want to install the scripts in */usr/bin* run

```
# make prefix=/usr install
```

Note: if you are using Debian, or some derived distro like Ubuntu, you can find a Debian package in the releases section.

## Git plug-in

1. Install the scripts
2. To initialize a repo just run the *kicad_pcb-diff-init.py* script from the root of the repo.
This will configure the repo to read extra configuration from the *.gitconfig* file.
It will also associate the *kicad_pcb* file extension with the *kicad_pcb-git-diff.py* script.
3. The initialization script will create a list of layers to be excluded in the *.kicad_pcb-git-diff* file.
Review this file and adjust it to your needs. Lines starting with *#* will be ignored.

Once configured the tool will be used every time you do a diff using *git*.

# Usage

The *kicad_pcb-git-diff.py* is a plug-in for *git* so you just need to configure *git* and then it becomes transparent. If you need to create a diff between two files outside *git* you can use the *kicad_pcb-diff.py* script.

You have to provide the name of the two PCBs to be compared. The additional command line options are:

## -h
Shows a very brief list of the available options.

## --help
Shows a more detailed list of the available options.

## -v/--verbose
Increases the level of verbosity. The default is a quite, specifying one level (*-v*) you'll get information messages about what's going on. If you increase the level to two (*-vv*) you'll get very detailed information, most probably useful only to debug problems.

## --version
Print the script version, copyright and license.

## --cache_dir
The PCB files are plotted to PDF files. One PDF file for layer. To avoid plotting them over and over you can specify a cache directory to store the PDFs.

## --output_dir
Two seconds after invoking the PDF viewer the output files are removed. If you want to keep them for later review, or two seconds isn't enough for your system, you can specify a directory to store the generated files.

## --resolution
The PDF files are converted to bitmaps to be compared. The default resolution for these bitmaps is 150 DPIs. This is a compromise between speed and legibility. For faster compares you can use a smaller resolution. For detailed comparison you can use a higher resolution. Be careful because the time is increased exponentially. You can also run out of resources. In particular ImageMagick defines some limits to the disk used for operations. These limits can be very low for default installations. You can consult the limits using the following command:

```identify -list resource```
 
 Consult ImageMagick documentation in order to increase them.
 
## --old_pcb_hash
The plotted PDF files for each layer are stored in the cache directory using a SHA1 of the PCB file as name for the directory. You can specify another hash here to identify the old PCB file. 
The *git* plug-in uses the hash provided by *git* instead of the SHA1 for the file.

## --new_pcb_hash
This is the equivalent of the *--old_pcb_hash* option used for the new PCB file.

## --exclude
Specifies the name of a file containing a list of layers to be excluded. Each line of the file is interpreted as a layer name. An example for this file could be:

```
B.Adhes
F.Adhes
Cmts.User
Eco1.User
Eco2.User
Edge.Cuts
Margin
```

Note that when using the *git* plug-in the script looks for a file named *.kicad_pcb-git-diff* at the root of the repo.

Using this you can reduce the time wasted computing diffs for empty or useless layers.

## --no_reader
Use it to avoid invoking the default PDF viewer. Note that you should also provide an output directory using *--output_dir*.

The default PDF reader is invoked using *xdg-open*

# Credits and notes

* This script is strongly based on Jesse Vincent [work](https://github.com/obra/kicad-tools).
* I borrowed the command to compare two images from Brecht Machiels. In particular from his [diffpdf.sh](https://gist.github.com/brechtm/891de9f72516c1b2cbc1) tool.
* I'm not a Python programmer, stackoverflow helps me ... 
