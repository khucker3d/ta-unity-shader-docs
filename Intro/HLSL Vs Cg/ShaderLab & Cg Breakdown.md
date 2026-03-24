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
```
Properties
    {
        [PerRendererData] _MainTex ("Sprite Texture", 2D) = "white" {}
        _Color ("Tint", Color) = (1,1,1,1)

        _StencilComp ("Stencil Comparison", Float) = 8
        _Stencil ("Stencil ID", Float) = 0
        _StencilOp ("Stencil Operation", Float) = 0
        _StencilWriteMask ("Stencil Write Mask", Float) = 255
        _StencilReadMask ("Stencil Read Mask", Float) = 255

        _ColorMask ("Color Mask", Float) = 15

        [Toggle(UNITY_UI_ALPHACLIP)] _UseUIAlphaClip ("Use Alpha Clip", Float) = 0
    }
```
---

### Subshader:
SubShaders allow you to define different GPU settings and shader programs for different hardware, render pipelines, and runtime settings. 

#### Subshader includes: 
- **Tags:** Predefined keys and values to determine how and when to use a given SubShader
```
  Tags
        {
            "Queue"="Transparent"
            "IgnoreProjector"="True"
            "RenderType"="Transparent"
            "PreviewType"="Plane"
            "CanUseSpriteAtlas"="True"
        }
```

- **Stencil:** Command to configure the stencil test, and to configure what the GPU writes to the stencil buffer
```
Stencil
        {
            Ref [_Stencil]
            Comp [_StencilComp]
            Pass [_StencilOp]
            ReadMask [_StencilReadMask]
            WriteMask [_StencilWriteMask]
        }
```

- **Render State Commands:** Sets the render state for that Pass, or within a SubShader block for that SubShader and Passes
```
        Cull Off
        Lighting Off
        ZWrite Off
        ZTest [unity_GUIZTestMode]
        Blend SrcAlpha OneMinusSrcAlpha
        ColorMask [_ColorMask]
```
 
---

### Pass:
A Pass is the fundamental element of a Shader object. It contains instructions for setting the state of the GPU, and the shader programs that run on the GPU.
```
Pass
        {
            Name "Default"
        CGPROGRAM
            // Logic here
        ENDCG
        }
```

#### Pass includes: 
- **Preprocessor Directives:** Fundamental elements of a Shader object that contain instructions for setting the GPU state, and shader programs that run on the GPU
```
            #pragma vertex vert
            #pragma fragment frag
            #pragma target 2.0

            #include "UnityCG.cginc"
            #include "UnityUI.cginc"

            #pragma multi_compile_local _ UNITY_UI_CLIP_RECT
            #pragma multi_compile_local _ UNITY_UI_ALPHACLIP
```
  
  - **Common directives found in UI shaders:**
    - Shader Programs 
    - Pragma Directives
    - (Built-In) Shader Includes
    - Target shader models & GPU features
    - GPU Instancing (UI shaders)

- **Structs** (Vertex & Fragment): Defines a new type of variable representing a set of other variables with native or compound types.
```
            struct appdata_t
            {
                float4 vertex   : POSITION;
                float4 color    : COLOR;
                float2 texcoord : TEXCOORD0;
                UNITY_VERTEX_INPUT_INSTANCE_ID
            };

            struct v2f
            {
                float4 vertex   : SV_POSITION;
                fixed4 color    : COLOR;
                float2 texcoord  : TEXCOORD0;
                float4 worldPosition : TEXCOORD1;
                UNITY_VERTEX_OUTPUT_STEREO
            };
```

- **Declared Cg/HLSL variables:** Shader declares Material properties in a Properties block
```
            sampler2D _MainTex;
            fixed4 _Color;
            fixed4 _TextureSampleAdd;
            float4 _ClipRect;
            float4 _MainTex_ST;
```

---

### Shaders: 
**Vertex Shader:** Shader program that runs on each vertex 
**Logic:** 
- Transform vertex position from object space into “clip space”- used by the GPU to rasterize the object on screen. 
- Pass the input texture coordinate unmodified - we’ll need it to sample the texture in the fragment shader.
```
            v2f vert(appdata_t v)
            {
                v2f OUT;
                UNITY_SETUP_INSTANCE_ID(v);
                UNITY_INITIALIZE_VERTEX_OUTPUT_STEREO(OUT);
                OUT.worldPosition = v.vertex;
                OUT.vertex = UnityObjectToClipPos(OUT.worldPosition);

                OUT.texcoord = TRANSFORM_TEX(v.texcoord, _MainTex);

                OUT.color = v.color * _Color;
                return OUT;
            }
```

