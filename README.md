# real-time-face-detection
creating a website that detect the face using OpenCV and Flask


the first step i created a new folder inside the htdocs in the xampp folder called real_time_face_detection


![Screenshot 2024-07-27 151704](https://github.com/user-attachments/assets/f56718d5-99b0-46c2-ac3d-00a0910c9031)


then before i wrote the code in the virtual studio code i installed two libraries which are opencv-python and flask by copying this code into the terminal 

```
pip install opencv-python flask
```


then i created a python code inside the real_time_face_detection folder called face_detection.py and wrote this code 


```
from flask import Flask, render_template, Response
import cv2

app = Flask(__name__)


camera = cv2.VideoCapture(0)

def detect_faces(frame):
    face_cascade = cv2.CascadeClassifier(cv2.data.haarcascades + 'haarcascade_frontalface_default.xml')
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    faces = face_cascade.detectMultiScale(gray, scaleFactor=1.1, minNeighbors=5, minSize=(30, 30))
    for (x, y, w, h) in faces:
        cv2.rectangle(frame, (x, y), (x+w, y+h), (255, 0, 0), 2)
    return frame

def gen_frames():
    while True:
        success, frame = camera.read()
        if not success:
            break
        frame = detect_faces(frame)
        _, buffer = cv2.imencode('.jpg', frame)
        frame = buffer.tobytes()
        yield (b'--frame\r\n'
               b'Content-Type: image/jpeg\r\n\r\n' + frame + b'\r\n')

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/Faisal_face_detection')
def video_feed():
    return Response(gen_frames(), mimetype='multipart/x-mixed-replace; boundary=frame')

if __name__ == '__main__':
    app.run(debug=True)
```

next i created another folder inside the real_time_face_detection folder called templates and i wrote this html code inside it 

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Real-Time Face Detection</title>
</head>
<body>
    <h1>Real-Time Face Detection</h1>
    <img src="{{ url_for('Faisal_face_detection') }}" width="100%" />
</body>
</html>
```

finally when i run the Python code and write http://127.0.0.1:5000/Faisal_face_detection in the web browser i could see a real time face detection 


![Screenshot 2024-07-27 151349](https://github.com/user-attachments/assets/a50c88f7-f522-424e-91b7-07516e570415)


