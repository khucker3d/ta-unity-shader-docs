## Shader Pipeline Overview
```
ShaderLab (Structure / Wrapper)
│
├── Properties (Material Inspector Inputs)
│
├── SubShader (Render Settings + Compatibility)
│   │
│   └── Pass (Rendering Step)
│       │
│       ├── HLSL / Cg Code (CGPROGRAM / HLSLPROGRAM)
│       │   │
│       │   ├── Vertex Shader (per vertex)
│       │   │   - Object Space → Clip Space
│       │   │   - Pass UVs, normals, data
│       │   │
│       │   └── Fragment Shader (per pixel)
│       │       - Sample textures
│       │       - Apply lighting
│       │       - Output final color (SV_Target)
│       │
│       └── GPU Execution
│           - Rasterization
│           - Pixel output to screen
│
└── Fallback Shader (if unsupported)
```
