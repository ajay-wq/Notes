# Notes
For Study Material


      git init
      git origin add remote git@github.com:ajay-wq/ExampleDrupal.git
      git status
      git branch
      git add file_name
      git commit -m "just for testing"
      git pull origin main
      git push origin main



For making Tabs inside web

==start==
{#
/**
* @file
* Default theme implementation to display a block.
*
* Available variables:
* - plugin_id: The ID of the block implementation.
* - label: The configured label of the block if visible.
* - configuration: A list of the block's configuration values.
* - label: The configured label for the block.
* - label_display: The display settings for the label.
* - provider: The module or other provider that provided this block plugin.
* - Block plugin specific settings will also be stored here.
* - content: The content of this block.
* - attributes: array of HTML attributes populated by modules, intended to
* be added to the main container tag of this template.
* - id: A valid HTML ID and guaranteed unique.
* - title_attributes: Same as attributes, except applied to the main title
* tag that appears in the template.
* - content_attributes: Same as attributes, except applied to the main content
* tag that appears in the template.
* - title_prefix: Additional output populated by modules, intended to be
* displayed in front of the main title tag that appears in the template.
* - title_suffix: Additional output populated by modules, intended to be
* displayed after the main title tag that appears in the template.
*
* @see template_preprocess_block()
*
* @ingroup themeable
*/
#}
{%
set classes = [
'block',
'block-' ~ configuration.provider|clean_class,
'block-' ~ plugin_id|clean_class,
]
%}
<div{{ attributes.addClass(classes) }}>
 {{ title_prefix }}
  {{ title_suffix }}

    <div class="Avetta-one-outer text-center pb-5 ">
        <div class="container pt-5 ">
            <div class="row text-center">
                <div class="col-md-12">
                    {% if label %}
                    <h2 class="deep-blue-font">{{ label }}</h2>
                    {% endif %}
                    <h3 class=" gray-color big-font">{{ content.field_title }} </h3>
                    <p class="gray-color mt-5 mb-5">{{ content.body }}</p>
                </div>
            </div>
           
            <div class="row">
             {% for key, option in content.field_text_with_logo %}
             {% if key|first != '#'%}
                <div class="col-md-6 plr-5">
                    <div class="row">
                        <div class="col-md-2">
                            <img src="{{file_url(option['#paragraph'].field_logo.entity.field_media_image.entity.uri.value)}}"
                                alt="">
                        </div>
                        <div class="col-md-9 item-heading-style">
                            <h3>{{ option['#paragraph'].field_title.value}} </h3>
                            <p>{{ option['#paragraph'].field_content_description.value|raw }}</p>
                        </div>
                    </div>
                </div>
              {% endif %}
              {% endfor %}
            </div>
            
        </div>
    </div>
</div>
==end==
