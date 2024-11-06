<script>
    import { MapLibre, GeoJSON, FillExtrusionLayer, LineLayer,Popup} from 'svelte-maplibre';
    import { onMount } from 'svelte';
    import * as d3 from 'd3';


    import mallorca from '$data/mallorcaSimple.json';
    let data=[];
    let months = []

    onMount(async () => {
        const raw = await d3.csv('data/SuperficieAll.csv', d3.autoType);
        data = raw.map(d=>({...d, code: "0"+d.code, monthString: d3.timeFormat('%Y-%m-%d')(d.month)}))
        
        months = Array.from(new Set(data.map(d=>d.month))).sort((a,b)=>a-b);
    });

    let selectedIndex = 0;


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

    const createBarPolygon = (lon, lat, offset, width = 0.01) => {
        return [
            [lon + offset - width/2, lat - width/2],
            [lon + offset + width/2, lat - width/2],
            [lon + offset + width/2, lat + width/2],
            [lon + offset - width/2, lat + width/2],
            [lon + offset - width/2, lat - width/2]
        ];
    };
   
        $: superficieTotal=  {
            type: 'FeatureCollection',
            features: mallorca.features.map(p => ({
                type: 'Feature',
                geometry: {
                    type: 'Polygon',
                    coordinates:[createBarPolygon(p.properties.geo_point_2d.lon,p.properties.geo_point_2d.lat, 0)]
                },
                properties: {...p.properties, superficie: data.filter(d=>p.properties.code==d.code).reduce((acc, curr) => {
                    acc[curr.monthString] = curr.total;
                    return acc;
                }, {})}
            }))
        }
           
        $: superficieIndustrial=  {
            type: 'FeatureCollection',
            features: mallorca.features.map(p => ({
                type: 'Feature',
                geometry: {
                    type: 'Polygon',
                    coordinates: [createBarPolygon(p.properties.geo_point_2d.lon,p.properties.geo_point_2d.lat, -0.02)]
                },
                properties: {...p.properties, superficie: data.filter(d=>p.properties.code==d.code).reduce((acc, curr) => {
                    acc[curr.monthString] = curr.industrial;
                    return acc;
                }, {})}
            }))
        }
        
           
        $: superficieResidential=  {
            type: 'FeatureCollection',
            features: mallorca.features.map(p => ({
                type: 'Feature',
                geometry: {
                    type: 'Polygon',
                    coordinates: [createBarPolygon(p.properties.geo_point_2d.lon,p.properties.geo_point_2d.lat, 0.02)]
                },
                properties: {...p.properties, superficie: data.filter(d=>p.properties.code==d.code).reduce((acc, curr) => {
                    acc[curr.monthString] = curr.residential;
                    return acc;
                }, {})}
            }))
        }
    
</script>

