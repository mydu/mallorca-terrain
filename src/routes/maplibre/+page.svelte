<script>
    import { MapLibre, GeoJSON, FillExtrusionLayer} from 'svelte-maplibre';
    import mallorca from '$data/mallorcaPop.json';

</script>

  <MapLibre
  style="https://basemaps.cartocdn.com/gl/positron-gl-style/style.json"
  class=""
  standardControls
  pitch={30}
  center={[2.7512019897940436, 39.79629267511315]}
  zoom={4}
>
  <GeoJSON data={mallorca}>
    <FillExtrusionLayer
      paint={{
        'fill-extrusion-base': [
        'interpolate',
            ['linear'],
            ['zoom'],
            14,
            0,
            14.05,
            ['get', 'render_min_height'],
        ],
        // 'fill-extrusion-color': "#a0a",
        'fill-extrusion-color': [
          'interpolate',
          ['linear'],
          // Population density
          ['/', ['get', '2023'],20],
          0,
          '#0a0',
          2000,
          '#a00',
        ],
        'fill-extrusion-opacity': 0.6,
        'fill-extrusion-height': ['/', ['get', '2023'], 20],
      }}
      beforeLayerType="symbol"
    >
      <!-- <Popup openOn="hover" let:data>
        {@const props = data?.properties}
        {#if props}
          <div class="flex flex-col gap-2">
            <div class="text-lg font-bold">{props.NAME}</div>
            <p>Population: {props.POPESTIMATE2020}</p>
          </div>
        {/if}
      </Popup> -->
    </FillExtrusionLayer>
  </GeoJSON>
</MapLibre>
  