---
id: 107
title: 'WordPress Widget: progress bar'
date: 2013-10-16T00:53:24+00:00
author: Etienne
layout: post
guid: http://geeksperiment.com/?p=107
permalink: /wordpress-widget-progress-bar/
image: /wp-content/uploads/2013/10/wip_thumb.png
categories:
  - Projects
tags:
  - javascript
  - php
  - projects
  - tutorial
  - widget
  - WordPress
---
I&rsquo;m still getting to know WordPress features and so far I&rsquo;m happy on how it works as well as a little bit frustrated. WordPress is perfect if you want an out-of-the-box solution for blogging, but it can be not so nice to tweak and play with&#8230; Thankfully the WP community is huge and you can find examples and documentation on almost every problem.<!--more-->

I&rsquo;m a versatile person, which explains the non consistence of the topics of this blog, but I also like to be organized (at least I try)&#8230;So I wanted a small thingy to see how I&rsquo;m progressing on my current projects/topics of interests.

&nbsp;

# What we want to achieve

The idea is to display a widget on the right sidebar of the theme, that contains the _name_ of a task and its _progression_. In WordPress, widgets and plugins aren&rsquo;t exactly the same:

  * Plugins are to WordPress what extensions are to Chrome, which means that they can be quite a lot of things, but generaly only the admin can modify their settings
  * Widgets are small visual components that allow you to quickly display information. Generaly widgets are based on plugins (except for WP default widgets)

I decided to take a full widget approach, so I will not use plugin settings page nor any other plugin specific functions. Everything will be done within the widget administration page.

# General overview

[<img class="aligncenter wp-image-257 size-full" src="http://geeksperiment.com/wp-content/uploads/2013/10/widget_arch-e13811485255672.png" alt="" width="599" height="233" srcset="http://geeksperiment.com/wp-content/uploads/2013/10/widget_arch-e13811485255672.png 599w" sizes="(max-width: 599px) 100vw, 599px" />](http://geeksperiment.com/wp-content/uploads/2013/10/widget_arch-e13811485255672.png)

Things we need to create:

1) The administration form, available in the Appearance > Widget menu of the administrator dashboard

2) Retrieve settings of the widget from the database

3) Save settings

4) Retrieve widget settings

5) Display the plugin

# Creating the widget

