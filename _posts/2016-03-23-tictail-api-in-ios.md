---
layout:     post
title:      "Using the Tictail API in iOS."
subtitle:   "Part 1 - Authentication"
date:       2014-03-23 16:20:00
author:     "Billy"
header-img: "img/post-bg-07.png"
---
## Developer portal

#### Create an app

Before you're able to talk to the Tictail API you have to create an app on tictail.com/developers.

1. Start by typing tictail.com/developers in your browser.
2. Sign up for a tictail account if you don't already have one.
3. Go to "My Apps" ([https://tictail.com/developers/admin/#/apps](https://tictail.com/developers/admin/#/apps))
4. Give it a name, pick an audience, make sure to set "Type" to external and type "tictailtest://auth" as Redirect URI (without "") (I'll get back to this later).
5. Create App.

#### Gather client id and secret

1. Go to the details page for your newly created app.
2. Select the tab called "Credentials".
3. Copy-paste both the "Client ID" and "Client Secret" to an empty text file, we're going to use this soon from our iOS app.

## iOS

### Add a custom URL scheme

1. Select your project in the left hand pane in Xcode.
2. Select the tab "info" and scroll down to "URL types".
3. Add a new type with the identifier of your choice and URL Schemes "tictailtest" (or what ever you choose when you created your app in the developer portal).

### Authentication

Before we can perform API calls on behalf of a store we need to show the user a login form, handle a redirect URI and exchange a code for a token.

1. Simply make a url containing the tictail url + oauth path + client id.
2. Either send the user to Safari for login or open the URL in a web view. I'll do the former because it's just one line of code :P

Example:
	```
	let authUrl = "https://tictail.com/oauth/authorize?response_type=code&client_id=MY-CLIENT-ID"
	if let url = NSURL(string: signInUrl) {
		UIApplication.sharedApplication().openURL(url)
	}
	```

When the user hit the login button your redirect URI will get called, which in our case is "tictailtest://auth". 

What we want to do next is to handle that redirect by checking if a code is present in the url, and if so, exchange it with an access token.

### Handle redirect URI

1. Implement the following method in your appdelegate: `func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject) -> Bool`
2. Parse the url using NSURLComponents, check if the path is "auth" and grab the value of the query parameter called "code".

```
	var variables = Dictionary<String, String>()
        if let components = NSURLComponents(URL: url, resolvingAgainstBaseURL: false),
        let query = components.query {
            for qs in query.componentsSeparatedByString("&") {
                // Get the parameter name
                let key = qs.componentsSeparatedByString("=")[0]
                // Get the parameter value
                var value = qs.componentsSeparatedByString("=")[1]				value = value.stringByReplacingOccurrencesOfString("+", withString: " ")            
   				if let decodedValue = value.stringByRemovingPercentEncoding {
   					variables[key] = decodedValue
    			}
                
            }
		}
```
### Trade code for access token

We're almost there! This is the last step before we'll have a token that we can use to call the API.

1. Create a NSURLRequest with the URL https://tictail.com/oauth/token and the following POST parameters: "client\_id","client\_secret","code" (the code you got in the previous paragraph),"grant\_type" (should always be "authorization_code").
2. Parse the response JSON and look for the keys "access_token" and "expires_in" and store them somewhere safe. (from Tictail.com: *You should treat the access\_token as you would with a password, keep it safe and never share it, it was given to you in trust.*)

## Done!

There you go! Now you're ready to call the Tictail API from your iOS app!
