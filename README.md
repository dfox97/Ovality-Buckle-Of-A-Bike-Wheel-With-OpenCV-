# Intro #
Second year project which involved detecting the ovality and buckle of a bike wheel. The project was a wheel truing system which showcased a mechatronics system.
This was the first time i used c++ and opencv.

Buckle and ovality of a bike wheel using raspberry pi with camera for image processing

# Process #

![image](https://user-images.githubusercontent.com/61083107/136714436-d70f5b43-83f8-4d87-bf93-cfcfb7a2aca3.png)

## Step by step ##
Step 1: The first step involved setting up the system, this involved placing the wheel stand in a position for the two cameras to track the ovality and buckle. The two cameras are placed on separate stands which where set at equal level so the two cameras can track the same spoke at the same time. 
Step 2: After the initial setup the first part of the code involved opening and editing the camera using code. This was done using the function “videocapture(0)” which opened the camera when the program was run. The second part involved changing the camera from RGB to greyscale colour to allow tracking to be easier. This was done using the function “CV_BGR2GRAY” this function converts the image to greyscale, see appendix A and B for more code and comments.
Step 3: The next part involved deciding on a resolution and pixel value for the camera and since the cameras where set at 640x480p the decision was to keep the resolution and pixel value capped at 640x480p since it would give the best results. As the program runs and the camera opens the window for the video capture would be set at 640x480p. Since the resolution and pixel values are known, the video capture can be edited with code as OpenCV can track pixel values on the screen.


Step 4: Create a program which will display a histogram, which will be used to track the buckle and ovality. This is done by tracking the intensity line across the centre of the screen, then as the bike wheel rotates it will show a spike in the histogram if the wheel has moved from left to right. The histogram width matches the camera width which equals 640, the height is also set at 256 this value was chosen so the as a preference when displaying the histogram window. The histogram will track the vertical intensity line down the centre of the screen, if the bike wheel moves left or right this will show in the histogram as it would spike. The steps for this are shown below;
•	Create histogram width and height using width at 640 and height at 256 which will then create a window at 640x256.
•	The mat function is used to clear the background for the histogram, using this line of code “Mat histImage(hist_h, hist_w, CV_8UC1, Scalar(255, 255, 255))” The 255 values make the background of the histogram white as the colours at 255 for greyscale are white. “CV_8UC1” is the channel number in this case its 1.
•	The final step in creating the histogram is to create the intensity lines which will show if the bike wheel has changed due to there being buckle or an irregularity with the ovality of the wheel. The line function is used to create the tracking line for the histogram; this function will create a centre line that will be used to track the intensity values. If the bike is buckled for example, then the line plotting the histogram will have a significant peak, which will then be used in an algorithm for determining its displacement. The intensity line is created by pointing to the width value (640) to assign the width of the line, then the height value is determined by taking the set histogram height (256), and then taking away the new width value (hist[i]). This then allows the histogram to track any change along the centre line due to the change in the displacement of the wheel’s buckle or ovality. The scalar colours are set at 0, so it distinguishes the intensity tracking line on the histogram from the white background. 0 will make the histogram intensity line grey. See appendix A and B which shows the code used to create the histogram.
Step 5: Once the histogram was created, it would be called in the main function to display the histogram and track the centre line. 


![image](https://user-images.githubusercontent.com/61083107/136714490-d7cce14c-cf71-478a-b97c-82f199af8014.png)
Figure 9:Original histogram

Step 6: Create a filter intensity variable, which makes the histogram intensity lines smoother to allow more accuracy
 
![image](https://user-images.githubusercontent.com/61083107/136714494-cf19ca0e-b821-4467-8ec9-1af55f4e71ac.png)
Figure 10:Filtered Histogram

Step 7: The main part of the code was finished, to reduce the background noise like shown in the images above cardboard with a white background was added which reduced the background noise. The above image shows multiple peaks due to the histogram picking up the background noise, when the card was placed it made the histogram more accurate when determining the buckle and ovality of the wheel.

![image](https://user-images.githubusercontent.com/61083107/136714503-8f4f3b37-fa0c-4579-8363-3b0452f9551a.png)
Figure 11: Image of white background histogram

Step 8: The histogram will spike when the wheel moves from left to right; the location of the displacement from the movement can then be determined. This is based on the intensity values plotted by the histogram feed from the input of the cameras visual display. The code initialises the restraints of the width of the values to range from 0 to 640 from left to right, based on the dimension of the window for the histogram being 640x480p. To reduce any noise or background interference from the light, an iteration is taken from the left side starting at 230 pixels, and from the right side at 410. These values are an equal distance from the midpoint at 320, and so the average taken will still be calibrated to determine the displacement.
Step 9: Once the above steps are complete this is then repeated for the second camera as the algorithm for the program does the same operation. The difference between the two cameras are the positions, the buckle camera is facing the wheel while the ovality camera is facing the side of the wheel. 
 
![image](https://user-images.githubusercontent.com/61083107/136714509-fb477062-0b1f-49e5-9a71-d4a390312b87.png)
Figure 12:Buckle and ovality camera setup

Step 10: Calculate the final value to send to the data processing team, this is calculated by using the formula:
 Displacement = (((left_location + right_location)/2)-320) / 10
Implementing this equation will print final value to the user for the displacement of both the buckle and the ovality of the wheel for each spoke. The algorithm is based on the histogram plot taking the highest intensity peak from the left and the right side, so the camera detects the edges of each side of the wheel. The average of these two values is taken, and then subtracted from the origin point of the midpoint at 320 in the x-plane. The value is then divided by 10 for the conversion ratio from 10 pixels to 1mm, giving a displacement value to a degree of uncertainty of ±0.5mm.
Step 11: To send the data to the team a protocol will be used to send the data to the processing team, this protocol will involve using serial communication. The RS232 cable would be used to transfer the data in ASCII one bit at a time. This would be done by some programming code which would convert the stored data location from the code into ASCII then this data would be sent over to the other team, once this is sent then the other team can use that data to correct the buckle and ovality. To send data from one device to another , example of using rs232 code is online which has been copied into this report to give a understanding on how the two devices would send and receive the data, all credit goes to the Stackechange forum [9]. The code picks up the serial port device and using the location of the device found from the RS232 setup, then the data can be coded and sent to the receiving device.