Useful resources: [WordPress Widget API](http://codex.wordpress.org/Widgets_API). If you&rsquo;re not familiar with the way that WordPress works, it&rsquo;a very good place to start.

The first step to create a new WordPress widget is to add a .php file in your wp-content/plugins ftp directory. My widget name is &laquo;&nbsp;workinprogress&nbsp;&raquo;, don&rsquo;t forget to replace that by &laquo;&nbsp;YourOwnWidgetName&nbsp;&raquo;. You have to inssert a mandatory header in this file. Sould look like this:

```php
<?php
/* Plugin Name: Work in progress
Plugin URI: geeksperiment.com
Description: Adding progress bars to a sidebar widget
Version: 1.0
Author: edg
Author URI: geeksperiment.com  */
?>
```

You can also add copyright informations, you can find examples of what it looks like.

Ok, lets start by creating the widget class and then register the widget with WordPress:

```php
/* Register widget with WordPress.	*/

function __construct() {

   parent::__construct(

   'workinprogress', // Base ID

   'WorkInProgress', // Name

    array( 'description' => __( 'Adding progress bars to a sidebar widget', 'text_domain' ), ) // Args

    );}

}//end of WIP class

//This adds your widget to your template	

// register widget	

function register_WorkInProgress() {

	register_widget( 'WorkInProgress' );

	add_action( 'widgets_init', 'register_WorkInProgress' );

}

```

## 2 .Getting settings (back-end side)

The form is created, the inputs can be filled, but won&rsquo;t be saved nor used by the widget just now. To get previously saved data, we complete the form function like so:

```php
* @see WP_Widget::form()	 

* @param array $instance Previously saved values from database.	 */		

	public function form( $instance ) {

	//allows admin to change widget name			

		if ( isset( $instance['title'])) {

			$title = $instance[ 'title' ];

			}			

		else {

			$title = __( 'Work in Progress', 'text_domain' );

			}		

//retrieves the previsously created tasks		

		$taskName = isset ($instance['taskName']) ? $instance['taskName'] : 'None';

		$taskProgress = isset ($instance['taskProgress']) ? $instance['taskProgress'] : 0;

//HTML content of the back end widget

// .............</code></pre>
  
 ```

For now the widget (after been activated via the &laquo;&nbsp;Installed plugins&nbsp;&raquo; menu) should look like this:

[<img class="aligncenter size-large wp-image-271" src="http://geeksperiment.com/wp-content/uploads/2013/10/widget_screen_big2-1024x393.png" alt="widget_screen_big" width="599" height="230" srcset="http://geeksperiment.com/wp-content/uploads/2013/10/widget_screen_big2-1024x393.png 1024w, http://geeksperiment.com/wp-content/uploads/2013/10/widget_screen_big2-1140x440.png 1140w, http://geeksperiment.com/wp-content/uploads/2013/10/widget_screen_big2.png 1352w" sizes="(max-width: 599px) 100vw, 599px" />](http://geeksperiment.com/wp-content/uploads/2013/10/widget_screen_big22.png)

## 3. Saving settings

After getting and displaying the settings of the task, we implement the modification and update. Add an update function to the WorkInProgress class:

```php
 * @see WP_Widget::update()		 

 * @param array $new_instance Values just sent to be saved.

 * @param array $old_instance Previously saved values from database.

 * @return array Updated safe values to be saved.*/

public function update( $new_instance, $old_instance ) {

	$instance = $old_instance;

	$instance['title'] = (!empty($new_instance['title']))? strip_tags( $new_instance['title'] ) : $instance['title'];

	$instance['taskName'] = (!empty( $new_instance['taskName']))? strip_tags($new_instance['taskName'] ): $instance['taskName'] ;

	$instance['taskProgress'] = (!empty( $new_instance['taskProgress'])) ? strip_tags($new_instance['taskProgress'] ): $instance['taskProgress'] ;

	return $new_instance;		

}
```

The back-end is completed, the widget can now by managed by an admin.

## 4. & 5. Actually showing the widget

Add one last function to the class. The function uses the current values of the params of the current $instance:

```php
* The following is the CORE of your widget

* @see WP_Widget::widget()

* @param array $args     Widget arguments.

* @param array $instance Saved values from database.*/

public function widget( $args, $instance ) {

//definition of the arguments (variables) of the widget

	$title = apply_filters( 'widget_title', $instance['title'] );

	$taskName = isset ($instance['taskName']) ? $instance['taskName'] : 'Not much';

	$taskProgress = isset ($instance['taskProgress']) ? $instance['taskProgress'] : '100';

	echo $args['before_widget'];

	if (!empty($title )) {

		echo $args['before_title'] . $title . $args['after_title']; //displays the title

	}

	echo '</pre>

<h3>'.$taskName.'</h3>

<div class="pb-wrapper"></div>

';

echo $args['after_widget'];

}
```
That&rsquo;s it, the widget is done&#8230;but there is still nothing on the page&#8230; I choosed to display a progress bar as a span included in a div, with the span&rsquo;s width reprensenting the progress value. A few reasons for that: I wanted something simple to begin with, so I didn&rsquo;t want to bother including all jQuery files and I wanted to use an image to make fun stuff (and see if my css wasn&rsquo;t too rusty).

&nbsp;

To actually see the widget on the page you just have to add css properties in your general styles.css file or if you prefer in a widget.css file located in the same path as the php file.

After doing so, add this line to the register_WorkInProgress function:

```php
  wp_enqueue_style( 'wip', plugins_url( "widget.css", __FILE__ ), array(), '1' );}
```

Any feedback is welcomed! 🙂