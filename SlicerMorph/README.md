# SlicerMorph Modules: Statistical Shape Analysis with the GPA module

## Preliminary Steps
* Review your *Temp* and *Cache* folder settings under Preferences. Make sure these two point to folders that you can easily access (e.g., c:/SlicerTemp/ for Temp and C:/SlicerTemp/RemoteIO for cache folders)

### Mouse Dataset 

1. Search for `Sample Data` module and scroll down to find the SlicerMorph section. Click on **Mouse Skull Landmarks**. This will download a file called `Mouse_Skull_LMs.zip` into your Cache folder, and automatically uncompress it into a subfolder at the same place as the zip file. Please check the folder contents and note that there are 126 fcsv files. You can drag'n'drop one of the fcsv files into Slicer and see that it contains 55 landmarks (you may need to hit the center field of view button in 3D rendering window). Once you verify your contents hit `CTRL + W` to empty your Slicer scene.

<img src="./images/Picture1.png">

2. Search for `GPA` module. You layout will switch to having two 3D rendering window, a single slice view, a chart view and a table view. 

<img src="./images/Picture2.png">

3. Click on the `..` button next to the `Landmark Folder` and navigate to your Mouse_Skulls_LMs subfolder from the previous step. 

<img src="./images/Picture3.png">

4. Click on the `..` button next to the `Output Folder` to specify where GPA module will save its out put (choosing the same folder as the previous one is fine). GPA module will create a time-stamped output folder each time you execute an analysis, so the results will not get overwritten. 

5. Do not enter any landmarks to skip, and leave `Skip Scaling during GPA` unchecked, and hit `Execute GPA + PCA`.

<img src="./images/Picture4.png">

6. Switch your file explorer in your computer, navigate to the `Mouse_Skulls_LMs` folder and see a new file with a date stamp was created. Go into the folder and note five CSV files that contain eigenvalues, eigenvectors, mean shape, PC scores, and combined output that contains new procrustes aligned coordinates, centroid sizes and Procrustes distances from the output. If you want to do more specific analysis, these will be files you will import into R/geomorph or other shape analysis packages. 

<img src="./images/Picture5.png">

7. At this point your Plot and Table window should populate with a histogram-like bar plot of Procrustes Distances, and a table that indicates specimen names sorted by Procrustes Distance. This is a good time to see some unique features of these two windows (such as interaction, being able to zoom in into a plot etc).

<img src="./images/Picture6.png">

8. Scroll down to the bottom of the Table view to see that there are four specimen with very very high PD values. The shape of the PD plot also indicates there is something very different about these four specimens. 

9. Likewise PCA Scatter plot options displays a suspiciously high variance (~98%) for PC1. Go ahead and choose PC1 for x-axis, PC2 for y-axis and hit `Scatter Plot` button. 

10. Note that plot window now changed to 'PCA Scatter Plot` from Procrustes Distance plot. You can enlarge this plot by selecting the "Plot only" layout from the layouts menu. If you expand the Plot window toolbar, you will see a field called Plot Chart that will let you go back and forth between plots created. Go and try switching back to Procrustes Distance plot and back to PCA scatter plot. 

<img src="./images/Picture7.png">

11. Hover over the four data points on the right-hand side of the PC1 axis and note that they are the same four specimens with the highest PD (you will need to zoom in to see them individually). So what is going on?

12. To visualize the PC1 vectors, go to the `Lollipop Plot Options` section and set the Vector One to PC1, then hit the `Lollipop Vector Plot` button. This set will automatically enable landmarks for the estimated mean shape and place eigenvector associated with PC1. By convention, this indicates how mean shape will change along the positive values of the selected PC. You should see that no other vector apart from LM25 is visible, thus PC1 (and almost all shape variation in this dataset) is influenced by LM25. This is a sign off trouble with this dataset. (HINT: If you toggle the `mean shape visibility` on and off, you will be able to see the other vectors.). Interact with the 3D window and note that you can zoom in/out as usual, and rotate. At this point, you can use either Viewport #1 or #2. 

<img src="./images/Picture8.png">

13. To further convince ourselves of the issue, you can get a sense of variance associated with each landmark by plotting them as either spheres (averaged across three dimensions), or ellipse (all axes are independently calculated). You can see that LM25 has variance orders of magnitude larger than the others, and it is pancake like appearance suggest that it is confined to two dimensions. 

<img src="./images/Picture9.png">


