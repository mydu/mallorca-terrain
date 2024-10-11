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
// export let heightScale;
export let isAnimated;
let heightScale = 1;
export let wireframeDensity = 0.5;


// function hexToRgb(hex) {
//   const r = parseInt(hex.slice(1, 3), 16);
//   const g = parseInt(hex.slice(3, 5), 16);
//   const b = parseInt(hex.slice(5, 7), 16);
//   return { r, g, b };
// }
// $: rgbColor = hexToRgb(color);

function hexToVector3(hex) {
  hex = hex.replace(/^#/, '');
  const bigint = parseInt(hex, 16);
  const r = (bigint >> 16) & 255;
  const g = (bigint >> 8) & 255;
  const b = bigint & 255;
  return new THREE.Vector3(r / 255, g / 255, b / 255);
}
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
// $: geometry = new THREE.PlaneGeometry(10, 10, width-1, height-1);
  // Modify the geometry creation
  // $: geometry = new THREE.PlaneGeometry(10, 10, 
  //   Math.floor((width - 1) * wireframeDensity), 
  //   Math.floor((height - 1) * wireframeDensity)
  // );

// $: positionAttribute = geometry.getAttribute('position')


  $: geometryWidth = Math.max(2, Math.floor((width - 1) * wireframeDensity) + 1);
  $: geometryHeight = Math.max(2, Math.floor((height - 1) * wireframeDensity) + 1);

  $: geometry = new THREE.PlaneGeometry(10, 10, geometryWidth - 1, geometryHeight - 1);

  $: {
    const positionAttribute = geometry.getAttribute('position');
    for (let i = 0; i < geometryHeight; i++) {
      for (let j = 0; j < geometryWidth; j++) {
        const index = i * geometryWidth + j;
        const heightMapX = Math.floor(j / wireframeDensity);
        const heightMapY = Math.floor(i / wireframeDensity);
        const heightMapIndex = heightMapY * width + heightMapX;
        const height = (heightMap[heightMapIndex] || 0) * heightScale / 1000;
        positionAttribute.setZ(index, height);
      }
    }
    geometry.computeVertexNormals();
  }
// $:  for (let i = 0; i < positionAttribute.count; i++) {
//     positionAttribute.setZ(i, heightMap[i] * heightScale /1000)
//   }

//  $: geometry.computeVertexNormals()

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
    uniform vec3 color;
    void main() {
      if (vHeight == 0.0) {
        discard;
      }
      gl_FragColor = vec4(color,0.3);
    }
  `;

  // $: material = new THREE.ShaderMaterial({
  //   vertexShader,
  //   fragmentShader,
  //   uniforms: {
  //     uColor: { value: new THREE.Color(0x2260ff) }
  //   },
  //   transparent: true,
  //   wireframe: true,
  //   flatShading: true
  // });

    // Update material color when color changes
  // Update material color when color changes
  // $: if (material) {
  //   material.uniforms.uColor.value.set(color);
  // }
</script>

<T.Mesh 
  geometry={geometry} 
  rotation.x={-90 * DEG2RAD}
>
  <T.ShaderMaterial
    vertexShader={vertexShader}
    fragmentShader={fragmentShader}
    uniforms={{
      color: {value: hexToVector3(color)}
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
  <OrbitControls autoRotate={isAnimated} enableZoom={true} />
</T.PerspectiveCamera>
<T.AmbientLight intensity={0.5} />
<T.DirectionalLight position={[10, 10, 10]} intensity={0.5} />
