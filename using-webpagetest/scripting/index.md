# Scripting

Pagetest has a scripting capability that lets you automate a multi-step test (for example, logging into a site or sending an e-mail message). The scripts are contained in files where a single file constitutes a browser session (the browser will be closed after the script is complete). The files are plain text files and can be edited by any text editor.
You can test a script in the desktop version by selecting "Run Script" from the File menu.
Each line of the script file contains a command and any necessary parameters and is tab-delimited (i.e. the command is followed by a tab then the first parameter then a tab and the second parameter etc until the end of the line). The number of parameters and what they control is dependent on the command.

Blank lines and lines beginning with // are ignored so you can embed comments in a script.
Script commands that operate on a DOM element identify the DOM element with a format of attribute=value where the attribute identifies a unique attribute of the DOM element that you want to act on. For example, if you are filling out a form and the element you want to populate looks like this:
<input type="text" class="tabInputFields" id="lgnId1" value="" tabindex="1" maxlength="97" name="loginId"/>
you could identify it as id=lgnId1 , name=loginId or tabindex=1

For form fields it is usually best to use the name attribute when it is available since that is what will be uploaded to the server but any attribute is fair game. The class attribute is special and is referenced as className instead of class. In addition to the DOM element attribute matching, there are two special attributes that you can use to match on the contents. innerText and innerHtml both of which will match the contents of the DOM element instead of it's attributes.
For example:
<div dojoattachpoint="containerNode" class="label">Delete</div>
Can be identified by innerText=Delete. The matching is case sensitive and matches the full string.

Hosted Scripting (WebPagetest)

The hosted version of pagetest supports executing uploaded scripts with a few limitations:
The hosted scripts can only have one step that produce data (see below for examples on how to suppress results for intermediate steps)
None of the commands that use external files are allowed (loadFile, loadVariables, fileDialog)
In order to suppress intermediate steps you need to make sure that data logging is disabled for the steps up to the one you want to record. For example:
logData    0

// put any urls you want to navigate
navigate    www.aol.com
navigate    news.aol.com

logData    1

// this step will get recorded
navigate    news.aol.com/world
The script above will navigate to the main aol portal, then to the news page and then finally to the world-news specific page (logging results for just the world news page). This lets you test the impact of a given path to a site on it's performance (shared css and js caching for example).
Another significant use case is if you want to test a site that requires authentication. Here is what an authentication script would look like:
logData	0

// bring up the login screen
navigate	http://webmail.aol.com

logData	1

// log in
setValue	name=loginId	someuser@aol.com
setValue	name=password	somepassword
submitForm	name=AOLLoginForm
You won't get a lot of feedback as to why a script failed so make sure to test your scripts locally with the desktop version of pagetest (file->Run Script) before uploading them for hosted testing. You can probably get away with a simple navigation path without testing but not the fancier scripts.

Command Reference

Navigation/DOM Interaction
navigate
Navigates the browser to the provided URL and waits for it to complete.
Browser Support: IE, Chrome, Firefox, Safari
usage: navigate	<url>
example: navigate	http://webmail.aol.com

<url> - URL to provide the browser for navigation (same as you would enter into the address bar)
click
Triggers a click event for the identified DOM element. This version does not have an implied wait and the script will continue running after the event is submitted (see clickAndWait for the wait version).
Browser Support: IE, Chrome, Firefox
usage: click	<attribute=value>
example: click	title=Delete (del)

<attribute'value> - DOM element to click on
clickAndWait
Triggers a click event for the identified DOM element and subsequently waits for browser activity to complete.
Browser Support: IE, Chrome, Firefox
usage: clickAndWait	<attribute=value>
example: clickAndWait	innerText=Send

<attribute'value> - DOM element to click on
selectValue
Selects a value from a dropdown list of the given DOM element.
Browser Support: IE
usage: selectValue	<attribute=value>	<value>
example: selectValue	id=country	usa

<attribute=value> - DOM element to select the value of
<value> - value to use
sendClick / sendClickAndWait
Creates a javascript OnClick event and sends it to the indicated element.
Browser Support: IE
usage: sendClickAndWait	<attribute=value>
example: sendClickAndWait	innerText=Send

<attribute=value> - DOM element to send the click event to
sendKeyDown / sendKeyUp / sendKeyPress (AndWait)
Creates a javascript keyboard event (OnKeyDown, OnKeyUp, OnKeyPress) and sends it to the indicated element.
Browser Support: IE
usage: sendKeyDownAndWait	<attribute=value>    <key>
example: sendKeyDownAndWait	name=user    x

