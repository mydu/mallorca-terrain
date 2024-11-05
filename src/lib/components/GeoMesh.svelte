<script>
  import { T } from '@threlte/core'
  import * as THREE from 'three'

  export let data
  export let projection
  let heightScale = 1
  // export let onFeatureClick = () => {}

  function processFeature(feature) {
    const vertices = []
    const { geometry, properties } = feature
    
    if (!geometry || !geometry.coordinates) return null
    
    const processCoordinates = (coords, type) => {
      const points = []

      switch(type) {
        case 'Point':
          const [x, y] = projection(coords)
          points.push([x, y])
          break
          
        case 'LineString':
          coords.forEach(coord => {
            const [x, y] = projection(coord)
            points.push([x, y])
          })
          break
          
        case 'Polygon':
          coords.forEach(ring => {
            ring.forEach(coord => {
              const [x, y] = projection(coord)
              points.push([x, y])
            })
            // Add separator for rings
            points.push(null)
          })
          break
          
        case 'MultiPolygon':
          coords.forEach(polygon => {
            polygon.forEach(ring => {
              ring.forEach(coord => {
                const [x, y] = projection(coord)
                points.push([x, y])
              })
              points.push(null)
            })
            points.push(null)
          })
          break
      }
      
      return points
    }

    const points = processCoordinates(geometry.coordinates, geometry.type)
    if (!points || points.length === 0) return null

    // Create separate line segments for each part
    let currentLine = []

    points.forEach(point => {
      if (point === null) {
        if (currentLine.length > 0) {
          // Close the line if it's a polygon
          if (geometry.type.includes('Polygon')) {
            currentLine.push(currentLine[0])
          }
          vertices.push(...currentLine)
          currentLine = []
        }
      } else {
        currentLine.push(point)
      }
    })


    // Handle any remaining line
    if (currentLine.length > 0) {
      vertices.push(...currentLine)
    }

    console.log(vertices)
    // Create geometry
    const bufferGeometry = new THREE.BufferGeometry()

    const positions = new Float32Array(vertices.flatMap(([x, y]) => {
      const z = (properties?.height || 0) * heightScale
      return [x, y, z]
    }))

    
    bufferGeometry.setAttribute('position', new THREE.BufferAttribute(positions, 3))

    return {
      geometry: bufferGeometry,
      properties
    }
  }

  $: features = data?.features?.map(processFeature).filter(Boolean) || []

</script>

{#each features as feature, i}
  <T.Line 
    geometry={feature.geometry}
  >
    <T.LineBasicMaterial 
      color={'#000000'}
      linewidth={1}
      transparent={true}
      opacity={0.8}
    />
  </T.Line>
{/each}