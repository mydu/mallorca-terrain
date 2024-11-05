<script>
    import { Canvas } from "@threlte/core";
    import Scene from '$lib/components/Choropleth.svelte';
    import geoMap from '$data/mallorcaChoropleth.json';
    import { Grid, Gizmo } from '@threlte/extras'

    import { onMount } from "svelte";
    import * as d3 from "d3";
	import ScenePolygon from "$lib/components/ScenePolygon.svelte";
    let width;
    let height;
    let gridSize = 10;
</script>

<div class="w-full h-screen" bind:clientWidth={width} bind:clientHeight={height}>
    <Canvas>
        <Gizmo
      horizontalPlacement="left"
      size={70}
      paddingX={10}
      paddingY={10}
    />
        <!-- <Scene geojson={geoMap} /> -->
        <Grid
            plane='xz'
            gridSize={[10,10]}
        />
         {#if width>0 && height>0}
            <ScenePolygon geojson={geoMap} {width} {height}/>
         {/if}
    </Canvas>
</div>
