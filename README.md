---
generated: 2025-08-28 16:56:33
source: Mistral OCR
images: 10
---

## Front page

## Exam information

DMK770025P - MED7 Real Time Computer Graphics

## Handed in by

Achillefs Apostolou
aapost22@student.aau.dk

## Exam administrators

Judi Stærk Poulsen
judi@create.aau.dk
$4+4599402468$

## Assessors

Henrique Galvan Debarba
Examiner
hgd@create.aau.dk
$4+4599407166$
Carlos Mauricio Castano Diaz
Internal co-examinator
cmcd@create.aau.dk
$4+4599402087$

Hand-in information

---

MED7 (CPH) Real-Time Computer Graphics

# Volumetric Rendering Using CT Scanned Data 

Achilles Apostolou Mini Project
![[RTCG-Achillefs (1)_p2_img1.png]]

1. Table of Contents ..... 1
2. Abstract ..... 2
3. Introduction ..... 2
3.1. Visualizing Data - Slices ..... 3
3.1.1. Axis Alignment ..... 3
3.1.2. Camera Alignment ..... 3
3.1.3. Transfer Functions ..... 4
4. Method ..... 5
4.1. Raw Data into 3D Volume ..... 5
4.1.1. 3D Slicer ..... 6
4.1.2. 3D Volume ..... 6
4.2. Ray Marching ..... 7
4.2.1. Camera to Fragment ..... 7
4.2.2. Fragment to Light ..... 8
4.2.3. Final Out custom Node Code ..... 8
4.2.4. Scene, Controls, Build ..... 10
5. Results ..... 10
6. Discussion ..... 11
7. References ..... 12

---

# 2. Abstract 

Volumetric Rendering is used to produce visual representations of 3D data sets, especially those with a volumetric component, like CT and MRI scans. One of Volumetric rendering's fundamental goals is to divide a 3D data set into a number of 2D images, or "slices," that can be viewed on a computer or printed on paper for Medical or Visualization purposes. That process takes place by turning Raw Image Data from a CT-Scan into a Volumetric Material to then be rendered with the help of a Ray Marcher in Real-Time. The Real-Time visualization usually takes place inside a Game Engine using a custom renderer.

## 3. Introduction

Volume Rendering has a lot of applications. In Computer Graphics, we can use it for simulating volumetric effects such as clouds and explosions. In Medical Visualization Volume Rendering is used to parse through data interactively. Volume Rendering is also used in several other industries such as Archeology, Geology, Material Science and BIOlogy as well as many others.

The idea is that Data is presented in the format of a sequence of images, that can be put together - like slices - to form volumetric data. That Volumetric Data is then taken and algorithmically forms an interactive 3D visualization out of it.
![[RTCG-Achillefs (1)_p3_img2.png]]

Figure 1: Volume Rendering for Visualization using Slices [1]

---

# 3.1 Visualizing Data - Slices 

A 3D Volume of data is rendered in the form of distinct slices using alpha blending. Initially, the data is stored in the GPU as a 3D Volumetric Texture that is then separated and parsed through. The order in which those images are rendered, however, is essential to the final image; which means that the slicing needs to be relative to the Camera Position.

### 3.1.1. Axis Alignment

One very important variable that changes based on the Camera Position in relation to the Volumetric Data is the Step Size. The Step Size essentially is responsible for calculating the sampling distribution of the data relatively to how the View Direction changes.
![[RTCG-Achillefs (1)_p4_img3.png]]

Figure 2: Step Size and Sampling Distribution [1]

### 3.1.2. Camera Alignment

The slicing however, can happen relative to the Camera position, and not relative to the absolute Axis alignment of the 3D Volume. This way, we can generate different planes -or slices- based on the View Direction. In order to do that, the slices need to be generated in Real-Time and therefore this calculation needs to happen per-frame. There are many ways to calculate the slicing: 1) It can happen in the CPU, 2) They can be generated inside a Geometry Shader, 3)It can happen by using Custom Clip Planes in the GPU and finally 4)

---

Similar to (3) it can be done by rendering an entire plane on each direction, but just having an Alpha value of Zero outside of the Volume Fragments, so that nothing is rendered outside of the Volumetric Object. This way, the Sampling Density can stay the same, no matter where the camera in relation to object is. [1]

# 3.1.3. Transfer Functions 

A Transfer Function (f) is a concept that is commonly used in Volumetric Rendering, to map data value 'f' to color (RGB) and opacity (Alpha) per fragment. ' $F$ ' is a function that changes depending on the data that is visualized each time, but it usually has a range of values depending on the sampling. This way, certain parts of the data can stand out more in the Volume and since the range can be manipulated in Real-Time, different parts of the Data is visualized for different applications.
![[RTCG-Achillefs (1)_p5_img4.png]]

