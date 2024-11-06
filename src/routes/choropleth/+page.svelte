<script>
    import { MapLibre, GeoJSON, FillExtrusionLayer, Popup} from 'svelte-maplibre';
    import mallorca from '$data/mallorcaPopChoro.json';
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
  zoom={8}
>
  <GeoJSON data={mallorca}>
    <FillExtrusionLayer
      paint={{
        'fill-extrusion-base': [
        'interpolate',
            ['linear'],
            ['zoom'],
            14,
            1,
            14.05,
            ['get', 'render_min_height'],
        ],
        // 'fill-extrusion-color': "#a0a",
        'fill-extrusion-color': [
          'interpolate',
          ['linear'],
          // Population density
          ['/', ['get', ''+selectedYear],20],
          0,
          '#0a0',
          2000,
          '#a00',
        ],
        'fill-extrusion-opacity': 0.6,
        'fill-extrusion-height': ['/', ['get', ''+selectedYear], 20],
      }}
      beforeLayerType="symbol"
    >
      <Popup openOn="hover" let:data>
        {@const props = data?.properties}
        {#if props}
          <div class="flex flex-col gap-2">
            <div class="text-lg font-bold">{props.mun_name}</div>
            <p>Population in {selectedYear}: {props[selectedYear]}</p>
          </div>
        {/if}
      </Popup>
    </FillExtrusionLayer>
  </GeoJSON>
</MapLibre>
  