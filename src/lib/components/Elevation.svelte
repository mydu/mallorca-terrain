<script>
  import { T } from '@threlte/core';
  import { Align, OrbitControls } from '@threlte/extras'
  import GeoTIFF, { fromUrl, fromUrls, fromArrayBuffer, fromBlob } from 'geotiff';
	import { onMount } from 'svelte';
	import { Plane } from 'three';
  
  let image = {};
  let positions;

  onMount(async () => {
    const rawTiff = await fromUrl('data/mallorca90.tif');
    const tiff = await rawTiff;
    const tifImage = await rawTiff.getImage();


    const width = tifImage.getWidth();
    const height= tifImage.getHeight();
    

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
      positions[p * 3 + 2] = t[p];
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
  <Align>
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
    <T.PointsMaterial size={0.25} />
    </T.Points>
  </Align>
{/if}
<T.Mesh>
  <!-- <T.PlaneGeometry args={[image.width, image.height]} /> -->
  <T.MeshStandardMaterial color={"#3dbb9f"}  transparent opacity={0.8} />
</T.Mesh>
<T.PerspectiveCamera
    makeDefault
    position={[22, 15,22]}
    on:create={({ ref }) => {
        ref.lookAt(0, 0, 4);
    }}>	
    <OrbitControls
      enableDamping
      target={[0, 4, 0]}
    />
</T.PerspectiveCamera>
<T.AmbientLight intensity={0.2} />
<T.DirectionalLight castShadow position={[10, 20, 5]} />

<!-- <T.Mesh receiveShadow rotation.x={-90 * (Math.PI / 180)}>
	<T.CircleGeometry args={[30, 72]} />
	<T.MeshStandardMaterial />
</T.Mesh> -->

