![Title Image lol](https://d3kjluh73b9h9o.cloudfront.net/optimized/4X/4/a/d/4ade2ab5b0a1dc722069a5616882a0152426432a_2_690x390.png)

> [!WARNING]
> **Real-time lighting does not work in this template.** All lighting must be baked. Movable lights will have no effect without additional configuration.
>
> If you are converting an existing project to use this template, **back up your project first**. Disabling Lumen and Virtual Shadow Maps can break existing lighting setups and is not easily reversible.

# Perfectly Optimized — UE5 Performance Template

A minimal Unreal Engine 5 starter template built for **low-end hardware**. Replaces UE5's default ray-traced, virtualized rendering pipeline with a lightweight forward-style configuration suited to older GPUs, integrated graphics, and budget laptops.

> If you've been held back by Lumen, Nanite, or DX12 overhead — this is your starting point.

---

## Why This Exists

UE5's defaults (Lumen GI, Nanite, Virtual Shadow Maps, DX12) are designed for high-end GPUs. On anything older or less capable, they tank frame rates and cause unpredictable stutters. This template disables those systems upfront so you're building on a **stable, predictable baseline** — similar to what you'd get from Unity's forward renderer.

---

## What's Changed

### Renderer
| Setting | Default UE5 | This Template |
|---|---|---|
| RHI | DirectX 12 | **Vulkan** |
| Anti-Aliasing | TAA | **FXAA** |
| MSAA | Enabled | **Disabled** |
| Global Illumination | Lumen | **Disabled (baked)** |
| Reflections | Lumen | **Disabled** |
| Shadows | Virtual Shadow Maps | **Disabled** |
| Geometry | Nanite | **Disabled** |

### Post Processing
Unnecessary post-process passes stripped out. No bloom, lens flare, or ambient occlusion overhead by default.

### Package / Build
- Prerequisites installer removed
- Editor content excluded from cook
- Movies excluded from staging

---

## Intended For

- Older PCs, budget laptops, integrated GPUs
- VR and mobile targets
- Stylized, low-poly, or small-scale 3D projects
- Performance-sensitive prototypes
- Developers coming from Unity who want a familiar rendering cost profile

## Not Intended For

- Cinematic or high-fidelity rendering
- Nanite/Lumen showcases
- Projects requiring dynamic global illumination

---

## Contents

| | |
|---|---|
| Blueprints | None |
| Maps | 1 (lightweight sample scene) |
| Input Bindings | None |
| Network Replication | No |
| Platform Support | Windows only |

---

## Getting Started

```
1. Clone or download this repository (or grab it from Fab)
2. Open the .uproject in Unreal Engine 5
3. Build lighting (required — Lumen is disabled)
4. Start building
```

Baked lighting is **required** since Lumen is off. If you skip the lighting build, your scene will be unlit.

---
