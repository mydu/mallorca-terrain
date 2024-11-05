<script>
    import { MapLibre, GeoJSON, FillExtrusionLayer, LineLayer} from 'svelte-maplibre';
    import { onMount } from 'svelte';
    import * as d3 from 'd3';


    import mallorca from '$data/mallorcaSimple.json';
    let data=[];

    onMount(async () => {
        const raw = await d3.csv('data/SuperficieAll.csv', d3.autoType);
        data = raw.map(d=>({...d, code: "0"+d.code}))
    });

    let selectedYear = 2023;

    const style = {
        version: 8,
        name: 'Empty',
        sources: {},
        layers: [{
            id: 'background',
            type: 'background',
            paint: {
                'background-color': '#ffffff'  // or any color you prefer
            }
        }]
    }
    $: superfieData =  {
        type: 'FeatureCollection',
        features: mallorca.features.map(p => ({
            type: 'Feature',
            geometry: {
                type: 'Polygon',
                coordinates: [
                    [
                        [p.properties.geo_point_2d.lon - 0.01, p.properties.geo_point_2d.lat - 0.01],
                        [p.properties.geo_point_2d.lon + 0.01, p.properties.geo_point_2d.lat - 0.01],
                        [p.properties.geo_point_2d.lon + 0.01, p.properties.geo_point_2d.lat + 0.01],
                        [p.properties.geo_point_2d.lon - 0.01, p.properties.geo_point_2d.lat + 0.01],
                        [p.properties.geo_point_2d.lon - 0.01, p.properties.geo_point_2d.lat - 0.01]
                    ]
                ]
            },
            properties: {...p.properties, superfie: data.filter(d=>p.properties.code==d.code)}
        }))
    }


</script>

<div class="m-auto relative z-10 w-60">
    <input
      type="range"
      min="2000"
      max="2023"
      bind:value={selectedYear}
      class="w-full"
    />
    <p>{selectedYear}</p>
  </div>

<MapLibre
    style={style}
    class=""
    standardControls
    pitch={30}
    center={[2.7512019897940436, 39.79629267511315]}
    zoom={8} >
    <GeoJSON data={mallorca}>
        <LineLayer
            layout={{ 'line-cap': 'round', 'line-join': 'round' }}
            paint={{ 'line-color': ['get', 'color'], 'line-width': 3 }}
            beforeLayerType="symbol"
            />
    </GeoJSON>
    <GeoJSON data={superfieData}>
        <!-- <FillExtrusionLayer
            paint={{
                'fill-extrusion-color':[
                        'interpolate',
                        ['linear'],
                        ['/', ['get', ''+selectedYear],20],
                        0, '#0a0',
                        200000, '#aa0',
                        400000, '#a00'
                    ],
                'fill-extrusion-height': ['/', ['get', ''+selectedYear], 20],
                'fill-extrusion-opacity': 0.8,
            }}
        >
            <div slot="popup" let:feature>
                <h3>Population: {feature.properties.population}</h3>
            </div>
        </FillExtrusionLayer> -->
   
    </GeoJSON>
</MapLibre>