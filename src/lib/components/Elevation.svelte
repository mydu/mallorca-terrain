<script>
  import { Canvas, T } from '@threlte/core'
  import { DEG2RAD } from 'three/src/math/MathUtils'
  import * as THREE from 'three'
  import { Align, OrbitControls } from '@threlte/extras';

  // Terrain parameters
export let width ;
export let height;
export let heightMap;
export let color;
export let heightScale;
// function hexToRgb(hex) {
//   const r = parseInt(hex.slice(1, 3), 16);
//   const g = parseInt(hex.slice(3, 5), 16);
//   const b = parseInt(hex.slice(5, 7), 16);
//   return { r, g, b };
// }
// $: rgbColor = hexToRgb(color);

// $: console.log(rgbColor)

  // // Create height map
  // function createHeightMap() {
  //   const size = segments + 1
  //   const data = new Float32Array(size * size)
  //   for (let i = 0; i < size; i++) {
  //     for (let j = 0; j < size; j++) {
  //       const index = i * size + j
  //       // Simple noise function for demonstration
  //       data[index] = Math.sin(i * 0.2) * Math.cos(j * 0.2) * 2
  //     }
  //   }
  //   return data
  // }

  // const heightMap = createHeightMap()


  // Create geometry
$: geometry = new THREE.PlaneGeometry(10, 10, width-1, height-1);
$: positionAttribute = geometry.getAttribute('position')

$:  for (let i = 0; i < positionAttribute.count; i++) {
    positionAttribute.setZ(i, heightMap[i] * heightScale /1000)
  }

 $: geometry.computeVertexNormals()

  // Material
  // Custom shader material
  const vertexShader = `
    varying float vHeight;
    void main() {
      vHeight = position.z;
      gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
    }
  `;

  const fragmentShader = `
    varying float vHeight;
    void main() {
      if (vHeight == 0.0) {
        discard;
      }
      gl_FragColor = vec4(1.0, 1.0, 1.0, 0.5);
    }
  `;

  $: material = new THREE.ShaderMaterial({
    vertexShader,
    fragmentShader,
    uniforms: {
      uColor: { value: new THREE.Color(0x2260ff) }
    },
    transparent: true,
    wireframe: true,
    flatShading: true
  });

    // Update material color when color changes
  // Update material color when color changes
  $: if (material) {
    material.uniforms.uColor.value.set(color);
  }
</script>

<T.Mesh 
  geometry={geometry} 
  rotation.x={-90 * DEG2RAD}
>
  <T.ShaderMaterial
    vertexShader={vertexShader}
    fragmentShader={fragmentShader}
    uniforms={{
      uColor: new THREE.Color("#ff00ff")
    }}
    transparent={true}
    wireframe={true}
    flatShading={true}
   />

  <!-- <T.MeshStandardMaterial 
    color={color} 
    wireframe={true} 
    flatShading={true} 
    transparent opacity={0.5} /> -->
</T.Mesh>
<T.PerspectiveCamera
  makeDefault
  position={[0, 10, 90]} fov={3}
>
  <OrbitControls autoRotate={true} enableZoom={true} />
</T.PerspectiveCamera>
<T.AmbientLight intensity={0.5} />
<T.DirectionalLight position={[10, 10, 10]} intensity={0.5} />
