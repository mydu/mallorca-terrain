<script>
  import { Canvas } from '@threlte/core'
  import Scene from '$lib/components/Elevation.svelte';
  import { fromArrayBuffer } from 'geotiff';

  let tifFile;
  let uploadStatus = '';
  let tiffData = null;

  async function handleUpload() {

    if (!tifFile) {
      uploadStatus = 'Please select a file to upload';
      return;
    }
    if (tifFile[0].name.split('.').pop() !== 'tif' && tifFile[0].name.split('.').pop() !== 'tiff') {
      uploadStatus = 'Please select a valid .tif or .tiff file';
      return;
    }

    try {
      const arrayBuffer = await tifFile[0].arrayBuffer();
      const tiff = await fromArrayBuffer(arrayBuffer);
      const image = await tiff.getImage();
      const rasters = await image.readRasters();
      
      tiffData = {
        width: image.getWidth(),
        height: image.getHeight(),
        rasters: rasters
      };
      uploadStatus = 'TIF file read successfully';
    } catch (error) {
      uploadStatus = 'Error reading TIF file: ' + error.message;
      tiffData = null;
    }
  }
</script>

<div class="flex h-[100vh] bg-black">
  <Canvas>
    {#if tiffData}
      <Scene width={tiffData.width} height={tiffData.height} heightMap={tiffData.rasters[0]} />
    {/if}
  </Canvas>
</div>

<div class="absolute top-4 left-4 bg-white p-4 rounded">
  <input type="file" bind:files={tifFile} accept=".tif,.tiff" />
  <button on:click={handleUpload}>Upload and Read TIF</button>
  {#if uploadStatus}
    <p>{uploadStatus}</p>
  {/if}
  {#if tiffData}
    <p>Width: {tiffData.width}, Height: {tiffData.height}</p>
    <!-- You can display or process tiffData.rasters here -->
  {/if}
</div>