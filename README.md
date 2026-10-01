# Real-Time Drowsiness Detection System

## Project Overview

The **Real-Time Drowsiness Detection System** is a computer vision project developed in Python to detect whether a driver is becoming drowsy while driving.

The system uses a webcam to continuously monitor the driver's face. It detects the face, identifies important facial landmarks, and analyzes the driver's **eyes and mouth**.

The system mainly detects drowsiness using **eye closure**. It also detects **yawning** as another indication of tiredness. When the driver's eyes remain closed for a certain number of consecutive frames or a yawn is detected, the system activates an alarm to alert the driver.

---

## Objective

The main objective of this project is to develop a real-time system that can:

* Detect the driver's face using a camera.
* Detect important facial landmarks.
* Monitor the driver's eyes.
* Detect prolonged eye closure.
* Detect yawning.
* Generate an alarm when drowsiness is detected.

The system is designed to provide an immediate warning to the driver before drowsiness can lead to an accident.

---

# Technologies Used

### Python

Python is used as the main programming language for implementing the complete system.

### OpenCV

OpenCV is used for:

* Capturing video from the webcam.
* Converting frames to grayscale.
* Detecting the driver's face.
* Drawing facial regions and displaying the processed video.

The project uses OpenCV's **Haar Cascade face detector** for face detection.

### Dlib

Dlib is used for detecting **68 facial landmarks** on the driver's face.

These landmarks provide the coordinates of different facial features, including the eyes and mouth.

### Imutils

Imutils is used to simplify image and video processing operations. It is also used for resizing frames and working with the video stream and facial landmarks.

### NumPy

NumPy is used for numerical calculations, including calculating the average positions of facial landmark points.

### SciPy

SciPy is used to calculate the **Euclidean distance** between facial landmark points.

### Playsound

The Playsound library is used to play the alarm sound when drowsiness or yawning is detected.

---

# How the System Works

The system processes the driver's video continuously.

The complete flow is:

**Webcam → Face Detection → Facial Landmark Detection → Eye Analysis + Mouth Analysis → Drowsiness/Yawn Detection → Alarm**

---

## 1. Capture Video

The system starts a video stream using the webcam.

Each frame captured from the camera is resized to a width of **450 pixels** before processing.

The frame is then converted from color to grayscale.

Grayscale images make face detection easier and reduce the amount of data that needs to be processed.

---

## 2. Detect the Driver's Face

The system uses OpenCV's:

**`haarcascade_frontalface_default.xml`**

to detect the driver's face.

The Haar Cascade detector searches the grayscale frame for regions that look like a human face.

When a face is detected, the coordinates of the face are obtained.

The detected face is then converted into a format that can be processed by Dlib.

---

## 3. Detect 68 Facial Landmarks

After detecting the face, the project uses:

**`shape_predictor_68_face_landmarks.dat`**

to identify **68 facial landmark points**.

These points represent important locations on the face, including:

* Eyes
* Eyebrows
* Nose
* Mouth
* Jaw

For this project, the most important landmarks are the ones around the **left eye, right eye, and mouth**.

The facial landmark coordinates are converted into a NumPy array so that they can be used for calculations.

---

# 4. Eye Aspect Ratio (EAR)

The main method used by this project to detect drowsiness is the **Eye Aspect Ratio (EAR)**.

EAR is a numerical value calculated from the positions of the six landmark points around each eye.

The project calculates:

* Vertical distance between specific eye landmarks.
* Another vertical distance between eye landmarks.
* Horizontal distance across the eye.

The EAR is calculated using:

**EAR = (A + B) / (2 × C)**

where:

* **A** = vertical distance between two eye landmark points.
* **B** = another vertical distance between two eye landmark points.
* **C** = horizontal distance between the two ends of the eye.

The system calculates EAR separately for the left and right eye and then takes their average.

---

## 5. Detecting Closed Eyes

When the eyes are open, the EAR value is relatively higher.

When the eyes start closing, the vertical distances between the eye landmarks decrease, causing the EAR value to decrease.

The project uses:

**EYE_AR_THRESH = 0.3**

as the threshold.

Therefore:

* **EAR ≥ 0.3** → Eyes are considered open.
* **EAR < 0.3** → Eyes are considered closed.

However, the system does not immediately consider one closed frame as drowsiness.

This is important because people naturally blink.

---

# 6. Detecting Drowsiness

The project uses a counter to determine whether the eyes have remained closed for a prolonged period.

The important parameter is:

**EYE_AR_CONSEC_FRAMES = 30**

Whenever:

**EAR < 0.3**

the counter is increased.

If the EAR goes back above the threshold, the counter is reset to zero.

If the counter reaches **30 consecutive frames**, the system considers it a drowsiness event.

The screen then displays:

**"DROWSINESS ALERT!"**

and the alarm is activated.

This approach prevents normal short blinks from triggering the drowsiness alarm.

---

# 7. Yawning Detection

The project also includes a separate mechanism for detecting yawning.

The system uses facial landmarks around the mouth to calculate the distance between the upper and lower lip.

The project calculates the average position of selected upper-lip and lower-lip landmark points.