<attribute=value> - DOM element to send the click event to
<key> - Key command to send (special values are ENTER, DEL, DELETE, BACKSPACE, TAB, ESCAPE, PAGEUP, PAGEDOWN)
setInnerHTML
Sets the innerHTML of the given DOM element to the provided value. This is usually used for filling in something like an editable HTML panel (like the message body in webmail). Use this if you want to include HTML formatting.
Browser Support: IE, Chrome, Firefox
usage: setInnerHTML	<attribute=value>	<value>
example: setInnerHTML	contentEditable'true	%MSG%


<attribute=value> - DOM element to set the innerText of
<value> - value to use
setInnerText
Sets the innerText of the given DOM element to the provided value. This is usually used for filling in something like an editable HTML panel (like the message body in webmail). Use this if you don't want to include any HTML formatting.
Browser Support: IE, Chrome, Firefox
usage: setInnerText	<attribute=value>	<value>
example: setInnerText	contentEditable'true	%MSG%


<attribute=value> - DOM element to set the innerText of
<value> - value to use
setValue
Sets the value attribute of the given DOM element to the provided value. This is usually used for filling in text elements on a page (forms or otherwise). Currently only "input" and "textArea" element types are supported.
Browser Support: IE, Chrome, Firefox
usage: setValue	<attribute=value>	<value>
example: setValue	name'loginId	userName

<attribute=value> - DOM element to set the value of
<value> - value to use
submitForm
Triggers a submit event for the identified form.
Browser Support: IE, Chrome, Firefox
usage: submitForm	<attribute=value>
example: submitForm	name'AOLLoginForm

<attribute=value> - Form element to submit
exec
Executes javascript.
Browser Support: IE, Chrome, Firefox
usage: exec	<javascript code>
example: exec	window.setInterval('window.scrollBy(0,600)', 1000);
execAndWait
Executes javascript and waits for the browser to complete any activity generated from the action.
Browser Support: IE, Chrome, Firefox
usage: execAndWait	<javascript code>
example: execAndWait	window.setInterval('window.scrollBy(0,600)', 1000);
fileDialog
Clicks on the identified button to act on a local file, specifies the file provided and then closes the local file browsing dialog. This is primarily used for uploading local files (attaching to mail, uploading pictures, etc).
Browser Support: IE
usage: fileDialog	<attribute=value>	<file>
example: fileDialog	type=file	msg.gif

<attribute=value> - DOM element to click on.
<file> - file to attach/upload.
Unless a page has more that one button for attaching files (highly unlikely) you should be able to always use type'file for the DOM element.
The path search order for the file is to first check the same directory the script is running in, then the directory that pagetest was installed to and then finally try the file as an absolute path.
End Conditions
requiredRequest
Forces the test to wait for the given request before completing.
Browser Support: IE
usage: requiredRequest	<url substring>
example: requiredRequest	adsWrapper.js

<url substring> - Part of the request URL to match (case-sensitive substring match)
You can specify multiple requests by repeating the command for each new request:
requiredRequest	www.aol.com
requiredRequest	www.google.com
navigate	www.google.com
setABM
Sets the "Activity Based Measurement" mode. The valid values are:
0 - Disabled (Web 1.0 - Measure based off of document complete) 1 - Enabled (Web 2.0 - Measure until activity stops) 2 - Automatic (Measure until activity stops but record if the Document Complete or Fully Loaded numbers should be used)
The default if not specified in the script is 2 (Automatic)
Browser Support: IE
usage: setABM	<mode>
example: setABM	0

<mode> - ABM mode to use
setActivityTimeout
Overrides the timeout value for the time after the last network activity before a test is considered complete (defaults to 2000 which is 2 seconds).
Browser Support: IE, Chrome, Firefox, Safari
usage: setActivityTimeout	<timeout in milliseconds>
example: setActivityTimeout	5000

<timeout in milliseconds> - Number of milliseconds after the last network activity (after onload) before calling a test done.
setDOMElement
Sets the attribute of a DOM element that is required for the next event to complete successfully. The measurement will not complete until the DOM element appears or the action times out. Additionally, the time when the DOM element appears will be recorded in the results.
Browser Support: IE, Chrome, Firefox
usage: setDOMElement	<attribute=value>
example: setDOMElement	name'loginId

<attribute=value> - DOM element to wait for
setDOMRequest
Sets the substring of a Http Request that is required for the next event to complete successfully. The measurement will not complete until that particular Http Request finishes or the action times out. Additionally, the time when the Http Request happens will be recorded in the results.
Browser Support: IE
usage: setDOMRequest	<target>	<value>
example: setDOMRequest	www.google.com/images

