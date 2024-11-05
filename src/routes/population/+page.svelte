<script>
    import { Canvas } from "@threlte/core";
    import Scene from '$lib/components/Choropleth.svelte';
    import geoMap from '$data/mallorcaChoropleth.json';

    import { onMount } from "svelte";
    import * as d3 from "d3";
	import ScenePolygon from "$lib/components/ScenePolygon.svelte";
    let width;
    let height;
//     function createPolygonShape(geoJson) {
//         const shape = new Shape();

//         const [firstRing, ...holes] = geoJson.geometry.coordinates;

//         firstRing.forEach(([x, y], index) => {
//         if (index === 0) {
//         shape.moveTo(x, y);
//         } else {
//         shape.lineTo(x, y);
//         }
//         });

//         holes.forEach((hole) => {
//         const holePath = new Shape();
//         hole.forEach(([x, y], index) => {
//         if (index === 0) {
//             holePath.moveTo(x, y);
//         } else {
//             holePath.lineTo(x, y);
//         }
//         });
//         shape.holes.push(holePath);
//         });

//         return shape;
//     }


</script>

<div class="w-full h-screen" bind:clientWidth={width} bind:clientHeight={height}>
    <Canvas>
        <!-- <Scene geojson={geoMap} /> -->
         {#if width>0 && height>0}
            <ScenePolygon geojson={geoMap} {width} {height}/>
         {/if}
    </Canvas>
</div>