Figure 3:Transfer Function 'f' [1]

---

# 4. Method 

In order to make this Real-Time Medical Visualization application, there were two different parts that needed to be implemented. The first part was to get a medical Raw Data file and turn it into a 3D Volumetric Texture. The second part was to create a custom simple Ray Marcher to render that Data using a Game Engine in Real Time.

### 4.1. Raw Data into 3D Volume

The first step was to find a Raw Data file. For this, either an MRI or a CT-Scan could be used. Those data files usually come in either the .NRRD or .DICOM file formats. For this project, the CT-chest.nrrd file was used containing data from a male's chest CT-Scan.

There are several software platforms for this type of data handling and medical image analysis and visualization. The most notable are 3D Slicer, MITK, OsiriX, Amira, ImageJ, MeVisLab as well as others [10]. Each have their strengths and weaknesses and the best choice depends on the requirements and goals of each respective project. For this project, 3D Slicer was used to handle and segment the raw data.
![[RTCG-Achillefs (1)_p6_img5.png]]

Figure 4: 3D Slicer interface. Handling CT-Scanned Data.

---

# 4.1.1. 3D Slicer 

3D Slicer is a very strong software that has many applications and tools. The most important Module for this kind of data handling, is "Sampling". In the sampling tab, editing tools such as "paint", "threshold" and "scissors" can be used to select certain parts of the data to be visualized, and discard the rest, or segment them in different categories.

When having selected the data of interest, the option "Show 3D" can be selected, to generate a 3D mesh that can be then exported as a .sll file. For this project's goal however, a sequence of images from one of the views would be enough.

By going to Utilities -> Screen Capture and then Selecting which Orthographic View to focus on, the software could export either a video or an image sequence. [5]

### 4.1.2. 3D Volume

In order to create a 3D Texture in Unity, the workflow is usually fairly simple. However, it can only be created by using a single image file and not an image sequence. Therefore the sequence needed to be turned into an Image Atlas (Grid). [6]

64 Images were exported from 3D Slicer to be turned into an 8x8 Atlas. Then Adobe Photoshop was used to turn the sequence into a single Image File. Using Photoshop's batch cropping automated the procedure.
![[RTCG-Achillefs (1)_p7_img6.png]]

Then, Unity was responsible to turn the Atlas into a 3D Texture.

- Import the Image file in Unity as texture
- Change the Texture Shape to 3D Texture
- Change the Rows and Columns to 8 (64 Images)
- Alpha Source: Input Texture Alpha
- Wrap Mode: Clamp
- Apply

---

# 4.2. Ray Marching 

In computer graphics, Ray Marching is used to render implicit surfaces. An implicit surface is a mathematical function that describes a set of points in 3D Space instead of tesselating the surface into triangles, as it is done in traditional polygon-based rendering.

For this project, a very simple Ray Marcher was made in Unity's shader graph to parse through the volumetric object. This Custom Ray Marcher was based on the Ray Marcher implementations of Matias Lavik[3] and Dylan Meville[4].

The scene essentially contains 3 Game Objects: The Main Camera, the Volumetric Object and the Directional Light.

### 4.2.1. Camera to Fragment

![[RTCG-Achillefs (1)_p8_img7.png]]

Figure 5: ShaderGraph: Ray Marching from camera to fragment.

---

Rays are starting from the camera, towards the Volumetric Object, intersects the box, steps through it in small increments and samples the density that's inside the Volume per fragment to accumulate the total Volumetric Density. To do that, the float variables numSteps, stepSize and densityScale are used.

# 4.2.2. Fragment to Light 

![[RTCG-Achillefs (1)_p9_img8.png]]

Figure 6: ShaderGraph: Ray Marching from fragment to light.
For every pixel that the density is sampled from, a ray is being cast towards the light of the scene, to accumulate how much light would reach that pixel.

### 4.2.3. Final Out Custom Node Code

The following code is the HLSL code inside the Final Out Custom Node that takes all the inputs mentioned above (for both the lighting and the volume marching) and outputs the Final Light Ray as well as the Transmission.

---

