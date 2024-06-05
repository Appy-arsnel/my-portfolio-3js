<template>
  <div ref="container"></div>
</template>

<script setup>
import * as THREE from 'three'
import { onMounted, ref } from 'vue'
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';

const container = ref(null)



const scene = new THREE.Scene();

const color = 0xFFFFFF;
const intensity = 10;
const light = new THREE.AmbientLight(color, intensity);
scene.add(light);


const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 2000);

const renderer = new THREE.WebGLRenderer();

renderer.setSize(window.innerWidth, window.innerHeight);
scene.fog = new THREE.FogExp2(0x11111f, 0.002);
renderer.setClearColor(scene.fog.color);

let loaderTexture = new THREE.TextureLoader();
loaderTexture.load("/smoke.png", function(texture) {
    const cloudGeo = new THREE.PlaneGeometry(500, 500);
    const cloudMaterial = new THREE.MeshLambertMaterial({
        map: texture,
        transparent: true,
        depthWrite: false
    });

    for (let p = 0; p < 25; p++) {
        let cloud = new THREE.Mesh(cloudGeo, cloudMaterial);
        cloud.position.set(
            Math.random() * 800 - 400,
            500,
            Math.random() * 500 - 450
        );
        cloud.rotation.x = 1.16;
        cloud.rotation.y = -0.12;
        cloud.rotation.z = Math.random() * 360;
        cloud.material.opacity = 0.6;

        // Optionally set render order
        cloud.renderOrder = p;

        scene.add(cloud);
    }
});

// Create the rain geometry
const rainCount = 10000; // Number of raindrops
const rainGeo = new THREE.BufferGeometry();
const rainVertices = [];
const rainSpeeds = [];

for (let i = 0; i < rainCount; i++) {
  const x = Math.random() * 400 - 200;
  const y = Math.random() * 500 - 250;
  const z = Math.random() * 400 - 200;
  rainVertices.push(x, y, z);
  
  // Assign a random speed for each raindrop
  rainSpeeds.push(Math.random() * 0.2 + 0.1);
}

rainGeo.setAttribute('position', new THREE.Float32BufferAttribute(rainVertices, 3));

// Create the rain material
const rainMaterial = new THREE.PointsMaterial({
  color: 0xaaaaaa,
  size: 0.1,
  transparent: true,
});

// Create the Points object
const rain = new THREE.Points(rainGeo, rainMaterial);
scene.add(rain);



const loader = new GLTFLoader();

// Replace 'path/to/your/model.glb' with the actual path to your model
const modelPath = '/Bus station.glb';
loader.load(modelPath, (gltf) => {
  scene.add(gltf.scene);

}, undefined, (error) => {
  console.error(error);
});

camera.position.z = 5;
// Position the camera
camera.position.set(10, 4, 3);
camera.lookAt(new THREE.Vector3(0, 0, 0));

// Create OrbitControls
const controls = new OrbitControls(camera, renderer.domElement);
controls.enableDamping = true; // an animation loop is required when either damping or auto-rotation are enabled
controls.dampingFactor = 0.25;
controls.screenSpacePanning = false;
controls.maxPolarAngle = Math.PI/1.5   ;



// Handle window resize
window.addEventListener('resize', function () {
    const newWidth = window.innerWidth;
    const newHeight = window.innerHeight;

    camera.aspect = newWidth / newHeight;
    camera.updateProjectionMatrix();

    renderer.setSize(newWidth, newHeight);
});

function animate() {
  requestAnimationFrame(animate);
  
   // Get the positions array
  const positions = rainGeo.attributes.position.array;

  // Update the positions of the raindrops
  for (let i = 0; i < rainCount; i++) {
    positions[i * 3 + 1] -= rainSpeeds[i];

    // Reset the position if the raindrop is below a certain level
    if (positions[i * 3 + 1] < -250) {
      positions[i * 3 + 1] = 250;
    }
  }

  // Mark the positions attribute as needing an update
  rainGeo.attributes.position.needsUpdate = true;

  // Update controls if you have them
  controls.update();
  

  renderer.render(scene, camera);
}


onMounted(() => {
  /* init(); */
  container.value.appendChild(renderer.domElement);
  animate();
});


</script>
