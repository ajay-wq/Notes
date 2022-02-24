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



For making Tabs inside website

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


Form alters which used in modules of drupal

==start==


      function undp_custom_undprecovery_form_alter(&$form, FormStateInterface $form_state, $form_id) {
          /* @var Drupal\Core\Entity\FieldableEntityInterface $entity */
          $formObject = $form_state->getFormObject();
          if ($formObject instanceof \Drupal\Core\Entity\EntityFormInterface) {
            $entity = $formObject->getEntity();
            if ($entity->getEntityTypeId() === 'node' && in_array($entity->bundle(), ['landing_page'])) {
              $form['#attached']['library'][] = 'undp_custom_undprecovery/admin_theme_css';
            }
          }     
      }
      
                  /**  
             * Implements hook_field_widget_WIDGET_TYPE_form_alter().  
             */  

            function undp_custom_futureofdevelopment_field_widget_entity_reference_paragraphs_form_alter(&$element, \Drupal\Core\Form\FormStateInterface $form_state, $context) {
              /** @var \Drupal\field\Entity\FieldConfig $field_definition */
              $field_definition = $context['items']->getFieldDefinition();
              $paragraph_entity_reference_field_name = $field_definition->getName();

              if ($paragraph_entity_reference_field_name == 'field_paragraph_reference') {
                /** @see \Drupal\paragraphs\Plugin\Field\FieldWidget\ParagraphsWidget::formElement() */
                $widget_state = \Drupal\Core\Field\WidgetBase::getWidgetState($element['#field_parents'], $paragraph_entity_reference_field_name, $form_state);

                /** @var \Drupal\paragraphs\Entity\Paragraph $paragraph */
                $paragraph_instance = $widget_state['paragraphs'][$element['#delta']]['entity'];
                $paragraph_type = $paragraph_instance->bundle();

                // Determine which paragraph type is being embedded.
                if ($paragraph_type == 'our_vision') {
                  $dependee_field_name = 'field_style_vision';
                  $selector = sprintf('select[name="%s[%d][subform][%s]"]', $paragraph_entity_reference_field_name, $element['#delta'], $dependee_field_name);

                  // Dependent fields.
                  $element['subform']['field_link']['#states'] = [
                    'visible' => [
                      $selector => ['value' => 'image_sm'],
                   ],
                  ];
                }
              }
            }



==end==


Form alters which used in theme of drupal