The vertical distance between these points is then calculated.

The threshold used by the project is:

**YAWN_THRESH = 20**

If the calculated lip distance becomes greater than 20, the system considers it a possible yawn.

The screen displays:

**"Yawn Alert"**

and the alarm can be activated.

Therefore, the project does not depend only on eye closure. It also uses mouth movement as another indication of possible drowsiness.

---

# 8. Alarm System

The project uses an audio file called:

**`Alert.wav`**

for the warning alarm.

The alarm is played using the **Playsound** library.

The project uses Python's **Threading** functionality so that the alarm can play without completely stopping the video-processing loop.

There are separate alarm states for:

* Drowsiness
* Yawning

This allows the system to continue monitoring the driver while the warning sound is being played.

---

# 9. Information Displayed on the Screen

While the system is running, the processed video is displayed in a window called **Frame**.

The system displays:

* Facial landmark outlines around the eyes.
* Facial landmark outline around the mouth.
* Current **EAR value**.
* Current **YAWN value**.
* **DROWSINESS ALERT!** when prolonged eye closure is detected.
* **Yawn Alert** when the mouth-opening threshold is exceeded.

The user can press **`q`** to stop the application.

---

# Algorithm

The complete algorithm used in the project is:

1. Start the webcam video stream.
2. Resize each captured frame.
3. Convert the frame to grayscale.
4. Detect the driver's face using the Haar Cascade classifier.
5. Convert the detected face into a Dlib rectangle.
6. Detect 68 facial landmarks using Dlib's shape predictor.
7. Extract the left and right eye landmarks.
8. Calculate the EAR for both eyes.
9. Calculate the average EAR.
10. If EAR is below 0.3, increase the closed-eye counter.
11. If the eyes remain closed for 30 consecutive frames, trigger the drowsiness alarm.
12. Extract the mouth landmarks.
13. Calculate the distance between the upper and lower lip.
14. If the lip distance is greater than 20, trigger the yawn alert.
15. Display the processed frame and EAR/YAWN values.
16. Continue the process until the user presses `q`.

---

# Testing and Results

The system was tested under different conditions to understand how well the face, eyes, and drowsiness detection worked.

## 1. Different Lighting Conditions

The system was tested under normal ambient lighting.

Under sufficient lighting, the system was able to detect the driver's face and eyes successfully.

The project also showed that **direct light falling onto the camera can affect the detection**, making face and eye detection less reliable.

---

## 2. Different Face Positions

The driver was tested with the face positioned at different locations.

### Center Position

When the driver's face was positioned in the center, the system successfully detected:

* Face
* Eyes
* Eye blinks
* Drowsiness

### Right Position

When the driver's face was positioned toward the right, the system was still able to detect the face, eyes, blinking, and drowsiness.

### Left Position

The system was also tested with the driver's face positioned toward the left and was able to detect the relevant facial features and drowsiness.

---

## 3. Driver Wearing Spectacles

The system was tested while the driver was wearing spectacles.

The system was still able to detect the driver's face and eyes and monitor eye activity.

---

## 4. Head Tilt

The project was also tested when the driver's head was tilted.

When the driver's face was tilted by more than approximately **30 degrees from the vertical plane**, face and eye detection became unsuccessful.

This showed one of the limitations of the detection approach used in the project.

---

## 5. Real-World Testing

The system was tested in a real vehicle by placing the camera near the **visor of the car** and focusing it toward the driver.

During testing, the system generally produced the expected output when the camera had a clear view of the driver's face.

The main issue observed during real-world testing was **direct light falling onto the camera**, which could interfere with face and eye detection.

---

# Limitations

Based on the testing performed, the project has some limitations:

* Face and eye detection can be affected by strong direct light.
* Detection becomes unreliable when the driver's head is tilted significantly.
* The system requires the driver's face to remain reasonably visible to the camera.
* The Haar Cascade face detector can be less accurate in difficult conditions.
* The system primarily relies on eye closure and mouth opening to identify possible drowsiness.

---

# Future Scope

One proposed future improvement is to convert the system into a **smartphone application**.

The application could use the smartphone's camera to monitor the driver. The phone could be placed in a position where its camera has a clear view of the driver's face.

This would make the system easier to use without requiring a separate camera and computer setup.

---

# Project Summary

This project demonstrates a real-time computer vision approach for detecting driver drowsiness.

The system uses **OpenCV for face detection and video processing**, **Dlib's 68-point facial landmark predictor for locating facial features**, and mathematical measurements such as **Eye Aspect Ratio (EAR)** and **lip distance** to identify possible drowsiness.

The system triggers an alarm when the driver's eyes remain closed for **30 consecutive frames** or when the detected mouth opening exceeds the defined yawn threshold.

The project was tested under different lighting conditions, face positions, with spectacles, and with different head positions. The testing showed that the system works under normal conditions but can have difficulty with strong direct light and significant head tilting.

Overall, the project provided practical experience in **Python, OpenCV, Dlib, facial landmark detection, real-time video processing, eye-state analysis, and computer vision-based alert systems**.
