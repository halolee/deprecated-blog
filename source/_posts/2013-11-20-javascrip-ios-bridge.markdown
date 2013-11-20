---
layout: post
title: "javascrip ios bridge"
date: 2013-11-20 19:46
comments: true
categories: [html,javascript,ios,bridge]
---


Background
===

For the most of the time, the UIWebview in iOS native code act as a browser and handle the communication of webpage. 

In order for a deep level communcate between a webpage and iOS native application, i.e. to invoke iOS native functionality by javascript function, then we need to build a javascrip-iOS bridge.

Below is a simplified and minified iOS-JS bridge.

Javascript
===

NativeBridge.js
---

	var NativeBridge = {
	  callbacksCount : 1,
	  callbacks : {},
    
	  call : function call(callbackId, callback) {
    
    	NativeBridge.callbacks[callbackId] = callback;
    
	    var iframe = document.createElement("IFRAME");
	    iframe.setAttribute("src", "sp://claimID/" + callbackId);
	    document.documentElement.appendChild(iframe);
	    iframe.parentNode.removeChild(iframe);
    	iframe = null;
    
  		}

	};
	

This is the essential part. By calling this script, the webpage can call back on the function on the iOS side.

Webview-script.js
---

	function passBackClaimId(claimID){

    	NativeBridge.call(12341234123123, function () {});
    
	};

It only contain a function call to initiate the function defined in the nativeBridge.js.

webview-document.html
----
	<html>
		<head>
		  <title>Webview document</title>
			  <script type="text/javascript" src="NativeBridge.js"></script>
			  <script type="text/javascript" src="webview-script.js"></script>
		</head>

		<body>
		  <h4>UIWebView HTML document</h4>
    
  			<form>
		      <input type=button value="Submit" onClick= passBackClaimId() >
        	  <input type=button value="Show query" onClick="alert('Query string: '+self.location.search)">

		  </form>
		</body>

	</html>

By clicking on the Submit button, it will invoke the javascript-iOS call to pass the claimID back to iOS application.

Another button is designed to catch the parameter passed in. That part is still under construction. Current function is if the web page is accessed through a query string. i.e. compare the following url
	
	http://localhost:8888/webview-document.html
	http://localhost:8888/webview-document.html?itemName=LEDTV&itemPrice=3000
	
We can see that the lower url string have a query string attached at the back of the base url.

and the query string is 

	?itemName=LEDTV&itemPrice=3000
	
which is normally the part we parse the parameter into the webpage.


iOS
----

	- (id)initWithFrame:(CGRect)frame 
        {
          if (self = [super initWithFrame:frame]) {
            
            // Set delegate in order to "shouldStartLoadWithRequest" to be called
            self.delegate = self;
            
            // Set non-opaque in order to make "body{background-color:transparent}" working!
            self.opaque = NO;
            
            // Instanciate JSON parser library
            json = [ SBJSON new ];
            
            // load our html file
            NSString *path = [[NSBundle mainBundle] pathForResource:@"webview-document" ofType:@"html"];
            [self loadRequest:[NSURLRequest requestWithURL:[NSURL fileURLWithPath:path]]];

        //      NSString *urlString = [[NSString alloc] initWithFormat:@"http://localhost:8888/webview-document.html?itemName=%@&purchasePrice=%@",@"SamsumTV",@"4000"];
        //      [self loadRequest:[NSURLRequest requestWithURL:[NSURL URLWithString:urlString]]];
          }
          return self;
        }


        // This selector is called when something is loaded in our webview
        // By something I don't mean anything but just "some" :
        //  - main html document
        //  - sub iframes document
        //
        // But all images, xmlhttprequest, css, ... files/requests doesn't generate such events :/
        - (BOOL)webView:(UIWebView *)webView2 
                  shouldStartLoadWithRequest:(NSURLRequest *)request 
                  navigationType:(UIWebViewNavigationType)navigationType {

            NSString *requestString = [[request URL] absoluteString];
          
          NSLog(@"request : %@",requestString);
          
          if ([requestString hasPrefix:@"sp://claimID/"]) {
            
            NSArray *components = [requestString componentsSeparatedByString:@"sp://claimID/"];
            
              NSString* callbackId = (NSString*)[components objectAtIndex:1] ;
              
              [self saveClaimID:callbackId];
            
            return NO;
          }
          
          return YES;
        }

        - (void)saveClaimID:(NSString *)claimID{
            NSLog(@"\n\n\nClaimID is %@\n\n\n",claimID);
        }


And need to setup webview delegate in the .h file

	@interface MyWebView : UIWebView <UIWebViewDelegate> 

That's pretty much about it. For a complete and more robust version, please find the guys in the reference.


Reference
----


For source code architecture and the concept of javascript-iOS bridge, credit goes to this guy
[https://github.com/marcuswestin/WebViewJavascriptBridge](https://github.com/marcuswestin/WebViewJavascriptBridge)


for the web form button, credit goes to this guy
[http://www.javascripter.net/faq/querystr.htm](http://www.javascripter.net/faq/querystr.htm )