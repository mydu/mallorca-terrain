<script>

    import geoJsonData from '$data/mallorcaChoropleth.json';
    import { Canvas, T } from '@threlte/core'
    import { OrbitControls } from '@threlte/extras';

    import { Shape, ShapeGeometry, MeshBasicMaterial, Mesh, Group } from "three";
    export let geojson;

    function createPolygonShape(feature) {
        
        const shape = new Shape();
        const [firstRing, ...holes] = feature.geometry.coordinates;
        console.log(firstRing);
        firstRing.forEach(([x, y], index) => {
            if (index === 0) {
            shape.moveTo(x, y);
            } else {
            shape.lineTo(x, y);
            }
        });

        holes.forEach((hole) => {
            const holePath = new Shape();
            hole.forEach(([x, y], index) => {
            if (index === 0) {
                holePath.moveTo(x, y);
            } else {
                holePath.lineTo(x, y);
            }
            });
            shape.holes.push(holePath);
        });

        return shape;
    }
</script>

<!-- <T.Scene >
    {#each geoJsonData.features as feature}
    <T.Mesh>
        <T.ShapeGeometry shape={createPolygonShape(feature)} />
        <T.MeshBasicMaterial color="#000000" side={2} />
    </T.Mesh>
    {/each}
</T.Scene> -->


<T.PerspectiveCamera
  makeDefault
  position={[0, 10, 90]} fov={3}
>
  <OrbitControls 
    enableZoom={true} />
</T.PerspectiveCamera>
<T.AmbientLight intensity={0.5} />
<T.DirectionalLight position={[10, 10, 10]} intensity={0.5} />



