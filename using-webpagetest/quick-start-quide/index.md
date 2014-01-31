Quick Start Guide
At its core, WebPagetest is used for measuring and analyzing the performance of web pages.  There are a lot of options that may seem intimidating at first but doing quick testing is pretty simple.  This guide will walk you through submitting a test and interpreting the results.

Contents
1 Running a Performance Test
1.1 Enter The Page URL:
1.2 Select a Location:
1.3 Select a Browser:
1.4 Submit the Test
2 Interpreting the Results
2.1 Optimization Grades:
2.1.1 Keep-alive Enabled:
2.1.2 Compress Text:
2.1.3 Compress Images:
2.1.4 Cache Static Content:
2.1.5 Combine JS/CSS Files:
2.1.6 Use of CDN:
2.2 High-level Metrics:
2.2.1 Repeat View:
2.2.2 Document Complete:
2.2.3 Fully Loaded:
2.2.4 Load Time:
2.2.5 First Byte:
2.2.6 Start Render:
2.2.7 DOM Elements:
2.2.8 The DOM Elements metric is the count of the DOM elements on the tested page as measured at the end of the test.
2.2.9 Requests:
2.2.10 Bytes In:
Running a Performance Test

Enter The Page URL:

The first thing you need to do is decide on a page to test.  Most people start with the main page for their site (but don't neglect the other pages people visit either).  Once you have decided on a page you'd like to test you go to WebPagetest and give it the URL for the page you want tested:


Select a Location:

Next you should decide where you want the test to be run from.  WebPagetest has test machines that are physically located around the world and you should test from a location that is close to where your users are visiting from.  You can either pick a location from the list or if you click on the Change button you can pick a location from a map view (just click on a balloon to select a location and then click ok).  If you hold the pointer over the bubbles they will display a message telling you where the location is:


Select a Browser:

Finally, you need to decide what browser to use for the test.  Different locations support different browsers so if a given location doesn't have the browser you are looking for, you can try a different location.  The Dulles, VA USA location supports all of the browsers that WebPagetest works with (Internet Explorer 6, 7, 8 and 9).  Ignore the "dynaTrace" browsers for now, those are used for more advanced analysis.  We usually recommend using IE7 for initial testing because it's pretty much the worst-case scenario and makes it easier to see a lot of issues so if you're not sure what browser to start with just use IE7.


Submit the Test

After everything is configured the way you like, click on the   button and your request will be sent to the test location for testing.  The test may take a while to run depending on how many tests are ahead of yours (figure at least one minute for each test ahead of yours but it's not unusual for it to be even longer than that).  Once the test is complete you will be presented with the results.

Interpreting the Results

The results screen can be a bit intimidating the first time you see it because there is a lot of information to consume but there are some key pieces of information to look at first.

Optimization Grades:

At the top of the results page are a set of grades for the most critical performance optimizations.  These cover the basic optimizations that apply to just about all sites and anything that isn't an A or a B justifies some serious scrutiny and investigation.



Keep-alive Enabled:

Each request for a piece of content on the page (image, javascript, css, flash, etc) needs to be made over a connection to the web server.  Setting up new connections can take a lot of time so it is best to re-use connections when you can and keep-alive is the way that is done.  They are enabled by default on most configurations and are part of the HTTP 1.1 standard but there are times when they will be broken (sometimes unintentionally).  Enabling keep-alives is usually just a configuration change on the server and does not require any changes to the page itself and can usually reduce the time to load a page by 40-50%.
Compress Text:

Just about everything on a page that isn't an image or video is text of some kind (html, javascript, css).  Text compresses really well and HTTP provides a way to transfer the files in compressed form.  Enabling compression for text resources is usually just a server configuration change without requiring any changes to the page itself and can both improve the performance and reduce the costs of serving the content (by reducing the amount of data transmitted).  Since text resources are usually downloaded at the beginning of the page (javascript and css), delivering them faster has a much larger impact on the user experience than excessive bytes on images or other content.
Compress Images:

The image compression check just looks at photo images (JPEG files) and makes sure the quality isn't set too high.  JPEG images can usually be compressed pretty substantially without any noticeable reduction in visual quality.  We use a standard of compressing the images at a quality level of "50" in Photoshop's "Save for Web" mode but generally you should compress them as much as you can before they start to look bad.  It's also not uncommon for other data to be included in photos, particularly if they came from a digital camera (information about the camera, lens, location, even thumbnail images) and all of that should be removed from images before being published to a web page.
Cache Static Content:

Static Content are the pieces of content on your page that don't change frequently (images, javascript, css).  You can configure them so that the user's browser will store them in a cache so if the user comes back to the page (or visits another page that uses the same file) they can just use the copy they already have instead of requesting the file from the web server.  Successfully caching static content in the user's browser can significantly improve the performance of a repeat view visit (up to 80+% depending on the page) and reduces the load on the web servers.  It can sometimes be tricky to implement caching without breaking a page though so don't just enable it blindly (you need to be able to change the file name for any files that you expect to change).
Combine JS/CSS Files:

Improving performance usually means reducing the number of requests for content and one of the easiest (and most significant) ways to do that is to reduce the number of individual css and javascript files that load at the beginning of the page (in the <head> which blocks the page from displaying to the user).  An easy way to achieve this is to just merge all of the javascript code into a single file and the css into a single file so you have one of each (with the css preferably being loaded before the javascript).  This can have a very substantial impact on the user experience by reducing the amount of time before they see something appear on the screen.
Use of CDN:

Each request for a piece of content to the web server has to travel from the user's browser all the way to the server and back.  As you get further and further from the server this can become a significant amount of time (which adds up quickly as there are more requests on the page).  Ultimately the time it takes is limited by the speed of light so there's not much you can do except to move your server closer to the users.  That is exactly what a Content Distribution Network (CDN) does.  They have servers all over the world that are close to users and they can serve a web site's static content from servers close to users.  The only case where it doesn't make sense to use a CDN is if all of the users for a web site are close to the web server already (like for a community web site).
High-level Metrics:

The data table at the top of the results page gives you some high-level information about the page that was loaded:



First View:
The First View row is a test that was done with a browser that had its cache and cookies cleared out and represents what a first-time visitor to the page will experience.
Repeat View:

The Repeat View row is a test that was done immediately after the First View test without clearing out anything.  The browser window is closed after the First View test and then a new browser is launched to do the Repeat View test.  The Repeat View test represents what someone will see if they are coming back to the page some time after visiting it the first time (and is a best-case scenario since the test is re-visiting the page immediately).
Document Complete:

The metrics grouped together under the Document Complete heading are the metrics collected up until the browser considered the page loaded (onLoad event for those familiar with the javascript events).  This usually happens after all of the images content have loaded but may not include content that is triggered by javascript execution.
Fully Loaded:

The metrics grouped together under the Fully Loaded heading are the metrics collected up until there was 2 seconds of no network activity after Document Complete.  This will usually include any activity that is triggered by javascript after the main page loads.
Load Time:

The Load Time is the time from when the user started navigating to the page until the Document Complete event (usually when all of the page content has loaded).
First Byte:

The First Byte time is the time from when the user started navigating to the page until the first bit of the server response arrived.  The bulk of this time is usually referred to the "back-end time" and is the amount of time the server spent building the page for the user.
Start Render:

The Start Render time is the first point in time that something was displayed to the screen.  Before this point in time the user was staring at a blank page.  This does not necessarily mean the user saw the page content, it could just be something as simple as a background color but it is the first indication of something happening for the user.
DOM Elements:

The DOM Elements metric is the count of the DOM elements on the tested page as measured at the end of the test.


Requests:

This is the number of requests that had to be made by the browser for pieces of content on the page (images, javascript, css, etc).
Bytes In:

This is the amount of data that the browser had to download in order to load the page.  It is also commonly referred to as the "Page Size".