# Python Ray Tracer (Blender Plugin)
[![Button Icon2]][Link2]
<!----------------------------------------------------------------------------->
[Link2]: https://github.com/likornguth/blender-raytracer 'Link with example title.'
<!---------------------------------[ Buttons ]--------------------------------->
[Button Icon2]: https://img.shields.io/badge/GitHub-EF2D5E?style=for-the-badge&color=black&logoColor=white&logo=GitHub
## Sample Output (Cornell Box Scene)




![alt text](<src/todo3_8samples.png>)

## Overview

**Project Name:** simpleRT_plugin.py  
**Purpose:** Blender add-on for a minimal ray tracing engine designed for CS148 HW5.  
**Features Implemented:**
- Area light support
- Sampling techniques
- Indirect illumination (global illumination)

---

## Plugin Information

- **Name:** `simple_ray_tracer`
- **Description:** Simple Ray Tracer for CS148
- **Author:** CS148
- **Version:** 0.0.2023
- **Blender Version Compatibility:** 3.5.1
- **Category:** Render
- **Wiki URL:** [Stanford CS148 Course](http://web.stanford.edu/class/cs148/)

---

## Features & Implementation Details

### 1. **Ray Casting**
- Uses Blender’s built-in `scene.ray_cast()` function.
- Determines whether a ray intersects with objects in the scene.
- Returns hit location, normal, and object data.

### 2. **Sampling & Hemisphere Sampling**
- Implements Monte Carlo sampling with a hemisphere function.
- Generates uniformly distributed directions for indirect lighting calculations.
- Uses random values for spherical coordinates conversion.

**Code Snippet:**
```python
def sample_hemisphere():
    r1 = np.random.rand()
    r2 = np.random.rand()
    
    cos_theta = r1
    sin_theta = np.sqrt(1 - cos_theta ** 2)
    
    phi = r2 * 2 * np.pi
    
    x = sin_theta * np.cos(phi)
    y = sin_theta * np.sin(phi)
    z = cos_theta

    return np.array([x, y, z])
```

### 3. Transforming Local to World Space
- Converts sampled ray directions from local hemisphere space to world space.
- Uses a transformation matrix formed by the normal and two perpendicular basis vectors.

**Code Snippet:**
```python
def transform_to_world_space(ray_direction, x, y, n):
    transform = np.array([x, y, n]).T  
    return transform @ ray_direction
```

## 5. Area Light Implementation
- Adds support for area lights by sampling points on the light source.
- Adjusts intensity based on normal alignment (tilt factor).

**Code Snippet:**
```python
if light.data.type == 'AREA':
    r = sqrt(np.random.rand())
    theta = 2 * pi * np.random.rand()
    emit_loc_local = Vector((r * np.cos(theta), r * np.sin(theta), 0))
    emit_loc_world = light.matrix_world @ emit_loc_local
    light_loc = emit_loc_world
```
## 6. Reflection and Transmission
- Implements reflectivity based on Fresnel equations.
- Handles transparency and refraction based on material index of refraction (IOR).

**Code Snippet:**

```python
reflectivity = mat.mirror_reflectivity
if mat.use_fresnel:
    n2 = mat.ior
    r0 = ((1 - n2) / (1 + n2)) ** 2
    reflectivity = r0 + (1 - r0) * ((1 + ray_dir.dot(hit_norm)) ** 5)
```
## 7. Low-Discrepancy Sampling (Van der Corput Sequence)
- Used for anti-aliasing in rendering.
- Generates quasi-random offsets for pixel sampling.

**Code Snippet:**

```python
def corput(n, base=2):
    q, denom = 0, 1
    while n:
        denom *= base
        n, remainder = divmod(n, base)
        q += remainder / denom
    return q - 0.5
```

## 8. Rendering Pipeline
- Iterates through image pixels and samples per pixel.
- Uses recursive tracing for depth-based illumination effects.
- Implements a progress update system for rendering status.

**Code Snippet:**

```python
for y in RT_render_scene(scene, width, height, depth, samples, buf):
    status = f"pass {y//height+1}/{samples} | Remaining {timedelta(seconds=remain)}"
    self.update_stats("", status)
    print(status, end="\r")
```

# Performance Considerations
## Optimizations:
- Uses numpy for efficient vector operations.
- Implements stratified sampling for better noise reduction.
- Caches light calculations to reduce redundant operations.
## Potential Improvements:
- Implement acceleration structures (BVH or KD-Tree).
- Improve anti-aliasing with more advanced sampling strategies.
- Add support for additional shading models.
# Conclusion
This Blender plugin extends the simpleRT engine with features such as area lighting, global illumination, and improved sampling techniques. The implementation follows standard ray tracing methodologies while leveraging Blender's built-in API for scene querying and rendering integration. Future improvements could focus on performance optimizations and additional rendering effects.