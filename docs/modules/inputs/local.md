# Module `local`

This IGM module is designed to load spatial 2D raster data from NetCDF or Tiff files specified by the `input_file` parameter. The module converts all existing 2D fields (present in the NetCDF file or in the designated folder for Tiff files) into TensorFlow variables. At a minimum, the module is expected to import basal topography represented by the `topg` variable. Additionally, it completes the data by deriving basal topography from ice thickness and surface topography when necessary. Other fields present will also be converted to TensorFlow variables, allowing them to be accessed in the code via `state.myvar`. For example, providing the `icemask` variable can be useful for defining an accumulation area, which is beneficial for modeling individual glaciers and preventing overflow into neighboring catchments.

The module offers functions for resampling the data, where the `coarsen` parameter can be set to values like 2, 3, or 4 (with a default value of 1 indicating no coarsening). It also supports cropping the data by setting the `crop` parameter to `True` and specifying the desired bounds.

Additionally, by setting `icemask_invert` to `True`, an ice mask can be generated from an ESRI Shapefile specified by the `icemask_shapefile` parameter. This mask can identify areas that should contain glaciers or areas that should remain glacier-free, based on the `icemask_include` parameter.

The module also supports restarting an IGM run using a NetCDF file produced from a previous IGM run. To achieve this, provide the output NetCDF file from the previous run as input to IGM. The module will seek data corresponding to the starting time defined by `processes.time.start` and initialize the simulation at that time.

This module depends on the `xarray` and `rioxarray` Python libraries.

**Contributors:** B. Finley, A. Henz (icemask add-on), G. Jouvet (tif format)

## Config Structure  
~~~yaml
{% include  "../../../igm/igm/conf/inputs/local.yaml" %}
~~~

## Parameters


{% macro flatten_config(config, help, prefix='') %}
  {% for key, value in config.items() %}
    {% set full_key = prefix ~ key if prefix == '' else prefix ~ '.' ~ key %}
    {% set help_entry = help.get(key, {}) %}
    {% if value is mapping %}
      {{ flatten_config(value, help_entry, full_key) }}
    {% else %}
      <tr>
        <td>{{ full_key }}</td>
        <td>
          {% if help_entry.Type %}
            <span class="{{ help_entry.Type }}_table">{{ help_entry.Type }}</span>
          {% endif %}
        </td>
        <td>
          {% if help_entry.Units %}
            <span class="math">\( {{ help_entry.Units }} \)</span>
          {% endif %}
        </td>
        <td>{{ help_entry.Description or '' }}</td>
        <td>{{ value }}</td>
      </tr>
    {% endif %}
  {% endfor %}
{% endmacro %}


{% set config = load_yaml('igm/igm/conf/inputs/local.yaml') %}
{% set help = load_yaml('igm/igm/conf_help/inputs/local.yaml') %}
{% set header = load_yaml('igm/igm/conf_help/header.yaml') %}
{% set module_key = config.keys() | list | first %}
{% set module = config[module_key] %}
{% set module_help = help %}

<table>
  <thead>
    <tr>
      <th>Name</th>
      {% for key in header %}
      <th>{{ key }}</th>
      {% endfor %}
      <th>Default Value</th>
    </tr>
  </thead>
  <tbody>
    {{ flatten_config(module, module_help) }}
  </tbody>
</table>

<!-- Load MathJax v3 -->
<script>
  window.MathJax = {
    tex: {
      inlineMath: [['$', '$'], ['\\(', '\\)']]
    }
  };
</script>

<script type="text/javascript">
  MathJax.Hub.Queue(["Typeset", MathJax.Hub]);
</script>