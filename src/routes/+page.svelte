<script>
  import { Canvas} from '@threlte/core'
  import Scene from '$lib/components/Elevation.svelte';
  import { fromArrayBuffer } from 'geotiff';
  import { STLExporter } from 'three/addons/exporters/STLExporter.js';

  let tifFile;
  let uploadStatus = '';
  let tiffData = null;

  let textColor = '#ffffff'; // Default color
  let bgColor = '#000000'; // Default color
  let vertaxScale = 0.5; // Default height scale
  let isAnimated = true;
  let rotateSpeed = 0.5;

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
  let blob;
  let scene;

  function exportSTL() {
    const exporter = new STLExporter()
    const stl = exporter.parse(scene)
    
    // Create a Blob with the STL data
    blob = new Blob([stl], { type: 'application/octet-stream' })
}
</script>

<div class="flex h-[100vh]" style="background-color: {bgColor}">
  <Canvas>
    {#if tiffData}
      <Scene 
        bind:scene={scene}
        width={tiffData.width} 
        height={tiffData.height} 
        wireframeDensity = {vertaxScale}
        isAnimated={isAnimated}
        {rotateSpeed}
        heightMap={tiffData.rasters[0]} 
        color={textColor} />
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
    <!-- You an display or process tiffData.rasters here -->
  {/if}
  <div>
    <span>Texture Color</span>
    <input type="color" bind:value={textColor} />
    <span>Background Color</span>
    <input type="color" bind:value={bgColor} />
    <div>
      <span>wireframe density</span>
      <input type="range" min="0.05" max="1" step="0.05" bind:value={vertaxScale} />
      <span>{vertaxScale}</span>
    </div>
  </div>
  <span>animate scene</span>
  <input type="checkbox" bind:checked={isAnimated} />
  <span>rotate speed</span>
  <input type="range" min="0.1" max="1" step="0.1" bind:value={rotateSpeed}  />
  <span>{rotateSpeed}</span>
  <!-- <button on:click={exportSTL}>Export STL</button> -->
  {#if blob}
    <a href={URL.createObjectURL(blob)} download="terrain.stl">download stl</a>
  {/if}
</div>



