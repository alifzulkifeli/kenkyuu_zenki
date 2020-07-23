
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
