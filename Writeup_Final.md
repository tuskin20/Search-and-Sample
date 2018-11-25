## Project: Search and Sample Return

[//]: # (Image References)

[image1]: ./test_dataset/IMG/robocam_2018_10_30_22_28_40_150.jpg


---
### Writeup / README


This is a writeup for the first project: Search and Sample Return. Below I'll describe how I developed and modified functions, including the "process_image" function, for the perception portion in the "Notebook Analysis".  After that I'll describe how I applied this to the "Autonomous Navigation and Mapping" in the perception_step and how I modified the decisions_step functions.

### Notebook Analysis
#### 1. Function Modifications
The first modification I made was to the "perspect_transform" function.  I created a mask of ones the same size as the image and did the same transformation to it then used it as a mask in the "color_thresh" function to use only the data before the transform.

Using "matplotlib" I was able to measure a range of RGB values for a sample rock image.  I then used these thresholds to identify rocks in the "color_thresh" function.  Using the threshold that was determined earlier for navigable terrain, I made the remaining unidentified pixels obstacles.


#### 2. `process_image` Function Development
The `process_image` function essentially puts everything together developed in the previous functions to process images recorded earlier.  These images were processed and put into a movie, "test_mapping.mp4".  This replaced the previous example movie of the same name.

The movie will show red for obstacle terrain, green for navigable, and blue for rocks.  The movie is composed of 3 views in a quadrant format.  The upper left is the rover's view, upper right is the transformed image, and the bottom left shows the mapping of terrain over the worldmap.

Below is a sample image from the movie.

![alt text][image1]
### Autonomous Navigation and Mapping

#### 1. `perception_step()` and `decision_step()` Function Development
In the `perception_step`, I changed the `color_thresh` function a little and changed it to take two tuples, one for the minimum threshold and the maximum.  This cleaned it up and made it flexible for making changes.

Another change from the notebook was to the `perspect_transform` function.  I modified the "mask" to zero out the pixels above the horizon.  Often the sky would be viewed as navigable terrain and mapped to the world map, affecting the fidelity negatively. Removing 55% improved fidelity quite a bit.

In the `perception_step` I also added filter to not update pixels on the world map if the measurement was taken with pitch or roll greater than +/-1 degree.  This also helped with fidelity.  I also changed the function to increase the pixel value by 10 instead of just making it 255 if viewed.  This is also improved fidelity.

I made a few changes to the `decision_step`, including adding an angle bias.  This would point the rover slightly to the left of the mean of the navigable terrain.  The idea was to encourage a clockwise path around the map.  The other part I added to the function was a timer to use for measuring how long the rover was "stuck" while trying to go forward.  If the rover observed navigable terrain and tried going forward for 2 seconds and had a velocity less than 0.1m/s then it was "stuck".  I created a new mode, "stuck", that would do a zero velocity turn for 1 second then "stop".  This might not have been the most efficient way to handle this situation but it did work when I observed it.

#### 2. Autonimous Navigation Results  

**Note: Simulation ran in a window at 640x480 with Good graphics quality. 27fps typical.**

In the most recent results of running autonomously the rover mapped 46.2% of the world with 75.7% fidelity and located 3 rocks.

Changes I would make would be to optimize the speed of the rover with mapping.  I'd also add functionality to pick up rocks.  Another improvement would be to use the mapped information to make future decisions more intelligently (i.e. exploring to new areas, getting unstuck by retracing route, etc.).


