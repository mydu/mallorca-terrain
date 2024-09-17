<script>
  import { T } from '@threlte/core';
  import { OrbitControls } from '@threlte/extras';
  

  import * as d3 from 'd3';
    let length = 500;

    const x = Array.from({length}, d3.randomInt(0,length));
    const y = Array.from({length}, d3.randomInt(0,length));
    const z = Array.from({length}, d3.randomInt(0,length));
    const data = d3.range(length).map(i=>({
      x:x[i],
      y: y[i],
      z: z[i]
    }))
    $: xScale = d3
    .scaleLinear()
    .domain([0, length])
    .range([0, 100]);

    $: yScale = d3
        .scaleLinear()
        .domain([0, length])
        .range([0, 100]);

    $: zScale = d3
        .scaleLinear()
        .domain([0, length])
        .range([0, 100]);


</script>

{#each data as d}
    <T.Mesh
        position.x={xScale(d.x)}
        position.z={zScale(d.z)}
        position.y={yScale(d.y)}
        scale.y={1}
        castShadow
    >
        <T.BoxGeometry />
        <T.MeshStandardMaterial color={'#B392AC'} />
    </T.Mesh>
{/each}


<T.PerspectiveCamera
    makeDefault
    position={[22, 15,22]}
    on:create={({ ref }) => {
        ref.lookAt(0, 0, 4);
    }}>	
    <OrbitControls
    enableDamping
    target={[0, 4, 0]}
    />
</T.PerspectiveCamera>
<T.AmbientLight intensity={0.2} />
<T.DirectionalLight castShadow position={[10, 20, 5]} />

<!-- <T.Mesh receiveShadow rotation.x={-90 * (Math.PI / 180)}>
	<T.CircleGeometry args={[30, 72]} />
	<T.MeshStandardMaterial />
</T.Mesh> -->

