# 6 月 29 日

set up localhost web server

- set the face recognition to works in browser
- using html,inline,css and javascript
- user need to allow the browser camera usage

### procedure

1. cretae html template to make it run in browser

```html
<!DOCTYPE html>
<html lang="en">
	<head>
		<meta charset="UTF-8" />
		<meta name="viewport" content="width=device-width, initial-scale=1.0" />
		<meta http-equiv="X-UA-Compatible" content="ie=edge" />
		<title>Document</title>
	</head>
	<body></body>
</html>
```

2. after that to make the display center and the canvas can draw on the top of canvas, i wrote the next line of code using inline css

```css
body {
	margin: 0;
	padding: 0;
	width: 100vw;
	height: 100vh;
	display: flex;
	justify-content: center;
	align-items: center;
}

canvas {
	position: absolute;
}
```

3. next i import the face script used. the script.js is my script to detect face and initialize the webcam.

```html
<script defer src="face-api.min.js"></script>
<script defer src="script.js"></script>
```

4. to import the video in html using video tag

```html
<video id="video" width="720" height="560" autoplay muted></video>
```

5. This is the final html file

```html
<!DOCTYPE html>
<html lang="en">
	<head>
		<meta charset="UTF-8" />
		<meta name="viewport" content="width=device-width, initial-scale=1.0" />
		<meta http-equiv="X-UA-Compatible" content="ie=edge" />
		<title>Document</title>
		<script defer src="face-api.min.js"></script>
		<script defer src="script.js"></script>
		<style>
			body {
				margin: 0;
				padding: 0;
				width: 100vw;
				height: 100vh;
				display: flex;
				justify-content: center;
				align-items: center;
			}

			canvas {
				position: absolute;
			}
		</style>
	</head>

	<body>
		<video id="video" width="720" height="560" autoplay muted></video>
	</body>
</html>
```

# 7 月 2 日

Implement the face recognition using javascript