{#if months}
    <div class="m-auto relative z-10 w-60">
            <input
            type="range"
            min={0}
            max={months.length-1}
            bind:value={selectedIndex}
            class="w-full"
            />
            <p>{ d3.timeFormat('%Y-%m-%d')(months[selectedIndex])}</p>
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
        <GeoJSON data={superficieTotal}>
            <FillExtrusionLayer
                paint={{
                    
                'fill-extrusion-height': [
                    'get',
                        ['string', ['to-string', d3.timeFormat('%Y-%m-%d')(months[selectedIndex])]],
                        ['get', 'superficie']
                    
                ],                // 'fill-extrusion-height': [
                //     'match',
                //     ['get', 'monthString', ['get', 'superficie']],
                //         d3.timeFormat('%Y-%m-%d')(months[selectedIndex]),
                //     [
                //     'number',
                //         ['get', 'total', ['get', 'superficie']]
                //     ],
                //     0  // Default height when month doesn't match
                // ],
                // 'fill-extrusion-height': [
                //     'match',
                //     ['get', 'monthString', ['get', 'superficie']],
                //     d3.timeFormat('%Y-%m-%d')(months[selectedIndex]),
                //     [
                //     'number',
                //         ['get', 'total', ['get', 'superficie']]
                //     ],
                //     0  // Default height when month doesn't match
                // ],
                'fill-extrusion-base': 0,
                'fill-extrusion-color': '#ff0000',
                'fill-extrusion-opacity': 0.8
            }}>
                <!-- <div slot="popup" let:feature>
                    <h3>Population: </h3>
                </div> -->
                <Popup openOn="hover" let:data>
                    {@const props = data?.properties}
                    {#if props}
                    <div class="flex flex-col gap-2">
                        <div class="text-lg font-bold">{props.name}</div>
                    </div>
                    {/if}
                </Popup>
            </FillExtrusionLayer>
        </GeoJSON>
        <GeoJSON data={superficieResidential}>
            <FillExtrusionLayer
                paint={{
                    
                'fill-extrusion-height': [
                    'get',
                        ['string', ['to-string', d3.timeFormat('%Y-%m-%d')(months[selectedIndex])]],
                        ['get', 'superficie']
                    
                ],                // 'fill-extrusion-height': [
                //     'match',
                //     ['get', 'monthString', ['get', 'superficie']],
                //         d3.timeFormat('%Y-%m-%d')(months[selectedIndex]),
                //     [
                //     'number',
                //         ['get', 'total', ['get', 'superficie']]
                //     ],
                //     0  // Default height when month doesn't match
                // ],
                // 'fill-extrusion-height': [
                //     'match',
                //     ['get', 'monthString', ['get', 'superficie']],
                //     d3.timeFormat('%Y-%m-%d')(months[selectedIndex]),
                //     [
                //     'number',
                //         ['get', 'total', ['get', 'superficie']]
                //     ],
                //     0  // Default height when month doesn't match
                // ],
                'fill-extrusion-base': 0,
                'fill-extrusion-color': '#00ff00',
                'fill-extrusion-opacity': 0.8
            }}>
                <!-- <div slot="popup" let:feature>
                    <h3>Population: </h3>
                </div> -->
                <Popup openOn="hover" let:data>
                    {@const props = data?.properties}
                    {#if props}
                    <div class="flex flex-col gap-2">
                        <div class="text-lg font-bold">{props.name}</div>
                    </div>
                    {/if}
                </Popup>
            </FillExtrusionLayer>
        </GeoJSON>
        <GeoJSON data={superficieIndustrial}>
            <FillExtrusionLayer
                paint={{
                    
                'fill-extrusion-height': [
                    'get',
                        ['string', ['to-string', d3.timeFormat('%Y-%m-%d')(months[selectedIndex])]],
                        ['get', 'superficie']
                    
                ],                // 'fill-extrusion-height': [
                //     'match',
                //     ['get', 'monthString', ['get', 'superficie']],
                //         d3.timeFormat('%Y-%m-%d')(months[selectedIndex]),
                //     [
                //     'number',
                //         ['get', 'total', ['get', 'superficie']]
                //     ],
                //     0  // Default height when month doesn't match
                // ],
                // 'fill-extrusion-height': [
                //     'match',
                //     ['get', 'monthString', ['get', 'superficie']],
                //     d3.timeFormat('%Y-%m-%d')(months[selectedIndex]),
                //     [
                //     'number',
                //         ['get', 'total', ['get', 'superficie']]
                //     ],
                //     0  // Default height when month doesn't match
                // ],
                'fill-extrusion-base': 0,
                'fill-extrusion-color': '#0000ff',
                'fill-extrusion-opacity': 0.8
            }}>
                <!-- <div slot="popup" let:feature>
                    <h3>Population: </h3>
                </div> -->
                <Popup openOn="hover" let:data>
                    {@const props = data?.properties}
                    {#if props}
                    <div class="flex flex-col gap-2">
                        <div class="text-lg font-bold">{props.name}</div>
                    </div>
                    {/if}
                </Popup>
            </FillExtrusionLayer>
        </GeoJSON>
    </MapLibre>

{/if}