Plotting the point cloud from the landmark variance shows that four points from the landmark 25 cluster were placed at the origin. Clearly LM25 in these four specimens have an issue, and is better left out of the analysis. 

<img src="./images/Picture10.png">

14. To repeat the analysis without LM25, first we need to clear our scene. Hit the `Reset Scene` button at the bottom of the GPA module and note that everything created by this module is removed. 

15. Now go to `Setup Analysis` section of the module and repeat the step #1 above. It should remember the last folder you open. This time you will enter 25 to `exclude landmarks` field to conduct the analysis without LM25. 

16. Review the outputs and confirm that the Procrustes distance bar plot and PC1xPC2 scatter plot have been updated and no longer show the outliers observed in the previous run. 

<img src="./images/Picture11.png">

Try things like projecting the results in 2D Slice viewer, which you can switch the plane like in normal slice view you have been viewing. Unfortunately, it doesn't automatically center the field of view like scans we have been loading, thus you need to use your `left` and `middle` mouse clicks to zoom in/out and pan the field of view manually. Trying having more than one PC plotted as lollipop plots etc...


### Visualizing PCA results using a 3D model. 
So far we have been looking at output of our PC analysis as series of vectors. With 55 LMs, it might be difficult to interpret what's going on, or judge the relative importance of these vectors. The alternative is to find a `reference model` and first warp to mean shape using Thin Plate Splines. That's because our PCA space is centered on the mean shape of our population and all shape changes are defined with respect to this mean shape. If you provide a 3D model (supported formats are vtk/ply/stl/obj) and its associated set of landmarks, SlicerMorph GPA module with automatically do this for you. Alternatively, if you have no model, you can set up the 3D visualization with the meanshape and visualize the PC deformations acting on the landmark points only. 

1. Go back to the `Sample Data` module, scroll down to SlicerMorph section and click on `Mouse Skull Reference Model`. This action will create two files in your cache folder called `4074_S_lm1.fcsv` and `4074_skull.vtk`. 

<img src="./images/Picture13.png">

3. Go back to `GPA` module, scroll down to the section `Setup 3D Visualization` and browse to the location of the landmark (FCSV) and model (VTK) files in your cache. Select `Apply`. You will now see a gray  and blue mouse skull in your viewports. Gray represents the mean shape, and blue one represents the mean shape warped along specified PC axes and scores. 

4. Go to `PCA Visualization Parameters` and set the first slider to PC1, and start deforming the mean shape along it. Try adding PC2 (or others)

<img src="./images/Picture14.png">

5. To capture a PC deformation as an animation, go to the 'Create animation of PC Warping' menu and hit 'Start Recording'. Move the PC Sliders to create the deformations that will be in the exported animation. After applying the deformations, hit 'Stop Recording'.

<img src="./images/Picture15.png">

6. When the recording is stopped, the Sequences module will automatically open and show the image sequence browser that has been created, titled 'GPASequenceBrowser'. This contains the sequence of images generated during the recording. Select the play button with the green arrow icon to review the images as an animation.

<img src="./images/Picture16.png">

7. To export the animation created, use the search bar to switch to the Screen Capture module, which supports video exports. Set the 'Master view' to "'View2' to export only the animation from the PC deformation 3D viewer. Select the 'Animation mode' to 'sequence' to set the sequence browser created in the previous step as the source of the animation and confirm that the sequence titled 'GPASequenceBrowser' is selected. To export as an MP4 format video, go tp the 'Output' menu  and set the 'Output type' to 'video. Confirm the export properties and hit 'Capture' to start the export.

<img src="./images/Picture17.png">

8. To view the animation created, you can browse to the output file location specified in the Screen Capture module, or click the button with the green arrow icon next to the 'Capture' button. This will open a popup with the exported animation.

<img src="./images/Picture18.png">

### [Videos of GPA tool functionality on the SlicerMorph youtube channel:](http://bit.ly/SM_youtube)

# Placing Semi-landmark points
The criteria for landmarks to be homologous and reproducible results in very sparse data from images. For many data sets, this may not be sufficient to capture shape changes, especially along curves or smooth surfaces in 3D images. One goal of our project was to provide some simple ways for users to generate more points on a 3D surface. Each method comes with drawbacks and advantages based on the tradeoffs made between computation time, equidistant point spacing, sampling extent, and homology of the points that should be evaluated based on the application.

