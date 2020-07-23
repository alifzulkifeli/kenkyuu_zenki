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
	<body>
	</body>
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
