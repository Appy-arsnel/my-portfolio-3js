<template>
  <div ref="container"></div>
</template>

<script setup>
import * as THREE from 'three'
import { onMounted, ref } from 'vue'

const container = ref(null)

onMounted(() => {
  // Three.js code goes here
  const scene = new THREE.Scene()
  scene.fog = new THREE.FogExp2(0x11111f, 0.002)

  const camera = new THREE.PerspectiveCamera(60,window.innerWidth / window.innerHeight,1,1000);
  camera.position.z = 5;
  
  

  const renderer = new THREE.WebGLRenderer()
  renderer.setClearColor(scene.fog.color);
  renderer.setSize(window.innerWidth, window.innerHeight)
  container.value.appendChild(renderer.domElement)

  const geometry = new THREE.BoxGeometry()
  const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 })
  const cube = new THREE.Mesh(geometry, material)
  scene.add(cube)

  const ambient = new THREE.AmbientLight(0x555555);
  scene.add(ambient)
  
  //LIGHTS
    var dLight = new THREE.DirectionalLight(0xffffff);
    dLight.position.set(0, 0, 0);
    dLight.shadowCameraVisible = true;
    dLight.shadowCameraNear = 1;
    dLight.shadowCameraFar = 150;
    dLight.castshadow = true;
    scene.add(dLight);

  function animate() {
    requestAnimationFrame(animate)
    cube.rotation.x += 0.01
    cube.rotation.y += 0.01
    renderer.render(scene, camera)
  }

  animate()
})
</script>