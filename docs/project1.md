# Interactive 3D Lite-Brite Display with Customizable Lighting
<img width="1316" alt="Screenshot 2023-11-08 at 12 37 19 PM" src="https://github.com/likornguth/LiteBrite/assets/97198397/128ae6c0-339a-4c42-87ea-8cbbfd07f0de">


[![Button Example]][Link]
<!----------------------------------------------------------------------------->
[Link]: https://lite-brite-mickey.vercel.app/ 'Link with example title.'
<!---------------------------------[ Buttons ]--------------------------------->
[Button Example]: https://img.shields.io/badge/Check_it_out_>-37a779?style=for-the-badge


## Project Description:
This technical project involves creating an interactive 3D scene that recreates a classic children's toy and a customizable lighting setup using the Three.js library. The scene consists of a 3D Mickey Mouse lightbox and a desk-like structure, with various elements that can be controlled and customized.
![mickeylitebrite](https://github.com/likornguth/LiteBrite/assets/97198397/31094b2a-5cf7-4dab-ac5b-b2d805e4555d)

## Key Components and Features:

### 3D Scene Setup:
```
import * as THREE from 'three';

const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera( 75, window.innerWidth / window.innerHeight, 0.1, 10000 );
const renderer = new THREE.WebGLRenderer();
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap; // default THREE.PCFShadowMap

```
- The project utilizes the Three.js library to create a 3D scene.
- A camera is set up to view the scene from a specific perspective.
- A renderer is used to display the 3D scene in a web browser.
- Soft shadows are enabled for more realistic lighting

### Customizable Lighting:
```
// LED panel 
const rectLight = new THREE.RectAreaLight(
    0xF6E7D2,
    settings.brightness,
    width,
    height,
);

// point light 1
const pt1 = new THREE.PointLight( 0xCBC3E3, 10, 500 );
pt1.position.set( 30, 50, 5 );
pt1.lookAt(12,20,0);

// point light 2
const pt2 = new THREE.PointLight( 0xCBC3E3, 10, 1000 );
pt2.position.set( 0, 50, 5 );
pt2.lookAt(12,20,0);

// point light 3
const pt3 = new THREE.PointLight(0xCBC3E3, 10, 500);
pt3.position.set( 60, 50, 5 );
pt3.lookAt(12,20,0);

// point light 4
const pt4 = new THREE.DirectionalLight( 0xCBC3E3, 1 );
pt4.position.set( 25,100,-70 );
pt4.rotation.y -= Math.PI/2;

// ambient scene lighting
const light = new THREE.AmbientLight( 0xF6E7D2 ); // soft white light
light.position.z = -25;
light.position.y = 5;
light.intensity = 0;

// add all lights to scene (obs)
obs.add( rectLight, pt1, pt2, pt3, pt4, light );
```

- The scene features customizable lighting using various types of light sources.
- Users can adjust the brightness of the scene's lighting using a GUI control.
- Lighting options include ambient light and multiple point lights with adjustable colors.
### Interactive Controls:

```
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
import {GUI} from 'dat.gui';

const controls = new OrbitControls( camera, renderer.domElement );
controls.update();

const gui = new GUI();

const scenelights = gui.addFolder('Scene Lighting');
scenelights.add(settings, 'ambient', 0,1);
scenelights.add(settings, 'point1');
scenelights.add(settings, 'point2');
scenelights.add(settings, 'point3');

```

- The scene allows users to interact with the 3D objects using an OrbitControls interface. Users can rotate and zoom the camera to explore the scene.
- A GUI interface is provided to adjust the lighting settings and control the visibility of specific point lights.

### Material Customization:

```
const lightsettings = gui.addFolder('Brite-Lite Settings');
lightsettings.addColor(settings, 'bulb1');
lightsettings.addColor(settings, 'bulb2');
lightsettings.addColor(settings, 'bulb3');
lightsettings.addColor(settings, 'bulb4');
lightsettings.addColor(settings, 'bulb5');
lightsettings.add(settings, 'brightness', 1, 500);

```

- The Mickey Mouse design is made up of different colored bulbs, and the project allows users to customize the colors of these materials using the GUI interface.
- Each bulb material can be individually adjusted in terms of color.

### 3D Shape Extrusion:
```
const flmesh = new THREE.Mesh(fl, wallmat);
// adjust rotation
flmesh.rotation.z += Math.PI/2;
flmesh.rotation.x -= Math.PI/2;

// set position to centered and hieght just below y=0
flmesh.position.x = 30;
flmesh.position.y = -.75; 
flmesh.position.z = 25;

// allow desk to recieve shadows from objects in real time
flmesh.receiveShadow = true;
scene.add(flmesh);

```
- The project utilizes extrusion to create a desk-like structure within the scene.
The desk-like structure is customizable in terms of color and material.

### Textures:

```
// reflection mapping on lightbulbs

const hdrEquirect = new RGBELoader().load(
    "./src/empty_warehouse_01_2k.hdr",  
    () => { 
      hdrEquirect.mapping = THREE.EquirectangularReflectionMapping; 
    }
  );
```

- Textures are applied to specific objects within the scene, enhancing their visual appearance.
- Realistic reflection on bulbs is achieved by mapping a HDR environment map of a warehouse with bright overhead lighting onto each sphere

<img width="846" alt="Screenshot 2023-11-07 at 3 40 57 PM" src="https://github.com/likornguth/LiteBrite/assets/97198397/dd130d0c-ee8e-4a43-8bcf-19d4812ebeef">

### External Data Integration:

```
fetch('src/Mickey Mouse - Sheet1.csv')
    .then(response =>response.text())
    .then(text => {
        data = Papa.parse(text).data;

        for (var i = 0; i < data.length; i++) {
            mickey.push((int)(data[i]));
          }      
        mickey = mickey.reverse();
    });
```
- Colors were translated manually from reference image, converted to 35 x 32 px array of pixel coordinates, and then converted to CSV file
- The project loads external data from a CSV file and incorporates it into the Mickey Mouse figure.

### Plotting Points on the Pegboard
```
for(var i = h/8-2; i>=0; i--){
    // for each row
    for(var j = 0; j<((w/8-1)+(1-i%2)); j++){
        // for each column
        const newmesh = new THREE.Mesh(geometry1, mtls[mickey[ct]]);
        newmesh.scale.x = 0.6;
        newmesh.scale.y = 0.6;
        newmesh.scale.z = 0.6;
        if(i%2==0){
            newmesh.position.x = 1.6*j+1.25;
        }
        else{
            newmesh.position.x = 1.6*j+1.8;
        }
        newmesh.position.y = 1.6*i+1.8;
        newmesh.position.z = .5;
        obs.add(newmesh);
        
        if(i%2==0){
            boardshape.holes.push(new THREE.EllipseCurve(
                ax+5,  ay+8,            // ax, aY
                2.5, 2.5,           // xRadius, yRadius
                0,  2 * Math.PI,  // aStartAngle, aEndAngle
            ));
        }
        else{
            boardshape.holes.push(new THREE.EllipseCurve(
                ax+8,  ay+8,            // ax, aY
                2.5, 2.5,           // xRadius, yRadius
                0,  2 * Math.PI,  // aStartAngle, aEndAngle
            ));
        }
        ax = ax+8;
        ct++;
        
    }
    ax = 0;
    ay+=8;
}
```
- The board in the reference image contained alternating rows of 35 and 36 holes, offset by one 
- This loop has 2 functions
    - Punch a circular hole into the box geometry of the pegboard, so the LED light can shine through
    - Create a new instance of a lightbulb object of the appropriate color in each hole



### Performance Monitoring:
```
import Stats from 'three/examples/jsm/libs/stats.module';
const stats = new Stats();
document.body.appendChild(stats.dom);

```
- The project incorporates a performance monitoring tool (Stats) to track and display the frame rate and other performance metrics.

### Responsive Design:

```
renderer.setSize( window.innerWidth, window.innerHeight );
document.body.appendChild( renderer.domElement );
const width = 60;
const height = 50;
```

- The scene adjusts to the dimensions of the user's browser window.
- This project offers an interactive and visually appealing 3D scene with Mickey Mouse and allows users to customize the lighting, materials, and more using a user-friendly graphical interface. Users can explore the scene from different angles and observe the visual effects of their customization choices.

#### Animation Loop
```
function animate() {
	requestAnimationFrame( animate );
    rectLight.intensity = settings.brightness;
    light.intensity = settings.ambient;
    pt1.visible = settings.point1;
    pt2.visible= settings.point2;
    pt3.visible = settings.point3;
    material1.color.set(settings.bulb5);
    material2.color.set(settings.bulb1);
    material3.color.set(settings.bulb2);
    material4.color.set(settings.bulb3);
    material5.color.set(settings.bulb4);
    controls.update();
	renderer.render( scene, camera );
    stats.update();
}
```

- The animation loop is constantly updated to reflect changes in the GUI parameters as well as any window size changes