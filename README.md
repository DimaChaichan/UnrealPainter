# Unreal Painter
<p align="center">
  <img src="./assets/logo_big.png" width="250"/>
</p>

This project is an easy to use mesh painter for the Unreal Engine.\
You can use it for in-game painting or as a damage system. There are two modes Dynamic or Baked.\
You can define textures (RenderTargets) and then use it in the material.

Inspired by [Ryan Brucks](https://shaderbits.com/blog/uv-dilation) and [Tom Looman](https://www.tomlooman.com/unreal-engine-render-character-wounds/)

![Demo 1](./assets/demo_01.gif)\
![Demo 2](./assets/demo_02.gif)

## Install 
Simply add this project to your content folder in your Unreal Project. 

## How to Use
Simply add the **BC_PaintSystem** component to your Actor.\
![demo_component.png](Assets%2Fdemo_component.png)\
Now the meshes and the maps to be used must be set. The **SetupMeshes** function is used for this.\
The function requires a Meshes array as **BST_PaintSetupData**.
Struct **BST_PaintSetupData:**
- Mesh: On which mesh to paint
- Material Index: Which material index should be used
- Unwrap: Is used when the PaintSystem is bundled. See here: [Baker](#Use Baker)
- UVsOffset: If your UVs are outside the normal 0-1 range. e.g. MetaHumen Body
- Maps: BST_PaintMapData, Which maps should be used

Struct **BST_PaintMapData:**
- Name: Map name
- Type: Color or Normal
- Size: RenderTarget Size

 ![demo_setup_meshes.png](Assets%2Fdemo_setup_meshes.png)
 
### Dynamic or Baked
There is a dynamic and a baked mode. \
In dynamic mode, a new unwrap is always created based on the current location position. 
In baked mode, you create your own unwrap based on the pre-skinned data. You can use the Baker for this. \
Dynamic mode requires a SceneCaptureCamera and is therefore more precise. But it is also more expensive in performance. 

#### Setup Dynamic Mode 
To use Dynamic mode, you must do the following:
- Add a **SceneCaptureCamera2D**
- Call **SetupSceneCapture** add the the new SceneCaptureCamera2D
- Set the bool Dynamic in the **BP_PaintSystem** Component

Now you can start painting!
With the setting **Delay Between Dynamic Unwrap** you can control how long the last unwrap capture may have been.
For example, if you want to paint on the same unwrap several times in a frame, this will help with the performance.

# ![demo_dynamic.png](Assets%2Fdemo_dynamic.png)

#### Use Baker
There is a Baker Actor to facilitate the creation of the baked unwrap. \
The actor is called **BP_CoreUVBaker**. Simply drag it into the scene. \
Now set the mesh of which you want to have the unwrap and then click on. \
**Capture SkeletonMesh** or **Capture StaticMesh**, depending on what you want to capture. \
# ![demo_baker.png](Assets%2Fdemo_baker.png)

The unwrap is now created in the Texture Target UVs. \
Now click on Add Dilate to get an extension of the UVs. \
Now you can click on **Save Dilate Texture** and the unwrap will be created in the folder of the Actor. \
This unwrap can now be used in **Setup Meshes**. 

## Paint
To paint, you only need to call the function **Paint with Direction**.
# ![demo_paint.png](Assets%2Fdemo_paint.png)

#### Function Inputs
- Map: Name of the Map.All maps with the same name are used. No matter which mesh the map has.
- Location: World Location
- Normal: The direction in which you want to project.
- Bone Name: Is only used for SkeletonMesh that are baked. The hit location is calculated back to the preskinned location.
- Rotation: Project Rotation
- Size: Size in World Units
- Opacity: Opacity
- Brush: Project texture
- Bush Shape: You can project with a box or a capsule Shape.
- Brush Length: How long the bush should be. So you can also paint the back of meshes.

#### Function Outputs
The output forwards all inputs, making it easier to build chains of nodes. \
You also get the painted render targets.

## Demo Files
In the folder Demo you will find some examples of how to use the system. \
All blueprints react to PointDamage.

## Tested
Currently only tested on PC with Unreal 5.3. \
But I think it should run in most versions of Unreal and with the baked version it should run on most devices.