==start==
                        <?php

            /**
             * @file
             * Bootstrap sub-theme.
             *
             * Place your custom PHP code in this file.
             */

            use Drupal\Core\Form\FormStateInterface;
            use Drupal\Core\Theme\ThemeSettings;
            use Drupal\system\Form\ThemeSettingsForm;
            use Drupal\Core\Form;
            use Drupal\Core\Url;
            use Drupal\Core\Link;
            use Drupal\image\Entity\ImageStyle;
            use Drupal\views\Views;
            use Drupal\block\Entity\Block;
            use Drupal\taxonomy\Entity\Term;
            use Drupal\menu_link_content\Entity\MenuLinkContent;
            use Drupal\Core\Entity\EntityTypeManager;

            use Drupal\Core\Ajax\AjaxResponse;
            use Drupal\Core\Ajax\InvokeCommand;
            use Symfony\Component\HttpFoundation\RedirectResponse;

            function uch_div_form_system_theme_settings_alter(&$form, \Drupal\Core\Form\FormStateInterface &$form_state, $form_id = NULL) {
              // Work-around for a core bug affecting admin themes. See issue #943212.
              if (isset($form_id)) {
                return;
              }

              /*$num_social_links = $form_state->get('num_social_links');
                // We have to ensure that there is at least one name field.
              if ($num_social_links === NULL) {
                $social_links_field = $form_state->set('num_social_links', 1);
                $num_social_links = 1;
              }*/

              // $form['test'] = array(
              //   '#type'          => 'textfield',
              //   '#title'         => t('Test'),
              //   '#description'   => t("Place this text in the widget spot on your site."),
              // );
              $form['site_settings'] = [
                  '#type' => 'vertical_tabs',
                  '#title' => t('Site Settings'),
              ];

              $form_state->setCached(FALSE);
              $form['actions']['submit'] = [
                '#type' => 'submit',
                '#value' => t('Submit'),
              ];
              /*$form['general'] = [
                  '#type' => 'details',
                  '#title' => t('General Settings'),
                  '#group' => 'site_settings',
              ];
              $form['general']['exclude_nids_header'] = [
                '#type' => 'textfield',
                '#title' => t('Exclude header nids'),
                '#default_value' => theme_get_setting('exclude_nids_header')
              ];
              $form['general']['exclude_nids_footer'] = [
                '#type' => 'textfield',
                '#title' => t('Exclude footer nids'),
                '#default_value' => theme_get_setting('exclude_nids_footer')
              ];*/
              $form['header'] = [
                  '#type' => 'details',
                  '#title' => t('Header Settings'),
                  '#group' => 'site_settings',
              ];
              // https://drupal.stackexchange.com/questions/212480/drupal-8-form-api-image-preview
              $form['header']['site_title'] = [
                  '#type' => 'textfield',
                  '#title' => t('Site Title'),
                  '#default_value' => theme_get_setting('site_title'),
              ];
              $form['header']['site_logo'] = [
                  '#type' => 'managed_file',
                  '#title' => t('Site Logo'),
                '#upload_location' => 'public://header-logo/',
                '#upload_validators' => [
                    'file_validate_extensions' => ['png jpg jpeg'],
                ],
                  '#default_value' => theme_get_setting('site_logo'),
              ];
              $form['header']['site_logo_url'] = [
                '#type' => 'textfield',
                '#title' => t('Site Logo URL'),
                '#default_value' => theme_get_setting('site_logo_url'),
              ];
              $form['header']['site_logo_alt'] = [
                '#type' => 'textfield',
                '#title' => t('Site Logo Alt'),
                '#default_value' => theme_get_setting('site_logo_alt'),
              ];
              $form['header']['header_background'] = [
                  '#type' => 'managed_file',
                  '#title' => t('Header Background'),
                '#upload_location' => 'public://header-logo/',
                '#upload_validators' => [
                    'file_validate_extensions' => ['png jpg jpeg'],
                ],
                  '#default_value' => theme_get_setting('header_background'),
              ];
              $form['header']['header_background_alt'] = [
                '#type' => 'textfield',
                '#title' => t('Header Background Alt'),
                '#default_value' => theme_get_setting('header_background_alt'),
              ];
              /*$form['header']['main_phone'] = [
                  '#type' => 'textfield',
                  '#title' => t('Main Phone'),
                  '#default_value' => theme_get_setting('main_phone'),
              ];*/
              $form['footer'] = [
                  '#type' => 'details',
                  '#title' => t('Footer Settings'),
                  '#group' => 'site_settings',
              ];
              $form['footer']['footer_logo'] = [
                '#type' => 'managed_file',
                '#title' => t('Footer Logo'),
                '#upload_location' => 'public://footer-logo/',
                '#upload_validators' => [
                    'file_validate_extensions' => ['png jpg jpeg'],
                ],
                '#default_value' => theme_get_setting('footer_logo'),
              ];
              $form['footer']['footer_logo_alt'] = [
                '#type' => 'textfield',
                '#title' => t('Footer Logo Alt'),
                '#default_value' => theme_get_setting('footer_logo_alt'),
              ];
              $form['footer']['footer_background'] = [
                '#type' => 'managed_file',
                '#title' => t('Footer Background'),
                '#upload_location' => 'public://footer-logo/',
                '#upload_validators' => [
                    'file_validate_extensions' => ['png jpg jpeg'],
                ],
                '#default_value' => theme_get_setting('footer_background'),
              ];
              $form['footer']['footer_background_alt'] = [
                '#type' => 'textfield',
                '#title' => t('Footer Background Alt'),
                '#default_value' => theme_get_setting('footer_background_alt'),
              ];
              $form['footer']['footer_logo_url'] = [
                '#type' => 'textfield',
                '#title' => t('Footer Logo URL'),
                '#default_value' => theme_get_setting('footer_logo_url'),
              ];
              $form['footer']['footer_blurb'] = [
                '#type' => 'textarea',
                '#title' => t('Footer Blurb'),
                '#default_value' => theme_get_setting('footer_blurb'),
              ];
              $form['footer']['copyright'] = [
                  '#type' => 'textfield',
                  '#title' => t('Copyright'),
                  '#default_value' => theme_get_setting('copyright'),
              ];
              $form['gcs'] = [
                '#type' => 'details',
                '#title' => t('Google Custom Search Settings'),
                '#group' => 'site_settings',
              ];
              $form['gcs']['cx'] = [
                '#type' => 'textfield',
                '#title' => t('CX'),
                '#default_value' => theme_get_setting('cx'),
              ];
              $form['gcs']['domain'] = [
                '#type' => 'textfield',
                '#title' => t('Domain Restriction'),
                '#default_value' => theme_get_setting('domain'),
              ];
            /*  $form['footer']['footer_top'] = [
                  '#type' => 'textfield',
                  '#title' => t('Footer Top'),
                  '#default_value' => theme_get_setting('footer_top'),
              ];*/
              $form['#submit'][] = 'uch_div_settings_form_submit_alt';
            }

            function uch_div_settings_form_submit_alt(&$form, \Drupal\Core\Form\FormStateInterface &$form_state){
                  $site_logo = $form_state->getValue('site_logo');
                  if (isset($site_logo[0])) {
                        $image_fid = $site_logo[0];
                        $image = file_load($image_fid);
                        if (is_object($image)) {
                              if (!$image->isPermanent()) {
                                    $image->setPermanent();
                                    $image->save();
                              }
                        }
                  }
                  $header_background = $form_state->getValue('header_background');
                  if (isset($header_background[0])) {
                        $image_fid = $header_background[0];
                        $image = file_load($image_fid);
                        if (is_object($image)) {
                              if (!$image->isPermanent()) {
                              $image->setPermanent();
                              $image->save();
                              }
                        }
                  }
                  $footer_logo = $form_state->getValue('footer_logo');
                  if (isset($footer_logo[0])) {
                        $image_fid = $footer_logo[0];
                        $image = file_load($image_fid);
                        if (is_object($image)) {
                              if (!$image->isPermanent()) {
                                    $image->setPermanent();
                                    $image->save();
                              }
                        }
                  }
                  $footer_background = $form_state->getValue('footer_background');
                  if (isset($footer_background[0])) {
                        $image_fid = $footer_background[0];
                        $image = file_load($image_fid);
                        if (is_object($image)) {
                              if (!$image->isPermanent()) {
                                    $image->setPermanent();
                                    $image->save();
                              }
                        }
                  }
            }

            function uch_div_preprocess_page(&$variables) {
                  /*TODO
                   *
                   * Fix this function
                   */
                  $site_logo = theme_get_setting('site_logo');
                  $site_logo_url = '';
                  if(!empty($site_logo)){
                        $site_logo_entity = file_load($site_logo[0]);
                        $site_logo_url = ImageStyle::load('header_logo')->buildUrl($site_logo_entity->getFileUri());
                  }
                  if(isset($site_logo_url)){
                        $variables['site_logo'] = $site_logo_url;
                  }
                  $variables['site_logo_alt'] = theme_get_setting('site_logo_alt');

                  $header_background = theme_get_setting('header_background');
                  $header_background_url = '';
                  if(!empty($header_background)){
                        $header_background_entity = file_load($header_background[0]);
                        $header_background_url = ImageStyle::load('header_background_image')->buildUrl($header_background_entity->getFileUri());
                  }
                  if(isset($header_background_url)){
                        $variables['header_background'] = $header_background_url;
                  }
                  $variables['header_background_alt'] = theme_get_setting('header_background_alt');

                  $footer_logo = theme_get_setting('footer_logo');
                  $footer_logo_url = '';
                  if(!empty($footer_logo)){
                        $footer_logo_entity = file_load($footer_logo[0]);
                        $footer_logo_url = ImageStyle::load('footer_logo')->buildUrl($footer_logo_entity->getFileUri());
                  }
                  if(isset($footer_logo_url)){
                        $variables['footer_logo'] = $footer_logo_url;
                  }
                  $variables['footer_logo_alt'] = theme_get_setting('footer_logo_alt');

                  $footer_background = theme_get_setting('footer_background');
                  $footer_background_url = '';
                  if(!empty($footer_background)){
                        $footer_background_entity = file_load($footer_background[0]);
                        $footer_background_url = ImageStyle::load('header_background_image')->buildUrl($footer_background_entity->getFileUri());
                  }
                  if(isset($footer_background_url)){
                        $variables['footer_background'] = $footer_background_url;
                  }
                  $variables['footer_background_alt'] = theme_get_setting('footer_background_alt');

                  try {
                        $variables['footer_logo_url'] = Url::fromUri(theme_get_setting('footer_logo_url'));
                  } catch (exception $e) {
                        \Drupal::logger('uch_div_theme')->error($e);
                        global $base_url;
                        $variables['footer_logo_url'] = $base_url;
                  }

              try {
                $variables['site_logo_url'] = Url::fromUri(theme_get_setting('site_logo_url'));
              } catch (exception $e) {
                \Drupal::logger('uch_div_theme')->error($e);
                global $base_url;
                $variables['site_logo_url'] = $base_url;
              }
                  // kint(theme_get_setting('footer_logo_url'));

                  // $variables['main_phone'] = theme_get_setting('main_phone');
                  $variables['copyright'] = theme_get_setting('copyright');
                  $variables['footer_top'] = theme_get_setting('footer_top');
                  $variables['newsletter_blurb'] = theme_get_setting('newsletter_blurb');
                  $variables['exclude_nids_header'] = theme_get_setting('exclude_nids_header');
                  $variables['exclude_nids_footer'] = theme_get_setting('exclude_nids_footer');
                  $variables['footer_blurb'] = theme_get_setting('footer_blurb');
              $variables['site_title'] = theme_get_setting('site_title');
              $variables['cx'] = theme_get_setting('cx');
              $variables['domain'] = theme_get_setting('domain');

              // $tempstore = \Drupal::service('user.private_tempstore')->get('uch_div_utilities');
              // $tempstore->set('num_search_blocks', 0);

              // $session = \Drupal::request()->getSession();
              // $session->set('num_search_blocks', 0);

              /*$view_id = \Drupal::routeMatch()->getParameter('view_id');
              if(!is_null($view_id)){
                // kint($view_id);exit;
                $view = \Drupal\views\Views::getView($view_id);
                $view->setDisplay('block_3');
                // kint($variables);exit;
                $render = $view->render();
                $variables['calendar_block'] = $render;
              }*/

              $block_manager = \Drupal::service('plugin.manager.block');
              // You can hard code configuration or you load from settings.
              $config = [];
              $plugin_block = $block_manager->createInstance('search_form_block', $config);
              // Some blocks might implement access check.
              // $access_result = $plugin_block->access(\Drupal::currentUser());
              // Return empty render array if user doesn't have access.
              // $access_result can be boolean or an AccessResult class
              $search_block = $plugin_block->build();
              $variables['search_block'] = $search_block;

            }


            /**
             * Implements hook_preprocess_paragraph().
             */
            function uch_div_preprocess_paragraph(&$variables) {
              if ($node = \Drupal::request()->attributes->get('node')) {
                $variables['nid'] = $node->id();
              }
              $paragraph = $variables['paragraph'];
              $parentBundle = $paragraph->getParentEntity()->bundle();
              // kint($paragraph);
              if($paragraph->getParagraphType()->id() == 'automated_carousel'){
                $number_in_carousel = (int)$paragraph->get('field_number_in_carousel')->getValue()[0]['value'];
                $content_type = $paragraph->get('field_content_type')->getValue()[0]['value'];
                $tags = $paragraph->get('field_selected_tags')->getValue();
                //$tags needs to be iterated over to pull out [INDEX]['target_id']
                $tags_string = '';
                foreach ($tags as $index => $data) {
                  // ($index != 0) ? $tags_string.= ',';
                  if($index != 0){
                    $tags_string.= ',';
                  }
                  $tags_string.= $data['target_id'];
                  // echo'<pre>';var_dump($data);exit;
                }
                if($tags_string == ''){
                  $tags_string = 'all';
                }
                $args = [$content_type, $tags_string];
                $view = \Drupal\views\Views::getView('automated_carousel');
                if (is_object($view)) {
                  // kint($view);exit;
                  $view->setArguments($args);
                  $view->setDisplay('block_1');
                  $view->getPager()->setItemsPerPage($number_in_carousel);
                  $view->preExecute();
                  $view->execute();
                  // $view->serialize();
                  // $test = $view->result;
                  $result = $view->result;
                  // $test = $view->result();
                  // kint($result);exit;
                  $result_nodes = [];
                  foreach ($result as $index => $data) {
                    $node = \Drupal::entityManager()->getStorage('node')->load($data->nid);
                    $result_nodes[] = $node;
                    // kint($node);exit;
                  }
                  //below is code for full view. Current code is result only.
                  // $variables['automated_carousel_view'] = $result['#view'];

                  $variables['automated_carousel_view_result'] = $result_nodes;
                }
              }
              if($paragraph->getParagraphType()->id() == 'automated_person_listing'){
                $person_type = $paragraph->get('field_person_type')->getValue()[0]['target_id'];
                $number_in_listing = (int)$paragraph->get('field_number_in_carousel')->getValue()[0]['value'];
                $args = [$person_type];
                $variables['view_args'] = $args;
                // $view = \Drupal\views\Views::getView('automated_person_listing');
                // if (is_object($view)) {
                  // $view->setDisplay('embed_1');
                  // $view->setArguments($args);
                  // $view->getPager()->setItemsPerPage($number_in_listing);
                  // $render = $view->render();
                  // $variables['automated_person_listing_view'] = $render;
                  // $view->preExecute();
                  // $view->execute();
                  // $view->serialize();
                  // $test = $view->result;
                  // $result = $view->result;
                  // $test = $view->result();
                  // kint($result);exit;
                  // $result_nodes = [];
                  // foreach ($result as $index => $data) {
                    // $node = \Drupal::entityManager()->getStorage('node')->load($data->nid);
                    // $result_nodes[] = $node;
                    // kint($node);exit;
                  // }
                  //below is code for full view. Current code is result only.
                  // $variables['automated_carousel_view'] = $result['#view'];
                  // $variables['automated_person_listing_view_result'] = $result_nodes;
                // }
                // kint($person_type);exit;
              }
              if($paragraph->getParagraphType()->id() == 'calendar'){
                // needs handler for tags
                //#JUMPTARG
                // $path = \Drupal::request()->getpathInfo();
                // $arg = explode('/',$path);
                // kint($arg);exit
                $tags = $paragraph->get('field_selected_tags')->getValue();
                $tags_string = '';
                foreach ($tags as $index => $data) {
                  // ($index != 0) ? $tags_string.= ',';
                  if($index != 0){
                    $tags_string.= ',';
                  }
                  $tags_string.= $data['target_id'];
                  // echo'<pre>';var_dump($data);exit;
                }
                if($tags_string == ''){
                  $tags_string = 'all';
                }
                $args = [$tags_string];

                $view = \Drupal\views\Views::getView('calendar_news_feed');
                $view->setDisplay('block_1');
                $view->setArguments($args);
                $render = $view->render();
                $variables['calendar_news_feed'] = $render;

                $view = \Drupal\views\Views::getView('calendar_news_feed');
                $view->setDisplay('block_2');
                $view->setArguments($args);
                $render = $view->render();
                $variables['calendar_news_feed_a'] = $render;

                $view2 = \Drupal\views\Views::getView('events_listing');
                $view2->setDisplay('block_5');
                // kint($arg);
                // if(array_key_exists(2, $arg)){
                //   $view2->setArguments([$arg[2]]);
                // }
                $render = $view2->render();
                $variables['calendar_block'] = $render;

                $view2a = \Drupal\views\Views::getView('events_listing');
                $view2a->setDisplay('block_3');
                /*if(array_key_exists(2, $arg)){
                  $view->setArguments([$arg[2]]);
                }*/
                $render = $view2a->render();
                $variables['calendar_block_a'] = $render;

                $view3 = \Drupal\views\Views::getView('events_listing');
                $view3->setDisplay('block_1');
                /*if(array_key_exists(2, $arg)){
                  $view->setArguments([$arg[2]]);
                }*/
                $render = $view3->render();
                $variables['events_list'] = $render;

                $vid = 'event_type';
                $terms =\Drupal::entityTypeManager()->getStorage('taxonomy_term')->loadTree($vid);
                foreach ($terms as $term) {
                  $variables['event_types'][$term->tid] = $term;
                }

                $view4 = \Drupal\views\Views::getView('events_listing');
                $view4->setDisplay('page_1');
                $variables['base_events_path'] = $view4->getPath();

                $variables['#cache']['contexts'][] = 'route';
                $variables['#attached']['library'][] = 'uch_div/fullcalendar_extension';
              }
              if($paragraph->getParagraphType()->id() == 'sightings_recent_columns'){
                $view5 = \Drupal\views\Views::getView('sightings_recent_columns');
                $view5->setDisplay('block_1');
                $render = $view5->render();
                $variables['sightings_columns'] = $render;

              }
              if($paragraph->getParagraphType()->id() == 'sightings_search'){
                $form = \Drupal::formBuilder()->getForm('\Drupal\archive_search\Form\ArchiveSearch');
                $variables['archive_form'] = $form;
              }
              if($paragraph->getParagraphType()->id() == 'faculty_search'){
                $form = \Drupal::formBuilder()->getForm('\Drupal\archive_search\Form\PersonSearch');
                $variables['archive_form'] = $form;
              }
              if($paragraph->getParagraphType()->id() == 'automated_carousel'){
                $view = \Drupal\views\Views::getView('automated_carousel');
                $view->setDisplay('block_1');
                $variables['base_sightings_path'] = $view->getPath();
                $field_info = Drupal\field\Entity\FieldConfig::loadByName('node', 'sightings', 'field_featured_image');
                $image_uuid = $field_info->getSetting('default_image')['uuid'];
                $image = Drupal::service('entity.repository')->loadEntityByUuid('file', $image_uuid);
                $variables['default_uri_value'] = $image->getFileUri();
              }

            }


            function uch_div_preprocess_html(&$variables) {
              if ($node = \Drupal::request()->attributes->get('node')) {
                $variables['attributes']['class'][] = 'page-node-' . $node->id();

                if ($node->getType() === 'news') {// Check if node type is basic page
                  // kint($node);exit;
                  //kint($node->get('field_featured_image')->value);exit;news_detail_featured_image
                  if (!is_null($node->field_featured_image->entity)) {
                    $styled_image_url = ImageStyle::load('news_detail_featured_image')->buildUrl($node->field_featured_image->entity->getFileUri());
                  } else {
                    $styled_image_url = '';
                  }
                  $og_image = [
                      '#tag' => 'meta',
                      '#attributes' => [
                          'property' => 'og:image',
                          'content' => $styled_image_url,
                      ],
                  ];
                  $og_title = [
                      '#tag' => 'meta',
                      '#attributes' => [
                          'property' => 'og:title',
                          'content' => $node->getTitle(),
                      ],
                  ];
                  $og_description = [
                      '#tag' => 'meta',
                      '#attributes' => [
                          'property' => 'og:description',
                          'content' => $node->get('body')->summary,
                      ],
                  ];
                  $twitter_card = [
                    '#tag' => 'meta',
                    '#attributes' => [
                      'property' => 'twitter:card',
                      'content' => 'summary_large_image'
                    ]
                  ];
                  $variables['page']['#attached']['html_head'][] = [$og_image, 'og:image'];
                  $variables['page']['#attached']['html_head'][] = [$og_title, 'og:title'];
                  $variables['page']['#attached']['html_head'][] = [$og_description, 'og:description'];
                  $variables['page']['#attached']['html_head'][] = [$twitter_card, 'twitter:card'];
                }
              }
              $current_path = \Drupal::service('path.current')->getPath();
              $variables['current_path'] = \Drupal::service('path.alias_manager')->getAliasByPath($current_path);
              // $_SESSION['uch_div']['num_search_blocks'] = 1;
              // $session = \Drupal::request()->getSession();
              // $session->set('num_search_blocks', 0);

              // $bundle = 'taxonomy_menu'; // or $bundle='my_bundle_type';
              // $query = \Drupal::entityQuery('node');
              //   $query->condition('status', 1);
              //   $query->condition('type', $bundle);
              // $entity_ids = $query->execute();
              /*$entity = \Drupal::entityTypeManager()
              ->getStorage('taxonomy_menu')
              ->loadMultiple();
              $tmp = $entity['test']->getMenuParent();
              $uuid = array_reverse(explode(':',$tmp))[0];
              // $id = '55834cd0-9ed8-4229-8307-04021126a659';
              $menu_link_content = \Drupal::entityManager()->loadEntityByUuid('menu_link_content', $uuid);
              $node = $menu_link_content->getUrlObject()->getRouteParameters()['node'];
              // kint($menu_link_content->getUrlObject()->getRouteParameters());
              kint( $entity['test']->getVocabulary());exit;*/
            }

            function uch_div_preprocess_breadcrumb(&$variables){

              // kint($variables['links']);exit;
              /*kint($variables);
              foreach ($variables['links'] as $key => $value) {
                kint($value);
              }
              exit;*/

              /*$variables['mt_setting']['breadcrumb_separator'] = '>';

              if($variables['breadcrumb']){
                $request = \Drupal::request();
                $route_match = \Drupal::routeMatch();
                $page_title = \Drupal::service('title_resolver')->getTitle($request, $route_match->getRouteObject());
                if (!empty($page_title)) {
                  $variables['breadcrumb'][] = array(
                    'text' => $page_title,
                  );
                  // Add cache context based on url.
                  $variables['#cache']['contexts'][] = 'url';
                }
              }*/

              // kint($variables['links']);
              /*$variables['breadcrumb'] = array();
              $variables['node'] = \Drupal::routeMatch()->getParameter('node');
              $variables['type'] = \Drupal::routeMatch()->getParameter('node')->getType(); 
              $host = \Drupal::request()->getSchemeAndHttpHost();

              switch($variables['type']) {
                case 'news':
                  $variables['breadcrumb'] = array(
                    array(
                      'text' => 'Home',
                      'url' => $host
                    ),
                    array(
                      'text' => '>',
                    ),
                    array(
                      'text' => 'News',
                      'url' => ''
                    ),
                    array(
                      'text' => '>',
                    ),
                    array(
                      'text' => $variables['node']->getTitle(),
                      'url' => $variables['node']->URL()
                    ),
                  );
                  break;

                case 'person':
                  $variables['breadcrumb'] = array(
                    array(
                      'text' => 'Home',
                      'url' => $host
                    ),
                    array(
                      'text' => '>',
                    ),
                    array(
                      'text' => 'Person',
                      'url' => ''
                    ),
                    array(
                      'text' => '>',
                    ),
                    array(
                      'text' => $variables['node']->getTitle(),
                      'url' => $variables['node']->URL()
                    ),
                  );
                  break;  
              }*/
            }

              function uch_div_preprocess_node(&$variables){
                $variables['node'] = \Drupal::routeMatch()->getParameter('node');
                if(!is_null($variables['node'])){
                  $variables['type'] = \Drupal::routeMatch()->getParameter('node')->getType(); 
                  switch($variables['type']){
                    case 'news':
                    case 'sightings':
                      // Load block
                      $block = \Drupal\block\Entity\Block::load('socialsharingblock');
                      $variables['block_view'] = \Drupal::entityTypeManager()
                        ->getViewBuilder('block')
                        ->view($block);
                      $view = \Drupal\views\Views::getView('sightings_finder');
                      $view->setDisplay('page_1');
                      $variables['base_sightings_path'] = $view->getPath();
                      //load default image uuid
                      $field_info = Drupal\field\Entity\FieldConfig::loadByName('node', 'sightings', 'field_featured_image');
                      $image_uuid = $field_info->getSetting('default_image')['uuid'];
                      $variables['default_uuid'] = $image_uuid;
                      break;

                    case 'publication':
                      // kint($variables['node']->field_author);exit;
                      $names = '';
                      foreach ($variables['node']->field_author as $item) {
                        // kint($item->getValue());exit;
                        $nid = $item->getValue()['target_id'];
                        $node = \Drupal\node\Entity\Node::load($nid);
                        $name = $node->get('field_first_name')->getValue()[0]['value'].' '.$node->get('title')->getValue()[0]['value'];
                        if($names != ''){
                          $names.= ', ';
                        }
                        $names.= $name;
                        // kint($node->get('title')->getValue()[0]);exit;
                      }
                      $variables['author_names'] = $names;
                      break;

                    case 'person':
                      $view = Views::getView('person_publications');
                      $view->setDisplay('block_1');
                      if(!empty($variables['node']->id())){
                        $view->setArguments([$variables['node']->id()]);
                      }
                      $view->execute();
                      $view_render = $view->render();
                      $view_result = \Drupal::service('renderer')->renderRoot($view_render);
                      // kint($view->total_rows);
                      $variables['publication_rows'] = $view->total_rows;
                      $variables['publication_image'] = $view_result;

                      // Load block
                      $block = \Drupal\block\Entity\Block::load('socialsharingblock');
                      $variables['block_view'] = \Drupal::entityTypeManager()
                        ->getViewBuilder('block')
                        ->view($block);

                      // $entity = $variables['row']->_entity;
                      $field_info = Drupal\field\Entity\FieldConfig::loadByName('node', 'person', 'field_featured_image');
                      $image_uuid = $field_info->getSetting('default_image')['uuid'];
                      $image = Drupal::service('entity.repository')->loadEntityByUuid('file', $image_uuid);
                      $variables['default_uri'] = $image->getFileUri();

                      $directory_url = Url::fromRoute('view.person_search.page_1');
                      $variables['directory_url_actual'] = $directory_url->toString();
                      // $node = \Drupal\node\Entity\Node::load($nid);
                      // ksm($variables['node']);

                      // $person_nid = $variables['node']->id();

                      // $query = \Drupal::entityQuery('node');
                      // $query->condition('status', 1);
                      // $query->condition('type', 'publication');
                      // $query->condition('field_author.entity.nid',$person_nid);
                      // $entity_ids_author = $query->execute();

                      // $run = \Drupal::entityQuery('node');
                      // $run->condition('status', 1);
                      // $run->condition('type', 'course');
                      // $run->condition('field_instructor.entity.nid',$person_nid);
                      // $entity_ids_instructor = $run->execute();

                      // $nodes =  \Drupal\node\Entity\Node::loadMultiple($entity_ids_author);

                      // foreach($nodes as $node){
                      //  // echo $node->get('field_featured_image')->getValue();
                      //  //kint($node->field_featured_image[0]->entity()->uri()->value());
                      //  //field_featured_image['#items'].entity.uri.value
                      // }

                      //  exit;
                      // $query = new EntityFieldQuery();
                      // $node_entities = $query
                      //   ->entityCondition('entity_type', 'node', '=')
                      //   ->propertyCondition('type', 'song', '=')
                      //   ->propertyCondition('status',1,'=') // assuming you just want published songs
                      //   ->fieldCondition('field_singer','target_id',$singer_nid)
                      //   ->execute();
                      // if ($node_entities) {
                      //   $song_nids = array_keys($node_entities['node']);
                      //   $variables['song_nodes'] = node_load_multiple($song_nids);
                      // }
                      break;

                    case 'page':
                      // below code from https://drupal.stackexchange.com/questions/171686/how-can-i-programmatically-display-a-block
                      $block_manager = \Drupal::service('plugin.manager.block');
                      // You can hard code configuration or you load from settings.
                      $config = [];
                      $plugin_block = $block_manager->createInstance('sidebar_menu_block', $config);
                      // Some blocks might implement access check.
                      $access_result = $plugin_block->access(\Drupal::currentUser());
                      // Return empty render array if user doesn't have access.
                      // $access_result can be boolean or an AccessResult class
                      /*if (is_object($access_result) && $access_result->isForbidden() || is_bool($access_result) && !$access_result) {
                        // You might need to add some cache tags/contexts.
                        return [];
                      }*/
                      $block_render = $plugin_block->build();
                      // if block empty that means root page and return a flag
                      // kint(empty($block_render['#markup']));exit;
                      // echo'<pre>';var_dump(empty($block_render['#markup']));exit;
                      // node.field_hide_sidebar.value != 1 or node.field_hide_sidebar.value is empty
                      // ksm($variables['node']);
                      $variables['switch'] = TRUE;
                      $node = $variables['node'];
                      // ksm($node->get('field_hide_sidebar')->getValue()[0]['value']);
                      // ksm($node->get('field_hide_sidebar')->getValue() == 1);
                      if(!is_null($node->get('field_hide_sidebar'))) {
                        if ($node->get('field_hide_sidebar')->getValue()[0]['value'] == 1) {
                          $variables['sidebar_block_empty'] = "page-no-sidebar";
                          $variables['switch'] = FALSE;
                        }
                      }
                      $menu_link_manager = \Drupal::service('plugin.manager.menu.link');
                      $result = $menu_link_manager->loadLinksByRoute('entity.node.canonical', array('node' => $node->id()));
                      $variables['at_root'] = FALSE;
                      foreach ($result as $key => $value) {
                        // kint($value->getMenuName());
                        if($value->getMenuName() == 'page-menu'){
                          if($value->getParent() == ''){
                            $variables['at_root'] = TRUE;
                          }
                          // kint($value->getParent());
                        }
                      }
                      // $sidebar_text_count = $node->get('field_sidebar_text')->count();
                      $variables['has_sidebar_text'] = FALSE;
                      // if($sidebar_text_count){
                        // $variables['has_sidebar_text'] = TRUE;
                      // }

                      $variables['sidebar_block'] = $block_render;

                      if($variables['switch']){
                        if($variables['at_root'] && $variables['has_sidebar_text']){
                          $variables['switch'] = TRUE;
                        } else if($variables['at_root']){
                          $variables['switch'] = FALSE;
                        }
                      }

                      // parse shortcode
                      $str = $node->get('body')->getValue()[0]['value'];
                      $re = '/\<p\>\[(.*?)\]\<\/p\>/m';
                      preg_match_all($re, $str, $matches, PREG_SET_ORDER, 0);
                      foreach ($matches as $index => $array) {
                        switch ($array[1]) {
                          case 'faculty_tags':
                              // current implementation will break tag listings across columns. If we need to ensure that all tags which fall under the same parent category then we need to nest <ul>s inside the top-level <li>s.
                            $directory_url = Url::fromRoute('view.person_search.page_1');
                            // ksm($url->toString());
                            $terms = \Drupal::entityTypeManager()->getStorage('taxonomy_term')->loadTree('faculty_tags',0,NULL,TRUE);
                            $faculty_tags_list_items = '';
                            foreach ($terms as $term) {
                              if (is_null($term->parent->entity)) {
                                $faculty_tags_list_items.= '<li class="header">'.$term->name->value.'</li>';
                              } else {
                                $faculty_tag = str_replace(' ', '-', strtolower($term->name->value));
                                $directory_url_actual = $directory_url->toString();
                                // ksm($directory_url_actual);
                                $faculty_tags_list_items.= '<li><a href="'.$directory_url_actual.'/all/faculty/all/faculty/'.$faculty_tag.'">'.$term->name->value.'</a></li>';
                              }
                            }
                            $faculty_tags_html = '<div class="faculty-tags">
                              <ul>
                                '.$faculty_tags_list_items.'
                              </ul>
                            </div>';
                            $str = str_replace('<p>[faculty_tags]</p>', $faculty_tags_html, $str);
                            break;

                          default:
                            # code...
                            break;
                        }
                      }
                      $variables['body_processed'] = $str;

                      break;
                  }
                }

              }

            function uch_div_preprocess_taxonomy_term(&$variables){
              $vid = $variables['term']->bundle();
              $vocab = \Drupal::entityTypeManager()->getStorage('taxonomy_vocabulary')->load($vid);
              $variables['vocab_name'] = $vocab->get('name');
              // below code from https://drupal.stackexchange.com/questions/171686/how-can-i-programmatically-display-a-block
              $block_manager = \Drupal::service('plugin.manager.block');
              // You can hard code configuration or you load from settings.
              $config = [];
              $plugin_block = $block_manager->createInstance('sidebar_menu_block', $config);
              // Some blocks might implement access check.
              $access_result = $plugin_block->access(\Drupal::currentUser());
              // Return empty render array if user doesn't have access.
              // $access_result can be boolean or an AccessResult class
              /*if (is_object($access_result) && $access_result->isForbidden() || is_bool($access_result) && !$access_result) {
                // You might need to add some cache tags/contexts.
                return [];
              }*/
              $block_render = $plugin_block->build();
              // kint($block_render);exit;
              $variables['sidebar_block'] = $block_render;
              // kint($variables['term']->bundle());

              // below code from https://drupal.stackexchange.com/questions/171686/how-can-i-programmatically-display-a-block
              $block_manager = \Drupal::service('plugin.manager.block');
              // You can hard code configuration or you load from settings.
              $config = [];
              $plugin_block = $block_manager->createInstance('system_breadcrumb_block', $config);
              // Some blocks might implement access check.
              $access_result = $plugin_block->access(\Drupal::currentUser());
              // Return empty render array if user doesn't have access.
              // $access_result can be boolean or an AccessResult class
              /*if (is_object($access_result) && $access_result->isForbidden() || is_bool($access_result) && !$access_result) {
                // You might need to add some cache tags/contexts.
                return [];
              }*/
              $block_render = $plugin_block->build();
              $variables['breadcrumbs_block'] = $block_render;
            }

            function uch_div_preprocess_views_view(&$variables){

              //events preprocess
              if($variables['id'] == 'events_listing' && $variables['display_id'] == 'page_1'){
                $path = \Drupal::request()->getpathInfo();
                $arg  = explode('/',$path);
                //arg[2] will be type of event
                // kint($arg);exit;
                $view = \Drupal\views\Views::getView('events_listing');
                $view->setDisplay('block_3');
                if(array_key_exists(2, $arg)){
                  $view->setArguments([$arg[2]]);
                }
                $render = $view->render();
                $variables['calendar_block'] = $render;

                $vid = 'event_type';
                $terms =\Drupal::entityTypeManager()->getStorage('taxonomy_term')->loadTree($vid);
                foreach ($terms as $term) {
                 $variables['event_types'][$term->tid] = $term;
                }

                $vocab = \Drupal::entityTypeManager()->getStorage('taxonomy_vocabulary')->load($vid);
                $variables['vocab_name'] = $vocab->get('name');

                $view2 = \Drupal\views\Views::getView('events_listing');
                $view2->setDisplay('page_1');
                $variables['base_events_path'] = $view2->getPath();

                // below code from https://drupal.stackexchange.com/questions/171686/how-can-i-programmatically-display-a-block
                $block_manager = \Drupal::service('plugin.manager.block');
                // You can hard code configuration or you load from settings.
                $config = [];
                $plugin_block = $block_manager->createInstance('system_breadcrumb_block', $config);
                // Some blocks might implement access check.
                $access_result = $plugin_block->access(\Drupal::currentUser());
                // Return empty render array if user doesn't have access.
                // $access_result can be boolean or an AccessResult class
                /*if (is_object($access_result) && $access_result->isForbidden() || is_bool($access_result) && !$access_result) {
                  // You might need to add some cache tags/contexts.
                  return [];
                }*/
                $block_render = $plugin_block->build();
                $variables['breadcrumbs_block'] = $block_render;

                $path = \Drupal::request()->getpathInfo();
                $arg  = explode('/',$path);
                // kint($arg);
                //0 - none
                //1 - events
                //2 - events
                //3 - year
                //4 - month
                //5 - day
                $type = 'all';
                $year = 'all';
                $month = 'all';
                $day = 'all';
                if(array_key_exists(2, $arg)){
                  $type = $arg[2];
                }
                if(array_key_exists(3, $arg)){
                  $year = $arg[3];
                }
                if(array_key_exists(4, $arg)){
                  $month = $arg[4];
                }
                if(array_key_exists(5, $arg)){
                  $day = $arg[5];
                }
                $variables['type_filter'] = $type;
                $variables['year_filter'] = $year;
                $variables['month_filter'] = $month;
                $variables['day_filter'] = $day;

                // kint($variables['#attached']);
                $variables['#cache']['contexts'][] = 'route';
                $variables['#attached']['library'][] = 'uch_div/fullcalendar_extension';
              }

              //multimedia preprocess
              if($variables['id'] == 'multimedia_listing' && $variables['display_id'] == 'page_1'){
                // below code from https://drupal.stackexchange.com/questions/171686/how-can-i-programmatically-display-a-block
                $block_manager = \Drupal::service('plugin.manager.block');
                // You can hard code configuration or you load from settings.
                $config = [];
                $plugin_block = $block_manager->createInstance('system_breadcrumb_block', $config);
                // Some blocks might implement access check.
                $access_result = $plugin_block->access(\Drupal::currentUser());
                // Return empty render array if user doesn't have access.
                // $access_result can be boolean or an AccessResult class
                /*if (is_object($access_result) && $access_result->isForbidden() || is_bool($access_result) && !$access_result) {
                  // You might need to add some cache tags/contexts.
                  return [];
                }*/
                $block_render = $plugin_block->build();
                $variables['breadcrumbs_block'] = $block_render;
              }

              //news preprocess
              if($variables['id'] == 'news_search' && $variables['display_id'] == 'page_1'){
                $view = \Drupal\views\Views::getView('news_search');
                $view->setDisplay('page_1');
                $variables['base_news_path'] = $view->getPath();

                $view2 = \Drupal\views\Views::getView('news_listing_featured_tags');
                $view2->setDisplay('block_1');
                $render = $view2->render();
                $variables['featured_tags_view'] = $render;

                $view3 = \Drupal\views\Views::getView('listing_page_tags');
                $view3->setDisplay('block_3');
                $render = $view3->render();
                $variables['all_tags_view'] = $render;

                // below code from https://drupal.stackexchange.com/questions/171686/how-can-i-programmatically-display-a-block
                $block_manager = \Drupal::service('plugin.manager.block');
                // You can hard code configuration or you load from settings.
                $config = [];
                $plugin_block = $block_manager->createInstance('system_breadcrumb_block', $config);
                // Some blocks might implement access check.
                $access_result = $plugin_block->access(\Drupal::currentUser());
                // Return empty render array if user doesn't have access.
                // $access_result can be boolean or an AccessResult class
                /*if (is_object($access_result) && $access_result->isForbidden() || is_bool($access_result) && !$access_result) {
                  // You might need to add some cache tags/contexts.
                  return [];
                }*/
                $block_render = $plugin_block->build();
                $variables['breadcrumbs_block'] = $block_render;
              }
              if($variables['id'] == 'news_listing_featured_tags' && $variables['display_id'] == 'block_1'){
                $view = \Drupal\views\Views::getView('news_search');
                $view->setDisplay('page_1');
                $variables['base_news_path'] = $view->getPath();
                $variables['featured_tags_view_title'] = $variables['view']->getDisplay()->display['display_options']['block_description'];
              }

              //publications preprocess
              if($variables['id'] == 'publications_search' && $variables['display_id'] == 'page_1'){
                $view = \Drupal\views\Views::getView('publications_search');
                $view->setDisplay('page_1');
                $variables['base_publications_path'] = $view->getPath();

                $view2 = \Drupal\views\Views::getView('publications_listing_featured_tags');
                $view2->setDisplay('block_1');
                $render = $view2->render();
                $variables['featured_tags_view'] = $render;

                // below code from https://drupal.stackexchange.com/questions/171686/how-can-i-programmatically-display-a-block
                $block_manager = \Drupal::service('plugin.manager.block');
                // You can hard code configuration or you load from settings.
                $config = [];
                $plugin_block = $block_manager->createInstance('system_breadcrumb_block', $config);
                // Some blocks might implement access check.
                $access_result = $plugin_block->access(\Drupal::currentUser());
                // Return empty render array if user doesn't have access.
                // $access_result can be boolean or an AccessResult class
                /*if (is_object($access_result) && $access_result->isForbidden() || is_bool($access_result) && !$access_result) {
                  // You might need to add some cache tags/contexts.
                  return [];
                }*/
                $block_render = $plugin_block->build();
                $variables['breadcrumbs_block'] = $block_render;
              }
              if($variables['id'] == 'publications_listing_featured_tags' && $variables['display_id'] == 'block_1'){
                $view = \Drupal\views\Views::getView('publications_search');
                $view->setDisplay('page_1');
                $variables['base_publications_path'] = $view->getPath();
                $variables['featured_tags_view_title'] = $variables['view']->getDisplay()->display['display_options']['block_description'];
              }

              //directory preprocess
              if($variables['id'] == 'person_search' && $variables['display_id'] == 'page_1'){
                $view = \Drupal\views\Views::getView('person_search');
                $view->setDisplay('page_1');
                $variables['base_directory_path'] = $view->getPath();

                $view2 = \Drupal\views\Views::getView('person_listing_featured_titles');
                $view2->setDisplay('block_1');
                $render = $view2->render();
                $variables['featured_titles_view'] = $render;

                // below code from https://drupal.stackexchange.com/questions/171686/how-can-i-programmatically-display-a-block
                $block_manager = \Drupal::service('plugin.manager.block');
                // You can hard code configuration or you load from settings.
                $config = [];
                $plugin_block = $block_manager->createInstance('system_breadcrumb_block', $config);
                // Some blocks might implement access check.
                $access_result = $plugin_block->access(\Drupal::currentUser());
                // Return empty render array if user doesn't have access.
                // $access_result can be boolean or an AccessResult class
                /*if (is_object($access_result) && $access_result->isForbidden() || is_bool($access_result) && !$access_result) {
                  // You might need to add some cache tags/contexts.
                  return [];
                }*/
                $block_render = $plugin_block->build();
                $variables['breadcrumbs_block'] = $block_render;

                $path = \Drupal::request()->getpathInfo();
                $arg  = explode('/',$path);
                //0 - none
                //1 - directory
                //2 - letter
                //3 - title
                //4 - department
                //5 - person type

                $department_filter = '';
                $vid = 'department';
                // $terms =\Drupal::entityTypeManager()->getStorage('taxonomy_term')->loadTree($vid);
                // ksm($terms);
                $vocabulary_name = 'department'; //name of your vocabulary
                $query = \Drupal::entityQuery('taxonomy_term');
                $query->condition('vid', $vocabulary_name);
                $group = $query->orConditionGroup()
                ->notExists('field_hide_from_directory')
                ->condition('field_hide_from_directory', '1', '<>');
                $query->condition($group);
                // $query->condition('field_hide_from_directory.value', 1, '!=');
                $query->sort('weight');
                $tids = $query->execute();
                $terms = Term::loadMultiple($tids);
                uasort($terms, 'sort_taxonomy_term_objs');
                // $output = '<ul>';
                foreach($terms as $term) {
                    // $name = $term->getName();;
                    // $url = Url::fromRoute('entity.taxonomy_term.canonical', ['taxonomy_term' => $term->id()]);
                    // $link = Link::fromTextAndUrl($name, $url);
                    // $link = $link->toRenderable();
                    // $output .='<li>'.render($link).'</li>';
                  // ksm($term);
                  $variables['departments'][$term->id()] = $term->getName();
                  if(array_key_exists(4, $arg)){
                   // echo'<pre>';var_dump($term);exit;
                   // ksm($arg[4]);
                   if ($arg[4] == strtolower(str_replace(' ', '-', $term->getName()))) {
                     // ksm($arg[4]);
                     $department_filter = strtolower(str_replace(' ', '-', $term->getName()));
                   }
                  }
                }
                // $output .= '</ul>';
                // foreach ($terms as $term) {
                //  $variables['departments'][$term->tid] = $term;
                //  if(array_key_exists(4, $arg)){
                //   // echo'<pre>';var_dump($term);exit;
                //   // ksm($arg[4]);
                //   if ($arg[4] == strtolower(str_replace(' ', '-', $term->name))) {
                //     // ksm($arg[4]);
                //     $department_filter = strtolower(str_replace(' ', '-', $term->name));
                //   }
                //  }
                // }
                $variables['department_filter_active'] = $department_filter;

                $vocab = \Drupal::entityTypeManager()->getStorage('taxonomy_vocabulary')->load($vid);
                $variables['vocab_name'] = $vocab->get('name');

                $person_filter = '';
                $vid = 'person_type';
                $terms =\Drupal::entityTypeManager()->getStorage('taxonomy_term')->loadTree($vid);
                uasort($terms, 'sort_taxonomy_term_objs_alt');
                // echo'<pre>';var_dump($terms);exit;
                foreach ($terms as $term) {
                 $variables['person_types'][$term->tid] = $term;
                 if(array_key_exists(5, $arg)){
                  if ($arg[5] == strtolower(str_replace(' ', '-', $term->name))) {
                    $person_filter = strtolower(str_replace(' ', '-', $term->name));
                  }
                 }
                }
                if($person_filter == '' && !array_key_exists(5, $arg)){
                  $person_filter = 'faculty';
                }
                $variables['person_filter_active'] = $person_filter;

                $letter_filter = '';
                if(array_key_exists(2, $arg)){
                  $letter_filter = $arg[2];
                }

                $variables['letter_filter_active'] = $letter_filter;

                $vocab = \Drupal::entityTypeManager()->getStorage('taxonomy_vocabulary')->load($vid);
                $variables['person_types_vocab_name'] = $vocab->get('name');

                /**
                 * here we must extract the different URL args separated by / & store them in variables. If no arg is found, will default to 'all'
                 */
                $query_combine = \Drupal::request()->get('combine');
                $path = \Drupal::request()->getpathInfo();
                $arg  = explode('/',$path);
                // ksm($arg);
                // ksm($combine);
                //0 - none
                //1 - directory
                //2 - letter
                //3 - type
                //4 - department
                $letter = 'all';
                $type = 'all';
                $department = 'all';
                $person_type = 'all';
                $faculty_tag = 'all';
                $combine = '';
                if(array_key_exists(2, $arg)){
                  $letter = $arg[2];
                }
                if(array_key_exists(3, $arg)){
                  $type = $arg[3];
                } else {
                  $type = 'faculty';
                }
                if(array_key_exists(4, $arg)){
                  $department = $arg[4];
                }
                if(array_key_exists(5, $arg)){
                  $person_type = $arg[5];
                } else {
                  $person_type = 'faculty';
                }
                if(array_key_exists(6, $arg)){
                  $faculty_tag = $arg[6];
                }
                if($query_combine != '') {
                  $combine = '?combine='.$query_combine;
                }
                $variables['letter_filter'] = $letter;
                $variables['type_filter'] = $type;
                $variables['department_filter'] = $department;
                $variables['person_type_filter'] = $person_type;
                $variables['faculty_tag'] = $faculty_tag;
                $variables['combine'] = $combine;

                $filters = true;
                if( $letter == 'all' && $type == 'all' && $department == 'all'){
                  $filters = false;
                }
                $variables['filters'] = $filters;
                // ksm($variables['combine']);

                // kint($variables['view']->getTitle());

                $variables['person_search_title'] = $variables['view']->getTitle();
              }
              if($variables['id'] == 'person_listing_featured_titles' && $variables['display_id'] == 'block_1'){
                $view = \Drupal\views\Views::getView('person_search');
                $view->setDisplay('page_1');
                $variables['base_directory_path'] = $view->getPath();
                $variables['featured_titles_view_title'] = $variables['view']->getDisplay()->display['display_options']['block_description'];
                $query_combine = \Drupal::request()->get('combine');
                $path = \Drupal::request()->getpathInfo();
                $arg  = explode('/',$path);
                // kint($arg);
                //0 - none
                //1 - directory
                //2 - letter
                //3 - type
                //4 - department
                $letter = 'all';
                $type = 'all';
                $department = 'all';
                $person_type_filter = 'all';
                $faculty_tag = 'all';
                $combine = '';
                if(array_key_exists(2, $arg)){
                  $letter = $arg[2];
                }
                if(array_key_exists(3, $arg)){
                  $type = $arg[3];
                } else {
                  $type = 'faculty';
                }
                if(array_key_exists(4, $arg)){
                  $department = $arg[4];
                }
                if(array_key_exists(5, $arg)){
                  $person_type_filter = $arg[5];
                } else {
                  $person_type_filter = 'faculty';
                }
                if(array_key_exists(6, $arg)){
                  $faculty_tag = $arg[6];
                }
                if($query_combine != ''){
                  $combine = '?combine='.$query_combine;
                }
                $variables['letter_filter'] = $letter;
                $variables['type_filter'] = $type;
                $variables['department_filter'] = $department;
                $variables['person_type_filter'] = $person_type_filter;
                $variables['faculty_tag'] = $faculty_tag;
                $variables['combine'] = $combine;
              }

              //sightings preprocess
              if($variables['id'] == 'sightings_finder' && $variables['display_id'] == 'page_1'){
                $view = \Drupal\views\Views::getView('sightings_finder');
                $view->setDisplay('page_1');
                $variables['base_sightings_path'] = $view->getPath();

                $view2 = \Drupal\views\Views::getView('sightings_listing_featured_tags');
                $view2->setDisplay('block_1');
                $render = $view2->render();
                $variables['featured_tags_view'] = $render;

                $view3 = \Drupal\views\Views::getView('listing_page_tags');
                $view3->setDisplay('block_2');
                $render = $view3->render();
                $variables['all_tags_view'] = $render;

                // below code from https://drupal.stackexchange.com/questions/171686/how-can-i-programmatically-display-a-block
                $block_manager = \Drupal::service('plugin.manager.block');
                // You can hard code configuration or you load from settings.
                $config = [];
                $plugin_block = $block_manager->createInstance('system_breadcrumb_block', $config);
                // Some blocks might implement access check.
                $access_result = $plugin_block->access(\Drupal::currentUser());
                // Return empty render array if user doesn't have access.
                // $access_result can be boolean or an AccessResult class
                /*if (is_object($access_result) && $access_result->isForbidden() || is_bool($access_result) && !$access_result) {
                  // You might need to add some cache tags/contexts.
                  return [];
                }*/
                $block_render = $plugin_block->build();
                $variables['breadcrumbs_block'] = $block_render;
              }
              if($variables['id'] == 'sightings_listing_featured_tags' && $variables['display_id'] == 'block_1'){
                $view = \Drupal\views\Views::getView('sightings_finder');
                $view->setDisplay('page_1');
                $variables['base_sightings_path'] = $view->getPath();
                $variables['featured_tags_view_title'] = $variables['view']->getDisplay()->display['display_options']['block_description'];
              }
              if($variables['id'] == 'listing_page_tags' && $variables['display_id'] == 'block_2'){
                $view = \Drupal\views\Views::getView('listing_page_tags');
                $view->setDisplay('block_2');
                $variables['all_tags_view_title'] = $variables['view']->getDisplay()->display['display_options']['block_description'];
              }
              if($variables['id'] == 'listing_page_tags' && $variables['display_id'] == 'block_3'){
                $view = \Drupal\views\Views::getView('listing_page_tags');
                $view->setDisplay('block_3');
                $variables['all_tags_view_title'] = $variables['view']->getDisplay()->display['display_options']['block_description'];
              }
            }

            function uch_div_preprocess_views_view_fields(&$variables){
              //news fields preprocess
              if($variables['view']->id() == 'news_search' && $variables['view']->getDisplay()->display['id'] == 'page_1'){
                $view = \Drupal\views\Views::getView('news_search');
                $view->setDisplay('page_1');
                $variables['base_news_path'] = $view->getPath();
                $node = $variables['row']->_entity;
                $summary = $node->body->summary;
                $body = $node->body->value;
                $text = '';
                if (!is_null($summary) && strlen($summary) > 0) {
                  if (strlen($summary > 250)) {
                    $text = mb_substr($summary, 0, 250).'...';
                  } else {
                    $text = $summary;
                  }
                } elseif(mb_strlen($body) > 0) {
                  if (mb_strlen($body) > 250) {
                    $body_tmp = html_entity_decode($body);
                    $body_tmp_2 = strip_tags($body_tmp);
                    $text = mb_substr($body_tmp_2, 0, 250).'...';
                  } else {
                    $text = $body_tmp_2;
                  }
                }
                $variables['clean_blurb_text'] = $text;
              }
              if($variables['view']->id() == 'news_listing_featured_tags' && $variables['view']->getDisplay()->display['id'] == 'block_1'){
                $view = \Drupal\views\Views::getView('news_search');
                $view->setDisplay('page_1');
                $variables['base_news_path'] = $view->getPath();
              }

              //publications fields preprocess
              if($variables['view']->id() == 'publications_search' && $variables['view']->getDisplay()->display['id'] == 'page_1'){
                $view = \Drupal\views\Views::getView('publications_search');
                $view->setDisplay('page_1');
                $variables['base_publications_path'] = $view->getPath();
              }
              if($variables['view']->id() == 'publications_listing_featured_tags' && $variables['view']->getDisplay()->display['id'] == 'block_1'){
                $view = \Drupal\views\Views::getView('publications_search');
                $view->setDisplay('page_1');
                $variables['base_publications_path'] = $view->getPath();
              }

              //sightings fields preprocess
              if($variables['view']->id() == 'sightings_finder' && $variables['view']->getDisplay()->display['id'] == 'page_1'){
                $view = \Drupal\views\Views::getView('sightings_finder');
                $view->setDisplay('page_1');
                $variables['base_sightings_path'] = $view->getPath();
                $entity = $variables['row']->_entity;
                $field_info = Drupal\field\Entity\FieldConfig::loadByName('node', 'sightings', 'field_featured_image');
                $field_title = html_entity_decode($entity->title[0]->value);
                $variables['field_title'] = $field_title;
                $image_uuid = $field_info->getSetting('default_image')['uuid'];
                $image = Drupal::service('entity.repository')->loadEntityByUuid('file', $image_uuid);
                $variables['default_uri'] = $image->getFileUri();
                $node = $variables['row']->_entity;
                $summary = $node->body->summary;
                $body = $node->body->value;
                $text = '';
                if (!is_null($summary) && strlen($summary) > 0) {
                  if (strlen($summary > 250)) {
                    $text = mb_substr($summary, 0, 250).'...';
                  } else {
                    $text = $summary;
                  }
                } elseif(mb_strlen($body) > 0) {
                  if (mb_strlen($body) > 250) {
                    $body_tmp = html_entity_decode($body);
                    $body_tmp_2 = strip_tags($body_tmp);
                    $text = mb_substr($body_tmp_2, 0, 250).'...';
                  } else {
                    $text = $body_tmp_2;
                  }
                }
                $variables['clean_blurb_text'] = $text;
              }
              if($variables['view']->id() == 'sightings_listing_featured_tags' && $variables['view']->getDisplay()->display['id'] == 'block_1'){
                $view = \Drupal\views\Views::getView('sightings_finder');
                $view->setDisplay('page_1');
                $variables['base_sightings_path'] = $view->getPath();
              }
              if($variables['view']->id() == 'listing_page_tags' && $variables['view']->getDisplay()->display['id'] == 'block_2'){
                $view = \Drupal\views\Views::getView('sightings_finder');
                $view->setDisplay('page_1');
                $variables['base_sightings_path'] = $view->getPath();
              }
              if($variables['view']->id() == 'listing_page_tags' && $variables['view']->getDisplay()->display['id'] == 'block_3'){
                $view = \Drupal\views\Views::getView('news_search');
                $view->setDisplay('page_1');
                $variables['base_news_path'] = $view->getPath();
              }


              //directory fields preprocess
              if($variables['view']->id() == 'person_search' && $variables['view']->getDisplay()->display['id'] == 'page_1'){
                $view = \Drupal\views\Views::getView('person_search');
                $view->setDisplay('page_1');
                $variables['base_directory_path'] = $view->getPath();
                $path = \Drupal::request()->getpathInfo();
                $arg  = explode('/',$path);
                // kint($arg);
                //0 - none
                //1 - directory
                //2 - letter
                //3 - type
                //4 - department
                $letter = 'all';
                $type = 'all';
                $department = 'all';
                $person_type_filter = 'all';
                $faculty_tag = 'all';
                if(array_key_exists(2, $arg)){
                  $letter = $arg[2];
                }
                if(array_key_exists(3, $arg)){
                  $type = $arg[3];
                }
                if(array_key_exists(4, $arg)){
                  $department = $arg[4];
                }
                if(array_key_exists(5, $arg)){
                  $person_type_filter = $arg[5];
                }
                if(array_key_exists(6, $arg)){
                  $faculty_tag = $arg[6];
                }
                $variables['letter_filter'] = $letter;
                $variables['type_filter'] = $type;
                $variables['department_filter'] = $department;
                $variables['person_type_filter'] = $person_type_filter;
                $variables['faculty_tag'] = $faculty_tag;
              }
              if($variables['view']->id() == 'person_listing_featured_titles' && $variables['view']->getDisplay()->display['id'] == 'block_1'){
                $view = \Drupal\views\Views::getView('person_search');
                $view->setDisplay('page_1');
                $variables['base_directory_path'] = $view->getPath();
                $query_combine = \Drupal::request()->get('combine');
                $current_name = strtolower($variables['row']->_entity->getName());
                $path = \Drupal::request()->getpathInfo();
                $arg  = explode('/',$path);
                // kint($arg);
                //0 - none
                //1 - directory
                //2 - letter
                //3 - type
                //4 - department
                $letter = 'all';
                $type = 'all';
                $department = 'all';
                $person_type_filter = 'all';
                $faculty_tag = 'all';
                $combine = '';
                $class = '';
                if(array_key_exists(2, $arg)){
                  $letter = $arg[2];
                }
                if(array_key_exists(3, $arg)){
                  $type = $arg[3];
                  // kint($type);
                  // kint($current_name);
                  // exit;
                  if($type == str_replace(' ', '-', $current_name)){
                    $class = 'active-filter';
                  }
                } else {
                  if($current_name == 'faculty'){
                    $class = 'active-filter';
                  }
                }
                if(array_key_exists(4, $arg)){
                  $department = $arg[4];
                }
                if(array_key_exists(5, $arg)){
                  $person_type_filter = $arg[5];
                } else {
                  $person_type_filter = 'faculty';
                }
                if(array_key_exists(6, $arg)){
                  $faculty_tag = $arg[6];
                }
                if($query_combine != ''){
                  $combine = '?combine='.$query_combine;
                }
                $variables['letter_filter'] = $letter;
                $variables['type_filter'] = $type;
                $variables['department_filter'] = $department;
                $variables['person_type_filter'] = $person_type_filter;
                $variables['faculty_tag'] = $faculty_tag;
                $variables['combine'] = $combine;
                $variables['class'] = $class;
              }
              if($variables['view']->id() == 'sightings_recent_columns' && $variables['view']->getDisplay()->display['id'] == 'block_1'){
                $entity = $variables['row']->_entity;
                $field_info = Drupal\field\Entity\FieldConfig::loadByName('node', 'sightings', 'field_featured_image');
                $image_uuid = $field_info->getSetting('default_image')['uuid'];
                $image = Drupal::service('entity.repository')->loadEntityByUuid('file', $image_uuid);
                $variables['default_uri'] = $image->getFileUri();
              }
              if($variables['view']->id() == 'automated_person_listing' && $variables['view']->getDisplay()->display['id'] == 'block_1'){
                $entity = $variables['row']->_entity;
                $field_info = Drupal\field\Entity\FieldConfig::loadByName('node', 'person', 'field_featured_image');
                $image_uuid = $field_info->getSetting('default_image')['uuid'];
                $image = Drupal::service('entity.repository')->loadEntityByUuid('file', $image_uuid);
                $variables['default_uri'] = $image->getFileUri();
              }
              if($variables['view']->id() == 'news_search' && $variables['view']->getDisplay()->display['id'] == 'page_1'){
                $entity = $variables['row']->_entity;
                $field_info = Drupal\field\Entity\FieldConfig::loadByName('node', 'news', 'field_featured_image');
                $image_uuid = $field_info->getSetting('default_image')['uuid'];
                $image = Drupal::service('entity.repository')->loadEntityByUuid('file', $image_uuid);
                $variables['default_uri'] = $image->getFileUri();
              }
              if($variables['view']->id() == 'person_search' && $variables['view']->getDisplay()->display['id'] == 'page_1'){
                $entity = $variables['row']->_entity;
                $field_info = Drupal\field\Entity\FieldConfig::loadByName('node', 'person', 'field_featured_image');
                $image_uuid = $field_info->getSetting('default_image')['uuid'];
                $image = Drupal::service('entity.repository')->loadEntityByUuid('file', $image_uuid);
                $variables['default_uri'] = $image->getFileUri();
              }
              if($variables['view']->id() == 'events_listing' && $variables['view']->getDisplay()->display['id'] == 'page_1'){
                  $entity = $variables['row']->_entity;
                  $node = $entity;
                  $start_date = $node->get('field_start_date')->getValue()[0]['value'];
                $end_date = $node->get('field_end_date')->getValue()[0]['value'];
                $start_date_dt = new DateTime($start_date, new DateTimeZone('UTC'));
                $start_date_dt->setTimeZone(new DateTimeZone('America/Chicago'));
                $end_date_dt = new DateTime($end_date, new DateTimeZone('UTC'));
                $end_date_dt->setTimeZone(new DateTimeZone('America/Chicago'));

                $variables['start_date_corrected'] = $start_date_dt->getTimestamp();
                $variables['end_date_corrected'] = $end_date_dt->getTimestamp();
              }
              if($variables['view']->id() == 'news_rss_feed' && $variables['view']->getDisplay()->display['id'] == 'page_1'){
                  $entity = $variables['row']->_entity;
                  $node = $entity;
                  $first = $node->get('field_first_name')->getValue()[0]['value'];
                  $last = $node->get('field_last_name')->getValue()[0]['value'];
                  $name = $first.' '.$last;
                  if ($name == ' ') {
                        $uid = $node->getOwnerId();
                        $user = \Drupal\user\Entity\User::load($uid);
                        $first = $user->get('field_first_name')->getValue()[0]['value'];
                        $last = $user->get('field_last_name')->getValue()[0]['value'];
                        $name = $first.' '.$last;
                        if ($name == ' ') {
                              $name = $username = $user->getDisplayName();
                        }
                  }
                  $variables['name'] = $name;

                  $unix_date = $node->getCreatedTime();
                  $item_date = date(DATE_RSS, $unix_date);
                  $variables['item_date'] = $item_date;
              }
              if($variables['view']->id() == 'rss_feed'){
                  $entity = $variables['row']->_entity;
                  $node = $entity;
                  $uid = $node->getOwnerId();
                  $user = \Drupal\user\Entity\User::load($uid);

                  $unix_date = $node->getCreatedTime();

                  $first = $user->get('field_first_name')->getValue()[0]['value'];
                  $last = $user->get('field_last_name')->getValue()[0]['value'];
                  $name = $first.' '.$last;
                  if ($variables['view']->getDisplay()->display['id'] == 'page_1') {
                        $first = $node->get('field_first_name')->getValue()[0]['value'];
                        $last = $node->get('field_last_name')->getValue()[0]['value'];
                        $name_tmp = $first.' '.$last;
                        if ($name_tmp != ' ') {
                              $name = $name_tmp;
                        }
                  } elseif($variables['view']->getDisplay()->display['id'] == 'page_2') {
                        $tid = $node->get('field_sightings_author')->getValue()[0]['target_id'];
                        $term = \Drupal::entityTypeManager()->getStorage('taxonomy_term')->load($tid);
                        // ksm($node->get('field_sightings_author')->getValue()[0]['target_id']); gives tid
                        $name_tmp = $term->getName();
                        if (!is_null($name_tmp)) {
                              $name = $name_tmp;
                        }
                        $unix_date = strtotime($node->get('field_date')->getValue()[0]['value']);

                        // ksm();
                  }
                  $body = $node->get('body')->getValue()[0];
                  if ($body['summary'] == '') {
                        $raw_body_content = strip_tags($body['value']);
                        $description = strtok($raw_body_content, "\n");
                  } else {
                        $description = $body['summary'];
                  }

                  $variables['description'] = $description;

                  // $raw_body_content = strip_tags($node->get('body')->getValue()[0]['value']);
                  // $pos = strpos($raw_body_content, '.');
                  // $str = strtok($raw_body_content, "\n");
                  // $summary_chunk = substr($raw_body_content, 0, 100);
                  // $first_sentence = substr($raw_body_content, 0, $pos+1);
                  // ksm($body);
                  /*$first = $node->get('field_first_name')->getValue()[0]['value'];
                  $last = $node->get('field_last_name')->getValue()[0]['value'];
                  $name = $first.' '.$last;
                  if ($name == ' ') {
                        $uid = $node->getOwnerId();
                        $user = \Drupal\user\Entity\User::load($uid);
                        $first = $user->get('field_first_name')->getValue()[0]['value'];
                        $last = $user->get('field_last_name')->getValue()[0]['value'];
                        $name = $first.' '.$last;
                        if ($name == ' ') {
                              $name = $username = $user->getDisplayName();
                        }
                  }
                  */
                  $variables['name'] = $name;
                  // $variables['name'] = '$name';

                  $item_date = date(DATE_RSS, $unix_date);
                  // ksm($unix_date);
                  // ksm($item_date);
                  $variables['item_date'] = $item_date;
              }
            }

            function uch_div_preprocess_views_view_unformatted(&$variables){
              /*ksm($variables);
              if($variables['view']->id() == 'person_listing_featured_titles' && $variables['view']->getDisplay()->display['id'] == 'block_1'){
                ksm('test');
              }*/
            }

            function uch_div_views_pre_view(ViewExecutable $view, $display_id, array &$args){
              if($view->id() == 'events_listing' && $display_id == 'page_1'){
                exit('test!');
              }
            }

            function uch_div_preprocess_sitemap(&$variables){
              // below code from https://drupal.stackexchange.com/questions/171686/how-can-i-programmatically-display-a-block
              $block_manager = \Drupal::service('plugin.manager.block');
              // You can hard code configuration or you load from settings.
              $config = [];
              $plugin_block = $block_manager->createInstance('system_breadcrumb_block', $config);
              // Some blocks might implement access check.
              $access_result = $plugin_block->access(\Drupal::currentUser());
              // Return empty render array if user doesn't have access.
              // $access_result can be boolean or an AccessResult class
              /*if (is_object($access_result) && $access_result->isForbidden() || is_bool($access_result) && !$access_result) {
                // You might need to add some cache tags/contexts.
                return [];
              }*/
              $block_render = $plugin_block->build();
              $variables['breadcrumbs_block'] = $block_render;
            }

            function uch_div_preprocess_block(&$variables){
              // $block = Block::load($variables['elements']['#id']);
              // if($block->id() == 'uch_div_search'){
                // $region = $block->getRegion();
                // $time = microtime(true);
                // $variables['time'] = $time;
                // kint($region);
                // kint($block);
                // kint(microtime(true));
              // }
              // kint($block->id());
            }

            function uch_div_form_alter(&$form, \Drupal\Core\Form\FormStateInterface $form_state, $form_id){
              // ksm($form_id);
              // echo'<pre>';var_dump($form_id);exit;
              // $form['#cache']['max-age'] = 0;
              // $form['#cache']['contexts'][] = 'session';
              // \Drupal::service('page_cache_kill_switch')->trigger();
              // $form_state->disableCache();
              if ($form_id == 'views_exposed_form') {
                $form['combine']['#placeholder'] = 'Search';
              }
              if ($form_id == 'contact_message_contact_us_form') {
                $form['actions']['submit']['#submit'][] = 'contact_form_submit';
                $form['subject']['widget'][0]['value']['#placeholder'] = $form['subject']['widget'][0]['value']['#title'];
                $form['message']['widget'][0]['value']['#placeholder'] = $form['message']['widget'][0]['value']['#title'];
                if($form['name']['#type'] != 'item'){
                  $form['name']['#placeholder'] = $form['name']['#title'];
                  $form['name']['#title_display'] = 'invisible';
                  $form['mail']['#placeholder'] = $form['mail']['#title'];
                  $form['mail']['#title_display'] = 'invisible';
                }
                $tmp = $form['field_i_am_a']['widget']['#options'];
                $tmp['_none'] = '- '.$form['field_i_am_a']['widget']['#title'].' -';
                $form['field_i_am_a']['widget']['#options'] = $tmp;
                // ksm($form['field_i_am_a']['widget']);
                $form['field_i_am_a']['widget']['#title_display'] = 'invisible';
                $form['subject']['#title_display'] = 'invisible';
                $form['subject']['widget'][0]['value']['#title_display'] = 'invisible';
                $form['message']['widget'][0]['value']['#title_display'] = 'invisible';
                // ksm($form['subject']['widget'][0]);

              }
              if ($form_id == 'search_block_form') {
                //  $form_state->setRequestMethod('POST');
                // $form_state->setCached(TRUE);
                // $session = \Drupal::request()->getSession();
                // $num_search_blocks = $session->get('num_search_blocks');
                // $session->set('num_search_blocks', $num_search_blocks+1);
                // $form['keys']['#id'] = $form['keys']['#title']->render().$num_search_blocks;
                // $tempstore = \Drupal::service('user.private_tempstore')->get('uch_div_utilities');
                // $num_search_blocks = $tempstore->get('num_search_blocks');
                // kint('test');exit('test');
                // $tempstore->set('num_search_blocks', $num_search_blocks+1);
                /*if($_SESSION['num_search_blocks'] == 1){
                  $num_search_blocks = 2;
                  $_SESSION['num_search_blocks'] = 0;
                } else {
                  $num_search_blocks = 1;
                  $_SESSION['num_search_blocks'] = 1;
                }*/
                // $_SESSION['uch_div']['num_search_bloc  ks']
                // $num_search_blocks = $_SESSION['uch_div']['num_search_blocks'];
                // $_SESSION['uch_div']['num_search_blocks'] = $num_search_blocks+1;
                // $session = \Drupal::request()->getSession();
                // $num_search_blocks = $session->get('num_search_blocks');
                // $session->set('num_search_blocks', $num_search_blocks+1);
                // $time = microtime(true);
                // $form['keys']['#id'] = $form['keys']['#title']->render().$num_search_blocks;
                // $form['#cache'] = ['max-age' => 0];
                // kint($form['keys']['#title']->render());exit;
              }
              if($form_id == "search_form"){
                $field_value = $form['basic']['keys']['#default_value'];
                $word = 'site:divinity.uchicago.edu';
                if(!empty($field_value)){
                  if(strpos($field_value, $word) == false){
                    $doamin = 'site:divinity.uchicago.edu';
                    $search_value = trim($field_value);
                    $explode = explode(" ",$search_value);
                    $formate = implode('+', $explode);
                    $redirect_path = "/search/cse";
                    $response = new RedirectResponse($redirect_path."?keys=".$formate.'+'.$doamin);
                    $response->send();
                  }
                  else{
                    $default_value = str_replace("site:divinity.uchicago.edu","",$field_value);
                    $form['basic']['keys']['#default_value'] = $default_value; 
                    $form['#submit'][] = 'custom_search_form_submit';
                  }
                }
                // echo '<pre>'; print_r($form);exit;
              }

              // $form['#cache']['max-age'] = 0;
              // $form['#cache']['contexts'][] = 'session';
              // \Drupal::service('page_cache_kill_switch')->trigger();
              // $form_state->disableCache();

            }

            /**
             * Implements hook_theme_suggestions_HOOK_alter().
             *
             * This hook for adding a custom suggestion page template.
             *
             * Place this function in your THEME_NAME.theme file.
             * then you can use the template page--[content-type].html.twig
             *
             * Tested and worked with Drupal Version 8.3.x
             */
            function uch_div_theme_suggestions_page_alter(array &$suggestions, array $variables) {
              if ($node = \Drupal::routeMatch()->getParameter('node')) {
                $content_type = $node->bundle();
                $suggestions[] = 'page__'.$content_type;
              }
              if (!is_null(Drupal::requestStack()->getCurrentRequest()->attributes->get('exception'))) {
                $status_code = Drupal::requestStack()->getCurrentRequest()->attributes->get('exception')->getStatusCode();
                $suggestions[] = 'page__' . (string) $status_code;
              }

            }

            function contact_form_submit($form, FormStateInterface $form_state){
              $current_uri = \Drupal::request()->getRequestUri();
              $redirect_path =  $current_uri;
              $url = url::fromUserInput($redirect_path);
              $form_state->setRedirectUrl($url);
            }

            function custom_search_form_submit($form, FormStateInterface $form_state){

              $values = $form_state->getValues();
              $doamin = 'site%3Adivinity.uchicago.edu';
              $search_value = trim($values['keys']);
              $explode = explode(" ",$search_value);
              $formate = implode('+', $explode);
              $redirect_path = "/search/cse";
              $response = new RedirectResponse($redirect_path."?keys=".$formate.'+'.$doamin);
              $response->send();
            }

            // function uch_div_form_search_block_alter(&$form, &$form_state) {
            //   $form['actions']['submit']['#value'] = 'sdfsfsfsfsf';
            //   echo '<pre>'; print_r( $form['actions']['submit']['#value']);exit;
            // }

            function sort_taxonomy_term_objs($a, $b){
              if ($a->getName() == $b->getName()) {
                  return 0;
              }
              return ($a->getName() < $b->getName()) ? -1 : 1;
            }

            function sort_taxonomy_term_objs_alt($a, $b){
              if ($a->name == $b->name) {
                  return 0;
              }
              return ($a->name < $b->name) ? -1 : 1;
            }
==end==
