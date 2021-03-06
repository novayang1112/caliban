# Caliban: Data Curation Tools for DeepCell.

Caliban is a segmentation and tracking tool used for human-in-the-loop data curation. It displays lineage data along with raw and annotated images. The output files prepare this information as training data for DeepCell.

## Instructions for Running Caliban Locally on Desktop
```bash
git clone https://github.com/vanvalenlab/caliban.git
cd caliban
cd desktop
python3 caliban.py [input file location]
```

**Accepted file types:**
Caliban can open .trk files or .npz files. .npz files must contain two zipped files corresponding to raw images and annotated images. If the files are not named 'raw' and 'annotated' or 'X' and 'y', the first file in the .npz will be opened as the raw images. Raw and annotated images must both be in 4D arrays in the shape (frames, y, x, channels or features).

## Tools Guide
Files can be edited using keyboard operations.

### Navigation through Frames:

*a or &larr;* - Back one frame  

*d or &rarr;* - Forward one frame


### Edit Operations:

Caliban's default setting allows operations to be carried out quickly and easily on existing segmentations. The actions that can modify cell labels and/or lineage information are:

*click* - click on a cell label to select it. Up to two cells can be selected at one time.

*r* - replace: relabel all instances of a selected cell label with a second selected cell label; replaces lineage data in a trk file

*r* - relabel: sequentially relabel all cells in frame, starting from 1, when no cells are selected (npz only)

*c* - create: relabel selected cell with an unused label

*p* - parent: assign parent/daughter relationship to pair of selected cells in trk file

*p* - predict: predict zstack relationships in npz when no cells are selected

*s* - swap: swap labels and lineage information between two selected cells  

*x* - delete: remove selected cell mask in frame

*w* - watershed: call watershed transform to split one cell label into two


*esc* - cancel operation  
*space bar* - confirm operation  
*s* - confirm operation in a single frame, when applicable

You can also use *esc* or click on the black background to return back to a state where no cells are selected.

**In annotation (pixel editing) mode:**

Keybinds in pixel editing mode are different from those in the label-editing mode.

Annotation mode focuses on using an adjustable brush to modify annotations on a pixel level, rather than using operations that apply to every instance of a label within a frame or set of frames. The brush tool will only make changes to the currently selected value. Ie, a brush set to edit cell 5 will only add or erase "5" to the annotated image. 

*i* - invert greyscale raw image

*k* - apply sobel filter to raw image

*j* - apply adaptive histogram equalization to raw image

*-/=* - increment value that brush is applying

*&darr; &uarr;* - change size of brush tool

*x* - toggle eraser mode

*n* - change brush label to an unusued label, so that a new cell can be created with a unique id

*p* - color picker (click on a label to change the brush value to it)

*r* - turn on "conversion brush" setting, which changes brush behavior so that one label value is overwritten with another label value. No other labels are affected, and conversion brush will not draw on background. After turning on conversion brush, click on cell labels as prompted to set brush values.

*l* - contour prediction for determining inter-cell edges. After turning this on, use two clicks to draw a line that is a guess at where the border between two cells is. Predicted contour points (based on the darkness of the raw image) will be displayed on screen until "c" is selected or the frame is changed. Contour points will not affect the annotation file and are only displayed as a guide to the user.

*t* - threshold to predict annotations based on brightness. After turning this on, click and drag to draw a bounding box around the cell you wish to threshold. Make sure to include some background in the bounding box for accurate threshold predictions. Whatever was thresholded as foreground within the bounding box will be added to the annotation as a new cell with unique label. 


### Viewing Options:

*h* - switch between highlighted mode and non-highlighted mode (outside of the pixel editor)
          (once highlight mode is on, use *-/=* to decrement/increment selected cell label number)

*h* - switch between showing and hiding annotation masks in the pixel editor
    
*z* - switch between annotations and raw images

*v* - switch between showing and hiding cursor

*f* - cycle between different annotations when no cells are selected

*c* - cycle between different channels when no cells are selected

*shift + up/down* - cycle between colormaps for viewing raw images (does not apply to pixel editor)

*e* - toggle annotation mode (when nothing else selected)

*scroll wheel* - change image or annotation contrast


### To Save:

Once done, use the following key to save the changed file. 
The tool will also save the original file in the same folder.
In npz mode, a new npz file will be saved with a version number. An npz can be saved as a trk file (select "t" in response to save prompt). This will bundle together the current channel and feature of the npz along with a generated lineage file, which will contain label and frame information and empty parent/daughter entries for each cell. The new trk file can then be edited in Caliban's trk mode to add relationship information.

*s* - save


## Instructions for Running Caliban in a Docker Container

In addition to having Docker, you will also need to have a VNC viewer to view the application inside the container. 

To install one, you can go to http://realvnc.com to download a free VNC viewer.
[Direct Link to Download Page](https://www.realvnc.com/en/connect/download/viewer/)

### Build a Docker Container

```bash
git clone https://github.com/vanvalenlab/caliban.git
cd caliban
docker build -t caliban .
```
### Run the New Docker Image

```bash
docker run \
-p 5900:5900 \
-v $PWD/caliban/desktop:/usr/src/app/desktop  \
--privileged \
caliban:latest
```
This will launch a new Docker container and run Xvfb, Fluxbox, and a VNC server. To access the container’s display, point a VNC client to 127.0.0.1.

Inside the VNC client, one can access Caliban through the terminal emulator. Start the terminal by right-clicking the desktop and selecting

```bash
Applications > Terminal Emulators > XTerm
```
Next, enter the following into the terminal and Caliban will start:

```bash
cd desktop
python3 caliban.py [input file location]
```

To see an immediate example with a sample .trk file, you can run 

```bash
cd desktop
python3 caliban.py examples/trackfile1.trk
```


