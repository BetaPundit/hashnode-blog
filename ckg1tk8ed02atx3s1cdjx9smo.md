# Yoga Guru - My Final Year Project

## About Yoga Guru
Yoga Guru is your personalized yoga trainer app based on Flutter. It uses poseNet, a pre-trained deep learning model, to estimate body poses in real-time and predict yoga asanas.

### Link to Code 
%[https://github.com/BetaPundit/Yoga-Guru] 


## How I built it (The Story)
- The idea came to me when I first saw the PoseNet framework which is a computer vision model that estimates the pose of a person in an image or a video by estimating where key body joints are. 

- I started by wireframing the app and designed different app screens. 

- I then built different components in Flutter and then used the Flutter Tflite package to run a pre-trained PoseNet model on the image stream (video frame).

- Then I trained a custom model on the output of the above PoseNet model to classify Yoga Poses. It certainly took some time to find images on google to create a dataset and fit a classifier on it.

- The challenge now was to run the 2 model (PoseNet and the custom Yoga Classifier) in a pipeline in the app. For this, I had to understand how to write platform-specific code in Flutter and run custom Tflite models in Android!

- It was a challenging task but in the end, all the hard work paid off and the app was up and running! 


## Additional Thoughts
The art of practising yoga helps in controlling an individual's mind, body and soul. It brings together physical and mental disciplines to achieve a peaceful body and mind. Thus, by building this app, I wish that everyone can access and learn Yoga and keep their body, soul and mind in tune.

Any contributions to the app are most welcome!