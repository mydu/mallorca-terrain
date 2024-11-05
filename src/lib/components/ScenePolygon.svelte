<script>
  import { T } from '@threlte/core'
  import { OrbitControls } from '@threlte/extras'
  // import { geoMercator } from 'd3-geo'
  import * as d3 from 'd3';
  import GeoJSONMesh from  "$lib/components/GeoMesh.svelte"
  
  export let geojson;
  export let width;
  export let height;

  console.log(width/height)

  // Complex GeoJSON example with multiple geometry types
  const sampleGeoJSON = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {
          "name": "Simple Polygon",
          "height": 1,
          "color": "#ff0000"
        },
        "geometry": {
          "type": "Polygon",
          "coordinates": [[
            [-1, -1],
            [1, -1],
            [1, 1],
            [-1, 1],
            [-1, -1]
          ]]
        }
      },
      {
        "type": "Feature",
        "properties": {
          "name": "Simple Polygon",
          "height": 1,
          "color": "#ff0000"
        },
        "geometry": {
          "type": "Polygon",
         "coordinates": [[
          [
            2.727809861,
            9.82338978807184
        ],
        [
            2.736496045,
            9.816390349071874
        ],
        [
            2.740015358,
            9.806406143071946
        ],
        [
            2.727809861,
            9.82338978807184
        ],
          ]]
          }
        },
      // {
      //   "type": "Feature",
      //   "properties": {
      //     "name": "MultiPolygon",
      //     "height": 0.5,
      //     "color": "#0000ff"
      //   },
      //   "geometry": {
      //     "type": "MultiPolygon",
      //     "coordinates": [
      //       [[[2, 2], [3, 2], [3, 3], [2, 3], [2, 2]]],
      //       [[[2, -2], [3, -2], [3, -1], [2, -1], [2, -2]]]
      //     ]
      //   }
      // },
      // {
      //   "type": "Feature",
      //   "properties": {
      //     "name": "LineString",
      //     "color": "#00ff00"
      //   },
      //   "geometry": {
      //     "type": "LineString",
      //     "coordinates": [
      //       [-2, 0],
      //       [-1.5, 1],
      //       [-1, 0]
      //     ]
      //   }
      // }
    ]
  }

  // Setup D3 projection
  $: projection = d3.geoMercator()
      .translate([0,0])
      // .translate([width/2, height/2])
      .scale(100)
    // .scale([100]);

  // $: path = d3.geoPath().projection(projection)
  //    // Compute the bounding box of the GeoJSON
  // $: feature = path(geojson);
  // $: console.log(feature)
  // // $: bbox = feature.getBBox();

  // $: vertices = new Float32Array(feature.reduce((acc, d) => [...acc, ...d.geometry.coordinates[0].flatMap((c) => [c[0], c[1], 0])], []));
  // Adjust the camera to fit the GeoJSON area
  // const aspect = 1;
  // $: scale = Math.min(1 / bbox.width * (aspect < 1 ? aspect : 1), 1 / bbox.height * (aspect >= 1 ? 1 : aspect));
  // $: cameraX = (bbox.x + bbox.width / 2) * scale;
  // $: cameraY = (bbox.y + bbox.height / 2) * scale;

  // camera.zoom = scale;
  // camera.updateProjectionMatrix();
</script>

<T.PerspectiveCamera
  makeDefault
  position={[45,1.5, 90]} fov={3}
>
  <OrbitControls 
    enableZoom={true} />
</T.PerspectiveCamera>
<T.AmbientLight intensity={0.5} />
<T.DirectionalLight position={[10, 10, 10]} intensity={0.5} />

<!-- <T.Points>
  <T.BufferGeometry>
      <T.BufferAttribute
        args={[vertices, 3]}
      />
  </T.BufferGeometry>
  <T.PointsMaterial size={1} color={"0x000000"} />
</T.Points> -->
<GeoJSONMesh 
  data={sampleGeoJSON}
  {projection}
/>
