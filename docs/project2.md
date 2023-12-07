# WebGL Stereoscope Effect

## Overview
The application sets up a 3D scene containing multiple spheres and allows users to switch between different rendering effects: Stereo, Anaglyph, and Parallax. It offers real-time interaction by capturing mouse movements to control the camera's position and direction.

[![Button Example]][Link]
<!----------------------------------------------------------------------------->
[Link]: https://stereo-camera.vercel.app/ 'Link with example title.'
<!---------------------------------[ Buttons ]--------------------------------->
[Button Example]: https://img.shields.io/badge/Check_it_out_>-37a779?style=for-the-badge



![Alt text](<src/Screenshot 2023-12-05 at 1.34.40 PM.png>)

-------

## What is Stereoscopic Rendering?
Stereoscopic rendering is based on the principle of presenting two slightly different images to each eye, mimicking the way human eyes perceive depth in the real world. It involves:

1. <b>Dual Perspective Rendering: </b>
Two separate views of the scene, representing the left-eye and right-eye perspectives, are generated. These views simulate the slight offset observed by each eye in a natural setting.

1. <b>Displaying Views: </b>
The left-eye and right-eye images are typically displayed side by side or in quick succession. When viewed through stereoscopic glasses (often polarized or active-shutter glasses), each eye perceives its respective image, resulting in a perception of depth and three-dimensionality.

1. <b>Adjusting Camera Settings: </b>
In 3D applications, the camera setup is crucial. The camera settings need to mimic human eye separation to create an accurate stereoscopic effect.

--------
## Parallax Barrier

![Alt text](<src/Screenshot 2023-12-05 at 1.35.54 PM.png>)

------- 

The parallax barrier effect is a glasses-free 3D technology used in displays, such as autostereoscopic screens (screens that provide 3D without requiring glasses). Key aspects include:

1. <b>Barrier Layer: </b>
A barrier layer consisting of a series of precise slits or barriers is placed in front of the display panel.

1. <b>Directional Light Control: </b>
The barrier restricts the direction of light emitted by the display, ensuring different pixels or subpixels are visible from different viewing angles.

1. <b>Perspective Variation: </b>
Each eye sees a slightly different set of pixels due to the barrier's directional control, creating a stereoscopic effect without requiring glasses.

------
## Anaglyph Rendering
![Alt text](<src/Screenshot 2023-12-05 at 1.35.06 PM.png>)

--------

Anaglyph rendering uses color filtering to create the illusion of depth perception without specialized displays. It involves:

1. <b>Dual-Color Images: </b>
Two images of the scene are rendered, each overlaid with a different color filter (typically red-cyan or red-blue).

1. <b>Use of Glasses: </b>
Anaglyph glasses (with corresponding color filters) are worn by the viewer. Each lens filters out one of the colored images, ensuring each eye sees only its respective view.

1. <b>Color Separation:</b>
 When the filtered images are superimposed in the brain, the color differences create a perception of depth and three-dimensionality.


-----


## Key Components

### 1. Scene Initialization (`init()` Function)

```
// create GUI to switch between fx
const gui = new GUI()
    var stereofx =
    gui.addFolder('Stereo Camera');
    gui.add(settings,'Effects',['Stereo','Anaglyph','Parallax']).onChange(function(value){
        switchEffect(value);
    });
    gui.open()
    var curreffect = settings.Effects;
    container = document.createElement( 'div' );
    document.body.appendChild( container );

    camera = new THREE.PerspectiveCamera( 60, window.innerWidth / window.innerHeight, 1, 100000 );
    camera.position.z = 3200;

    scene = new THREE.Scene();

    // Environment rendered as Cubemap
    scene.background = new THREE.CubeTextureLoader()
.setPath( 'textures/cubemaps/' )
.load( [
    'px.png',
    'nx.png',
    'py.png',
    'ny.png',
    'pz.png',
    'nz.png'
] );
```
- GUI Creation: Utilizes `dat.GUI` to create an interface for selecting rendering effects.
- Environment Setup: Establishes the 3D environment, including a container for the scene, camera, textures, materials, and sphere meshes.
- Renderer Configuration: Configures the WebGL renderer and sets its pixel ratio.

------ 

### 2. Effect Switching (`switchEffect(value)` Function)

```
function switchEffect(value){
    if(value == 'Stereo'){
        effect = new StereoEffect( renderer );
        effect.setSize( window.innerWidth, window.innerHeight );
    }
    if(value == 'Anaglyph'){
        effect = new AnaglyphEffect(renderer);
        effect.setSize(window.innerWidth, window.innerHeight );
    }
    if(value == 'Parallax'){
        effect = new ParallaxBarrierEffect(renderer);
        effect.setSize(window.innerWidth, window.innerHeight);
    }
    
    
}
```

- Effect Handling: Dynamically switches the rendering effect based on user selection in the GUI (Stereo, Anaglyph, Parallax).

### 3. Event Handling
```
function onWindowResize() {

    windowHalfX = window.innerWidth / 2;
    windowHalfY = window.innerHeight / 2;

    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();

    effect.setSize( window.innerWidth, window.innerHeight );
    

}
```
- Window Resize: Adjusts camera aspect ratio and effect size upon window resize.
```
function onDocumentMouseMove( event ) {

    mouseX = ( event.clientX - windowHalfX ) * 10;
    mouseY = ( event.clientY - windowHalfY ) * 10;

}
```
- Mouse Movement: Tracks mouse movement to control the camera's position and orientation.

### 4.Animation Loop
```
function animate() {

    requestAnimationFrame( animate );
    render();

}
```
- `animate()` Function: Starts the animation loop using requestAnimationFrame.
```
function render() {

    const timer = 0.0001 * Date.now();

    camera.position.x += ( mouseX - camera.position.x ) * .05;
    camera.position.y += ( - mouseY - camera.position.y ) * .05;
    camera.lookAt( scene.position );

    for ( let i = 0, il = spheres.length; i < il; i ++ ) {

        const sphere = spheres[ i ];

        sphere.position.x = 5000 * Math.cos( timer + i );
        sphere.position.y = 5000 * Math.sin( timer + i * 1.1 );

    }

    effect.render( scene, camera );

}
```
- `render()` Function: Updates the scene elements and camera positions based on mouse movement and time, then renders the selected effect.

### Breakdown of Operations
- GUI Setup: Enables users to choose between rendering effects.
- Scene Creation: Generates a 3D environment with spheres positioned randomly within the scene.
- Rendering Pipeline: Facilitates rendering through WebGL using different effects.
- User Interaction: Captures mouse movement to dynamically control the camera's viewpoint.
- Responsive Design: Ensures the application adapts to window size changes for a consistent user experience.
### Overall Functionality
- Interactive Visualization: Presents a 3D scene with the ability to switch between multiple rendering effects.
- Real-time Interaction: Allows users to manipulate the scene via mouse movement.
- Adaptive Rendering: Handles dynamic resizing of the viewport for consistent display across various window sizes.
