# Face Mask Detection
Upload an image or video to detect whether you or someone else is wearing a face mask!

## Description
The Face Mask Detection where it uses facial and object recognition to accurately distinguish those with or without masks. The user would upload an image and the program would analyze it and output whether or not they're wearing a mask. This project is needed to prevent higher risk of spreading the coronavirus and to enforce those who don't follow proper guidelines to wear a mask.

![Thumbnail](/blog/header.jpg)

## Creator
Hello! My name is Pratik, a sophomore studying Computer Science. I have many interests but currently, I want to explore more in the field of robotics, artificial intelligence, and front-end development. Outside of coding, I love to do digital design, gaming, hanging out with friends, and streaming on [twitch](#)! I also have a personal website [here](https://pratikrk.github.io/portfolio/) if you would like to know more about me.

### Why I Created this Project
I created this project because I wanted to address the problem with people not following the guidelines for wearing face-masks during a global pandemic. It saddens me to see those not putting on masks when needed in public because it causes a higher risk of spreading the virus even more. Thus, I created this project to encourage and enforce people to wear one.

Another reason why I created this project is because like most college students, we want to be able to attend in-person classes as soon as possible and the only way we can go back to our college campuses is for the number of COVID cases to die down. Therefore, I felt that like a project like this can simulate technology that can benefit those who are trying to prevent the virus from spreading as much as possible.

## Project Features
* **Python:** A programming language that can be used, for example, back-end and software development.
* **Microsoft Azure Functions:** Helps you launch serverless applications.
* **Face and Object Recognition/Detection**
  * **Artificial Intelligence**
  * **Machine Learning**
  * **Deep Learning**
* **Google Collaborate**
* **Front-End-Development**
  * **HTML**
  * **CSS**
  * **Javascript**
  * **JQuery**
  * **Bootstrap**
* **OpenCV:** A library of programming functions mainly aimed at real-time computer vision. 
* **Tensorflow API:** A free and open-source software library for machine learning. 
  *  **Keras:** An open-source software library that provides a Python interface for artificial neural networks.

## Diagram of Project
![Diagram](/blog/diagram.png)

## Facial and Object Recognition
We need to create the code for the program that reads an image pixel by pixel and uses facial and object recognition to output a frame and percentage on the face in order to detect whether or not the person is wearing a face mask.

I did this part of the project in an environment called google colaboratory. It's a colab, similiar to Jupyter Notebook that allows anyone to write and execute python code through the browser, and is especially well suited to machine learning, data analysis and much more. I also created a repository in github entirely for my code so I can clone it every time I enter back to my colab file.

First, we have to import many different libraries in order for this to work and the ones we need are shown below.

```
import cv2
import os
from tensorflow.keras.preprocessing.image import img_to_array
from tensorflow.keras.models import load_model
from tensorflow.keras.applications.mobilenet_v2 import preprocess_input
import numpy as np
from google.colab.patches import cv2_imshow
```

We're using a very basic and simple face detector called the [Haar Cascade](https://opencv-python-tutroals.readthedocs.io/en/latest/py_tutorials/py_objdetect/py_face_detection/py_face_detection.html) due to my first time playing around with face detection.The Haar Cascade algorithm is a machine learning object detection algorithm which is used to identify objects in an image or video. Instead of creating and training the model from scratch, we use the “haarcascade_frontalface_alt2.xml” to detect faces. It's one of the oldest face detector file applications and it detects the face in a matter of few seconds using machine learning. It's not the best in accuracy but it's one of the fastest. Once the face is detected, then we will use this model file from Keras Tensorflow called mask_recog.h5 to detect masks.

```
faceCascade = cv2.CascadeClassifier("haarcascade_frontalface_alt2.xml")
model = load_model("mask_recog.h5")
```

Once we have everything set up, we start the code. First, we need to convert the file into a grayscale image or video and then use our Haar Cascade to detect the face in the image. Note, my code can currently only detect one face at a time. It will output an error if there is more than one face or more than one output (ex. detects one person wearing a mask and one not wearing a mask). Once the images and faces are identified, you send it out to the mask detector application. We basically pre-processed the image in order to meet the requirements for Haar Cascade application. Now, we send that image to a predictor file through the model_recog.h5 OpenCV file and the rest of the code is basically it creating a frame around the face of the person and outputting a percentage as well. A person wearing a mask will be labeled with a rectangular green frame with a high percentage (most likely between 95%-100%) and a person not wearing a mask will be labeled with a rectangular red frame with a low percentage (< 90%). 

```
#converting the file into a grayscale image or video
def face_mask_detector(frame):
  frame = cv2.imread(fileName) #google colab library that will be later commented out
  gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
  faces = faceCascade.detectMultiScale(gray,
                                        scaleFactor=1.1,
                                        minNeighbors=5,
                                        minSize=(60, 60),
                                        flags=cv2.CASCADE_SCALE_IMAGE)
  faces_list=[]
  preds=[]
  
  #mask-detector function
  #creation and design of the frame output
  for (x, y, w, h) in faces:
      face_frame = frame[y:y+h,x:x+w]
      face_frame = cv2.cvtColor(face_frame, cv2.COLOR_BGR2RGB)
      face_frame = cv2.resize(face_frame, (224, 224))
      face_frame = img_to_array(face_frame)
      face_frame = np.expand_dims(face_frame, axis=0)
      face_frame =  preprocess_input(face_frame)
      faces_list.append(face_frame)
      if len(faces_list)>0:
          preds = model.predict(faces_list)
      for pred in preds:
          (mask, withoutMask) = pred
      label = "Mask" if mask > withoutMask else "No Mask"
      color = (0, 255, 0) if label == "Mask" else (0, 0, 255)
      label = "{}: {:.2f}%".format(label, max(mask, withoutMask) * 100)
      cv2.putText(frame, label, (x, y- 10),
                  cv2.FONT_HERSHEY_SIMPLEX, 1, color, 2)

      cv2.rectangle(frame, (x, y), (x + w, y + h),color, 3)
      cv2_imshow(frame) #google colab library that will be later commented out
  return frame
```

Finally, we just give the program what file we want it to read and we do that by inputting it and then showing it on google colab through the imshow library. The image output  function code is very simple but the video output function is more because it needs to detect each frame and gives us a file output (due to google colab not having a live server to allow us to watch a video). Thus, there would be a generated output file for the video. Thus, our working code is created and now we need to connect it to our front-end for others to use it.

```
#image output
input_image = cv2.imread("image.jpg")
output = face_mask_detector(input_image)
cv2_imshow(output)
```

```
#video output
cap = cv2.VideoCapture('video13.mp4')
ret, frame = cap.read()
frame_height, frame_width, _ = frame.shape
out = cv2.VideoWriter('output.avi',cv2.VideoWriter_fourcc('M','J','P','G'), 10, (frame_width,frame_height))
print("Processing Video...")
while cap.isOpened():
  ret, frame = cap.read()
  if not ret:
    out.release()
    break
  output = face_mask_detector(frame)
  out.write(output)
out.release()
print("Done processing video")
```

## Front-End Development
Due to time contraints, I was inspired by a particular design and used a template and modified it to my own in order to make my website application. Below is the homepage of my website. I used HTML, CSS, Javascript, JQuery, and Bootstrap to create and modify my website. 

![Homepage](/blog/homepage.png)

### Layout of Website
1. **Homepage:** Main page that demonstrates my title, logo, navigation bar, and an iconic carousel for important reminders and relevant information.
2. **Demo Page:** To demonstrate the easy and simple steps of how to upload an image or video to create an output.
3. **Upload Page:** The area where the user will upload their image and video and the output will display back with a frame and percentage that shows if the person is wearing a mask or not.
4. **About Page:** The project description, information, and future discussion are posted there.
5. **Gallery Page:** A carousel containing lots of images that I tested out to show some samples of what similar results could be produced.

### Static Web App
We had to use static web app in order to deploy our website. 

Here is a link to my website: [Click Here](https://victorious-ocean-0b1502910.1.azurestaticapps.net/)

## Back-End Development

![Diagram2](/blog/ai.png)

### Uploading File
We would need to create two buttons to wear we can pick or upload our chosen image or video file and then creating a submit button in order to send the data to our HTTP trigger. Once it does that, it goes through the face mask detection functions that we created and await for our JSON response to bring back the image or video with whether or not the person is wearing a mask.

### Azure Function App
Now we need to create a Microsoft Azure function to call the API, face detector function, and front-end image in order to read the uploaded file from the user. First, I went on my portal and created a entirely new function. Below are the settings I used but they can vary for others. 

![Function](/blog/function.png)

Then, after creating the function, I went on Visual Studios and installed the necessary extensions (such as Azure Functions) and uploaded the files I wanted to put in my function. Then I deployed it from VS code to the portal and thus, it successfully connected. There were definetely errors in terms of installing the correct libraries and such but I was able to fix most of it. After it was done connecting, I used Postman to test it out as well and it worked! Now, it became the matter of trying to figure out how connect it to my front-end as well. 

![Postman](/blog/postman.png)

## Results
The results should be similiar to what you see below. The image on the left presents a person not wearing a mask while the person on the right presents a person wearing a mask.

![No Mask and Mask](/blog/nomaskandmask.png)


## Future Plans
I plan to have many improvements and suggestions to my project. 
* In the future, I want to integrate this into a webcam where it’s able to detect LIVE and in real-time of whether or not the person is wearing a mask or not. 
  * However, this might not require Azure Functions so it would stray a different path.
* I want to use a different algorithm where it's able to more accurately detect faces because sometimes, when someone uploads an image, it doesn't always work and only seems to be working for really high-quality images which is not feasible for real-life images. 
* I also want to implement an algorithm to where it can detect more than one face instead of only one. 


## Special Thanks
I want to thank so many people who supported my journey throughout BitCamp. I first want to thank you chetan and chaitanya, my personal mentor who fought through and through with me in trying to fix errors and help me understand so many concepts. I learned so much from him and really want to say that he's an amazing mentor. 11/10! I also want to thank you Julia, Emily, and Evelyn for being awesome and kind instructors who kept me organized and supported us so much. Thank you for all the communication and flexibility you gave us! Finally, I want to thank you Daniel, and Shreya for providing and teaching us with very well-explained and professional lectures to those who had no clue about Microsoft Azures in the beginning. I was very grateful for every one of you and for this great opportunity! It was a fun experience that I recommend to those who want to learn more about Serverless Microsoft Azure.




