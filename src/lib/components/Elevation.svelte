<script>
  import { T } from '@threlte/core';
  import { OrbitControls } from '@threlte/extras';
  import GeoTIFF, { fromUrl, fromUrls, fromArrayBuffer, fromBlob } from 'geotiff';
	import { onMount } from 'svelte';
	import { Plane } from 'three';
  
  let image = {};

  onMount(async () => {
    const rawTiff = await fromUrl('data/mallorca90.tif');
    const tiff = await rawTiff;
    const tifImage = await rawTiff.getImage();

    image = {
        width: tifImage.getWidth(),
        height: tifImage.getHeight(),
      };
    const data = await image.readRasters();
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
<T.Mesh>
  <T.PlaneGeometry args={[image.width, image.height]} />
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