**Fragment (AKA Pixel) Shader:** Shader program that runs on every pixel
**Logic:**
- Outputs a color
- Has an SV_Target semantic
- The function frag has a return type of fixed4 (low-precision RGBA color)

_**Tip:** Optimizing fragment shaders is important to ensure overall game performance_

```
            fixed4 frag(v2f IN) : SV_Target
            {
                half4 color = (tex2D(_MainTex, IN.texcoord) + _TextureSampleAdd) * IN.color;

                #ifdef UNITY_UI_CLIP_RECT
                color.a *= UnityGet2DClipping(IN.worldPosition.xy, _ClipRect);
                #endif

                #ifdef UNITY_UI_ALPHACLIP
                clip (color.a - 0.001);
                #endif

                return color;
            }
```


## The 
 ```
// Unity built-in shader source. Copyright (c) 2016 Unity Technologies. MIT license (see license.txt)

Shader "UI/Default"
{
    Properties
    {
        [PerRendererData] _MainTex ("Sprite Texture", 2D) = "white" {}
        _Color ("Tint", Color) = (1,1,1,1)

        _StencilComp ("Stencil Comparison", Float) = 8
        _Stencil ("Stencil ID", Float) = 0
        _StencilOp ("Stencil Operation", Float) = 0
        _StencilWriteMask ("Stencil Write Mask", Float) = 255
        _StencilReadMask ("Stencil Read Mask", Float) = 255

        _ColorMask ("Color Mask", Float) = 15

        [Toggle(UNITY_UI_ALPHACLIP)] _UseUIAlphaClip ("Use Alpha Clip", Float) = 0
    }

    SubShader
    {
        Tags
        {
            "Queue"="Transparent"
            "IgnoreProjector"="True"
            "RenderType"="Transparent"
            "PreviewType"="Plane"
            "CanUseSpriteAtlas"="True"
        }

        Stencil
        {
            Ref [_Stencil]
            Comp [_StencilComp]
            Pass [_StencilOp]
            ReadMask [_StencilReadMask]
            WriteMask [_StencilWriteMask]
        }

        Cull Off
        Lighting Off
        ZWrite Off
        ZTest [unity_GUIZTestMode]
        Blend SrcAlpha OneMinusSrcAlpha
        ColorMask [_ColorMask]

        Pass
        {
            Name "Default"
        CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #pragma target 2.0

            #include "UnityCG.cginc"
            #include "UnityUI.cginc"

            #pragma multi_compile_local _ UNITY_UI_CLIP_RECT
            #pragma multi_compile_local _ UNITY_UI_ALPHACLIP

            struct appdata_t
            {
                float4 vertex   : POSITION;
                float4 color    : COLOR;
                float2 texcoord : TEXCOORD0;
                UNITY_VERTEX_INPUT_INSTANCE_ID
            };

            struct v2f
            {
                float4 vertex   : SV_POSITION;
                fixed4 color    : COLOR;
                float2 texcoord  : TEXCOORD0;
                float4 worldPosition : TEXCOORD1;
                UNITY_VERTEX_OUTPUT_STEREO
            };

            sampler2D _MainTex;
            fixed4 _Color;
            fixed4 _TextureSampleAdd;
            float4 _ClipRect;
            float4 _MainTex_ST;

            v2f vert(appdata_t v)
            {
                v2f OUT;
                UNITY_SETUP_INSTANCE_ID(v);
                UNITY_INITIALIZE_VERTEX_OUTPUT_STEREO(OUT);
                OUT.worldPosition = v.vertex;
                OUT.vertex = UnityObjectToClipPos(OUT.worldPosition);

                OUT.texcoord = TRANSFORM_TEX(v.texcoord, _MainTex);

                OUT.color = v.color * _Color;
                return OUT;
            }

            fixed4 frag(v2f IN) : SV_Target
            {
                half4 color = (tex2D(_MainTex, IN.texcoord) + _TextureSampleAdd) * IN.color;

                #ifdef UNITY_UI_CLIP_RECT
                color.a *= UnityGet2DClipping(IN.worldPosition.xy, _ClipRect);
                #endif

                #ifdef UNITY_UI_ALPHACLIP
                clip (color.a - 0.001);
                #endif

                return color;
            }
        ENDCG
        }
    }
}
```