- using javascript library to make face recognition
- the library can be found [here](https://github.com/justadudewhohacks/face-api.js)
- the script.js will call the function in the api so we need to import the library first before script.js run

1. In script.js, this line of code will make us able to display input from webcam to browser

```js
const video = document.getElementById("video");

function startVideo() {
	navigator.getUserMedia(
		{ video: {} },
		(stream) => (video.srcObject = stream),
		(err) => console.error(err)
	);
}
```

2. next we need to import the model

```js
Promise.all([
	faceapi.nets.tinyFaceDetector.loadFromUri("/models"),
	faceapi.nets.faceLandmark68Net.loadFromUri("/models"),
	faceapi.nets.faceRecognitionNet.loadFromUri("/models"),
	faceapi.nets.faceExpressionNet.loadFromUri("/models"),
]).then(startVideo);
```

3. to start the faceapi, copy the code below

```js
video.addEventListener("play", () => {
	const canvas = faceapi.createCanvasFromMedia(video);
	document.body.append(canvas);
	const displaySize = { width: video.width, height: video.height };
	faceapi.matchDimensions(canvas, displaySize);
	setInterval(async () => {
		const detections = await faceapi
			.detectAllFaces(video, new faceapi.TinyFaceDetectorOptions())
			.withFaceLandmarks()
			.withFaceExpressions();
	}, 100);
});
```

4. to add box and other information, we can access a few method from faceapi object

```js
const resizedDetections = faceapi.resizeResults(detections, displaySize);
canvas.getContext("2d").clearRect(0, 0, canvas.width, canvas.height);
faceapi.draw.drawDetections(canvas, resizedDetections);
faceapi.draw.drawFaceLandmarks(canvas, resizedDetections);
faceapi.draw.drawFaceExpressions(canvas, resizedDetections);
```

5. the final code will be as follows

```js
const video = document.getElementById("video");

Promise.all([
	faceapi.nets.tinyFaceDetector.loadFromUri("/models"),
	faceapi.nets.faceLandmark68Net.loadFromUri("/models"),
	faceapi.nets.faceRecognitionNet.loadFromUri("/models"),
	faceapi.nets.faceExpressionNet.loadFromUri("/models"),
]).then(startVideo);

function startVideo() {
	navigator.getUserMedia(
		{ video: {} },
		(stream) => (video.srcObject = stream),
		(err) => console.error(err)
	);
}

video.addEventListener("play", () => {
	const canvas = faceapi.createCanvasFromMedia(video);
	document.body.append(canvas);
	const displaySize = { width: video.width, height: video.height };
	faceapi.matchDimensions(canvas, displaySize);
	setInterval(async () => {
		const detections = await faceapi
			.detectAllFaces(video, new faceapi.TinyFaceDetectorOptions())
			.withFaceLandmarks()
			.withFaceExpressions();
		const resizedDetections = faceapi.resizeResults(detections, displaySize);
		canvas.getContext("2d").clearRect(0, 0, canvas.width, canvas.height);
		faceapi.draw.drawDetections(canvas, resizedDetections);
		faceapi.draw.drawFaceLandmarks(canvas, resizedDetections);
		faceapi.draw.drawFaceExpressions(canvas, resizedDetections);
	}, 100);
});
```

# 7 月 10 日

implement the face recognition using javascript

- the result of this is as follows
- the face detection algorithm will detect the face in th webcam and mark it with blue box
- the result is as follows

### happy

![image](https://res.cloudinary.com/ddetpgs1k/image/upload/v1595475135/Screenshot_2020-07-23_at_11.05.41_AM_2_xyynhw.png)

### neutral

![image](https://res.cloudinary.com/ddetpgs1k/image/upload/v1595475105/Screenshot_2020-07-23_at_11.05.23_AM_2_izbte6.png)

### surprised

![image](https://res.cloudinary.com/ddetpgs1k/image/upload/v1595475106/Screenshot_2020-07-23_at_11.05.55_AM_2_q5lhff.png)

### angry

![image](https://res.cloudinary.com/ddetpgs1k/image/upload/v1595475079/Screenshot_2020-07-23_at_11.06.06_AM_2_u7ayrz.png)

# 7 月 15 日

pull faces from group image

- using pyhton library to test face reccognition
- the sourceo for the library can be found [here](https://pypi.org/project/face-recognition/)
- this library come with cli for quick testing
- for this time i try to pull image from a group picture

I wrote the code below to get a single person image from group image

```py
from PIL import Image
import face_recognition

image = face_recognition.load_image_file('./img/groups/team1.jpg')
face_locations = face_recognition.face_locations(image)

for face_location in face_locations:
  top, right , bottom, left = face_location

  face_image = image[top:bottom, left:right]
  pil_image = Image.fromarray(face_image)
  pil_image.show()
```

### input image

![input image](https://res.cloudinary.com/ddetpgs1k/image/upload/v1595476317/team1_nu3hpv.jpg)

### output image

![output image](https://res.cloudinary.com/ddetpgs1k/image/upload/v1595476321/Screenshot_2020-07-23_at_11.49.43_AM_2_amudoa.png)

# 7 月 19 日

find faces in group image

- this script is to identify how many people in the picture
- the application of my project is, if there are more than one person in the image, it will hide a private information

### code

```py
import face_recognition

image = face_recognition.load_image_file('./img/groups/team2.jpg')
face_locations = face_recognition.face_locations(image)

print(face_locations)

print(f'there are {len(face_locations)} people in this image')
```

for this i test it using command line interface

```zsh
(face-recog) (base) al@alifs-Air face-recog % python find_faces.py
[(39, 411, 101, 349), (101, 294, 163, 232), (38, 95, 90, 43), (72, 164, 124, 112)]
there are 4 people in this image
(face-recog) (base) al@alifs-Air face-recog % python find_faces.py
[(89, 79, 132, 36), (94, 429, 137, 386), (70, 371, 113, 328), (65, 487, 108, 443), (74, 655, 118, 612), (84, 573, 127, 530), (65, 213, 108, 170), (79, 295, 122, 252), (94, 157, 130, 121), (78, 121, 114, 85), (94, 245, 130, 209)]
there are 11 people in this image
```

# 7 月 20 日

## face match

- this code will take two image as input and it will compare the two image
- it will output a value if the similarity of face in the image is high the score will be low
- for this code it will print the result wether the input image is same as the output image or not

### code

```py
import face_recognition

image_of_bill = face_recognition.load_image_file('./img/known/Bill Gates.jpg')
bill_face_encoding = face_recognition.face_encodings(image_of_bill)[0]
print( type(face_recognition.face_encodings(image_of_bill)))


unknown_image = face_recognition.load_image_file('./img/unknown/mark.jpg')
unknown_face_encoding = face_recognition.face_encodings(unknown_image)[0]

#compare faces
result = face_recognition.compare_faces([bill_face_encoding],unknown_face_encoding)

if result[0]:
  print('This is Bill gates')
else:
  print('This is not Bill gates')
```

### result

```zsh
(face-recog) (base) al@alifs-Air face-recog % python face_match.py
This is Bill gates
(face-recog) (base) al@alifs-Air face-recog % python face_match.py
This is not Bill gates
```

# 7 月 22 日

## identify user

- for this test i have set the image of Bill Gates and Steve Jobs
- when other than two person, it will marked as unknown person

```py
import face_recognition
from PIL import Image, ImageDraw

image_of_bill = face_recognition.load_image_file('./img/known/Bill Gates.jpg')
bill_face_encoding = face_recognition.face_encodings(image_of_bill)[0]

image_of_steve = face_recognition.load_image_file('./img/known/Steve Jobs.jpg')
steve_face_encoding = face_recognition.face_encodings(image_of_steve)[0]

#create array  of encodings and names
known_face_encodings = [
  bill_face_encoding,
  steve_face_encoding
]

known_face_names = [
  'Bill Gates',
  'Steve Jobs'
]

# Load test image to find faces in
test_image = face_recognition.load_image_file('./img/groups/bill-steve-elon.jpg')

#find faces in test image
face_locations = face_recognition.face_locations(test_image)
face_encodings = face_recognition.face_encodings(test_image, face_locations)

#convert to PIL format
pil_image = Image.fromarray(test_image)

#Create a imagedraw instance
draw  = ImageDraw.Draw(pil_image)

#loop throught faces in test image
for(top, right, bottom, left), face_encodings in zip(face_locations, face_encodings):
  matches = face_recognition.compare_faces(known_face_encodings, face_encodings)

  name = 'Unknown Person'

  #if match
  if True in matches:
    first_match_index = matches.index(True)
    name = known_face_names[first_match_index]

  #draw box
  draw.rectangle(((left,top),(right,bottom)),outline=(0,0,0))

  #draw label
  text_width, text_height = draw.textsize(name)
  draw.rectangle(((left,bottom - text_height - 10),(right,bottom)), fill=(0,0,0),outline=(0,0,0))
  draw.text((left + 6 , bottom - text_height - 5), name, fill=(255,255,255,255))
del draw

#display image
pil_image.show()
```
### result
![image](https://res.cloudinary.com/ddetpgs1k/image/upload/v1595481091/Screenshot_2020-07-22_at_10.11.26_PM_2_fopwyg.png)
![image](https://res.cloudinary.com/ddetpgs1k/image/upload/v1595481616/Screenshot_2020-07-22_at_10.12.40_PM_3_niliqu.png)
