<script>
  import { T } from '@threlte/core';
  import { Align, OrbitControls } from '@threlte/extras';
  import { AutoColliders, Debug } from '@threlte/rapier'
  import { DEG2RAD } from 'three/src/math/MathUtils.js'

  import GeoTIFF, { fromUrl, fromUrls, fromArrayBuffer, fromBlob } from 'geotiff';
	import { onMount } from 'svelte';
	import { PlaneGeometry } from 'three';
  
  let image = {};
  let positions;

 
  onMount(async () => {
    const rawTiff = await fromUrl('data/mallorca90.tif');
    const tiff = await rawTiff;
    const tifImage = await rawTiff.getImage();


    const width = tifImage.getWidth();
    const height= tifImage.getHeight();
    image = {
      width,
      height
    }

    // const geometry = new PlaneGeometry(1,1, width-1, height-1)
    // const vertices = geometry.getAttribute('position').array;
    // console.log(vertices)

    const data = await tifImage.readRasters();

    const t = data[0];
    const length = t.length;
    positions = new Float32Array(length * 3);

    for (let p = 0; p < length; ++p) {
      // let c = colourScale(t[p]);
      let x = p % width;
      let y = p / width;
      positions[p * 3] = x
      positions[p * 3 + 1] = y;
      positions[p * 3 + 2] = t[p]/2;
    }
  });

</script>
<!-- 
{#each data as d}
    <T.Mesh
        position.x={xScale(d.x)}
        position.z={zScale(d.z)}
        position.y={yScale(d.y)}
        scale.y={1}
        castShadow
    >
        <T.BoxGeometry />
        <T.MeshStandardMaterial color={'#B392AC'} />
    </T.Mesh>
{/each} -->
{#if positions}
  <!-- <Align>
    <T.Points>
    <T.BufferGeometry>
        <T.BufferAttribute
          args={[positions, 3]}
          attach={(parent, self) => {
              parent.setAttribute('position', self)
              return () => {
              // cleanup function called when ref changes or the component unmounts
              // https://threlte.xyz/docs/reference/core/t#attach
              }
          }}
        />
    </T.BufferGeometry>
    <T.PointsMaterial size={1} />
    </T.Points>
  </Align> -->
  <!-- <T.Mesh name="terrain">
    <T.PlaneGeometry
      args={[1,1,image.width-1, image.height-1]} />
    <T.MeshBasicMaterial 
      color={"#3dbb9f"} 
      wireframe={true}
      transparent 
       />
  </T.Mesh> -->
{/if}

<T.PerspectiveCamera
    makeDefault
    position.y={5}
    position.z={10}
    lookAt.y={2}>
    <OrbitControls
      autoRotate={false}
      enableZoom={true}
      maxPolarAngle={DEG2RAD * 80}
    />
</T.PerspectiveCamera>
<T.AmbientLight intensity={0.2} />
<T.DirectionalLight castShadow position={[10, 20, 5]} />

<!-- <AutoColliders shape="trimesh">
  <T.Mesh
    {geometry}
    rotation.x={DEG2RAD * -90}
  >
    <T.MeshStandardMaterial />
  </T.Mesh>
</AutoColliders> -->
<!-- <T.Mesh receiveShadow rotation.x={-90 * (Math.PI / 180)}>
	<T.CircleGeometry args={[30, 72]} />
	<T.MeshStandardMaterial />
</T.Mesh> -->

