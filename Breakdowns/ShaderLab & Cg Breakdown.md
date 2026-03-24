# Tech Art: ShaderLab & Cg Breakdown (UI Default)
By Kellie Hucker

---

## Overview
A technical artist breakdown of Unity’s UI/Default shader, explaining how ShaderLab structure and Cg/HLSL code work together to render UI from vertex to pixel.

### Goal:
- Teach how to read and understand a real production shader
- Break down the relationship between material properties and shader code
- Explain how data flows through the GPU pipeline (vertex → fragment)
- Provide a foundation for building and modifying custom UI shaders

#### This breakdown walks through the key parts of a Unity shader:
- **Properties:** How materials expose controls to artists
- **SubShader:** How Unity selects rendering behavior based on hardware and settings
- **Pass:** How rendering steps are executed
- **Shader Programs (Cg/HLSL):** How GPU instructions are written and compiled
- **Vertex Shader:** How geometry is transformed and prepared
- **Fragment Shader:** How final pixel color is calculated
- **Fallback Shader:** How compatibility is handled across different hardware

---

## Shader Breakdown: 

### Properties: 
Properties are stored as part of the material asset. These are generally exposed in the material component for artists to create, edit, and share materials with different configurations.

<img width="670" height="338" alt="_Color (Tint, Color) (1,1,1,1)-20240917-153838" src="https://github.com/user-attachments/assets/5eccf0f5-f135-4c83-83b9-77fd0c77b90c" />

---

### Subshader:
SubShaders allow you to define different GPU settings and shader programs for different hardware, render pipelines, and runtime settings. 

#### Subshader includes: 
- **Tags:** Predefined keys and values to determine how and when to use a given SubShader

  <img width="354" height="194" alt="Queue=Transparent-20240917-153838" src="https://github.com/user-attachments/assets/6e76c6c2-1e24-44f6-a6d1-4b106acebcae" />

- **Stencil:** Command to configure the stencil test, and to configure what the GPU writes to the stencil buffer

  <img width="348" height="199" alt="Stencil-20240917-153838" src="https://github.com/user-attachments/assets/9984d725-7d3c-46dd-8576-a70f98ab3741" />

- **Render State Commands:** Sets the render state for that Pass, or within a SubShader block for that SubShader and Passes

  <img width="363" height="166" alt="Cull Off-20240917-153838" src="https://github.com/user-attachments/assets/8c08e839-6c9a-44c2-a5f6-ef96a56792ae" />
 
---

### Pass:
A Pass is the fundamental element of a Shader object. It contains instructions for setting the state of the GPU, and the shader programs that run on the GPU.

#### Pass includes: 
- **Preprocessor Directives:** Fundamental elements of a Shader object that contain instructions for setting the GPU state, and shader programs that run on the GPU

  <img width="562" height="253" alt="CGPROGRAM-20240917-153838" src="https://github.com/user-attachments/assets/47700583-555a-40b9-bb98-6973754189ca" />
  
  - **Common directives found in UI shaders:**
    - Shader Programs 
    - Pragma Directives
    - (Built-In) Shader Includes
    - Target shader models & GPU features
    - GPU Instancing (UI shaders)

- **Structs** (Vertex & Fragment): Defines a new type of variable representing a set of other variables with native or compound types.

  <img width="352" height="361" alt="struct appdata_t-20240917-153838" src="https://github.com/user-attachments/assets/c23aa8bb-1cd3-442b-8a65-30bb24b88761" />

- **Declared Cg/HLSL variables:** Shader declares Material properties in a Properties block

  <img width="233" height="135" alt="fixed4 _Color;-20240917-153838" src="https://github.com/user-attachments/assets/9764b7f2-0f48-42d3-94bd-3aa50d009dba" />

---

### Shaders: 
**Vertex Shader:** Shader program that runs on each vertex 

<img width="490" height="296" alt="v2f vert(appdata_t v)-20240917-153838" src="https://github.com/user-attachments/assets/28ab3801-77ab-48b8-88b2-67ae8b5761a5" />

**Logic:** 
- Transform vertex position from object space into “clip space”- used by the GPU to rasterize the object on screen. 
- Pass the input texture coordinate unmodified - we’ll need it to sample the texture in the fragment shader.


**Fragment (AKA Pixel) Shader:** Shader program that runs on every pixel

<img width="665" height="300" alt="Screenshot 2024-08-19 at 07 59 04-20240917-153838" src="https://github.com/user-attachments/assets/c4ea9aee-c043-4998-8da1-c060aa6e4533" />

**Logic:**
- Outputs a color
- Has an SV_Target semantic
- The function frag has a return type of fixed4 (low-precision RGBA color)

_**Tip:** Optimizing fragment shaders is important to ensure overall game performance_

 
### Fallback shader: 
If the hardware’s graphics card doesn't support something in the shader code’s subshader, a fallback shader can be assigned to provide a backup shader that Unity will automatically use if the primary shader isn't supported. 

<img width="489" height="311" alt="Screenshot 2025-05-01 at 08 32 23" src="https://github.com/user-attachments/assets/19dfe749-6c74-4d59-bb3d-cf8ca4c183d3" />

_**Tip:** Use a Unity shader type that matches the target hardware’s performance needs. Or an alternative version of your custom shader, which has been successfully tested to render on the low-end target hardware._

_**Note:** For custom UI shaders, I commonly will use Fallback “UI/Default”_
