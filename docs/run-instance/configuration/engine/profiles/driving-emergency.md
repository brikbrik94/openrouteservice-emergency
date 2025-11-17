# Emergency routing profile configuration

This example shows how to enable the built-in emergency vehicle encoder so that
openrouteservice can calculate routes that include roads tagged with
`access=no`, `service=emergency_access`, or other emergency-only tags.
The profile uses the same HTTP name (`driving-emergency`) that is exposed by the
API, therefore no extra translation is required when sending requests.

## Example `ors-config.yml`

```yaml
ors:
  engine:
    profiles:
      driving-emergency:
        enabled: true
        encoder_name: driving-emergency
        graph_path: graphs
        build:
          source_file: ./data/andorra-latest.osm.pbf
          instructions: true
          encoder_options:
            turn_costs: true
            block_fords: false
          preparation:
            min_network_size: 200
            methods:
              ch:
                enabled: true
                threads: 1
                weightings: fastest
              core:
                enabled: true
                threads: 1
                weightings: fastest
                landmarks: 32
          ext_storages:
            RoadAccessRestrictions:
              use_for_warnings: true
        service:
          maximum_distance: 200000
          maximum_snapping_radius: 400
          maximum_waypoints: 50
          execution:
            methods:
              core:
                active_landmarks: 8
```

Adjust the `source_file` path and any limits to match your hardware or data
coverage. After saving the configuration rebuild the graphs so the new encoder
information is written to disk. With this profile enabled you can call
`/ors/v2/directions/driving-emergency` (and the corresponding matrix, isochrone,
or matching endpoints) to obtain routes tailored to emergency vehicles.
