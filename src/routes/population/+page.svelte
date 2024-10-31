<script>
    import { Canvas } from "@threlte/core";
    import Scene from '$lib/components/Choropleth.svelte';
    import { onMount } from "svelte";
    import * as d3 from "d3";

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

// let mesh;
let data;

onMount(async () => {
    data= await d3.csv('data/population.csv');
    console.log(data)
});

</script>

<div class="w-full h-screen">
    <Canvas>

    </Canvas>
</div>
