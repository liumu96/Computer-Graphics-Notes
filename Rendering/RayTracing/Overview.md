## Overview

The goal of photorealistic rendering is to create an image of a 3D scene that is indistinguishabel from a photograph of the same scene. Almost all photorealistic rendering systems are based on teh ray-tracing algorithm. Ray tracing is actually a very simple algorithm. It based on following the path of a ray of light through a scene as it interacts with and bounces off objects in an environment. Although there are many ways to write a ray tracer, all such systems simulate at least the following objects and phenomena:

- _Cameras_: A camera model determins how and from where the scene is being reviewd, including how an image is recorded on a sensor. Many rendering systems generate viewing rays starting at the camera that are traced into the scene.

- _Ray-object intersections_: We must be able to tell precisely where a given ray intersects a given geometric object. In addition, we need to determine certain properties of the object at the intersection point, such as a surface normal or its material. Most ray tracers also have some facility for testing the intersection of a ray with mulple objects, typically returning the closest intersection along the ray.

- _Light sources_: Without lighting, there would be little point in rendering a scene. A ray tracer must model the distribution of light throughout the scene, including not only the locations of the lights themselves but also the way in which they distribute their energy throughout space.

- _Visibility_: In order to know whether a given light deposits energy at a point on a surface, we must know whether there is an uninterrupted path from the point to the light source. Fortunately, this question is easy to answer in a ray tracer, since we can just construct the ray from the surface to the light, find the closest ray-object intersection, and compare the intersection distance to the light distance.

- _Surface scattering_: Each object must provide a description of its appearance, including information about how light interacts with the object's surface, as well as the nature of the reradiated (or scattered) light. Models for surface scattering are typically parameterized so that they can simulate a variety of appearances.

- _Indirect light transport_: Because light can arrive a surface after bouncing off or passing through other surfaces, it is usually necessary to trace additional rays originating at the surface to fully capture this effect.

- _Ray propagation_: We need to know that what happens to the light traveling along a ray as it passes through space. If we are rendering a scene in a vacuum, light energy remains constant along a ray. Although true vacuums are unusual on Earth, they are a reasonable approximation for many environment, and so on.

## References

### Books Resources

- [Physical Based Rendering](https://www.youtube.com/watch?v=frLwRLS_ZR0)

### Video Resources

- [a non-technical introductrory video by Disney](https://www.youtube.com/watch?v=frLwRLS_ZR0)

### Code Resources

- _c++_: [Ray Tracing in One Weekend]()
- _JavaScript_: [Ray Tracing]()
