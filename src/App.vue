<script lang="ts" setup>
import {Mesh, MeshBasicMaterial, PerspectiveCamera, Scene, SphereGeometry, WebGLRenderer} from 'three'
import {computed, onMounted, ref, watch} from "vue";
import {useWindowSize} from "@vueuse/core";

const experience = ref<HTMLCanvasElement | null>();
const scene = new Scene()
const {width, height} = useWindowSize()
const aspectRatio = computed(() => width.value / height.value)

let renderer: WebGLRenderer;
let camera: PerspectiveCamera;

function updateRenderer() {
  renderer.setSize(width.value, height.value)
  renderer.setPixelRatio(window.devicePixelRatio);
}
function updateCamera() {
  camera.aspect = aspectRatio.value;
  camera.updateProjectionMatrix();
}
watch(aspectRatio, updateCamera)
watch(aspectRatio, updateRenderer)

camera = new PerspectiveCamera(75, aspectRatio as number, 0.1, 1000)
scene.add(camera)
camera.position.z = 5

const sphere = new Mesh(new SphereGeometry(1, 20, 20), new MeshBasicMaterial({color: 0x008080}));
scene.add(sphere)

const loop = () => {
  renderer.render(scene,camera)


  sphere.position.y += 0.01;


  requestAnimationFrame(loop);
}

onMounted(() => {
  renderer = new WebGLRenderer({canvas: experience.value as HTMLCanvasElement, antialias: true});
  updateRenderer()
  updateCamera()
  loop()
})
</script>

<template>
  <canvas ref="experience"/>
</template>

