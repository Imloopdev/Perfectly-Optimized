![Perfectly Optimized](https://d3kjluh73b9h9o.cloudfront.net/optimized/4X/4/a/d/4ade2ab5b0a1dc722069a5616882a0152426432a_2_690x390.png)

<div align="center">

# Perfectly Optimized

**A barebones Unreal Engine 5 starter template for low-end hardware**

![Unreal Engine](https://img.shields.io/badge/Unreal%20Engine-0E1128?logo=unrealengine)
![Renderer](https://img.shields.io/badge/Renderer-Deferred%20-2E7D32)
![Lighting](https://img.shields.io/badge/Lighting-Baked%20%2B%20Direct%20Dynamic-F9A825)
![RHI](https://img.shields.io/badge/RHI-Vulkan%20(DX12%20%2F%20DX11%20fallback)-AC162C)
![Price](https://img.shields.io/badge/Price-Free-blue)

[Fab Listing](https://www.fab.com/listings/060add23-7044-466c-9790-adfac5781d04) · [Forum Thread](https://forums.unrealengine.com/t/perfectly-optimized-free-template-made-for-older-hardware/2684738) · [Report an Issue](https://github.com/Imloopdev/Perfectly-Optimized/issues)

</div>

> [!IMPORTANT]
> **This template uses Unreal's basic _deferred_ renderer by default**, so almost every engine feature stays available. Building something for **VR**, or a **stylized low-poly** game with baked lights? You can switch to **forward shading** for a FPS boost. See [Switching to Forward Shading](#optional-switching-to-forward-shading).

> [!WARNING]
> **There is no dynamic global illumination.** Lumen is fully disabled. Movable lights **do** kinda work, but they only provide direct light and shadows. For indirect light you must **bake lighting** (Build → Build Lighting Only).
>
> **Back up your project before converting it to these settings.** Switching an existing Lumen project to baked lighting will break things. See [Migrating an Existing Project](#migrating-an-existing-project) for a proper guide on how to switch without breaking your project.

---

## Table of Contents

- [What This Is (and Isn't)](#what-this-is-and-isnt)
- [Should You Use This?](#should-you-use-this)
- [What You Give Up](#what-you-give-up)
- [What's Included](#whats-included)
- [Requirements and Platform Support](#requirements-and-platform-support)
- [Getting Started](#getting-started)
- [First Launch: What to Expect](#first-launch-what-to-expect)
- [Lighting Workflow (Baked Lighting)](#lighting-workflow-baked-lighting)
- [Settings Reference](#settings-reference)
- [Choosing a Graphics API (RHI)](#choosing-a-graphics-api-rhi)
- [Re-enabling Disabled Features](#re-enabling-disabled-features)
- [Optional: Switching to Forward Shading](#optional-switching-to-forward-shading)
- [Migrating an Existing Project](#migrating-an-existing-project)
- [Packaging Your Game](#packaging-your-game)
- [Profiling and Measuring Performance](#profiling-and-measuring-performance)
- [Benchmarks](#benchmarks)
- [Troubleshooting](#troubleshooting)
- [FAQ](#faq)
- [Known Issues](#known-issues)
- [Version Matrix](#version-matrix)
- [Changelog](#changelog)
- [Support and Contributing](#support-and-contributing)
- [License](#license)

---

## What This Is (and Isn't)

This template is a **preconfigured project**: a set of optimal engine settings on top of an empty map. It doesn't have custom code, plugins or a "increase fps button". Everything it does is visible in `Config/DefaultEngine.ini` and `Config/DefaultGame.ini`, and every change is documented in the [Settings Reference](#settings-reference) below.

UE5's default project is built to use **Lumen** (dynamic global illumination), **Nanite** (virtualized geometry), **Virtual Shadow Maps** and **Temporal Super Resolution**. These look like straight eye candy, but not everyone has an RTX 5090 in their back pocket. On older GPUs, integrated graphics and budget laptops they are often the main reason a nearly empty scene runs badly.

This template disables them and replaces them with the cheaper alternatives Unreal has had for years: **baked lightmaps, reflection captures, shadow maps, LODs and FXAA**. It also turns off features that cost memory, disk space or shader compile time, such as hardware ray tracing support, mesh distance fields and Substrate.

It keeps Unreal's **deferred renderer**, so screen-space effects, many dynamic lights and dense scenes all keep working the way you expect.

**What it is:**
- A clean starting point whose baseline cost is much lower than the stock UE5 project.
- A documented reference for *which* settings matter for performance and *why*.

**What it isn't:**
- A magic "optimize" button. Your content (the shit actually in your project) still decides your final performance.
- A guaranteed speed up for an existing project. Converting a project built around Lumen and Nanite takes real work. See [Should You Use This?](#should-you-use-this)

---

## Should You Use This?

### 🟢 Good fit
- **New projects** targeting older PCs, budget laptops or integrated GPUs (Intel UHD/Iris Xe, AMD APUs).
- Stylized, low-poly or small/medium 3D games.
- Games where lighting is mostly static, with a few movable lights.
- **VR projects.** Consider [switching to forward shading](#optional-switching-to-forward-shading) for MSAA.
- Developers coming from Unity's pipelines who want a familiar performance.
- Prototypes that should run well on any machine on your team.

### ⚠️ Fit with caveats
- **Converting an existing Lumen/Nanite project.** Expect to rebuild your lighting See [Migrating an Existing Project](#migrating-an-existing-project).
- **Large open worlds.** Baked lighting over huge areas means long build times. Consider fully dynamic skylight, no baking.

### ❌ Not a good fit
- Cinematic or photoreal rendering.
- Games that need **dynamic global illumination**, such as fully destructible environments, a real-time day/night cycle, or maps that need realistic indirect light.
- Nanite or Lumen showcases (duh).

---

## What You Give Up

Compared with the stock UE5 project:

| Feature | Status | Replacement / Workaround |
|---|---|---|
| **Lumen GI** |  Disabled | Baked lightmaps|
| **Lumen reflections** |  Disabled | Reflection captures. Screen space reflections can be enabled ([see below](#screen-space-reflections)). |
| **Nanite** |  Disabled | Traditional static mesh LODs (auto generated or manual) |
| **Virtual Shadow Maps** |  Disabled | Cascaded shadow maps and regular shadow maps. Stationary lights use baked distance field shadows. |
| **Temporal Super Resolution** |  Not the default (FXAA) | Switch to TAA or TSR in Project Settings if you really need it |
| **Hardware ray tracing** |  Disabled | None needed for this pipeline. Can be re-enabled. If you want GPU lightmass|
| **Mesh distance fields** |  Disabled | Turns off distance field AO and shadows. Enable it from project settings if you need those. |
| **Substrate materials** |  Disabled | Legacy material model. For better performance |
| **GPU Lightmass** | ⚠️ Needs ray tracing support | Use CPU Lightmass (the default), or temporarily enable ray tracing for bakes |
| Bloom, motion blur, lens flare, auto exposure, ambient occlusion | ⚠️ Off **by default** only | Turn them on per Post Process Volume. They are still supported. |

Everything else (decals, translucency, fog, volumetric fog, sky atmosphere, post-process materials, custom depth/stencil, Niagara, skeletal meshes, landscape, blah blah blah) works as normal.

> [!NOTE]
> If you [switch to forward shading](#optional-switching-to-forward-shading), you give up **additional** features, such as screen space reflections, SSAO and G-buffer based post process effects. They are listed in that section.

---

## What's Included

| | |
|---|---|
| **Maps** | `L_Empty`, an empty startup map |
| **Blueprints** | None |
| **C++** | None (Blueprint-only project) |
| **Input bindings** | None. Enhanced Input is set as the default input system. |
| **Network replication** | Not applicable (no gameplay code) |
| **Plugins enabled** | Modeling Tools Editor Mode (editor only, not packaged) |


---

## Platform Support

| Platform | Default API | Shader formats built | Notes |
|---|---|---|---|
| **Windows** | Vulkan | Vulkan SM6, DX12 SM6, DX11 SM5 | DX12 and DX11 are fallbacks for drivers with Vulkan problems |
| **Linux** | Vulkan | Vulkan SM6 | |
| **macOS** | Metal | Metal SM6 | |
| **Android / Mobile / standalone VR** | n/a | n/a | A separate mobile version is available on Fab |

> [!NOTE]
> **About SM6 and older GPUs.** Shader Model 6 needs a modern-ish GPU driver. If a very old GPU cannot run the Vulkan SM6 or DX12 SM6 path, launch with `-dx11` to use the DX11 SM5 renderer. See [Choosing a Graphics API](#choosing-a-graphics-api-rhi).

---

## Getting Started

### Option A: From Fab (recommended)

1. Open the **Epic Games Launcher** → **Fab Library** (or visit the [Fab listing](https://www.fab.com/listings/060add23-7044-466c-9790-adfac5781d04)).
2. Find **Perfectly Optimized**, choose the **latest** version, then click **Create Project**.
3. Open the project. See [First Launch](#first-launch-what-to-expect).

### Option B: From GitHub

1. **Download** or **clone** the repository:
   ```bash
   git clone https://github.com/Imloopdev/Perfectly-Optimized.git
   ```
2. *(Optional)* Rename the folder and the `.uproject` file to your game's name.
3. Right-click `PerfectlyOptimized.uproject` → **Switch Unreal Engine version** → choose **5.8** if prompted.
4. Double-click the `.uproject` to open it.

### After creating your project (important)

These steps personalize the project instead of a copy of the template:

1. **Regenerate the Project ID.** Every copy of the template starts with the same `ProjectID`, so change it
   - Close the editor, open `Config/DefaultGame.ini`, and replace the value of `ProjectID=` with a new random 32-character hexadecimal string. Any GUID generator works ig. Remove the dashes. You can check the result in **Project Settings → Project → Description → Project ID**.
2. **Set your project name, company and description** in **Project Settings → Project → Description**.
3. **Decide on your renderer.** Keep deferred (recommended), or [switch to forward shading](#optional-switching-to-forward-shading) now if you are making a VR or stylized game. Switching early is much easier than switching late.
5. **Build lighting** once: **Build → Build Lighting Only**.

---

## First Launch: What to Expect

1. **Shader compilation.** On the first open, Unreal compiles the shaders this project needs and caches them in `DerivedDataCache/`. This is a one-time cost per machine and engine version. Later launches are much faster. This template turns off ray tracing, distance fields, Substrate and other features specifically to **reduce** the number of shaders compiled.
2. **"Lighting needs to be rebuilt" message.** This is expected. Run **Build → Build Lighting Only**.
3. **The map is empty.** `L_Empty` is intentionally blank. Add a **Directional Light**, a **Sky Light**, **Sky Atmosphere** and **Exponential Height Fog** (or use **Window → Env. Light Mixer**) to get started. Then follow the [Lighting Workflow](#lighting-workflow-baked-lighting).

> [!TIP]
> The editor viewport always costs more than your packaged game. When judging performance, use **Standalone Game** or a packaged build. See [Profiling](#profiling-and-measuring-performance).

---

## Lighting Workflow (Baked Lighting)

Without Lumen, lighting in this template comes from two techniques:

- **Baked (precomputed) lighting:** Lightmass calculates direct and indirect light ahead of time and stores it in lightmap textures. It is basically free at runtime.Gives the biggest FPS boost one can get.
- **Dynamic direct lighting:** Movable lights light and shadow the scene in real time, but **without indirect light**.

### 1. Light Mobility

Every light has a **Mobility** setting. It is the most important lighting decision you make in this template.

| Mobility | Runtime cost | Bounce light | Shadows | Can move or change? | Use for |
|---|---|---|---|---|---|
| **Static** | Almost free | ✅ Baked | Baked only, no shadows from moving objects | ❌ | Most environment lights |
| **Stationary** | Low to medium | ✅ Baked | Baked for static objects, dynamic for movable ones | Color and intensity only | Sun, key lights near characters |
| **Movable** | Highest | ❌ None | Fully dynamic | ✅ | Flashlights, muzzle flashes, moving lights |

> [!NOTE]
> At most **4 stationary lights** can overlap any point (one per shadow channel). If more overlap, the extra ones fall back to dynamic shadows, which cost more, and the editor shows a red ✕ icon. Check **View Mode → Stationary Light Overlap** in the viewport.

### 2. Lightmap UVs

Every **static** mesh that receives baked lighting needs a **non-overlapping lightmap UV channel** (usually UV channel 1).

- On import, keep **Generate Lightmap UVs** enabled (it is on by default).
- In the Static Mesh Editor, check **Details → General Settings → Light Map Coordinate Index** and **Light Map Resolution**.
- Use **View Mode → Optimization Viewmodes → Lightmap Density** to check texel density. Aim for even coverage, and save resolution on objects the player won't look at closely.

Bad or overlapping lightmap UVs cause the classic baked-lighting artifacts: black splotches, light leaking and visible seams.

### 3. Lightmass Importance Volume

Place a **Lightmass Importance Volume** around the playable area. Lightmass then focuses its bounce-light calculation there instead of the whole map, which makes builds faster and gives better quality where it matters.

### 4. Reflections

With Lumen reflections disabled, reflections come from:
- **Sphere / Box Reflection Captures:** place them throughout your level, especially in rooms with different lighting. They capture a static cubemap.
- **Sky Light:** provides the fallback reflection and ambient light.
- *(Optional)* **Screen space reflections:** see [Re-enabling Features](#screen-space-reflections).
- *(Optional, expensive)* **Planar Reflections:** for mirrors or water only, where you really need them.

Reflection captures update when you build lighting. You can also refresh them manually: select a capture → **Update Captures**.

### 5. Building Lighting

- **Build → Build Lighting Only** bakes lightmaps.
- **Build → Lighting Quality** sets the quality: use **Preview** while iterating and **Production** for final builds.
- Settings that affect the whole level are under **World Settings → Lightmass** (number of bounces, indirect lighting quality, static lighting level scale).

> [!NOTE]
> **GPU Lightmass** (a much faster baker) requires *Support Hardware Ray Tracing*, which this template turns off. To use it:
> 1. Enable **Project Settings → Rendering → Hardware Ray Tracing → Support Hardware Ray Tracing** and restart.
> 2. Enable the **GPU Lightmass** plugin.
> 3. Bake, then turn ray tracing off again if you don't need it at runtime.
>
> CPU Lightmass (the default) works without any of this.

### 6. Unbuilt lighting preview

`r.Shadow.UnbuiltPreviewInGame=True` is set, so levels with unbuilt lighting still show preview shadows during Play-In-Editor. Final quality only appears after a bake.

---

## Settings Reference

Every non-trivial change this template makes, why it was made, and what it costs to change it back. All settings live in `Config/DefaultEngine.ini` unless noted otherwise.

Legend: 🔴 major performance impact · 🟡 moderate · 🟢 minor, cosmetic or compatibility-related.

### Global Illumination and Reflections

| Setting | Stock UE5 | This Template | Impact | Why |
|---|---|---|---|---|
| `r.DynamicGlobalIlluminationMethod` | `1` (Lumen) | **`0` (None)** | 🔴 | Lumen is usually the single largest GPU cost in a UE5 frame. Baked lightmaps replace it. |
| `r.ReflectionMethod` | `1` (Lumen) | **`0` (None)** | 🔴 | Lumen reflections are expensive. Reflection captures and the sky light are used instead. |
| `r.AllowStaticLighting` | `True` | **`True`** | 🟢 | Required for baked lightmaps. |
| `r.Lumen.HardwareRayTracing` | varies | **`False`** | 🟢 | No effect without Lumen; disabled for clarity. |
| `r.MegaLights.EnableForProject` | `False` | **`False`** | 🟢 | MegaLights is a high-end feature. |

### Geometry and Shadows

| Setting | Stock UE5 | This Template | Impact | Why |
|---|---|---|---|---|
| `r.Nanite.ProjectEnabled` | `True` | **`False`** | 🔴 | Nanite has a fixed base cost, and it is slower than traditional LODs on low-end GPUs and for low-poly content. |
| `r.Shadow.Virtual.Enable` | `1` | **`0`** | 🔴 | Virtual Shadow Maps are expensive and designed for Nanite. Cascaded shadow maps are used instead. |
| `r.Shadow.CSMCaching` | `False` | **`True`** | 🟡 | Caches static parts of cascaded shadow maps between frames. |
| `r.GenerateMeshDistanceFields` | `True` | **`False`** | 🟡 | Distance fields are built for every mesh on import. That costs memory, disk space and import time, and only Lumen, distance-field AO and distance-field shadows use them. |
| `r.MeshStreaming` | `True` | **`True`** | 🟢 | Streams mesh LODs to reduce memory. |

### Ray Tracing

| Setting | Stock UE5 | This Template | Impact | Why |
|---|---|---|---|---|
| `r.RayTracing` | varies | **`False`** | 🔴 | Ray tracing support compiles a large extra set of shaders and builds acceleration structures for every mesh, even if nothing uses them. |
| `r.RayTracing.RayTracingProxies.ProjectEnabled` | varies | **`False`** | 🟢 | Ray tracing only. |
| `r.RayTracing.Shadows` | `False` | **`False`** | 🟢 | |
| `r.PathTracing` | varies | **`False`** | 🟡 | The path tracer is for offline rendering only and adds shaders. |

### Shading and Materials

| Setting | Stock UE5 | This Template | Impact | Why |
|---|---|---|---|---|
| `r.ForwardShading` | `False` | **`False`** (deferred) | 🔴 | Deferred handles many lights and dense scenes well and keeps all features. See [Switching to Forward Shading](#optional-switching-to-forward-shading). |
| `r.ClusteredDeferredShading.EnableForProject` | varies | **`True`** | 🟡 | Clustered deferred lighting handles many local lights efficiently. |
| `r.Substrate` | varies | **`False`** | 🟡 | Substrate is a more physically rich material system with a bigger G-buffer and more shader permutations. The legacy material model is cheaper. |
| `r.StaticMesh.DefaultMeshPaintTextureSupport` | varies | **`False`** | 🟢 | Avoids opting every static mesh into mesh-paint textures. |
| `r.MeshPaintVirtualTexture.Support` | varies | **`False`** | 🟢 | Niche feature, so disabled. |
| `r.HeterogeneousVolumes` | varies | **`False`** | 🟢 | Fewer shaders for a niche volumetrics feature. |
| `r.DBuffer` | `True` | **`True`** | 🟢 | Needed for DBuffer decals. They also work if you switch to forward shading. |
| `r.CustomDepth` | `1` | **`1`** | 🟢 | Custom depth/stencil enabled on demand (outlines, masks). |

### Anti-Aliasing and Resolution

| Setting | Stock UE5 | This Template | Impact | Why |
|---|---|---|---|---|
| `r.AntiAliasingMethod` | `4` (TSR) | **`1` (FXAA)** | 🔴 | TSR is high quality but expensive on low-end GPUs. FXAA is nearly free. To trade some speed for less shimmering, switch to TAA (`2`). |
| `r.MSAACount` | `4` | **`2`** | 🟢 | **Has no effect in deferred.** It is preset to ×2 so that MSAA is affordable on low-end GPUs if you [switch to forward shading](#optional-switching-to-forward-shading) and enable MSAA. |
| `r.ScreenPercentage.Default` | `100` | **`100`** | 🟢 | Native resolution. Lower it at runtime for more performance. |

### Post Processing Defaults

These only control the **default** state. You can still enable any of them in a Post Process Volume.

| Setting | Stock UE5 | This Template | Impact | Why |
|---|---|---|---|---|
| `r.DefaultFeature.AutoExposure` | `True` | **`False`** | 🟡 | Predictable exposure while building levels, and saves a pass. |
| `r.DefaultFeature.MotionBlur` | `True` | **`False`** | 🟡 | Expensive and often unwanted. |
| `r.DefaultFeature.AmbientOcclusion` | `True` | **`False`** | 🟡 | Baked lighting already includes ambient occlusion. |
| `r.DefaultFeature.Bloom` | `True` | **`False`** | 🟢 | Cheap, but off for a clean baseline. Enable per Post Process Volume. |
| `r.DefaultFeature.LensFlare` | varies | **`False`** | 🟢 | |
| `r.DefaultFeature.LightUnits` | `1` | **`1` (Candelas)** | 🟢 | Standard UE light units. |

### Virtual Texturing

| Setting | Stock UE5 | This Template | Impact | Why |
|---|---|---|---|---|
| `r.VirtualTextures` | `False` | **`False`** | 🟡 | Virtual texturing adds overhead that small projects don't need. |
| `r.VirtualTexturedLightmaps` | `False` | **`False`** | 🟢 | |
| `r.TextureStreaming` | `True` | **`True`** | 🟢 | Standard texture streaming stays on. |

> [!NOTE]
> The `r.VT.*` and `r.vt.rvt.*` lines in the config have **no effect** while virtual texturing is off. They are left in place so they work out of the box if you turn VT on.

### Forward-Only Settings (no effect in deferred)

These are already set up so that [switching to forward shading](#optional-switching-to-forward-shading) works well right away.

| Setting | Value | What it does when forward shading is on |
|---|---|---|
| `r.EarlyZPass` | `3` | Full depth prepass, which forward shading needs so expensive lighting runs only on visible pixels |
| `r.VertexFoggingForOpaque` | `True` | Height fog calculated per vertex: cheaper, but can look wrong on large, low-poly triangles ([Troubleshooting](#troubleshooting)) |
| `r.MSAACount` | `2` | MSAA sample count, used when anti-aliasing is set to MSAA |

### Platform and Graphics API (`[/Script/WindowsTargetPlatform.WindowsTargetSettings]`)

| Setting | Stock UE5 | This Template | Why |
|---|---|---|---|
| `DefaultGraphicsRHI` | DX12 | **Vulkan** | Vulkan often has lower CPU overhead and fewer stutters on older hardware. **Results vary by GPU and driver**, so profile on your target machines. |
| `TargetedShaderFormats` | DX12 SM6 | **Vulkan SM6 + DX12 SM6 + DX11 SM5** | Keeps fallbacks for machines where Vulkan doesn't work. The editor only compiles shaders for the API it's running on, so the extra formats only add time when you package. |

### Packaging (`Config/DefaultGame.ini`)

See [Packaging Your Game](#packaging-your-game).

---

## Choosing a Graphics API (RHI)

The default on Windows is **Vulkan**. Neither API is universally faster: Vulkan is often better on older AMD hardware and for CPU-bound scenes, while DX12 or DX11 can be more stable on some NVIDIA and Intel drivers. **Test on your target hardware.**

### Switching temporarily (launch arguments)

Add these to the editor shortcut, a Standalone launch or a packaged game's shortcut:

| Argument | API | Notes |
|---|---|---|
| `-vulkan` | Vulkan SM6 | Default |
| `-dx12` | DirectX 12 SM6 | |
| `-dx11` | DirectX 11 SM5 | Most compatible and best for very old GPUs. MSAA isn't supported on DX11 (only relevant with forward shading). |

Example for a packaged game: `MyGame.exe -dx11`

### Switching permanently

**Project Settings → Platforms → Windows → Targeted RHIs / Default RHI.** Restart the editor afterwards. Shaders compile again for the new API.

### Shipping your game with an API choice

Players on problem drivers will thank you for a fallback. Common approaches:
- Ship a second shortcut or launcher option that adds `-dx11`.
- Expose an API option in your settings menu that writes the choice to config and asks the player to restart.

---

## Re-enabling Disabled Features

Every feature this template turns off can be turned back on. Each one costs performance, so enable only what you need. **Restart the editor** after changing any of these.

### Lumen (dynamic GI and reflections)
1. **Project Settings → Rendering → Global Illumination → Dynamic Global Illumination Method → Lumen**
2. **Reflections → Reflection Method → Lumen**
3. **Software Ray Tracing → Generate Mesh Distance Fields → On** (required for software Lumen; triggers a rebuild of every mesh's distance field)
4. Recommended: **Shadows → Shadow Map Method → Virtual Shadow Maps**

Lumen requires deferred shading, which is the default here.

### Screen Space Reflections
A cheap middle ground between reflection captures and Lumen. Deferred only.
- **Project Settings → Rendering → Reflections → Reflection Method → Screen Space**
- Tune intensity and quality per Post Process Volume (**Rendering Features → Screen Space Reflections**).

### Ambient Occlusion (SSAO)
- Per Post Process Volume: **Rendering Features → Ambient Occlusion → Intensity**, *or*
- Make it the default: **Project Settings → Rendering → Default Settings → Ambient Occlusion**.

### Nanite
1. **Project Settings → Rendering → Nanite → Nanite → On**
2. Enable Nanite per mesh (Static Mesh Editor → **Nanite Settings → Enable Nanite Support**).

### Virtual Shadow Maps
- **Project Settings → Rendering → Shadows → Shadow Map Method → Virtual Shadow Maps.** Works best with Nanite geometry.

### TAA / TSR
- **Project Settings → Rendering → Default Settings → Anti-Aliasing Method → Temporal Anti-Aliasing** (moderate cost) **or Temporal Super Resolution** (higher cost and higher quality, supports upscaling).

### Hardware Ray Tracing
- **Project Settings → Rendering → Hardware Ray Tracing → Support Hardware Ray Tracing.** Requires an SM6 API (DX12 or Vulkan) and an RT-capable GPU.

### Mesh Distance Fields
- **Project Settings → Rendering → Software Ray Tracing → Generate Mesh Distance Fields.** Needed for distance-field AO and distance-field shadows.

### Substrate
- **Project Settings → Rendering → Substrate → Substrate materials.**

> [!CAUTION]
> Enabling Substrate converts your existing materials. **Back up your project first.** Turning it off again later is not guaranteed to restore your materials exactly.

### Bloom / Motion Blur / Auto Exposure / Lens Flare
- Per Post Process Volume, *or* make them defaults in **Project Settings → Rendering → Default Settings**.

---

## Optional: Switching to Forward Shading

Unreal has two main desktop renderers. This template uses **deferred** by default, because it is the better general choice. **Forward shading** is a better fit for some projects, and switching only takes one setting.

### Should you switch?

| Your project | Recommendation |
|---|---|
| **VR** (PC VR) | ✅ **Switch to forward.** It allows MSAA, which gives crisp, stable edges in a headset without TAA's blur. |
| **Simple stylized / low-poly** scenes with **few dynamic lights** and mostly baked lighting | ✅ Forward can be slightly faster. Try both and measure. |
| Many dynamic lights | ❌ Stay deferred |
| Dense foliage, many particles, heavy translucency (lots of overdraw) | ❌ Stay deferred |
| Needs SSR, SSAO, contact shadows or G-buffer post-process effects | ❌ Stay deferred |
| Needs Lumen | ❌ Stay deferred (Lumen requires it) |
| Not sure | ❌ Stay deferred |

### How the two renderers differ

**Deferred** rendering (the default) works in two steps:
1. Every object is drawn once. Instead of being lit, it writes its surface properties (base color, normal, roughness, metallic and so on) into a set of screen-sized buffers called the **G-buffer**.
2. Lighting is then calculated **once per screen pixel**, reading from the G-buffer.

If five objects overlap a pixel, deferred pays for five cheap G-buffer writes and **one** lighting calculation. That is why it handles many lights and dense scenes well. The cost is memory bandwidth: the G-buffer has to be written and read every frame, and it can't be multisampled cheaply, so MSAA isn't available.

**Forward** rendering lights each object *while drawing it*:
- **No G-buffer.** That saves memory and bandwidth, and makes **MSAA** possible.
- **The cost scales with overdraw × lights × material complexity.** If five objects overlap a pixel, that pixel can be lit up to five times, each time looping over every light touching it.
- **Unreal forces a full depth prepass** to limit that waste: every mesh is drawn **twice**, once for depth, once for color. That doubles the vertex and draw-call cost.
- **MSAA ×4 stores four samples per pixel** in the color and depth buffers. That is a heavy bandwidth cost on integrated GPUs and older cards, so this template presets ×2.

**The rule of thumb:** forward wins with **few lights, little overdraw and simple materials**, and in **VR**. Deferred wins with **many lights, dense geometry, foliage or particles**, or when you need screen-space effects.

> [!WARNING]
> Forward shading is **not automatically faster.** Moving a typical project to forward shading with MSAA ×4 can make it noticeably **slower**. Always measure both renderers with your own content.

### What you give up with forward shading

On top of [the features this template already disables](#what-you-give-up):

| Feature | Status in Forward | Notes |
|---|---|---|
| **Screen space reflections (SSR)** | ❌ Not supported | Use reflection captures |
| **Screen space ambient occlusion (SSAO)** | ❌ Not supported or limited | Baked AO from Lightmass still works |
| **Contact shadows** | ❌ Not supported or limited | |
| **Any effect that reads the G-buffer** | ❌ | Post-process materials that sample *SceneTexture* properties like BaseColor, Roughness or Metallic won't work. *SceneDepth*, *CustomDepth* and *PostProcessInput* still work. |
| **Light functions / IES profiles** | ⚠️ Limited | Test your specific setup |
| **Dynamically shadowed translucency** | ⚠️ Limited | |
| **MSAA on DirectX 11** | ❌ Not supported | Use Vulkan or DX12 for MSAA. FXAA works on every API. |
| **Lumen (any form)** | ❌ Requires deferred | |

> [!NOTE]
> Unreal's forward renderer gains features with each engine release. Treat the "limited" items above as **"test before relying on it"**, not necessarily "broken".

### How to switch

1. **Back up your project** or commit to version control.
2. **Project Settings → Engine → Rendering → Forward Renderer → Forward Shading → On.** This sets `r.ForwardShading=True`.
3. **Restart the editor** when prompted. Expect a full shader recompile.
4. *(Optional, recommended for VR)* Set **Project Settings → Rendering → Default Settings → Anti-Aliasing Method → MSAA**. The sample count is already preset to **×2** (`r.MSAACount=2`). Use `4` for higher quality if your GPU budget allows.
5. **Measure** in a Standalone or packaged build ([Profiling](#profiling-and-measuring-performance)) and compare with deferred.

The forward-only settings (`r.EarlyZPass=3`, `r.VertexFoggingForOpaque`, `r.MSAACount=2`) are already configured; see [Forward-Only Settings](#forward-only-settings-no-effect-in-deferred).

**To switch back:** turn **Forward Shading** off, restart, and set anti-aliasing to FXAA or TAA, because MSAA isn't available in deferred.

### Anti-aliasing options with forward shading

| Method | `r.AntiAliasingMethod` | Cost | Quality | Notes |
|---|---|---|---|---|
| **FXAA** (template default) | `1` | Lowest | Softens edges, slight blur, doesn't fix shimmer | Works on every API |
| **TAA** | `2` | Moderate | Smooth and stable, can ghost or blur | Usually avoided in VR |
| **MSAA** | `3` | Moderate to high (bandwidth) | Crisp geometric edges, no temporal blur | **Forward only.** Not on DX11. Doesn't fix shader or specular shimmer. |
| **TSR** | `4` | Highest | Best quality, supports upscaling | Usually too expensive for low-end hardware |

You can test MSAA cost at runtime with the console command `r.MSAACount 1`, `2` or `4`.

### Keeping forward shading fast

1. **Bake as much as possible.** Static lights cost almost nothing at runtime.
2. **Keep movable lights few and small.** Use a short **Attenuation Radius** so each light touches as few pixels as possible. Turn off **Cast Shadows** on lights that don't need them.
3. **Watch overdraw.** Check *View Mode → Optimization Viewmodes → Quad Overdraw* and *Shader Complexity*. Large translucent particles, stacked foliage cards and layered decals are the usual culprits.
4. **Keep materials simple.** Every overlapping layer runs the full material and lighting, so complex materials hurt more than in deferred.
5. **Keep vertex and draw-call counts reasonable.** The depth prepass draws every mesh twice. Use LODs, instancing (ISM/HISM), merged actors and HLODs.
6. **Use MSAA ×2 rather than ×4** on low-end GPUs.
7. In `stat gpu`, watch **PrePass** (too many or too detailed meshes) and **BasePass** (too many lights, too much overdraw, or materials that are too complex). If BasePass dominates and you can't reduce it, **switch back to deferred**.

### VR setup checklist

1. **Switch to forward shading** (above) and set anti-aliasing to **MSAA** ×2 or ×4.
2. **Enable the XR plugin** for your target runtime (for example **OpenXR**) in **Edit → Plugins**, then restart.
3. **Enable Instanced Stereo:** *Project Settings → Rendering → VR → Instanced Stereo* (`vr.InstancedStereo=True`). It renders both eyes in one pass and greatly reduces CPU and draw-call cost. It is off in this template because the template isn't VR-specific.
4. Keep **motion blur** and **auto exposure** off for comfort (already off by default).
5. **Profile in VR Preview or a packaged build** with the headset on. VR has a strict frame budget (for example ~11.1 ms at 90 Hz).
6. *(Optional)* Look into **Variable Rate Shading / foveated rendering** (`xr.VRS.*`). It is supported in the config but off by default.
7. Check your headset runtime's documentation for graphics API requirements, and test VR on every API you ship.

> [!NOTE]
> Standalone headsets (Android-based) use the **mobile renderer**. Use the separate mobile version on Fab for those.

---

## Migrating an Existing Project

> [!WARNING]
> **Back up your project (or commit to version control) before you start.** Moving from Lumen to baked lighting changes how every level looks.

> [!CAUTION]
> **Do not copy the whole `Config/` folder or the whole `DefaultEngine.ini` into another project.** It contains this template's startup map, project redirects (`ActiveGameNameRedirects`) and other project-specific entries that will break your project. Copy **only the sections listed below**.

### Step 1: Copy the rendering settings
From this template's `Config/DefaultEngine.ini`, copy the contents of these sections into the same sections of your project's `DefaultEngine.ini`. Merge the keys; don't add the sections twice.
- `[/Script/Engine.RendererSettings]`
- `[/Script/WindowsTargetPlatform.WindowsTargetSettings]`: only `DefaultGraphicsRHI` and the `TargetedShaderFormats` lines
- *(Optional)* `[/Script/LinuxTargetPlatform.LinuxTargetSettings]` and `[/Script/MacTargetPlatform.MacTargetSettings]`

From `Config/DefaultGame.ini`, optionally copy `[/Script/UnrealEd.ProjectPackagingSettings]`.

Alternatively, set each value by hand in **Project Settings** using the [Settings Reference](#settings-reference). This is slower but safer.

### Step 2: Restart and recompile
Restart the editor. Expect a full shader recompile.

### Step 3: Fix lighting
1. Decide the mobility of each light ([Light Mobility](#1-light-mobility)). Most environment lights should be **Static** or **Stationary**.
2. Make sure static meshes have **lightmap UVs** and sensible **lightmap resolutions**.
3. Add a **Lightmass Importance Volume** to each level.
4. Add **Reflection Captures**, since Lumen reflections are gone.
5. Set the **Sky Light** to Stationary or Static and recapture it.
6. **Build lighting.**

### Step 4: Fix geometry
Nanite meshes without LODs now render at full detail at every distance. For any high-poly mesh:
- Static Mesh Editor → **LOD Settings → Number of LODs** (auto-generate), *or* import authored LODs.
- Consider disabling Nanite on meshes that relied on it. With Nanite off project-wide, they use their fallback mesh.

### Step 5: Measure
Compare before and after in a **packaged or Standalone build** ([Profiling](#profiling-and-measuring-performance)). If performance got worse, find out why with `stat gpu` and `ProfileGPU` before changing anything else.

### Step 6 (optional): Try forward shading
Only after the steps above work, and only if your project fits the [criteria](#should-you-switch). Change **one thing at a time** so you know what helped or hurt.

---

## Packaging Your Game

The template includes packaging defaults aimed at small, clean builds (`Config/DefaultGame.ini` → `[/Script/UnrealEd.ProjectPackagingSettings]`):

| Setting | Value | Why |
|---|---|---|
| `BuildConfiguration` | `PPBC_Shipping` | Shipping builds strip debug features and run fastest. Use *Development* while testing. |
| `FullRebuild` | `False` | Incremental packaging; much faster iteration. |
| `UsePakFile` / `bUseIoStore` | `True` | Standard UE5 containers, with faster loading. |
| `bCompressed` | `True` | Smaller download size. |
| `IncludePrerequisites` | `True` | Bundles the Visual C++ runtime installer so the game launches on fresh or older Windows PCs. |
| `IncludeCrashReporter` | `False` | Smaller build. Turn it on if you want crash reports from players. |
| `bShareMaterialShaderCode` | `True` | Deduplicates shader code, so the build is smaller. |
| `bSkipEditorContent` | `True` | Keeps editor-only content out of the build. |
| `bSkipMovies` | `True` | Skips the `Content/Movies` folder. **Set this to `False` if you use startup or in-game movies.** |
| `InternationalizationPreset` / `CulturesToStage` | English / `en` | Smaller build. **Add your cultures if you localize.** |

> [!NOTE]
> All maps in your project are cooked by default. To ship only specific maps, add them under **Project Settings → Packaging → List of maps to include in a packaged build**.

### How to package
**Platforms → Windows → Package Project** (or Linux/Mac), then choose an output folder.

---

## Profiling and Measuring Performance

**Never judge performance from the editor viewport.** The editor runs extra systems. Use **Play → Standalone Game**, **VR Preview**, or a **packaged Development build**.

| Command | What it shows |
|---|---|
| `stat fps` | Frame rate |
| `stat unit` | Frame time split into **Game** (CPU game thread), **Draw** (CPU render thread), **GPU**, **RHIT**. The largest number is your bottleneck. |
| `stat gpu` | GPU time per render pass (shadows, base pass, lighting, translucency, post) |
| `stat rhi` | Draw calls and primitives |
| `stat scenerendering` | Mesh draw calls, lights and more |
| `ProfileGPU` (Ctrl+Shift+,) | One-frame GPU breakdown, per pass and per draw |
| `r.ScreenPercentage 50` | Quick test: if FPS jumps, you're **GPU/pixel bound** |
| Unreal Insights | Detailed CPU/GPU timeline (`-trace=default` launch argument) |

Common low-end bottlenecks: too many **draw calls** (merge meshes, use instancing, HLODs), **overdraw** from translucency and foliage, **material complexity** (check *Shader Complexity* view mode), **shadow cost** (fewer shadow-casting movable lights, shorter shadow distances), and **texture memory** (`stat streaming`, `memreport`).

**Scalability settings** (`sg.*` / the **Settings → Engine Scalability** menu) are your friend. Expose them in your game's options menu so players on the weakest hardware can lower shadows, effects and resolution.

---

## Benchmarks

Measured in a packaged **Development** build, 1920×1080, `msi afterburner` averaged over 60 seconds on the default platform and clouds scene. 3 Tabs opened (YouTube, Spotify and Github), discord runnining and Spotify Playing Music in order to simulate an average gamers desktop environment.

| Hardware | Stock UE 5.8 Blank | Perfectly Optimized (Deferred, default) | Perfectly Optimized + Forward (FXAA) | Perfectly Optimized + Forward (MSAA ×2) |
|---|---|---|---|---|
| *RTX 4060 + Ryzen 5800X* | *102 FPS, 2337MB VRAM* | *525 FPS, 730MB VRAM* | *540 FPS, 694MB VRAM* | *532 FPS, 699MB VRAM* |


Compared to the base UE5 Project, You get **+414.71% FPS** (More than a 5x increase in framerate) and use **-68.76% VRAM** (Roughly 1/3 of the original memory)
> [!NOTE]
> Benchmarks measure the **baseline cost** of each configuration in the basic empty ue5 test scene (yk the one with the platform and clouds). Your game's results depend on your content, and the gap between forward and deferred depends heavily on light count and overdraw. Benchmarks for more hardware are appriciated. see [Contributing](#support-and-contributing).

---

## Troubleshooting

<details>
<summary><b>The scene is dark, black, or shows "Lighting needs to be rebuilt"</b></summary>

Lighting has not been baked. Run **Build → Build Lighting Only**. Static lights contribute **nothing** until baked. Also check you have a **Sky Light** for ambient light and reflections.
</details>

<details>
<summary><b>My light does nothing</b></summary>

- **Static** lights only show up after a lighting build.
- Check the light's **Intensity** and **Attenuation Radius**. Units are **Candelas** by default.
- More than 4 overlapping **Stationary** lights? Check *View Mode → Stationary Light Overlap*.
- **Movable** lights work immediately, but give direct light only, with no bounce.
</details>

<details>
<summary><b>Black splotches, light leaks or seams after baking</b></summary>

Lightmap UV problems. Make sure meshes have non-overlapping lightmap UVs (Generate Lightmap UVs on import), increase **Light Map Resolution** on problem meshes, and check *View Mode → Lightmap Density*. Thin walls should be at least ~10 cm thick to avoid leaks.
</details>

<details>
<summary><b>Crash or "Vulkan device not found" on startup</b></summary>

1. Update your GPU driver.
2. Launch with `-dx12`, or `-dx11` on very old GPUs. For the editor, add it to the shortcut target after the `.uproject` path.
3. If that works, change the default RHI permanently ([Choosing a Graphics API](#choosing-a-graphics-api-rhi)).
4. Please [open an issue](https://github.com/Imloopdev/Perfectly-Optimized/issues) with your GPU, driver version and the log from `Saved/Logs/`.
</details>

<details>
<summary><b>Crash when importing or opening an asset pack</b></summary>

1. Note exactly which asset pack and which action (import, open map, play) caused it.
2. Collect `Saved/Logs/<Project>.log` and the folder in `Saved/Crashes/`.
3. Try launching with `-dx12` to rule out a Vulkan driver issue.
4. [Open an issue](https://github.com/Imloopdev/Perfectly-Optimized/issues) with the logs attached.
</details>

<details>
<summary><b>The first launch compiles thousands of shaders</b></summary>

That is normal for any UE5 project on a new machine or engine version, and it only happens once. This template turns off ray tracing, distance fields and Substrate specifically to reduce the count. Later launches use the cache in `DerivedDataCache/`. Don't delete it unless you need to.
</details>

<details>
<summary><b>The packaged game doesn't start on another PC</b></summary>

- Make sure `IncludePrerequisites=True` (the default) and that the player ran `UEPrerequisiteSetup` if prompted.
- Try launching with `-dx11` on very old GPUs.
- Check `%LOCALAPPDATA%\<Project>\Saved\Logs\` on that machine.
</details>

<details>
<summary><b>My maps are missing from the packaged game</b></summary>

Check **Project Settings → Packaging → List of maps to include**, and that **Game Default Map** (Project Settings → Maps & Modes) points to your map, not `L_Empty`.
</details>

<details>
<summary><b>Performance is worse than the stock project</b></summary>

Profile before changing settings ([Profiling](#profiling-and-measuring-performance)):
- `stat unit`: is it the CPU (Game/Draw) or the GPU?
- `stat gpu`: which pass is expensive?
- Converted Nanite meshes without LODs are a common cause. See [Migrating](#step-4-fix-geometry).
- Many shadow-casting **movable** lights are expensive with any renderer. Make them stationary or static, or turn off **Cast Shadows** where you can.
- **Switched to forward shading?** Many lights, foliage or particles make forward slower. MSAA ×4 is expensive on low-end GPUs. See [Keeping forward shading fast](#keeping-forward-shading-fast), or switch back to deferred.
</details>

<details>
<summary><b>Edges look jagged or shimmer</b></summary>

FXAA is cheap but doesn't fix shimmering on fine detail or shiny surfaces. Switch to **TAA** (*Project Settings → Rendering → Anti-Aliasing Method*) for a moderate cost, or use MSAA if you've switched to forward shading.
</details>

<details>
<summary><b>Forward shading: fog looks blotchy, banded or wrong on large surfaces</b></summary>

`r.VertexFoggingForOpaque=True` calculates height fog per *vertex* when forward shading is on, which looks wrong on large, low-poly triangles such as floors and terrain. Turn it off for per-pixel fog at a small cost: *Project Settings → Rendering → Forward Renderer → Vertex Fogging for Opaque → Off*.
</details>

<details>
<summary><b>Forward shading: a post-process material shows black or wrong results</b></summary>

Forward shading has no G-buffer, so *SceneTexture* nodes reading **BaseColor, Roughness, Metallic, Specular** and so on return nothing useful. *SceneDepth*, *CustomDepth*, *CustomStencil* and *PostProcessInput0* still work. Redesign the effect around those, or switch back to deferred.
</details>

<details>
<summary><b>Forward shading: MSAA has no effect</b></summary>

- Check that **Forward Shading** is on and **Anti-Aliasing Method** is set to **MSAA**. MSAA does nothing in deferred.
- Check that `r.MSAACount` is 2 or 4.
- MSAA is **not supported on DX11**. Use Vulkan or DX12.
- MSAA only smooths geometry edges. Shimmer *inside* surfaces (specular, fine textures) needs mipmaps, roughness tweaks or TAA.
</details>

---

## FAQ

**Is this just "turning off Lumen and Nanite"?**
Mostly, yes, plus about a dozen less obvious settings that reduce shader count, memory and import cost, sensible packaging defaults, and this documentation. There is no hidden trick. The value is a consistent baseline and an explanation of every choice. See the [Settings Reference](#settings-reference).

**Will this make my existing game faster?**
It removes the expensive *defaults*. If your game relies on Lumen or Nanite, you have to replace them with baked lighting and LODs before you see gains. See [Should You Use This?](#should-you-use-this)

**Why deferred and not forward by default?**
Deferred is the better general-purpose choice: it handles many lights and dense scenes well and keeps every feature. Forward is better for VR and very simple scenes, so it's offered as a [one-setting switch](#optional-switching-to-forward-shading).

**Can I re-enable Lumen in just one level?**
The GI method is a project setting, but you can override GI and reflection methods per **Post Process Volume** (Global Illumination / Reflections → Method) as long as the project supports it. You must enable *Generate Mesh Distance Fields* project-wide for software Lumen.

**Does this work for VR?**
Yes. Follow the [VR setup checklist](#vr-setup-checklist).

**Does this work for mobile?**
This build targets desktop. A separate mobile version is available on Fab.

**Can I use this commercially?**
Yes. See [License](#license).



## Support and Contributing

- **Bugs and crashes:** [GitHub Issues](https://github.com/Imloopdev/Perfectly-Optimized/issues). Please include your engine version, renderer (deferred/forward), GPU, driver version, API (Vulkan/DX12/DX11), anti-aliasing method, and logs from `Saved/Logs/` and `Saved/Crashes/`.
- **Questions and discussion:** [Unreal Engine forum thread](https://forums.unrealengine.com/t/perfectly-optimized-free-template-made-for-older-hardware/2684738)
- **Benchmarks:** Results on your hardware are very welcome, including deferred vs. forward comparisons. Open an issue with your numbers and specs, using the [benchmark method](#benchmarks).
- **Pull requests:** Settings improvements are welcome. Please explain the *why* and include before/after measurements.


## License

Free to use in personal and commercial projects. When obtained through Fab, Fab's standard license terms apply.



<div align="center">

Made by **[Imloopdev](https://github.com/Imloopdev)** · If this template helped you, consider leaving a review on [Fab](https://www.fab.com/listings/060add23-7044-466c-9790-adfac5781d04) ⭐

</div>
