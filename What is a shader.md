# Tech Art: What Is A Shader
By Kellie Hucker


## What Is A Shader? 
A shader is a small program that runs on the GPU (Graphics Processing Unit) and controls how 2D/3D objects are rendered on screen. It's responsible for calculating things like lighting, color, and textures, determining the final visual appearance of objects. Think of it as a set of instructions that tells the GPU how to draw each pixel of a 2D/3D scene. 

## What they do:
Shaders are like mini-programs that manipulate the appearance of graphical data. They tell the GPU how to calculate the color of each pixel, based on factors like lighting, textures, and the object's properties. 

## Where they run:
Shaders are designed to run on the GPU, which is a specialized hardware unit designed for fast graphical calculations. This allows for efficient and complex visual effects without slowing down the rest of the computer. 

## Types of shaders:
There are different types of shaders, each with a specific role:
- **Vertex Shaders:** These shaders process individual vertices (points) of a 3D object, transforming them into their correct position on the screen and passing the data to fragment shaders. 
- **Fragment Shaders (or Pixel shaders):** These shaders process individual pixels, determining their final color by applying lighting, textures, and other visual effects. 
- **Geometry Shader:** (Rare in Unity) Takes whole primitives (triangles) as input and can output new geometry. Not widely supported.
- **Compute Shaders:** These shaders can perform general-purpose computations, not necessarily related to graphics, and can be used for things like particle effects or simulations. 
- **Tessellation Shader:** Splits meshes into finer geometry dynamically. Limited use in Unity (requires DX11+).

#### Unity Shaders: 
- Surface Shaders: Unity-specific abstraction that simplifies lighting integration. Good for standard materials with lighting support
- Vertex/Fragment Shaders: More control than surface shaders. You write both vertex and fragment logic manually
- Unlit Shaders: Don’t respond to light; useful for UI, FX, stylized visuals
- UI Shaders: Used in CanvasRenderer, often unlit, may include stencil/mask support
- Shader Graph: Node-based shader system in Universal/High Definition RP. Creates vertex/fragment shaders without writing code.
- Post-processing Shaders: Used for screen effects (e.g., blur, bloom, color grading) via full-screen quads.
- Compute Shaders: Run arbitrary parallel computation on the GPU. Great for GPGPU tasks (e.g. cloth, boids, heatmaps).

### By Render Pipeline:
#### Built-in Render Pipeline:
- Surface shaders
- Unlit
- Custom vertex/frag
- Legacy lighting

#### URP (Universal Render Pipeline):
- Shader Graph
- HLSL-based unlit/lit shaders

#### HDRP (High Def Render Pipeline):
- Shader Graph
- Complex physically based shaders
- HLSL-only for advanced cases

### Common Shaders:
- **Lit Shader:** Supports lighting, shadows, materials
- **Unlit Shader:** Pure color or texture, no lighting
- **Skybox Shader:** Special projection shader for backgrounds
- **Particles Shader:**  Supports transparency, motion, soft blending
- **UI/Mask Shader:** Includes stencil logic for UI elements
- **Custom FX Shader:**  God rays, outlines, water, holograms