```
float transmission = 0;
float transmittance = 1;
float lightAccumulation = 0;
float finalLight = 0;
float density = 0;
for(int i=0; i < numSteps; i++){
    rayOrigin +=(rayDirection*stepSize);
    float3 samplePos = rayOrigin+Offset;
    float sampleDensity = tex3D(Volume, samplePos).r;
    density += sampleDensity*densityScale;
//~~
//light loop
//--
    float3 lightRayOrigin = samplePos;
    for(int j=0; j< numLightSteps; j++){
        lightRayOrigin += lightDir*lightStepSize;
        float lightDensity = tex3D(Volume, lightRayOrigin).r;
        lightAccumulation += lightDensity*densityScale;
    }
//~~
    float lightTransmission = exp(-lightAccumulation);
    float shadow = darknessThreshold + lightTransmission * (1.0
-darknessThreshold);
    finalLight += density*transmittance*shadow;
    transmittance *= exp(-density*lightAbsorb);
}
transmission = exp(-density);
final_out = float3(finalLight, transmission, transmittance);
//return float3(finalLight, transmission, transmittance);
```

Figure 7: Code snippet for the main Node in the Shader Graph.

---

# 4.2.4. Scene, Controls, Build 

In order to Move around and inspect the Object in real time these simple C\# scripts have been made. Two sliders were implemented to change the scaling of the cube (so that it cuts through the Volume and changes what is visible and what is not). A simple control for the directional light has also been implemented, in such a way that by dragging the middle mouse button, the Light Direction rotates.
![[RTCG-Achillefs (1)_p11_img9.png]]

Figure 8: Code snippets for the Controls of the Objects.

## 5. Results

![[RTCG-Achillefs (1)_p11_img10.png]]

The Final Scene is a combination of all the theory and methods explained above. The Result is a very simple scene that can manipulate Volume Data in Real-Time. It's Important to Notice that this data started as a real CT-Scan and can now be Visualized using a Ray Marcher.

---

# 6. Discussion 

This project was very interesting to work with. Aside from the ones used for this implementation, there isn't much documentation or similar projects available [3, 4]. Finding Volumetric Data in the first place was difficult, as many medical projects that used it have been deprecated. However, there is a lot of medical software specifically created for this purpose. On top of that, Unity's ShaderGraph has become very powerful and makes implementations like this much easier than they used to be. It's important to notice that the more it advances, the more these implementations need to be adjusted (i.e. in ShaderGraph 12.1.8, some nodes have been deprecated. One of them is the "World Space Light Dir". In order to surpass it, a normalized vector of the global transform of the Directional Light object has been created to do that instead).

This Ray Marcher can fire rays from the camera to the volumetric object and then to the light. This implementation was based on a basic understanding of Volumetric Rendering and showcases its abilities. The algorithm has a lot of room for improvement in order to be product-ready for medical or computational research. As this area of visualization however has so many uses, it's absolutely worth it to spend time and optimize this implementation.

In Future Work there is a lot that could potentially be improved. The next step for this project would be to play around with Multidimensional Transfer Functions. This way, the data would not just be visualized, but also manipulated and segmented in Real-Time in the Game Engine.

---

# 7. References 

[1]: Cem Yuksel : Interactive Graphics 25 - Volume Rendering (School of Computing, University of Utah) https://www.youtube.com/watch?v=y4KdxaMC69w
[2]: Raw Data Example FIle from 3DSlicer
https://www.slicer.org/wiki/File:CT-chest.nrrd
[3]: Volume Renderer (mlavik1/UnityVolumeRendering)
https://matiaslavik.wordpress.com/2020/01/19/volume-rendering-in-unity/
https://github.com/mlavik1/UnityVolumeRendering
[4] DMVolumeRendering (/DMeville/DMVolumeRendering)
https://github.com/DMeville/DMVolumeRendering
[5] Jan Witowski: 3D Slicer Tutorial
https://www.youtube.com/watch?v=QTEti9aY0vs\&list=PLeaIM0zUlEqswa6Pskg9uMq15LiW WYP39
[6] https://docs.unity3d.com/ScriptReference/Texture3D.html https://docs.unity3d.com/Packages/com.unity.shadergraph@5.6/manual/Getting-Started.ht ml
[7] IT-MEDEX: Volume Rendering Engine
https://bitbucket.org/it-medex/volume-rendering-engine/src/master/
[8] https://www.radiantviewer.com/dicom-viewer-manual/3d-volume-rendering.html
[9] printspace3d: Make a 3D Print from Your MRI or CT Scan
https://www.printspace3d.com/make-a-3d-print-from-your-mri-or-ct-scan/
[10] https://teem.sourceforge.net/nrrd/volvis/index.html
[11] http://users.csc.calpoly.edu/ zwood/teaching/csc572/final11/ghflores/design.html