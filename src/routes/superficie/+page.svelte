<script>
    import { Canvas, T } from '@threlte/core'
    import { GeoJSON } from 'geojson'
    import * as THREE from 'three'
  
    export let geojsonData: GeoJSON
  
    function createPolygonMesh(polygon) {
      const shape = new THREE.Shape()
      
      polygon.coordinates[0].forEach((coord, index) => {
        if (index === 0) {
          shape.moveTo(coord[0], coord[1])
        } else {
          shape.lineTo(coord[0], coord[1])
        }
      })
  
      const geometry = new THREE.ShapeGeometry(shape)
      const material = new THREE.MeshBasicMaterial({ color: 0x00ff00, side: THREE.DoubleSide })
      
      return new THREE.Mesh(geometry, material)
    }
  
    function renderGeoJSON(geojson) {
      const group = new THREE.Group()
  
      geojson.features.forEach(feature => {
        if (feature.geometry.type === 'Polygon') {
          const mesh = createPolygonMesh(feature.geometry)
          group.add(mesh)
        }
        // Add handlers for other geometry types as needed
      })
  
      return group
    }
  
    $: geoJsonGroup = renderGeoJSON(geojsonData)
  </script>
  
  <Canvas>
    <T.PerspectiveCamera position={[0, 0, 10]} />
    <T.AmbientLight intensity={0.5} />
    <T is={geoJsonGroup} />
  </Canvas>