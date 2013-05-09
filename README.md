Oauth Module for [Parse Cloud Code](https://www.parse.com/docs/cloud_code_guide)
==

This is a modified version of [node-oauth](https://github.com/ciaranj/node-oauth).

How to use
-

1. Copy all the files to your project's `cloud` folder.
2. Use it in your main.js code like so (Note: This is an example of calling Yahoo! BOSS API from Parse Cloud Code)

		var webSearchUrl = 'http://yboss.yahooapis.com/ysearch/images';
		
		var qs = require('qs');
		var finalUrl = webSearchUrl + '?' + qs.stringify({
			q: encodeURIComponent(request.params.keyword),  //search keywords
			format: 'json',
			count: 30,
		});
	
		var OAuth = require('cloud/oauth').OAuth;
		var consumerKey = '<YOUR_CONSUMER_KEY';
		var consumerSecret = '<YOUR_CONSUMER_SECRET>';
		var oa = new OAuth(webSearchUrl, webSearchUrl, consumerKey, consumerSecret, "1.0", null, "HMAC-SHA1");
		oa.setClientOptions({ requestTokenHttpMethod: 'GET' });
	
		var orderedParameters= oa._prepareParameters('', '', 'GET', finalUrl, null);
		var headers= {};
		var authorization = oa._buildAuthorizationHeaders(orderedParameters);
		headers["Authorization"]= authorization;
	
		Parse.Cloud.httpRequest({
			url: finalUrl,
			headers: headers,
			success: function(httpResponse) {
				var json = JSON.parse(httpResponse.text);
				var images = json.bossresponse.images.results;
				response.success(images);
			},
			error: function(httpResponse) {
				response.error('Request failed with response code ' + httpResponse.text);
			}
		});
		
Note
-

1. https doesn't work. If you can make it work, please let me know.
2. HMAC-SHA1 signature method only.