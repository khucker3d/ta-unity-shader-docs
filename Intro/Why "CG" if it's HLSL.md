# Tech Art: Why "CG" if it's HLSL
By Kellie Hucker

In Unity, you will often see `CGPROGRAM` and `ENDCG` blocks in shaders, even though the underlying shading language is actually `HLSL`, not `Cg`.

Unity’s shader compilation system is based on a shared `Cg`/`HLSL` syntax. `Cg` (C for Graphics) was a shading language developed by NVIDIA, and it closely mirrored `HLSL` (Microsoft’s High-Level Shading Language). Unity adopted `Cg` early in its development, using `CGPROGRAM` and `ENDCG` as markers to define shader code blocks.

Although `Cg` was deprecated by NVIDIA around 2012–2014, Unity retained the same syntax and block structure for backward compatibility. Under the hood, however, Unity transitioned fully to `HLSL`.

Today, whether you are:
- Writing shaders in URP or HDRP
- Creating Shader Graph custom nodes
- Including shader code via #include

…you are working with `HLSL`, even if the code is wrapped in legacy `CGPROGRAM` blocks.

Modern Unity also supports `HLSLPROGRAM` and `ENDHLSL`, which are the preferred equivalents. Functionally, both approaches are identical in Unity 2018.1 and later, but `HLSLPROGRAM` better reflects current rendering workflows and standards.

---

This is `HLSL` code, even though it's inside a `CGPROGRAM` block.
```
CGPROGRAM
#pragma vertex vert
#pragma fragment frag

float4 frag() : SV_Target {
    return float4(1, 0, 0, 1); // HLSL code!
}
ENDCG
```

---

## `Cg` vs `HLSL` in Unity Shader Development
<img width="763" height="566" alt="Screenshot 2026-03-24 at 16 00 54" src="https://github.com/user-attachments/assets/838af9e1-20f3-4b42-9ea2-4a1d384217a7" />
<img width="763" height="373" alt="Screenshot 2026-03-24 at 16 01 15" src="https://github.com/user-attachments/assets/a6fba4af-8e3b-4091-9633-6622c4548465" />

---

_**Notes:**
- _Unity shaders still use `CGPROGRAM`/`ENDCG` mostly for legacy compatibility_
- _Actual code inside those blocks is 100% `HLSL`, compiled via Microsoft compilers_
- _You can safely use `HLSL` features and syntax—even in blocks marked `CGPROGRAM`_
- `CGPROGRAM` _is a legacy tag from Unity’s Cg era_
- _Inside those blocks, you're writing `HLSL`, because Unity now uses Microsoft’s shader compiler (dxc or FXC)_
- _Unity retains the `CGPROGRAM` tag for backward compatibility; changing it would break millions of existing shaders_