<target> - Http Request to wait for
<value> - It is optional. It can be TTFB or START. If it is not present, the time till the END of the Http Request will be recorded in the results 
setTimeout
Overrides the timeout value for the individual script steps.
Browser Support: IE, Chrome, Firefox, Safari
usage: setTimeout	<timeout in seconds>
example: setTimeout	240

<timeout in seconds> - Number of seconds to allow for the navigation/step to complete.
waitForComplete
Waits for measurement of the current activity to finish. This is usually not needed since most of the commands that initiate activity either implicitly wait or have a version of the command that includes a wait. This should only be used if none of those are suitable for the action being measured.
Browser Support: IE
usage: waitforComplete
example: waitforComplete
waitForJSDone
Waits for code on the page to explicitly indicate it is finished by calling window.webpagetest.done()
Browser Support: IE
usage: waitForJSDone
example: waitForJSDone
The javascript on the page would look like:
if( window.webpagetest )
    window.webpagetest.done();

Request Manipulation
block
Blocks individual requests from loading (useful for blocking content like ads). The command matches the list of things to block against the full url of each request (including host name).
Browser Support: IE, Chrome, Firefox
usage: block    <block strings>
example: block    adswrapper.js addthis.com

<block strings> - space-delimited list of substrings to block
setCookie
Stores a browser cookie to be used while navigating.
Browser Support: IE, Chrome, Firefox
usage: setCookie	<path>	<value>
example: setCookie	http://www.aol.com	zip=20166
example: setCookie	http://www.aol.com	TestData = Test; expires = Sat,01-Jan-2000 00:00:00 GMT

