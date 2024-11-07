# Life_Cycle_Tracking
Source Code for the FIEST tracking algorithm
# Life_Cycle_Tracking
Source Code for the FIEST tracking algorithm

This code contains the tracking algorithms used after image interpolation to fully track cells throughout the life cycle of the mode eukaryote S. cerevisiae. 
It requires a folder containing the segmentations obtained from each life cycle stage-specific Cellpose models. 
The toy data set "Pos13_1_B" (also in this repository) contains a representative time series with cells undergoing a full life cycle (sporulation, germination, mating, proliferation). The masks have endings according to the segmentation model; in this toy data example: 

![Table](Table_1.png)

Notice that masks without detections are not saved to save memory. The code will extract the correct mask number by looking into the file name, which has the following standardized naming convention: "img_000000000_Ph3_00", where the nine zeros between the underscores are changed to represent the current time points in the time series starting counting from zero. For instance, image number 717 will be: "img_000000716_Ph3_000". The masks look lke this: 

![image](Full_Life_Cycle_Masks.jpg)

The tracking of all life cycle stages is done in the following steps: 

1. Tracking of sporulating cells using the "_TET_masks.tif" masks; lines 76 - 343. The sporulated cells might produce discontinuous tracks initially (cell masks might be missed at some time points), but the tracking code completes the missing detections by using the existing detections to produce continuous tracks. Notice that in this toy data set, we have a "shock period," a harsh treatment of LiCl/Nystatin. During this period, cells do not move or grow; therefore, those time points are skipped for speed. 
To visualize the tracks, use plt.imshow() in lines 259 and 325 to visualize the initial discontinuous tracks (all_obj) and the final reconstituted tracks (TET_Size). 

 
2. The tracked TETmasks are inserted into the general detection by ProSeg in lines 350 - 476. This is required because the model for detecting sporulated cells (SpoSeg) produces more accurate masks than the model for the general detection of yeast cells (ProSeg). The resulting masks combine ProSeg + SpoSeg and are contained in the Art_MT variable. 
Each step can be visualized using the commented plt.imshow() distributed across the code every time a mask is modified. 


3. Tracking of all cells using masks contained in the Art_MT variable in lines 498 - 948. All cells in the Art_MT are tracked based on the cell mask overlap between two consecutive frames. Each cell is superimposed on the next segmented image mask, and the cell mask with the highest overlap is assigned as the same cell in the next frame. The process is repeated until all cells, including newborns, are tracked. The code includes corrections in case small segmentation artifacts are present and contains relabeling steps to ensure the cell indexes go from 1 to n. The fully tracked masks are stored in the Mask7 variable, and the size of the tracked cells can be visualized by plt.imshow() the Art_all_ob variable. 
Each step can be visualized using the commented plt.imshow() distributed across the code every time a mask is modified. 

4. Tracking of mating events (cells that fused through mating) using the "_MAT16_masks.tif" masks; lines 958 - 1197. The mating cells might produce discontinuous tracks initially (cell masks might be missing at some time points), but the tracking code completes the missing detections by using the existing detections to produce continuous tracks. Notice that in this toy data set, we have a "shock period," a harsh treatment of LiCl/Nystatin. During this period, cells do not move or grow; therefore, those time points are skipped for speed. The final tracked mating masks are stored in the variable Matmasks.

5. Tracking of mating events (cells that fused through mating) using the "_MAT16_masks.tif" masks; lines 958 - 1197. The mating cells might produce discontinuous tracks initially (cell masks might be missing at some time points), but the tracking code completes the missing detections by using the existing detections to produce continuous tracks. Notice that in this toy data set, we have a "shock period," a harsh treatment of LiCl/Nystatin. During this period, cells do not move or grow; therefore, those time points are skipped for speed. The final tracked mating masks are stored in the variable Matmasks.

6. The tracked Matmasks are improved by the general detection model ProSeg in lines 1209 - 1401. This is required because the morphological variation of mating events is better captured by fusing the information from masks in the mating cell masks derived from (MatSeg) with the cell masks from the general detection with ProSeg. The resulting Matmasks are a combination of ProSeg + MatSeg and are contained in the Matmasks variable. 
Each step can be visualized using the commented plt.imshow() distributed across the code every time a mask is modified. For example, use plt.show() in line 1393 to visualize the final reconstituted tracks (all_obj).
