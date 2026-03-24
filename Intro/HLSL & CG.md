# Tech Art: Shaderlab - HLSL & CG
By Kellie Hucker

## Resources
- https://docs.unity3d.com/Manual/Shaders.html
- https://docs.unity3d.com/Manual/writing-shader-writing-shader-programs-hlsl.html
- https://docs.unity3d.com/Manual/shader-performance-debugging.html

---

## ShaderLab
All shaders in Unity are written using **ShaderLab**.

ShaderLab is a wrapper language that:
- Defines shader structure (Properties, SubShader, Pass)
- Exposes material properties to the Inspector
- Wraps GPU shader code written in HLSL or Cg
- Enables Unity to cross-compile shaders across platforms

_In short, **ShaderLab organizes the shader**, while **HLSL/Cg define the GPU logic**._

**ShaderLab References:**
- https://docs.unity3d.com/Manual/SL-Reference.html
- https://docs.unity3d.com/Manual/SL-ShaderPrograms.html

---

## Cg (Computer Graphics)
Cg is an older high-level shading language developed by NVIDIA. Unity originally used Cg as its primary shader language, especially in the **Built-in Render Pipeline (BRP)**.

### Key Characteristics
- Used in `CGPROGRAM` blocks
- Automatically includes Unity’s built-in shader libraries
- Tightly coupled with the Built-in Render Pipeline
- Syntax is very similar to HLSL

**Even though Cg is deprecated, it is still important because:**
- Many legacy Unity shaders use Cg
- Built-in shaders (like `UI/Default`) are written in Cg-style blocks
- Understanding Cg helps when reading older projects or built-in shader code

---

## HLSL (High Level Shader Language)
HLSL is a high-level shading language developed by Microsoft for Direct3D. It is the **modern standard** used by Unity across all current render pipelines.

### Key Characteristics
- Used in `HLSLPROGRAM` blocks
- Requires explicit includes (`#include`) for shader libraries
- Fully supported in **URP, HDRP, and modern Unity workflows**
- More flexible and pipeline-agnostic than Cg

### Benefits
- Easier to maintain and scale
- Works across multiple render pipelines
- Aligns with modern graphics APIs and workflows
- Encourages explicit, readable shader structure

**HLSL References:**
- https://docs.unity3d.com/Manual/SL-BuiltinIncludes.html
- https://vfxdoc.readthedocs.io/en/latest/shaders/hlsl/
- https://en.wikipedia.org/wiki/High-Level_Shader_Language

---

## HLSL vs Cg
HLSL and Cg are very similar in syntax, but differ in usage and pipeline support.

### Key Differences
| Feature | Cg | HLSL |
|--------|-----|------|
| Status | Legacy / Deprecated | Modern Standard |
| Shader Block | `CGPROGRAM` | `HLSLPROGRAM` |
| Includes | Automatic (built-in) | Manual (`#include`) |
| Pipeline Support | Built-in Render Pipeline only | URP, HDRP, Built-in |
| Flexibility | Limited | High |

### Summary
- Cg represents Unity’s **legacy shader workflow**, with convenient automatic includes but limited to older pipelines.
- HLSL represents the **modern shader workflow**, requiring more explicit setup but providing greater flexibility, scalability, and compatibility across render pipelines.

---

## Technical Artist Notes

- You will encounter **Cg in legacy shaders and built-in Unity shaders**
- You will use **HLSL for all modern shader development**
- Understanding both is important for:
  - Debugging existing projects
  - Migrating shaders between pipelines
  - Building production-ready shader systems

---

## Quick Rule of Thumb
- Built-in Render Pipeline → Cg (legacy support)
- URP / HDRP → HLSL (modern standard)



 


