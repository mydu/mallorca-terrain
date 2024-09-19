<script>
  import { Canvas, T } from '@threlte/core'
  import { DEG2RAD } from 'three/src/math/MathUtils'
  import * as THREE from 'three'
  import { Align, OrbitControls } from '@threlte/extras';

  // Terrain parameters
export let width ;
export let height;
export let heightMap;

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
$: geometry = new THREE.PlaneGeometry(10,10, width-1, height-1);
$: positionAttribute = geometry.getAttribute('position')

$:  for (let i = 0; i < positionAttribute.count; i++) {
    positionAttribute.setZ(i, heightMap[i]/1000)
  }

 $: geometry.computeVertexNormals()

  // Material

</script>

<T.Mesh 
  geometry={geometry} 
  rotation.x={-90 * DEG2RAD}
>
  <T.MeshStandardMaterial color="#ffffff" wireframe={true} flatShading={true} transparent opacity={0.5} />
</T.Mesh>
<T.PerspectiveCamera
  makeDefault
  position={[0, 10, 90]} fov={3}
>
  <OrbitControls autoRotate={false} enableZoom={true} />
</T.PerspectiveCamera>
<T.AmbientLight intensity={0.5} />
<T.DirectionalLight position={[10, 10, 10]} intensity={0.5} />
