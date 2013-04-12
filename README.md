Push-Initiator-Webworks
=======================

To send pushes between the Server side and BlackBerry device we need to astablish a Push Initiator and a push Enabled, for a better comprehension about the push architecture visit: http://developer.blackberry.com/java/documentation/push_service_overview.html  

How to send pushes (Push Initiator) to the Blackberry device, Webworks PHP

1. First you need to create a push account (it will be usefull for any Initiator Push or Enabled Push, built in any language), so you need to rgister (to get the push service) into this hyperlink, https://www.blackberry.com/profile/?eventId=8121 
Once you will get your push service information from Rim (for the Push Initiato and Push Enabled), you will get a PPG url for the Initiator push like this one "https://cpxxxx.pushapi.eval.blackberry.com" so to make your PPG url for the Initiator push functional you need to add to it "/mss/PD_pushRequest", so your final PPG url that you will work with is this one "https://cpxxxx.pushapi.eval.blackberry.com/mss/PD_pushRequest"

2. Now you can start to build your Push Initiator (The push Initiator must be built all the time with a server side language, and be accessible through the interenet),
The language that I will demonstarte for you to built your Push Initiator is the PHP. 

 You need to initialize your data to access to the PPG url for the push Initiator and communicate the data that you want to push to the device (It's the PPG url of the push enabled that will receive the data and transfer them to the push enabled (client side)),
 
 Initialize your data in PHP:
 
								  				// APP ID provided by RIM
								      $appid = 'xxxx-xxx...xxx';
								  		  // Password provided by RIM for the Push Initiator
								   		 $password = 'xxxx';

  Get the data that you want to push, for example in this case we are trying to push information as PIN number (or even to  be used for the authentification to get a special information about the device):
    
									    //To push request only to a specific pin
									    $addresstosendto[] = $_REQUEST['bbpin'];  
    
    
  You can push as mush data as you could but you need to regroup them (under a form of application/xml) in one data to be pushed (we use also Post method to post aour dada) to the device
  
										    $data = '--mPsbVQo0a68eIL3OAxnm'. "\r\n" .
										    'Content-Type: application/xml; charset=UTF-8' . "\r\n\r\n" .
										    '<?xml version="1.0"?>
										    <!DOCTYPE pap PUBLIC "-//WAPFORUM//DTD PAP 2.1//EN" "http://www.openmobilealliance.org/tech/DTD/pap_2.1.dtd">
										    <pap>
										    <push-message push-id="' . $messageid . '" deliver-before-timestamp="' . $deliverbefore . '" source-reference="' . $appid . '">'
										    . $addresses .
										    '<quality-of-service delivery-method="unconfirmed"/>
										    </push-message>
										    </pap>' . "\r\n" .
										    '--mPsbVQo0a68eIL3OAxnm' . "\r\n" .
										    'Content-Type: text/plain' . "\r\n" .
										    'Push-Message-ID: ' . $messageid . "\r\n\r\n" .
										    stripslashes($_POST['message']) . "\r\n" .
										    '--mPsbVQo0a68eIL3OAxnm--' . "\n\r";
    
  And now you need to construct an url and parsing all the data on it, we will use the PPG url Intiator, using the method curl_setopt():
  
   
											    // set URL and other appropriate options
											    curl_setopt($ch, CURLOPT_URL, "https://cp3461.pushapi.eval.blackberry.com/mss/PD_pushRequest");
											    curl_setopt($ch, CURLOPT_HEADER, false);
											    curl_setopt($ch, CURLOPT_USERAGENT, "Hallgren Networks BB Push Server/1.0");
											    curl_setopt($ch, CURLOPT_HTTP_VERSION, CURL_HTTP_VERSION_1_1);
											    curl_setopt($ch, CURLOPT_HTTPAUTH, CURLAUTH_BASIC);
											    curl_setopt($ch, CURLOPT_USERPWD, $appid . ':' . $password);
											    curl_setopt($ch, CURLOPT_POST, 1);
											    curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
											    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
											    curl_setopt($ch, CURLOPT_HTTPHEADER, array("Content-Type: multipart/related; boundary=mPsbVQo0a68eIL3OAxnm; type=application/xml", "Accept: text/html, image/gif, image/jpeg, *; q=.2, */*; q=.2", "Connection: keep-alive"));
											    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
											
											    // Pass constructed url to the browser to be executed
											    $xmldata = curl_exec($ch);
 
 Normally now the push is send, and you can get response by parsing it to an xml data that you can read and print, to get the knowledge of your request,
  
											    $p = xml_parser_create();
											    xml_parse_into_struct($p, $xmldata, $vals);
    
 If the data was pushed successfully you will get the following message: The request has been accepted for processing.
 
 3.After that your Initiator Push is ready to use, you need to build a Push Enabled (client side that will be loaded in your device), you can see the following tutorial that is derived from the PushCapture by visiting: https://github.com/sekkalhidaya/Push-Enabled-Webworks  
 
    
      
  