<path> - Path where the cookie should be used/stored
<value> - Cookie value (if expiration information isn't included it will be stored as a session cookie)
setDns
Allows for overriding the IP address to be used for a host name. The original name will still be looked up so that accurate timings will be recorded but the address will be replaced by the one specified.
Browser Support: IE, Chrome, Firefox, Safari
usage: setDns	<host name>	<IP Address>
example: setDns	www.aol.com	127.0.0.1

<host name> - Host name for the DNS entry to override
<IP Address> - IP Address for the host name to resolve to
setDNSName
Allows for overriding a host name (creating a fake CNAME).
Browser Support: IE, Chrome, Firefox, Safari
usage: setDnsName	<name to override>	<real name>
example: setDnsName	pat.aol.com	www.aol.com

<name to override> - Host name to replace
<real name> - Real name to lookup instead
setUserAgent
Overrides the User Agent string sent by the browser
Browser Support: IE, Chrome (no SSL), Firefox, Safari
CAUTION : You will still be using the same browser engine so you are still limited by the capabilities and behavior of that browser even if you are spoofing another browser
usage: setUserAgent    <user agent string>
example: setUserAgent    Mozilla/5.0 (iPhone; U; CPU like Mac OS X; en) AppleWebKit/420+ (KHTML, like Gecko) Version/3.0 Mobile/1A543 Safari/419.3

<user agent string> - User agent string to use.
overrideHost
Replaces the value of the Host: HTTP header for the given host with the provided replacement.  It also adds a new header (x-Host:) with the original value.
Browser Support: IE, Chrome, Firefox, Safari (no SSL)
usage: overrideHost	<host>    <new host>
example: overrideHost	www.aol.com    www.notaol.com

<host> - host for which you want to override the Host: HTTP header
<new host> - value to set for the Host header
overrideHostUrl
For all requests to the given host, rewrite the requests to go to a different server and include the original host in the new URI.
Browser Support: IE
usage: overrideHostUrl	<host>    <new host>
example: overrideHostUrl	www.webpagetest.org    staging.webpagetest.org

<host> - host for which you want to redirect requests
<new host> - target server to receive the redirected requests

In this example, http://www.webpagetest.org/index.php will get rewritten to actually request http://staging.webpagetest.org/www.webpagetest.org/index.php
addHeader
Adds the specified header to every http request (in addition to the headers that exist, does not overwrite an existing header).
Browser Support: IE, Chrome, Firefox, Safari (no SSL)
usage: addHeader	<header>    {filter}
example: addHeader	Pragma: akamai-x-cache-on

<header> - Full header entry to add (including label)
{filter} - (optional) regex match for host names where the header should be added
setHeader
Adds the specified header to every http request, overriding the header if it already exists.
Browser Support: IE, Chrome, Firefox, Safari (no SSL)
usage: setHeader	<header>    {filter}
example: setHeader	UA-CPU: none-ya

<header> - Full header entry to set (including label)
{filter} - (optional) regex match for host names where the header should be set
resetHeaders
Clears any headers that were specified through addHeaders or setHeaders (in case you want to only override headers for part of a script).
Browser Support: IE, Chrome, Firefox, Safari
usage: resetHeaders
example: resetHeaders

Misc
combineSteps
Causes multiple script steps to be combined into a single "step" in the results
Browser Support: IE, Chrome, Firefox, Safari
usage: combineSteps	[count]
example: combineSteps

[count] - Number of script steps to merge (optional, defaults to 0 which is ALL)
Sample Script:
combineSteps
navigate	www.google.com
navigate	www.yahoo.com
navigate	www.aol.com
if/else/endif
Conditionally execute a block of script code based on run number or cached state for the test.  Conditional blocks can be nested.
Browser Support: IE, Chrome, Firefox, Safari
usage:  if	[cached|run]    <value>
        else
        endif

example:    if    run    1
            if    cached    0
            <do something for first view of first run>
            endif
            else
            <do something else for everything but first run>
            endif

[cached|run] - Compare against run number or cached state
<value> - matching run number or cached state to execute block

ignoreErrors
Continues running the script regardless of any errors. This is particularly useful if you expect a certain part of the script to intermittently fail but want to continue executing the script anyway. The default is for it to be disabled and any error in processing the script will stop the test.
Browser Support: IE
usage: ignoreErrors	<ignore>
example: ignoreErrors	1

<ignore> - set to 1 to turn on error ignoring and 0 to disable it
logErrors
Enables or disables the logging of script errors to the error log. The default is for it to be enabled.
Browser Support: IE
usage: logErrors	<log>
example: logErrors	0

<log> - set to 0 to turn off error logging and 1 to re-enable it
loadFile
Populates the provided variable name with the contents of the supplied file (contents are expected to be text, not binary).
The script engine will automatically replace all occurrences of any variable in the value parameter field (3rd parameter). There is no restriction on how the variables are named but it is strongly recommended that you use a convention of %variable% or $variable$ or something similar so that you don't accidentally substitute variables where you did not expect it to happen.
There is no limit on the number of variables you can use or the number of files you can load. Each new file just adds to the existing variable list. If there is a collision in variable names between two files, the one that is loaded last replaces all previous versions.
Browser Support: IE
usage: loadFile	<file>	<variable>
example: loadFile	msg.txt	%MSG%

<file> - file contents that are to be loaded into the provided variable name.
<variable> - variable name to associate with the file contents.
The path search order for the file is to first check the same directory the script is running in, then the directory that pagetest was installed to and then finally try the file as an absolute path.
loadVariables
Populates a set of variables from the provided file. The file is expected to be a flat text file with each variable on a line in the format "variable=value".
Browser Support: IE
i.e.
%AOLSN%=username
%AOLPW%=password
{noformat}

The script engine will automatically replace all occurrences of any variable in the value parameter field (3rd parameter).  There is no restriction on how the variables are named but it is strongly recommended that you use a convention of %variable% or $variable$ or something similar so that you don't accidentally substitute variables where you did not expect it to happen.

There is no limit on the number of variables you can use or the number of variable files you can load.  Each new variable file just adds to the existing variable list.  If there is a collision in variable names between two files, the one that is loaded last replaces all previous versions.

<code><pre>
usage: loadVariables	<variable file>
example: loadVariables	accounts.txt

<variable file> - file that includes the variable/value pairs (see format above).
The path search order for the file is to first check the same directory the script is running in, then the directory that pagetest was installed to and then finally try the file as an absolute path.
minInterval
Specifies a minimum interval that the following portion of the script can be run (in minutes). For example, if you only want to test sending of mail no more frequently than every 60 minutes (on a single machine) then you would insert a minInterval command at the point in the script where the mail is going to be sent. If the interval has not expired yet then the script will exit. You can also use it to make sure the same action is not being performed at the same time by multiple browsers by specifying an interval of 0 (which will allow the test to run but will pause the second script until the first script has finished).
Browser Support: IE
usage: minInterval	<key>	<interval>
example: minInterval	AOLSendMail	60

<key> - Unique event name that will be restricted to the interval
<interval> - Minimum time in minutes to allow between allowing the event to run
endInterval
Ends the block of code that is protected by a minInterval block. This is useful if the part of the script you want to protect with an interval is in the middle of the script and you always want to execute the remainder of the script (like logging out).
Browser Support: IE
usage: endInterval
example: endInterval

expireCache
Expires any cache entries that will expire within the specified number of seconds.  This can be used to simulate a repeat view after a certain amount of time (for example, what it would be like to browse the page the next day).  It doesn't help with simulating content changes but any resources with a short expiration will end up being checked with if-modified-since requests.
Browser Support: IE
usage: expireCache	<seconds>
example: expireCache	86400

<seconds> - Any resources with a cache lifetime less than this amount of time will be forced to expire.

firefoxPref
Allows you to specify arbitrary preferences that will be configured before launching the browser.
Browser Support: Firefox
usage: firefoxPref	<pref>    <value>
examples:
firefoxPref    network.http.pipelining    false
firefoxPref    network.http.pipelining.maxrequests    5
firefoxPref    general.useragent.override    "Some User Agent String"

<pref> - The preference that is to be modified
<value> - The value to use.  String values should be enclosed in quotes like the example.

setEventName
Sets the name of the event for the next measurable operation. It is important to only set this right before the action that will generate the activity you want to measure so that you don't inadvertently measure other page activity. Without explicit event names each step will be automatically named Step_1, Step_2, etc.
Browser Support: IE
usage: setEventName	<event name>
example: setEventName	loadWebmail

<event name> - Name to use for the event about to occur (in resulting log files)
setViewportSize
Changes the size of the visible browser window so that the page viewport matches the given dimensions.  If you get black areas on your screen shots then the viewport is larger than the desktop.
Browser Support: IE, Chrome, Firefox, Safari
usage: setViewportSize	<width>    <height>
example: setViewportSize    320    365

<width> - Viewport Width
<height> - Viewport Height

sleep
Pauses the script operation for a given number of seconds.
Browser Support: IE, Chrome, Firefox, Safari
usage: sleep	<seconds to sleep>
example: sleep	5

<seconds to sleep> - An integer indicating how long to sleep.  The allowable range is 1-30.

Sample scripts

Mail test
// load the account name and password
loadVariables	accounts.txt

// bring up the login screen
setEventName	launch
setDOMElement	name=loginId
navigate	http://webmail.aol.com

// ignore any errors from here on (in case the mailbox is empty or we get image challenged)
ignoreErrors	1

// log in
setValue	name=loginId	%AOLSN%
setValue	name=password	%AOLPW%
setDOMElement	className=turbogrid-row
setEventName	load
submitForm	name=AOLLoginForm

// only read and send a mail once an hour
minInterval	AOLMail	60

// close the today curtain
click	className=backdrop
sleep	5

// Open the first message with a subject of "test"
setDOMElement	className=msgBody
setEventName	read
clickAndWait	innerText=test

// delete the message
click	title=Delete (del)
sleep	5

// open the compose mail form
setDOMElement	contentEditable=true
setEventName	compose
clickAndWait	title=Write mail (Alt + w)

// send a test message to myself
sleep	1
setValue	tabindex=100	%AOLSN%
setValue	name=Subject	test
loadFile	msg.txt	%MSG%
setInnerText	contentEditable=true	%MSG%
fileDialog	type=file	msg.gif
sleep	1
setDOMElement	className=confirmMessage
setEventName	send
clickAndWait	innerText=Send

endInterval

// sign off
setEventName	logout
clickAndWait	className=signOutLink
MyAOL Authenticated profile
// load the account name and password
loadVariables	accounts.txt

// bring up the login screen
setDOMElement	name=loginId
navigate	https://my.screenname.aol.com/_cqr/login/login.psp?mcState=initialized&sitedomain=my.aol.com&authLev=0&siteState=OrigUrl%3Dhttp%3A%2F%2Fmy.aol.com%2F

// log in
setValue	name=loginId	%AOLSN%
setValue	name=password	%AOLPW%
setDOMElement	className=more_pics
submitForm	name=AOLLoginForm

DNS Override
This script will:
Create a fake DNS entry for www1.aol.com and have it lookup www.aol.com instead
Force www.aol.com to resolve to 127.0.0.1
Set a "zip" cookie on the www.aol.com domain
Navigate and measure the time to load www.aol.com
setDnsName	www1.aol.com	www.aol.com
setDns	www.aol.com	127.0.0.1
setCookie	http://www.aol.com	zip=20166
navigate	http://www.aol.com

iPhone Spoofing
This script will:
Use the iPhone user agent string
Change the browser dimensions to match the iPhone
Navigate to www.aol.com
setUserAgent	Mozilla/5.0 (iPhone; CPU iPhone OS 6_0 like Mac OS X) AppleWebKit/536.26 (KHTML, like Gecko) Version/6.0 Mobile/10A5376e Safari/8536.25
setViewportSize    320    356
navigate	http://www.aol.com

