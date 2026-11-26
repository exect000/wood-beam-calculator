
<script setup>
import { ref, watch, onMounted, onBeforeUnmount } from 'vue';
import * as THREE from 'three';
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';

const props = defineProps({
  length: { type: Number, required: true },
  width: { type: Number, required: true },
  height: { type: Number, required: true },
});

const container = ref(null);

let scene;
let camera;
let renderer;
let controls;
let beam;
let edges;
let resizeObserver;

function render() {
  if (renderer && scene && camera) {
    renderer.render(scene, camera);
  }
}

// Adjust the camera so the entire beam remains visible.
function fitCamera() {
  if (!camera || !controls) return;

  const { length, width, height } = props;

  const radius = Math.sqrt(
    length ** 2 + width ** 2 + height ** 2
  ) / 2;

  const verticalFov = THREE.MathUtils.degToRad(camera.fov);
  const horizontalFov = 2 * Math.atan(
    Math.tan(verticalFov / 2) * camera.aspect
  );

  const fov = Math.min(verticalFov, horizontalFov);
  const distance = Math.max(
    0.2,
    (radius / Math.sin(fov / 2)) * 1.25
  );

  const direction = camera.position.clone().normalize();
  camera.position.copy(direction.multiplyScalar(distance));

  camera.far = Math.max(100, distance * 20);
  camera.updateProjectionMatrix();

  controls.update();
  render();
}

// Rebuild the beam when its dimensions change.
function updateBeam() {
  if (!beam || !edges) return;

  const geometry = new THREE.BoxGeometry(
    props.length,
    props.height,
    props.width
  );

  beam.geometry.dispose();
  edges.geometry.dispose();

  beam.geometry = geometry;
  edges.geometry = new THREE.EdgesGeometry(geometry);

  fitCamera();
}

onMounted(() => {
  // Create the Three.js scene.
  scene = new THREE.Scene();
  scene.background = new THREE.Color(0xf1f5f9);

  // Create the camera.
  camera = new THREE.PerspectiveCamera(
    45, 1, 0.01, 1000
  );
  camera.position.set(1, 0.7, 1);

  // Create the renderer.
  renderer = new THREE.WebGLRenderer({
    antialias: true,
  });

  renderer.setPixelRatio(
    Math.min(window.devicePixelRatio || 1, 2)
  );

  renderer.domElement.style.width = '100%';
  renderer.domElement.style.height = '100%';
  renderer.domElement.style.display = 'block';

  container.value.appendChild(renderer.domElement);

  // Add lighting.
  const ambientLight = new THREE.AmbientLight(
    0xffffff, 1.3
  );
  scene.add(ambientLight);

  const directionalLight = new THREE.DirectionalLight(
    0xffffff, 2
  );
  directionalLight.position.set(4, 8, 6);
  scene.add(directionalLight);

  // Create the wooden beam.
  const geometry = new THREE.BoxGeometry(1, 1, 1);

  const material = new THREE.MeshStandardMaterial({
    color: 0xb9844c,
    roughness: 0.85,
  });

  beam = new THREE.Mesh(geometry, material);
  scene.add(beam);

  // Add dark outlines around the beam.
  edges = new THREE.LineSegments(
    new THREE.EdgesGeometry(geometry),
    new THREE.LineBasicMaterial({
      color: 0x5a3b25,
    })
  );
  scene.add(edges);

  // Enable mouse rotation and zooming.
  controls = new OrbitControls(
    camera,
    renderer.domElement
  );

  controls.enablePan = false;
  controls.addEventListener('change', render);

  // Handle window and container resizing.
  function resize() {
    const width = container.value.clientWidth;
    const height = container.value.clientHeight;

    if (!width || !height) return;

    camera.aspect = width / height;
    camera.updateProjectionMatrix();

    renderer.setSize(width, height, false);
    fitCamera();
  }

  resizeObserver = new ResizeObserver(resize);
  resizeObserver.observe(container.value);

  updateBeam();
  resize();
});

// React to changes in the calculator inputs.
watch(
  () => [props.length, props.width, props.height],
  () => {
    const dimensions = [
      props.length,
      props.width,
      props.height,
    ];

    if (dimensions.every(
      value => Number.isFinite(value) && value > 0
    )) {
      updateBeam();
    }
  }
);

// Clean up resources when the component is removed.
onBeforeUnmount(() => {
  resizeObserver?.disconnect();

  controls?.removeEventListener('change', render);
  controls?.dispose();

  beam?.geometry.dispose();
  beam?.material.dispose();

  edges?.geometry.dispose();
  edges?.material.dispose();

  renderer?.dispose();
  renderer?.domElement.remove();
});
</script>

<template>
  <div ref="container" class="beam-viewer"></div>

  <p class="viewer-hint">
    Pagrieziet modeli ar peli. Izmantojiet peles rullīti,
    lai pietuvinātu vai attālinātu.
  </p>
</template>

<style scoped>
.beam-viewer {
  width: 100%;
  height: 350px;
  overflow: hidden;
  border-radius: 10px;
  background: #f1f5f9;
  touch-action: none;
}

.viewer-hint {
  margin: 12px 0 0;
  text-align: center;
  font-size: 13px;
  color: #64748b;
}
</style>
