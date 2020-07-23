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
