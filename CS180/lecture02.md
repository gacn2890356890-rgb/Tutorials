# Intro to Computer Vision and Computational Photography

## The Camera

**Aperture**: Reduces blurring

 **Pinhole camera model**

- **Captures pencil of rays** – all rays through a single point.
- The point is called **Center of Projection (COP)**.
- The image is formed on the **Image Plane**.
- **Effective focal length $f$** is the distance from COP to Image Plane.

**Perspective Projection Derivation**

Based on **similar triangles** in the $y-z$ plane:

The relationship between the 3D point $P(x, y, z)$ and its 2D projection $P'(x', y')$ is:

$$\frac{y'}{f} = \frac{y}{z} \implies y' = f \frac{y}{z}$$

$$\frac{x'}{f} = \frac{x}{z} \implies x' = f \frac{x}{z}$$

**Summary of Coordinate Transformation:**

$$(x, y, z) \xrightarrow{\text{Projection}} \left( f \frac{x}{z}, f \frac{y}{z} \right)$$

**Orthographic / Parallel Projection**

* **Concept:** A special case of perspective projection where the camera is moved to **infinity** and zoomed in.
* **Ray Geometry:** Rays are **parallel** to the optical axis instead of converging at a single point.
* **Key Property:** **No "Distance-based Scaling"**. Objects retain their size regardless of their distance ($z$).
* **Projection Equation:** $(x, y, z) \rightarrow (x, y)$
* **Use Cases:** * **Architecture/Engineering:** For accurate measurements.
    * **Art:** Traditional Chinese scrolls.
    * **Games:** Isometric/Orthographic views (e.g., SimCity).
 
**Spherical Projection Model**

- **Geometry**: The Image Plane is a **sphere** centered at the Center of Projection (COP).

- **Coordinate System**: Points are represented using **Spherical Coordinates** $(\theta, \phi, r)$, where:

  - $\theta$: Azimuth angle (horizontal).
  - $\phi$: Polar/Zenith angle (vertical).
  - $r$: Radial distance from the origin.

- **Projection Logic**: Mapping from 3D to 2D is **trivial** because the image position is defined solely by the angles:

  $$(r, \theta, \phi) \rightarrow (\theta, \phi)$$

- **Key Insight**: Unlike planar projection, the resulting image **does not depend on the focal length $f$**. Changing $f$ (the radius of the sphere) does not shift the angular position of the projected points.

Why not make the aperture infinitely small?(The reason for lenses)

* **Trade-off 1: Brightness vs. Sharpness** Smaller holes block more light, requiring dangerously long exposure times (e.g., Abelardo Morell's 8-hour exposures).
* **Trade-off 2: Geometry vs. Physics (Diffraction)** * **Large Aperture:** Blurring is caused by **ray overlap** (multiple rays hitting the same spot).
    * **Tiny Aperture:** Blurring is caused by **Diffraction**, where light waves bend around the edges of the hole, spreading the light back out.
* **The "Sweet Spot":** There is an optimal aperture size where the sum of geometric blur and diffraction blur is minimized (shown as ~0.35mm in the slides).

| **Feature**       | **Depth of Field (DOF)**                                     | **Field of View (FOV)**                                      |
| ----------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Definition**    | The range of distances over which objects appear sharply focused. | The extent of the observable world seen by the camera at a given moment. |
| **Core Variable** | **Aperture**                                                 | **Focal Length**                                             |
| **Parameter**     | **F-number** ($f/D$)                                         | **Zoom** (Magnification)                                     |
| **Math Logic**    | Smaller aperture (Higher F-number) $\rightarrow$ **Deeper DOF**. | Shorter focal length ($f$) $\rightarrow$ **Larger FOV** ($\phi$). |
| **Visual Effect** | **Large Aperture:** Shallow DOF (bokeh/blurred background). **Small Aperture:** Deep DOF (everything in focus). | **Telephoto:** Narrow angle (Zoom in). **Wide-angle:** Broad angle (Zoom out). |

**Field of View (FOV) Calculation**

The size of the Field of View ($\phi$) is governed by the size of the camera sensor/retina ($d$) and the focal length ($f$):

$$\phi = 2 \tan^{-1}\left(\frac{d}{2f}\right)$$

* **$d$**: Sensor size (width or diagonal).
* **$f$**: Focal length.
* **$\phi$**: Field of View angle.

**Key takeaway:** A smaller FOV corresponds to a larger Focal Length (Zoom in), while a larger FOV corresponds to a smaller Focal Length (Wide angle).

**Dolly Zoom (Vertigo Shot)**
- **Mechanism**: Move Camera (Dolly) $\leftrightarrow$ Inverse Lens Zoom.
- **Effect**: Constant subject size + warped background perspective.
  - **Dolly In + Zoom Out**: Background stretches/recedes.
  - **Dolly Out + Zoom In**: Background compresses/crushes.


**Exposure Balancing**
- **Variables**: **Shutter Speed** (Time) & **Aperture** (F-number).
- **Trade-off**: Same Exposure = $F \uparrow$ (smaller hole) + Speed $\downarrow$ (longer time).
  - *Fast Shutter*: Freezes motion.
  - *Slow Shutter*: Motion blur.

**Lens Aberrations**
- **Chromatic**: Color fringing/halos (wavelength refraction variance).
- **Radial Distortion**:
  - **Barrel**: Lines bulge outward (Wide-angle).
  - **Pincushion**: Lines curve inward (Telephoto).
