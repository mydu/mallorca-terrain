<script>
  import { T } from '@threlte/core';
  import { Align, OrbitControls } from '@threlte/extras'
  import { BufferGeometry, Float32BufferAttribute } from 'three';


    import * as d3 from 'd3';
	import { PointsMaterial } from 'three';
    let length = 500;

    const geometry = new BufferGeometry();
    const positions = new Float32Array(length * 3);

    const x = Array.from({length}, d3.randomInt(0,length));
    const y = Array.from({length}, d3.randomInt(0,length));
    const z = Array.from({length}, d3.randomInt(0,length));

    const data = d3.range(length).map(i=>({
      x: x[i],
      y: y[i],
      z: z[i]
    }))

    const xScale = d3
    .scaleLinear()
    .domain([0, length])
    .range([0, 100]);

    const yScale = d3
        .scaleLinear()
        .domain([0, length])
        .range([0, 100]);

    const zScale = d3
        .scaleLinear()
        .domain([0, length])
        .range([0, 100]);


    data.forEach((d, i) => {
      positions[i * 3] = xScale(d.x);
      positions[i * 3 + 1] = yScale(d.y);
      positions[i * 3 + 2] = zScale(d.z);
    });
    console.log(positions)
    geometry.setAttribute('position', new Float32BufferAttribute(positions, 3));


</script>

    <Align>
        <T.Points>
        <T.BufferGeometry>
            <T.BufferAttribute
            args={[positions, 3]}
            attach={(parent, self) => {
                parent.setAttribute('position', self)
                return () => {
                // cleanup function called when ref changes or the component unmounts
                // https://threlte.xyz/docs/reference/core/t#attach
                }
            }}
            />
        </T.BufferGeometry>
        <T.PointsMaterial size={0.25} />
        </T.Points>
    </Align>
    <!-- <T.Points>
        <T.BufferGeometry {geometry} />
        <T.PointsMaterial color={'#B392AC'} size={0.1} sizeAttenuation={true} />
    </T.Points> -->
    <!-- <T.Mesh
        position.x={xScale(d.x)}
        position.z={zScale(d.z)}
        position.y={yScale(d.y)}
        scale.y={1}
        castShadow
    >
        <T.Points  />
        <T.PointsMaterial color={'#B392AC'} size={0.1} />
    </T.Mesh> -->


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

