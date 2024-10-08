<script>
    import { MapLibre, GeoJSON, FillExtrusionLayer } from 'svelte-maplibre';
    import { onMount } from 'svelte';
    import mallorca from '$data/mallorcaPop.json';
	import { pingpong } from 'three/src/math/MathUtils';

    let selectedYear = 2023;
    let populationData = [];

    const pointData =  {
        type: 'FeatureCollection',
        features: mallorca.features.map(point => ({
            type: 'Feature',
            geometry: {
                type: 'Polygon',
                coordinates: [
                    [
                        [point.properties.geo_point_2d.lon - 0.01, point.properties.geo_point_2d.lat - 0.01],
                        [point.properties.geo_point_2d.lon + 0.01, point.properties.geo_point_2d.lat - 0.01],
                        [point.properties.geo_point_2d.lon + 0.01, point.properties.geo_point_2d.lat + 0.01],
                        [point.properties.geo_point_2d.lon - 0.01, point.properties.geo_point_2d.lat + 0.01],
                        [point.properties.geo_point_2d.lon - 0.01, point.properties.geo_point_2d.lat - 0.01]
                    ]
                ]
            },
            properties: point.properties
        }))
    }
    
    $: getExtrusionColor = (feature) => {
        return [
            'interpolate',
            ['linear'],
            ['/', ['get', ''+selectedYear],20],
            0, '#0a0',
            200000, '#aa0',
            400000, '#a00'
        ];
    };

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
    style="https://basemaps.cartocdn.com/gl/positron-gl-style/style.json"
    class=""
    standardControls
    pitch={30}
    center={[2.7512019897940436, 39.79629267511315]}
    zoom={8}
>
    <GeoJSON data={pointData}>
        <FillExtrusionLayer
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
        </FillExtrusionLayer>
    </GeoJSON>
</MapLibre>