# GOTCHA!
GOTCHA! is a real-time face recognition system for automated classroom attendance. Using OpenCV for video capture and DeepFace for face recognition, it identifies students in real-time, enhancing efficiency and accuracy in attendance management, especially in large classes.

# Getting Started
To get started with GOTCHA!, follow these instructions:

**Prerequisites**
- Python 3.6 or higher

- OpenCV library

- DeepFace library

- A webcam or other video capture device

- A database of student faces stored in the specified directory (Desktop/TesT)

**Installation**
1. Install OpenCV:
```shell
pip install opencv-python
```

2. Install DeepFace:
```shell
pip install deepface
```

3. Ensure you have a directory with images of students labeled with their names, stored in Desktop/TesT.

# File Structure
The file structure of the GOTCHA! project is as follows:

```bash
GOTCHA/
│
├── main.py            # The main script to run the face recognition system.
│
└── Desktop/
    └── TesT/          # Directory containing images of students for face recognition.
```

**main.py**

The main.py file contains the core logic for capturing video from the webcam, performing face recognition using the DeepFace library, and displaying the recognized faces with their corresponding names and accuracy scores.

Here is the code with detailed explanations:

```python
import cv2
import os
from deepface import DeepFace

def realtime_face_recognition():
    # Define a video capture object. The argument '0' means we use the default webcam.
    vid = cv2.VideoCapture(0)

    while True:
        # Capture the video frame by frame. 'ret' is a boolean indicating if the frame was read correctly.
        ret, frame = vid.read()
        
        # If the frame was not captured correctly, print an error message and exit the loop.
        if not ret:
            print("Failed to capture frame from camera. Exiting...")
            break

        try:
            # Perform face recognition on the captured frame.
            # The 'find' method in DeepFace is used to find faces in the frame and match them with the database.
            people = DeepFace.find(img_path=frame, db_path="Desktop/TesT", model_name='Facenet512', distance_metric='euclidean_l2', enforce_detection=False)

            # Loop through each recognized face (if any) in the frame.
            for person in people:
                # Print the entire person dictionary to understand its structure and keys.
                print(person)
                
                # Check if the required keys for bounding box coordinates are present in the person dictionary.
                if 'source_x' in person and 'source_y' in person and 'source_w' in person and 'source_h' in person:
                    # Retrieve the coordinates of the face bounding box.
                    x = person['source_x'][0]
                    y = person['source_y'][0]
                    w = person['source_w'][0]
                    h = person['source_h'][0]
                    
                    # Draw a rectangle around the face using the bounding box coordinates.
                    cv2.rectangle(frame, (x, y), (x+w, y+h), (0, 255, 0), 2)

                    # Get the file path of the matched image from the database.
                    id = person['identity'][0]
                    # Extract the person's name from the file path.
                    name = os.path.splitext(os.path.basename(id))[0]
                    
                    # Assuming there is a key 'distance' that holds the accuracy score.
                    accuracy_key = 'distance'  # Replace with the correct key after inspecting the output.
                    accuracy = person[accuracy_key][0]  # Assuming the accuracy score is stored under this key.
                    
                    # If the accuracy is above 0.85, mark the face as "UNKNOWN".
                    if accuracy > 0.85:
                        name = "UNKNOWN"
                    
                    # Create the text to display on the frame, including the name and accuracy.
                    text = f"{name}, Distance: {accuracy:.2f}"
                    
                    # Put the text on the frame just above the bounding box.
                    cv2.putText(frame, text, (x, y - 10), cv2.FONT_HERSHEY_SIMPLEX, 0.9, (0, 0, 255), 2)
                    # Print the name and accuracy to the console.
                    print(text) 

        except Exception as e:
            # If an error occurs during face recognition, print the error message.
            print(f"Error during face recognition: {e}")

        # Display the resulting frame in a window.
        cv2.namedWindow('frame', cv2.WINDOW_NORMAL)
        cv2.resizeWindow('frame', 960, 720)
        cv2.imshow('frame', frame)

        # Check if the 'q' button is pressed to quit the program.
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break
    
    # Release the video capture object and close all windows.
    vid.release()
    cv2.destroyAllWindows()

# Perform real-time face recognition using the webcam.
realtime_face_recognition()
```

**Desktop/TesT/**

This directory holds the images of the students, each named according to the student's name. The DeepFace library uses these images to match and recognize faces in the video stream.

# Analysis

**Methods**

The analysis involves capturing video frames using OpenCV and applying the DeepFace library for face recognition. The system uses the Facenet512 model with the Euclidean L2 distance metric to identify faces.

**Visualizations**

The output is displayed in real-time with bounding boxes around recognized faces and text annotations indicating the person's name and the confidence level of the match. If the confidence level is above a certain threshold (0.85 in this case), the face is marked as "UNKNOWN".

**Insights**

This project demonstrates the feasibility of using facial recognition for automated attendance. The primary insight is that while the system can accurately recognize known faces, the threshold setting plays a crucial role in balancing false positives and negatives.

# Results
**Findings**

The GOTCHA system successfully identifies and labels known faces in real-time with reasonable accuracy. The use of a confidence threshold helps in distinguishing between known and unknown faces.

**Recommendations**

- Enhance the database: Include more varied images of each student to improve recognition accuracy.

- Fine-tune the confidence threshold: Adjust based on specific application requirements.

**Areas for Improvement**

- Scalability and Error Rates: As the number of students increases, the system may become more prone to errors and misidentifications. Managing a larger database requires more sophisticated algorithms to maintain accuracy.
- Distance and Angle Constraints: The system requires photos to be taken at certain distances and angles for optimal recognition. Ideally, each student should have at least three photos from different angles in the database to improve recognition accuracy.
- Handling Unrecognized Faces: When a person not in the database is detected, the system still tries to match them with an existing image, often leading to incorrect identifications. Implementing a more robust mechanism to flag and handle unrecognized faces would reduce these errors.


### Poster

Here is our final project poster, showcasing our introduction and testing results:

![Sample Output](https://github.com/RickyLu1216/Gotcha/blob/main/Introduction%20to%20AI%20final%20proj%20(1).png)

# Contributors

[**Ricky Lu**](https://github.com/RickyLu1216) - Project Lead (project development, coding, and testing)

[**Mario Chen**](

[**Matthew Chen**](https://github.com/MinyenChen) -code contributer（Debug,error solving,coding）

[**Ricky Chen**](https://github.com/Rickitiffi) - Non-code contributor (poster making, triage, and viewing)

[**Kaitlin Huang**](https://github.com/Kaitlin819) -Non-code contributor (poster making, triage, and viewing)

# Acknowledgments

We would like to thank:

- Professor **卞中佩 CP** for guidance and support.

- **Data Providers** for supplying the images used in the face database.

- Special thanks to our teaching assistant, **黃柏銓 Ian**, for his invaluable support and guidance throughout this project. We greatly appreciate his insights and assistance.

# References

- [OpenCV Documentation](https://docs.opencv.org/)
  
- [DeepFace Documentation](https://github.com/serengil/deepface)
