# MLKit-ARCore
This simple project detects objects using Firebase MLKit and tags them in with 3D labels in Augmented Reality. Using ARCore and Firebase RTDB, it also shares the labels between phones. This serves as a multiplayer image labeling game.

Based on: [CoreML-in-ARKit](https://github.com/hanleyweng/CoreML-in-ARKit)

![Alt Text](https://media.giphy.com/media/1oF1KALyk5Y2cagZVF/giphy.gif)

[Demo Video](https://photos.app.goo.gl/14uTsJq32qGVo2eKA)

Language: Swift 4.0

Content Technology: SceneKit, Firebase MLKit, ARCore, RTDB 

Note: SceneKit can achieve a 60 FPS on iPhone7+ - though when it gets hot, it'll drop to 30 FPS.

## Footnotes

- SceneKit Text Labels are expensive to render. Too many polygons (too much text, smoothness, characters) - can cause crashes. In future, SpriteKit would be more efficient for text-labels.

- Whilst ARKit's FPS , is displayed - MLKit's speed is not. However, it does appear sufficiently fast for real-time ARKit applications.

- Placement of the label is simply determined by the raycast screen centre-point to a ARKit feature-point. This could be altered for more stable placement.

## Building Blocks (Overview)

### Get CoreML running in real time in ARKit

- What we do differently here is we're using ARKit's ARFrame as the image to be fed into MLKit.

```
let pixbuff : CVPixelBuffer? = (sceneView.session.currentFrame?.capturedImage)
```

- We also use Threading to continuously run requests to MLKit in realtime, and without disturbing ARKit / SceneView

```
let dispatchQueueML = DispatchQueue(label: "dispatchqueueml")
...
loopMLKitUpdate() // on viewLoad
...
func loopMLKitUpdate() {
    dispatchQueueML.async {
        // 1. Run Update.
        self.updateMLKit()
        // 2. Loop this function.
        self.loopMLKitUpdate()
    }
}
```

### Add 3D Text

- Add a Tap Gesture.
- On Tap. Get the raycast centre point, translating it to appropriate coordinates.
- Render 3D text at that location. Use the most likely object.
