# Unity - Runtime OBJ Loader
More Extensive Documentation for the Free Unity Runtime OBJ Loader by Aaro4130.

## What is Unity Runtime OBJ Loader (ROL)?
ROL is a script that imports OBJs and their materials from a place on Disk into a unity scene during runtime.

### Features
(From https://forum.unity.com/threads/free-runtime-obj-loader.365884/)
- Ability to load OBJ file from stream
- mtl file loading, and texture loading (dds, crn, jpg, jpeg, png, tga, bmp)
- Triangle, Quad, and Ngon import
- Fast loading times
- Works in both the editor, and runtime*
- Usable in the editor

### Requirements
In order for this to work in runtime, you must go to the "Edit > Project Settings > Graphics" tab, and add "Standard (Specular Setup)" to the always included shaders. This will make your next build take a while.

If you don't do this, the required shader / shader variants won't exist.

- Current Recommended Unity Version: 2018.3.1 or higher

## Installation
Import into Unity from the Unity Asset Store, or here: https://assetstore.unity.com/packages/tools/modeling/runtime-obj-importer-49547

## Usage
(From https://forum.unity.com/threads/free-runtime-obj-loader.365884/)
### Editor
Select the GameObject menu (up top)
Then select "Import from OBJ"

### Runtime
```Csharp
using Dummiesman;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

/** Doesn't have to be run from start, can be used on any trigger. */
public class Startup : MonoBehaviour
{
    void Start()
    {
        //file path
        /** Sample OBJ from 
        * https://www.turbosquid.com/3d-models/free-obj-model-ivysaur-pokemon-sample/1136333 
        * Model .mtl file needs editing to add the texture by default
        */
        string filePath = @"Assets/Ivysaur_OBJ/Pokemon.obj";
        // MTL should be linked in the OBJ file by default, 
        // but if not, you can specify it manually
        string mtlPath = @"Assets/Ivysaur_OBJ/Pokemon.mtl";
        
        var loadedObj = new OBJLoader().Load(filePath, mtlPath);

    }

    void Update()
    {
        
    }
}
```

## Functions
```Csharp
public GameObject Load(Stream input);
```
Loads an OBJ from an input stream.
If there is no mtl file referenced in the input stream (ie by the OBJ file), the default Specular Shader (White) will be attached to the OBJ.

```Csharp
public GameObject Load(Stream input, Stream mtlInput);
```
Loads an OBJ from an input stream, and loads the associated mtl from the mtlInput Stream.

```Csharp
public GameObject Load(string path, string mtlPath);
```
Loads an OBJ from the given path, and the associated mtl from the given mtlPath. 

```Csharp
public GameObject Load(string path);
```
Loads an OBJ from a given path.

## Notes on MTL
*.mtl files refer to the material list that is saved next to the .obj file, they configure a set of Renderers.
Some .mtl files are missing things, or have shader settings misconfigured. It's not ROL that's failing here, we can edit the .mtl to work correctly.

### MTL Keys
Source: http://paulbourke.net/dataformats/mtl/
`newmtl <Material Name>` Creates a new material with the following settings.

`Ka <R> <G> <B>` - Ambient illumination component, RGB Range 0.0-1.0
`Kd <R> <G> <B>` - Diffuse illumination component, RGB Range 0.0-1.0
`Ks <R> <G> <B>` - Specular illumination component, RGB Range 0.0-1.0
`Tf <R> <G> <B>` - Transmission Filter, RGB Range 0.0-1.0
The above also may use Spectral Curves or CIEXYZ colours, see the above source for more info.

`illum <illum_#>` - Choose the illumination model.
| Illum # | Model |
|---|---|
| 0 | Color on and Ambient off|
| 1 | Color on and Ambient on |
| 2	| Highlight on |
| 3 | Reflection on and Ray trace on |
| 4	| Transparency: Glass on <br /> Reflection: Ray trace on |
| 5	| Reflection: Fresnel on and Ray trace on |
| 6	| Transparency: Refraction on <br /> Reflection: Fresnel off and Ray trace on |
| 7	| Transparency: Refraction on <br /> Reflection: Fresnel on and Ray trace on |
| 8 | Reflection on and Ray trace off |
| 9	| Transparency: Glass on <br /> Reflection: Ray trace off |
| 10 | Casts shadows onto invisible surfaces |


`d <factor>` - Dissolve for the material, factor Range 0.0-1.0
`Ns <Exponent>` - Specular Exponent, Range 0-1000

`map_Kd -options <args> <filename>`
Specifies that a color texture file or color procedural texture file is 
linked to the diffuse reflectivity of the material.  During rendering, 
the map_Kd value is multiplied by the Kd value.
 
"filename" is the name of a color texture file (.mpc), a color 
procedural texture file (.cxc), or an image file.
 
The options for the map_Kd statement are listed below. 
``` 
 	-blendu on | off
 	-blendv on | off
 	-cc on | off
 	-clamp on | off
 	-mm base gain
 	-o u v w
 	-s u v w
 	-t u v w
 	-texres value
```
Similar keys for Ambient (`map_Ka`), and Specular (`map_Ks`) mapping

`bump -options <args> <filename>`
Specifies that a bump texture file or a bump procedural texture file is 
linked to the material.
 
"filename" is the name of a bump texture file (.mpb), a bump procedural 
texture file (.cxb), or an image file.

### MTL Format
'#' denotes comments.
```mtl
newmtl my_red
    Material color
    & illumination
    statements

    texture map
    statements

    reflection map
    statement
 
newmtl my_blue
    Material color
    & illumination
    statements

    texture map
    statements

    reflection map
    statement
 
newmtl my_green
    Material color
    & illumination
    statements

    texture map
    statements

    reflection map
    statement
```
Typical organization of .mtl file

### MTL Example
The following imports an Ivysaur model from 
https://www.turbosquid.com/3d-models/free-obj-model-ivysaur-pokemon-sample/1136333
which has 2 materials attached, Outline and Pokemon.
I've added the map_Kd line at the bottom to load the texture.
```mtl
# MTL written from C:\3D_Exchange_Folder\Pokemon.obj
newmtl Outline
Kd 0 0 0
Ns 256
d 1
illum 2
Ka 0 0 0
Ks 0 0 0
newmtl Pokemon
Kd 0.48 0.48 0.48
Ns 256
d 1
illum 2
Ka 0 0 0
Ks 0 0 0
map_Kd Assets/Ivysaur_OBJ/Final_Pokemon_Diffuse.jpg
```

## About This Document
This document has been prepared by PhalanxHead for the purposes of making usage of the Unity Runtime OBJ Loader more clear.

PhalanxHead is not in any way associated with the development of the Project.
