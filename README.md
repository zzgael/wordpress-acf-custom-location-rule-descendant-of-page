Wordpress ACF Custom location rule : Descendant of Page
=====================================================

##About
[ACF](http://www.advancedcustomfields.com/) is a wonderful plugin for Wordpress to get the most of the custom fields.
Basic location rules are not always sufficient to target the right page being edited.
Basic rule "Parent page" only targets the descending pages of first level, meaning the children's pages will not be targeted.
This [custom rule](http://www.advancedcustomfields.com/resources/tutorials/custom-location-rules/) will target all pages from all levels descending from the specified page. 

##Requirements
Wordpress 3.8+
Advanced Custom Fields 3.5.7+

##Setup
Just copy/paste the following code inside your theme's functions.php and the custom rule will appear in your ACF form.

```php
add_filter('acf/location/rule_types', 'acf_location_rules_types');
function acf_location_rules_types( $choices ) {
    $choices['Page']['childof'] = 'Descending from the page';
    return $choices;
}
add_filter('acf/location/rule_values/childof', 'acf_location_rules_values_childof');
function acf_location_rules_values_childof( $choices ) {
    foreach(get_pages() as $p)
        $choices[ $p->ID ] = $p->post_title;
    return $choices;
}
add_filter('acf/location/rule_match/childof', 'acf_location_rules_match_childof', 10, 3);
function acf_location_rules_match_childof( $match, $rule, $options ) {
    global $post;
    $match = false;
    $selectedPageID = (int) $rule['value'];
 
    foreach(get_post_ancestors( $post->ID ) as $ancestor) {
      if($ancestor == $selectedPageID)
        $match = true;
    }
    if($rule['operator'] == "==")
      return $match;
    elseif($rule['operator'] == "!=")
      return !$match;
}
```
