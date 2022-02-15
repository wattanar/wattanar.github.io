---
title: Custom Column in List Table
tags:
  - Wordpress
  - Custom Fields
published: true
---

Custom field is powerful to wordpress development this post will show the example how to display custom field in list table page.

<!--more-->

In this example i'll create post type **Movie** to store content and add custom field name **studio** now you need 1 filter and 1 hook like code below

```php
function movie_edit_columns($columns){
  $columns = array(
    "cb" => "<input type=\"checkbox\" />",
    "title" => "Movie Title",
    "studio" => "Studio"
  );

  return $columns;
}
add_filter("manage_edit-movie_columns", "movie_edit_columns");
```

Define column to display with filter (change **movie** in **add_filter** parameter to post type you created)

```php
function lotto_custom_columns($column) {
  global $post;

  switch ($column) {
    case "studio":
        the_field('studio', $post->ID );
        break;

    }
}
add_action("manage_movie_posts_custom_column",  "movie_custom_columns");
```

Use hook to display data (change **movie** in **add_action** parameter to post type you created)

-- Done --