## Resampling curves
The simplest support for sampling points on a mesh is a newly implemented method to resample a curve at equidistant points snapped to an image surface. This has now been integrated into the core of Slicer and is available in the 'Resample' menu of the markups module. This function is covered in more detail in the measurements tutorial material.

<img src="./images/Picture18_5.png">

## Placing patches of semi-landmarks between landmark points

This method uses three landmarks already placed on the specimen to define a triangular region where semi-landmarks will be placed. A template triangular grid with a user-specified number of semi-landmark points is registered to the vertices of the bounding triangle using a thin-plate-spline deformation. The vertices of the triangular sampling grid are then projected to the surface of the specimen along the estimated normal vectors. After the projection step, the landmark points can be merged together and a curve of semi-landmarks placed along each unique triangle edge in the grid. The semi-landmarks can be generated for an individual sample or placed on other samples using a thin-plate spline (TPS) transform.

1. Download the Gorilla Skull Reference Model from the Sample Data into your cache. Load the mesh (VTK) and landmark file (FCSV) into 3D Slicer.

<img src="./images/Picture19.png">

2. Switch to the SemiLandmark module. It is found under the SlicerMorph Labs menu, or can be opened using the search bar. You will see the landmarks loaded are displayed in the module's landmark node viewer. Use the selection boxes to select the loaded mesh as the model and the loaded landmarks as the active landmark set.

<img src="./images/Picture20.png">

3. Now, you can enter the landmark numbers of three points to place the first patch. In the 'Semi-landmark grid points' field enter: 19, 29, 35. You will see that a triangular patch is placed at this point, and a new landmark node shows up in the landmark node viewer, named by the bounding landmark points of the triangle. 

<img src="./images/Picture21.png">

4. You can edit this node using the right-click menu, or by switching to the 'Markups' module. From here, the glyph size and color can be adjusted for visibility and the landmark points can be edited.

<img src="./images/Picture22.png"> 

5. If the semi-landmark patch was placed successfully, more patches can be placed. If the placement failed, this patch can be deleted or replaced. If the underlying geometry is not approximated well by a triangular patch, different coordinates should be used. If the point placement failed due to a secondary surface or noise in the mesh surface, the advanced properties of the SemiLandmarking module can be used to optimize placement. In the case of points placed on the incorrect mesh surface (eg interior surface, neighboring structure), the maximum projection distance should be adjusted. In the case of noise on the mesh surface, the landmark points may not provide a reasonable estimate of the patch surface normal. Increasing the smoothing of the surface normals used for projection can mitigate this.

<img src="./images/Picture23.png">

6. Apply patches of semi-landmarks until the region of interest is covered. Here, we have placed 18 triangular patches. 

<img src="./images/Picture24.png">

7. In the landmark node viewer, select the nodes to be merged into the final set of semi-landmarks. Here, every semi-landmark node has been selected (it is not necessary to select the original landmark node). Click the 'Merge highlighted nodes' button'. Depending on the number of semi-landmarks, this step may take a few minutes.

<img src="./images/Picture25.png">

8. A new, merged semi-landmark node is now displayed in the landmark node viewer containing the patch landmarks and curves placed along each unique triangle edge.

<img src="./images/Picture26.png">

9. Switch to the data browser and find the table 'Semi-landmark Grid' displayed in the 'Subject hierarchy' menu. Click the eye next to the table to view the list of triangle vertices for each patch in the merged landmark set. This can be used to apply semi-landmarks to new images using the same patch-placement.

<img src="./images/Picture27.png">

10. Click the 'Save' icon in the top toolbar to save the semi-landmark output. The semi-landmark node generated can be saved as a FCSV format file and the table of grid vertices can be saved as a CSV file.

<img src="./images/Picture28.png"> 

11. The table of grid vertices can be used to apply the same semi-landmark patches to new samples using the 'TransferSemiLandmarks' module. Or, the 'TransferSemiLandmarksWarp' module can be used to transfer the semi-landmarks from the template across subjects using a TPS transform. Open the 'TransferSemiLandmarksWarp' from the SlicerMorph Labs menu. The 'Base mesh' and 'Base landmarks'  can be set to the template and landmarks loaded in the scene. The 'Base semi-landmarks' should be set to the merged set of semi-landmarks created in this exercise. By specifying a directory of sample meshes and landmarks, the module will write a semi-landmark file for each sample to the output directory.

<img src="./images/Picture29.png"> 
