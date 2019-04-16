# Contributors 

Melissa Chen,
Lezhong Gao,
Kevin Quach,
Vaibhav Srivastava

# Lidar Car Detection

Detect target cars by using the method described below:

* Cluster 3D Points using region growing clustering of depth map grid cells
* Filter out clusters with width and depth
* Project clustered points on 360 degree panoramic view
* Predict boxes with pre-trained deep neural network
* Cluster boxes and select most probable box
* Track selected box

Reference: 
https://github.com/hb0702/Didi_Challenge_2017_ROS#didi-udacity-self-driving-car-challenge-2017
## Modifications

* Instead of using included bagplayer, play bagfiles using standard rosbag play commands
* Modify cluster files' parameters to more accurately match our collected Lidar data

## Running

### collect data 
bagfile including:
* /image_raw (camera)
* /tf & /tf_static (transform information)
* /velodyne_packets & /velodyne_points (Lidar data)
### run car detection program
    $ ./object_tracker --car 
### run bagfile
    $ rosbag play '$FILE_PATH'
### run rviz
    $ rosrun rviz rviz
select bagfiles topics in rviz

# Camera Car Detection
* Use YOLOv3 computer vision pretrained model
* Detect object frame by frame returning box coordinates in addition to object’s ‘name’ and ‘percentage probability’

The work dependencies below:
* Python3 
* ImageAI library 
* YOLOv3 pretrained model 

Reference:
https://heartbeat.fritz.ai/detecting-objects-in-videos-and-camera-feeds-using-keras-opencv-and-imageai-c869fe1ebcdb 

https://github.com/OlafenwaMoses/ImageAI/

### running 
Find the file named VideoDitection.py and modify some codes working for your video

    detector.setModelPath( os.path.join(execution_path , "YOLOv3_file_name"))
    
    video_path = detector.detectObjectsFromVideo(input_file_path=os.path.join( execution_path, "YOUR_VIDEO"), output_file_path=os.path.join(execution_path, "OUTPUT_FILE_NAME"), frames_per_second=29, log_progress=True)


# Adjacent Lane Detection

Utilize available “current lane” detection techniques for adjacent lane detection. The general method for detecting lanes is described below.

* Detect prominent straight lines in the camera feed through edge detection and feature extraction techniques using OpenCV
* Apply Canny Edge Detector to individual frames 
* Define a triangular mask that describes the lane area. Triangle is formed from the horizon point and two foreground lane points
* Perform a Hough Transform on each frame in order to mathematically define lines
* Visualize the lanes by overlaying the line plots on each frame
* If the roads are particularly noisy (e.g. cracks, tar repairs), the initially triangular mask can be adjusted to cut out the center of the expected lane area, since we know the lane lines will tend towards the edges of that area rather than the center

Reference: 
https://towardsdatascience.com/tutorial-build-a-lane-detector-679fd8953132#bbac

## Modifications to Source

* Redefine the triangular mask by inputting the horizon point and foreground lane points based on our collected data
* Adjust the parameters that define which lane lines to use. Source code expects one line on either side of a frame while ours defines lane lines by expected line angles within the frame. Different sets of parameters (slope thresholds, differentiation between left and right lines) are set depending on the input video.

## Running the Code

* Change video file called out in the script, solution.py, to the desired video 
* run solution.py


    $ python solution.py



# Lane Marker Detection 

Our scripts focus on identifying yellow lane markers. For our data set, the objective was to identify the center double yellow lane marker while driving in the outside lane of a two lane road. The process to do this is described below.

* Convert the acquired video data to image frames one by one by using readFrame command in MATLAB.
* Process the converted frames in the following manner: 
* Isolate yellow pixels on the basis of RGB thresholds.
* Change the color of isolated yellow pixels to white. 
* Perform Canny Edge detection on the isolated image.
* Perform Hough transform on the edge detected in the step above.
* Convert the line detected to green color and add the original image to the line detected image.
* Consolidate the frames back to make a video by using writeVideo command in MATLAB.

## Running the Code

* In order to get the results where the detected lines appear superimposed on the actual yellow lanes:
Run MATLAB and run the following file.

    
    image_colorpicker.m
 
Make sure you have stored the video in a folder to which a path is defined in the following code in the image_colorpicker.m file:

    shuttleVideo = VideoReader('lanedetection107secmass_ave_final.avi')

* In order to obtain a video that only shows the yellow lane markers, be sure to uncomment lines 33 and 34 from image_colorpicker.m before running

	
	33 else
	34     img(i,k,:) = 0;

* Once you have obtained a processed video file that only shows the highlighted center line, run the hough_transform.m script. Running this script will perform the canny edge detection and hough transform on each preprocessed frame obtained from image_colorpicker.m 



