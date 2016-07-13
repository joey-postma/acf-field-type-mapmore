# ACF MapMore Field

Complex map field for Advanced Custom Fields.

![Preview Field in Wordpress admin](https://raw.githubusercontent.com/sanatorium/acf-field-type-mapmore/master/screenshot.png)

**Supports**

- Areas
	- Circles
	- Polygons
- Routes
- Custom POIs

## Usage

### Displaying selected location


<style type="text/css">
    .marker {
    	display:none;
    }
    .acf-map {
    	width: 100%;
    	height: 400px;
    	border: 0;
    	margin: 0;
    }

    .acf-map img {
    	max-width: none !important;
    }
  </style>			

      <?php
      $neighbourhood_bounds = get_field('neighbourhood_bounds');
      $neighbourhood_data = json_decode($neighbourhood_bounds);

      switch ( $neighbourhood_data[0]->type ) :

        case 'rectangle': 
          $bounds = '{
                        north: '.$neighbourhood_data[0]->bounds->north.',
                        south: '.$neighbourhood_data[0]->bounds->south.',
                        east: '.$neighbourhood_data[0]->bounds->east.',
                        west: '.$neighbourhood_data[0]->bounds->west.'
                      }';

          // Get average for the center
          $center_lat = ($neighbourhood_data[0]->bounds->north + $neighbourhood_data[0]->bounds->south) / 2;
          $center_lng = ($neighbourhood_data[0]->bounds->east + $neighbourhood_data[0]->bounds->west) / 2;
          $map_center = '{"lat":'.$center_lat.',"lng":'.$center_lng.'}';
        break;

        case 'polyline':
          $path = json_encode($neighbourhood_data[0]->path);

          // Add everything up
          $coordinate_count = 0;
          $coordinate_lat = 0;
          $coordinate_lng = 0;
          foreach($neighbourhood_data[0]->path as $coordinate) :
            $coordinate_lat += $coordinate->lat;
            $coordinate_lng += $coordinate->lng;
            $coordinate_count++;
          endforeach;

          // Get average
          $coordinate_lat_avg += $coordinate_lat / $coordinate_count;
          $coordinate_lng_avg += $coordinate_lng / $coordinate_count;

          $map_center = '{"lat":'.$coordinate_lat_avg.',"lng":'.$coordinate_lng_avg.'}';
        break;

        case 'polygon':
          $paths = json_encode($neighbourhood_data[0]->path);

          // Add everything up
          $coordinate_count = 0;
          $coordinate_lat = 0;
          $coordinate_lng = 0;
          foreach($neighbourhood_data[0]->path as $coordinate) :
            $coordinate_lat += $coordinate->lat;
            $coordinate_lng += $coordinate->lng;
            $coordinate_count++;
          endforeach;

          // Get average
          $coordinate_lat_avg += $coordinate_lat / $coordinate_count;
          $coordinate_lng_avg += $coordinate_lng / $coordinate_count;

          $map_center = '{"lat":'.$coordinate_lat_avg.',"lng":'.$coordinate_lng_avg.'}';
        break;

        case 'circle':
          $radius = json_encode($neighbourhood_data[0]->radius);
          $map_center = '{"lat":'.$neighbourhood_data[0]->lat.',"lng":'.$neighbourhood_data[0]->lng.'}';
        break;

        case 'marker':
          // Backend doesn't work yet, center on Regina
          $map_center = '{"lat":50.445886,"lng": -104.618078}';
          
        break;

      endswitch;
      ?>


  <script>
    function initMap() {


      var map = new google.maps.Map(document.getElementById('map'), {
        zoom: 13,
        center: <?php echo $map_center; ?>,
        mapTypeId: google.maps.MapTypeId.TERRAIN
      });

      <?php
      switch ( $neighbourhood_data[0]->type ) :

        case 'rectangle': 
          ?>
          var neighbourhoodMap = new google.maps.Rectangle({
                bounds:         <?php echo $bounds; ?>,
                strokeColor:    '#FF0000',
                strokeOpacity:  0.8,
                strokeWeight:   2,
                fillColor:      '#FF0000',
                fillOpacity:    0.35
          });
          <?php
        break;

        case 'polyline':
          ?>
          var neighbourhoodMap = new google.maps.Polyline({
                path:           <?php echo $path; ?>,
                geodesic:       true,
                strokeColor:    '#FF0000',
                strokeOpacity:  1.0,
                strokeWeight:   2
          });
          <?php
        break;

        case 'polygon':
          ?>
          var neighbourhoodMap = new google.maps.Polygon({
                paths:          <?php echo $paths; ?>,
                strokeColor:    '#FF0000',
                strokeOpacity:  0.8,
                strokeWeight:   2,
                fillColor:      '#FF0000',
                fillOpacity:    0.35
          });
          <?php
        break;

        case 'circle':
          ?>
          var neighbourhoodMap = new google.maps.Circle({
                center:         <?php echo $map_center; ?>,
                radius:         <?php echo $radius; ?>,
                strokeColor:    '#FF0000',
                strokeOpacity:  0.8,
                strokeWeight:   2,
                fillColor:      '#FF0000',
                fillOpacity:    0.35
          });
          <?php
        break;

        case 'marker':
          ?>
          var neighbourhoodMap = new google.maps.Marker({
                position:   <?php echo $map_center; ?>,
                animation:  google.maps.Animation.DROP
          });
          <?php
        break;

      endswitch;
      ?>

      neighbourhoodMap.setMap(map);
    }
  </script>
  <script async defer
  src="https://maps.googleapis.com/maps/api/js?key=[YOUR API KEY]&callback=initMap">
  </script>


  <div class="acf-map" id="map"></div>


### Adding marker icons

Add any *.svg files to ``images/icons`` they will be automatically available as Marker icons


-----------------------

### Description

EXTENDED_DESCRIPTION

### Compatibility

This ACF field type is compatible with:
* ACF 5
* ACF 4

### Installation

1. Copy the `acf-mapmore` folder into your `wp-content/plugins` folder
2. Activate the MapMore plugin via the plugins admin page
3. Create a new field via ACF and select the MapMore type
4. Please refer to the description for more info regarding the field type settings

### Changelog
Please see `readme.txt` for changelog

### Credits

#### Icons

- Map Marker, Pin, thumb tack, Flag, Tree, forest, Flower, Sun, Crescent Moon, Tornado, Hail, Lightning, Day Fog, Bicycle, Car, Bus, Train, Hot Air Balloon, Departure, Airplane Mode, Tent, Image, Color, Shop, Hanging board by (Pham Thi Dieu Linh)[https://thenounproject.com/phdieuli/] from (the Noun Project)[https://thenounproject.com]




