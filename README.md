# Texture Apply Async
[![openupm](https://img.shields.io/npm/v/com.gilzoide.texture-apply-async?label=openupm&registry_uri=https://package.openupm.com)](https://openupm.com/packages/com.gilzoide.texture-apply-async/)

Alternative to [Texture2D.Apply()](https://docs.unity3d.com/ScriptReference/Texture2D.Apply.html) that doesn't require synchronizing with the render thread, avoiding stalls in the main thread.


## Features
- Asynchronous texture data update in CPU in the render thread, avoiding stalls in the main thread
- Supports Unity's Built-in Render Pipeline as well as Scriptable Render Pipelines (e.g: URP and HDRP).
- To guarante the texture is applied before appearing in the screen:
  + In BRP, updates are scheduled in the first Camera's `Camera.onPreRender` event
  + In SRP, updates are scheduled in `RenderPipelineManager.beginContextRendering` event
- Supports registering for updates every frame or for a single frame
- Prebuilt for Windows, Linux, macOS and Android
- Built from source in iOS, tvOS, visionOS and WebGL projects


## Caveats
- You should not update the texture's data while the camera/render pipeline is rendering, or else garbage data could be applied to the texture.


## How to install
Either:
- Use the [openupm registry](https://openupm.com/) and install this package using the [openupm-cli](https://github.com/openupm/openupm-cli):
  ```
  openupm add com.gilzoide.texture-apply-async
  ```
- Install using the [Unity Package Manager](https://docs.unity3d.com/Manual/upm-ui-giturl.html) with the following URL:
  ```
  https://github.com/gilzoide/unity-texture-apply-async.git#1.2.0
  ```
- Clone this repository or download a snapshot of it directly inside your project's `Assets` or `Packages` folder.


## Samples
- [Random Colors](Samples~/RandomColors): simple sample with a UI that shows random colors.
- [Plasma Color Job](Samples~/PlasmaColorJob): sample scene with a plasma effect UI that is updated using the C# Job System.


## How to use
```cs
using Gilzoide.TextureApplyAsync;

// 1. Create a `TextureApplyAsyncHandle` for your texture.
var textureApplyAsyncHandle = new TextureApplyAsyncHandle(myTexture);

// 2. If you want to update your texture every frame,
// schedule the apply handle to update every frame.
textureApplyAsyncHandle.ScheduleUpdateEveryFrame();

// 3. Update the texture data normally.
for (int x = 0; x < myTexture.width; x++)
{
    for (int y = 0; y < myTexture.height; y++)
    {
        myTexture.SetPixel(x, y, Random.ColorHSV());
    }
}

// 4. If you want to update your texture only once,
// schedule a one-shot update.
textureApplyAsyncHandle.ScheduleUpdateOnce();

// 5. Cancel updates if necessary.
// Works for both one-shot and every frame updates.
textureApplyAsyncHandle.CancelUpdates();

// 6. If you ever Reinitialize your Texture, make
// sure to Reinitialize your TextureApplyAsyncHandle
// or your app may crash!!!
myTexture.Reinitialize(width, height);
myTexture.Apply();
textureApplyAsyncHandle.Reinitialize();

// 7. Dispose of the `TextureApplyAsyncHandle` when not needed
// anymore, e.g. inside a component's `OnDestroy`.
textureApplyAsyncHandle.Dispose();
```
