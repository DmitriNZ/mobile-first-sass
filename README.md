#Mobile First Approach with SASS

Anyone who has used the Mobile First approach to build a website has discovered, sooner or later, that IE prior to 9 serve the mobile version.
This is because IE < 9 ignores everything inside the @media-query blocks.

There are already a number of solutions for this problem, such as:

[Jake Archibald](http://jakearchibald.github.io/sass-ie/) <br>
[Jeremy Keith](http://adactio.com/journal/4494/) <br>
[Respond.js](https://github.com/scottjehl/Respond)

I came up with my own solution which is based on what Jake suggests.

The problem I found with Jake's solution is that you need to have a mixin for min-width and a mixin for max-width.
I wanted one mixin where I could define my media query however I wanted.

So here's my solution:

##File Structure:
	index.html

	all.scss
	css/oldie.scss
	css/_utils.scss
	css/_global.scss

## index.html:

	<!--[if lte IE 8]>
	    <link rel="stylesheet" href="path/oldie.css">
	<![endif]-->
	<!--[if gt IE 8]><!-->
	    <link rel="stylesheet" href="path/all.css">
	<!--<![endif]-->


## all.scss

	@import '_utils';
	@import '_global';


## oldie.scss

	$old-ie: true;
	$mq-ie: desk;

	@import 'all';


## _utils.scss

	// Here we define that by default our users are not using an old browser
	// The use of !default allow us to override this variable before this point
	$old-ie: false !default;

	// Here we define some breakpoints - This can be whatever you want/need
    $desk-start: 1000px !default;


    // This is where the magic happens
    // Our media-query mixin takes one argument, which is the media-query type
    @mixin media-query($media-query) {

    	// If $old-ie is false, carry on...
        @if $old-ie == false {

        	// Here we check for the argument we passed when we included the media query
        	// If our argument is 'mobile', then go ahead and create our media query
        	// The media query can be anything you want.
            @if $media-query == mobile {
                @media only screen and (max-width: $desk-start) {
                	@content;
                }
            }

            // If our argument is 'desk', then go ahead and create our media query for desktop
            @if $media-query == desk {
                @media only screen and (min-width: $desk-start) {
                	@content;
                }

            }
            
            // If our argument is 'print', we can also set a print media query. win!
            @if $media-query == print {
            	@media print {
            		@content;
            	}
            }
        }

        // Now, if $mq-ie ($mq-ie is defined in oldie.scss) is the same as what we passed
        // Then go ahead and use that block of code.
        // On all.scss we defined $mq-ie as equals to 'desk', so basically what this is doing is
        // Checking for every include of the media-query mixin that uses 'desk' as the argument
        // And using this code as default
        @else if $mq-ie == $media-query {
        	@content;
        }
    }

## _global.scss (example)
	* { margin: 0; padding: 0; }

	body {
		background: #737E8C;
		color: #ffffff;
		font-size: 20px;
		line-height: 26px;
		padding: 60px 0 0;
	
		// This is an example of how to use the media-query mixin
		// Styles in this declaration will only show on desktop
		@include media-query(desk) {
			background: #8C7D73;
		}
	
	}
	
	a {
		color: #333333;
		font-weight: bold;
	}
	
	p {
		display: block;
		clear: both;
		padding: 60px 15%;
		text-align: center;
	}
	
	.code-sample {
		margin: 3%;
	
		@include media-query(desk) {
			float: left;
			width: 47%;
			margin: 0 1.5%;
		}
	}
	
	pre {
		background: #999999;
		font-size: 16px;
		padding: 0 0 0 20px;
	}

####I also created a [Demo page](http://www.pedroduarte.me/mobile-first-sass/) so you can see it in action.
