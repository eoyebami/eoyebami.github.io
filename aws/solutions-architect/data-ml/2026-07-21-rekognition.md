## Rekognition
- [Overview](#overview)
- [Features](#features)
- [Demo](#demo)

### Overview

* AWS `Rekognition` is a computer vision service that automates images/rekognition/image and video analysis
    - it uses pre-trained ai modesl to instantly detect objects, extract text, recognize celbs, moderate inappropriate content, verify identities, and analyze facial attributes without `ml` expertise
    - you can use it to catagorize and tag content based on what it recognizes
        * it suggest keywords based on that context
    - ideally for moderation 
        * ![alt text](images/rekognition/image.png)
    - `rekognition` also returns a confidence score from 0-100, for ever prediction
        * indicates how certain the model is about its analysis
        * you can set custom threshold, via `MinConfidence`, to filter results to reduce false positives or negatives

### Features

![alt text](images/rekognition/image-1.png)
* `Object and Scene detection`: are you at a beach, or a wedding, etc
* `Facial Analysis and Recognition`: are you happy or sad, is this a celebrity
    - age, race, gender, etc
* `Text in images/rekognition/image`: pulling text, letters, signs, license plates
* `Activity Detection`: eating, dancing, walking, etc
* `Unsafe Content Detection`: porn, violence

### Demo

1. Label generation
    - ![alt text](images/rekognition/image-2.png)
2. Image properties
    - ![alt text](images/rekognition/image-3.png)
3. Facial analysis
    - ![alt text](images/rekognition/image-4.png)
4. Facial similarity
    - ![alt text](images/rekognition/image-5.png)
5. Text in images/rekognition/image
    - ![alt text](images/rekognition/image-6.png)
* NOTE: you can use the sdk to interact with `rekognition`, above examples are done on the `ui`
