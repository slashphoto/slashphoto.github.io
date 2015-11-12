# slash.photo

slash.photo is a simple *slash command* that searches the Unsplash API from Slack. 

### Resources

This approach merged a number of different helpful resources.

1. Free hosting account on [OpenShift](https://www.openshift.com/pricing/index.html)
2. PHP script demonstrating how to post incoming webhooks on Slack by [Alex Stone](https://gist.github.com/alexstone/9319715)
3. Slack Integration by [David Mays](https://github.com/davidmays/slack-integration)


### Retreiving data from Slack to build a Slash Command
```
	function BuildSlashCommand($request)
	{
		$cmd = new stdClass();
		$cmd->Token = $request['token'];
		$cmd->TeamId = $request['team_id'];
		$cmd->team_domain = $request['team_domain'];
		$cmd->ChannelId = $request['channel_id'];
		$cmd->ChannelName = $request['channel_name'];
		$cmd->UserId = $request['user_id'];
		$cmd->UserName = $request['user_name'];
		$cmd->Command = $request['command'];
		$cmd->Text = $request['text'];
		$cmd->ResponseUrl = $request['response_url'];

		return $cmd;
	}
```

### Searching Unsplash API
```
    // Build Unsplash API search
	function RunImageSearch($text)
	{
		$enc = urlencode($text);
	    $client_id = 'YOUR TOKEN';
	    $imageSearchJson = get_url_contents("https://api.unsplash.com/photos/search/?query=".$text."&client_id=".$client_id."&per_page=25");
	    $images = json_decode($imageSearchJson);

	    return $images;
	}
	
	// cURL to get the contents of search from Unsplash API
	
	function get_url_contents($url) {
	    $crl = curl_init($url);

	    curl_setopt($crl, CURLOPT_USERAGENT, 'Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; .NET CLR 1.1.4322)');
	    curl_setopt($crl, CURLOPT_RETURNTRANSFER, 1);
	    curl_setopt($crl, CURLOPT_CONNECTTIMEOUT, 5);

	    $response = curl_exec($crl);
	    curl_close($crl);
	    return $response;
	}
```

### Authenticate a new user of your Slack app
```
  // Get unique 'code' from Slack
  $code = $_REQUEST['code'];

  // Set Auth Parameters
  $auth = new stdClass;
  $auth->client_id = "YOUR CLIENT ID";
  $auth->client_secret = "YOUR CLIENT SECRET";
  $auth->code = $code;
  $auth->redirect_uri = "YOUR REDIRECT URL";
  
  // Send Authentication info to Slack
  $auth_url = "https://slack.com/api/oauth.access?client_id={$auth->client_id}&client_secret={$auth->client_secret}&code={$auth->code}&redirect_uri={$auth->redirect_uri}";
  $ch = curl_init();
  curl_setopt($ch, CURLOPT_URL, $auth_url);
  curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "POST");
  curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
  $result = curl_exec($ch);
  // If there is an error stop process
  if($result === false)
  {
      echo 'Curl error: ' . curl_error($ch);
      die();
  }
  curl_close($ch);
    
  // Redirect people to your web page
  header("Location: http://YOURPAGE.COM");
  exit();
```

### API Documentation

- Unsplash: https://unsplash.com/developers

- Slack: https://api.slack.com/

