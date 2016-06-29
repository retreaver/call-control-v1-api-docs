By configuring a campaign, you can reuse the settings of the campaign when creating numbers. Campaigns should be configured before creating numbers.

**Deprecation Notice: As of 02/2014, this API will no longer be supported.**

# GET /campaigns

List all the campaigns in your account.

**Resource URL**
@note http://callpixels.com/campaigns.format

# GET /campaigns/cid/0044

Find a campaign by CID.

**Resource URL**
@note http://callpixels.com/campaigns/cid/0044.format

**Response**
~~~~
JSON
{
   "campaign":{
      "cid":"111",
      "name":"SuperFuntime",
      "updated_at":"2012-07-15T03:40:24Z",
      "created_at":"2012-04-16T13:50:21Z",
      "record_calls":true,
      "record_seconds":3600,
      "dedupe_seconds":86400,
      "affiliate_can_pull_number":false,
      "show_key":"5e2ba674a8a1fb34dddcf850139ffdd9"
      "greeting":{
         "message":"Hi There! Press one to continue.",
         "voice_gender":"Female"
      },
      "timers":[
         {
            "timer":{
               "id":195,
               "seconds":0,
               "url":"http://www.thertrack.com/click.track?CID=[campaign_id]&AFID=[affiliate_id]&SID=[sub_id]"
            }
         },
         {
            "timer":{
               "id":199,
               "seconds":90,
               "url":"http://www.thertrack.com/pixel.track?CID=[campaign_id]&AFID=[affiliate_id]&SID=[sub_id]&MerchantReferenceID=[caller_id]-[called_number]-[call_duration]"
            }
         }
      ],
      "menu_options":[
         {
            "menu_option":{
               "id":61,
               "option":"1",
               "target_cid":null,
               "target_number":"+18987748833"
            }
         }
      ]
   }
}
~~~~
# POST /campaigns

Create a new campaign.

**Resource URL**
@note http://callpixels.com/campaigns.format

##Parameters

@param cid {string} Your internal campaign ID that will be referenced in the future.
@param [name] {string} A name for the campaign for your reference.
@param [dedupe_seconds] {integer} Prevent a caller from causing any Connect (timer.seconds == 0) or Sale (timer.seconds > 0) timer from firing within n seconds. Default is 0 (disabled). Does not affect < 0 second timers.
@param [affiliate_can_pull_number] {boolean} Allow affiliates to access this campaign via our LinkTrust integration.
@param [record_calls] {boolean} Toggles call recording on and off. Defaults to true.
@param [record_seconds] {integer} Maximum length of the call recording. Defaults to 3600 (1 hour).
@param message {string} (Text-to-Speech) A message you want read aloud to the caller when they dial your number. Make sure to tell them to press one to continue.
@param voice_gender {string} (Text-to-Speech) Male or Female, the gender of the text-to-speech voice you want.
@param message_file {file} (Audio File) An audio file you would like played for the caller when they dial your number. Use this field with multipart/form-data submissions.
@param message_file_b64_data {string} (Audio File) A Base64-encoded audio file. Only use this field if you're not using the message_file field.
@param message_file_b64_filename {string} (Audio File) The original file name for the Base64-encoded audio file. Something like 'memo.flac'. We suggest using the highest quality audio available.
@param [repeat] {integer} The number of times to repeat the greeting. Defaults to 4.
@param timers_attributes {array} An array of timers.
@prams menu_option_attributes {array} An array of menu options.
@param [destroy_nested] {boolean} When set, causes existing timers and menu_options to be destroyed. Defaults to false. Existing timers and menu options can be updated by including their unique ID. Alternatively, you can simply remove all the existing timers and menu options by setting this to true and resubmit all the values.

**Timers**
We will fire any 0 second timer ('click timer') before firing a sale timer (> 0 seconds). Only the highest second value sale timer that is applicable to this call will be fired. If you have timers for 30 seconds and 90 seconds and the call lasts 2 minutes, only the 90 second sale timer will be fired.

##Nested Attribute: Timer
@param seconds {integer} The minimum length of time the call must last for this pixel to be fired. If set to 0, the pixel will fire at the beginning of the call, much like a click.
@param url {string} The URL that should be called for this timer. The URL does not have to be a pixel, and will be passed any cookies that were set on the 0-second timer. Our bot follows redirects intelligently.

**Menu Options**
Menu options are used to route calls based on what button the caller presses on their phone. Menu options can redirect a caller to a different phone number or another CallPixels campaign. You must provide at least one menu option, and you must provide an option for '1', which is the option used to route calls when there is no greeting configured.

##Nested Attribute: Menu Option
@param option {string} The button the caller will press. The default menu option for a campaign is the one for button press '1', so if you aren't using an IVR and want the call to forward instantly, set the option to '1'. (1 2 3 4 5 6 7 8 9 0 * #)
@param target_number {string} A phone number to redirect the caller to. All > 0 second timers will start when the call is answered.
@param target_cid {string} A campaign ID to redirect the caller to. The campaign must be configured in the system. If this value is set, it will overrride the target number. This option can be used to allow the caller to replay the greeting.

**Request**
~~~~
JSON
{
    "campaign": {
        "cid": "000333",
        "name": "MyCampaign",
        "message": "Thanks for calling, please press one to continue.",
        "voice_gender": "Male",
        "timers_attributes": [{
            "seconds": 0,
            "url": "http://callpixels.com/click.html"
        }, {
            "seconds": 90,
            "url": "http://callpixels.com/sale.html"
        }],
        "menu_options_attributes": [{
            "option": "1",
            "target_number": "+16474570424"
           }
        ]
    }
}
~~~~

**Response**
~~~~
JSON
{
   "campaign":{
      "cid":"000333",
      "name":"MyCampaign",
      "updated_at":"2012-07-15T03:34:00Z",
      "created_at":"2012-07-15T03:34:00Z",
      "record_calls":true,
      "record_seconds":3600,
      "dedupe_seconds":0,
      "affiliate_can_pull_number":false,
      "show_key":"5e2ba674a8a1fb34dddcf850139ffdd9"
      "greeting":{
         "message":"Thanks for calling, please press one to continue.",
         "voice_gender":"Male"
      },
      "timers":[
         {
            "timer":{
               "id":217,
               "seconds":0,
               "url":"http://callpixels.com/click.html"
            }
         },
         {
            "timer":{
               "id":218,
               "seconds":90,
               "url":"http://callpixels.com/sale.html"
            }
         }
      ],
      "menu_options":[
         {
            "menu_option":{
               "id":77,
               "option":"1",
               "target_cid":null,
               "target_number":"+16474570424"
            }
         }
      ]
   }
}
~~~~

# DELETE /campaigns/cid/0044

Delete a campaign by CID. You must delete all the numbers belonging to that campaign first.

**Resource URL**
@note http://callpixels.com/campaigns/cid/0044.format

# PUT /campaigns/cid/0044

Update a campaign by CID.

**Resource URL**
@note http://callpixels.com/campaigns/cid/0044.format