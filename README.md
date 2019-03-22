# Require-Featured-Image-while-adding-new-post-from-admin-panel

### About ###

When client will add post from the wp-admin panel, to make sure not to ruining the flow and presentation of what you’ve created. 
This snippet will require the author to select a featured image before the post can be published.


### Usage ###

Add this code snippet in your custom theme.

See the `functions.php` file, or use this below:

```php
<?php
add_action('save_post', 'wpds_check_thumbnail');
    add_action('admin_notices', 'wpds_thumbnail_error');

    function wpds_check_thumbnail($post_id) {

        // change to any custom post type
        if(get_post_type($post_id) != 'post')
        return;

        if ( !has_post_thumbnail( $post_id ) ) {
            // set a transient to show the users an admin message
            set_transient( &quot;has_post_thumbnail&quot;, &quot;no&quot; );

            // unhook this function so it doesn't loop infinitely
            remove_action('save_post', 'wpds_check_thumbnail');

            // update the post set it to draft
            wp_update_post(array('ID' =&gt; $post_id, 'post_status' =&gt; 'draft'));

            add_action('save_post', 'wpds_check_thumbnail');

        } else {

            delete_transient( &quot;has_post_thumbnail&quot; );
        }
    }

    function wpds_thumbnail_error()
    {
        // check if the transient is set, and display the error message
        if ( get_transient( &quot;has_post_thumbnail&quot; ) == &quot;no&quot; ) {
            echo &quot;
            &lt;div id=&quot;message&quot; class=&quot;error&quot;&gt;

            You must select Featured Image. Your Post is saved but it can not be published.

            &lt;/div&gt;
            &quot;
            delete_transient( &quot;has_post_thumbnail&quot; );
        }

    }
    ```
