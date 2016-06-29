Number pools are used to dynamically assign numbers whenever a user visits your website.

#GET number_pools

Returns all the number pools you have in our system.

**Resource URL**
@note http://callpixels.com/number_pools.format

#GET number_pools/1

Returns a single number pool based on our internal ID.

**Resource URL**
@note http://callpixels.com/number_pools/1.format

~~~~
JSON
{
    "number_pool": {
        "id": 1,
        "numbers_count": 0,
        "type": "Toll-free",
        "country": "CA",
        "max_pool_size": 10,
        "buffer_seconds": 0,
        "updated_at": "2013-04-25T16:16:51Z",
        "created_at": "2013-04-25T16:16:51Z",
        "google_analytics": false,
        "hide_embedded_access": false,
        "afid": "0001",
        "cid": "111",
        "reserve_size": 1
    }
}
~~~~


#GET number_pools/for/cid/0002/afid/0033

Searches for number pools matching the provided criteria. The afid portion is optional.

**Resource URL**
@note http://callpixels.com/number_pools/for/cid/111/afid/0001.format
@note http://callpixels.com/number_pools/for/cid/111.format

#POST number_pools

Creates a new number pool.

**Resource URL**
@note http://callpixels.com/number_pools.format

##Parameters

@param cid {string} The campaign ID this number pool belongs to. You must set up the campaign before creating a number pool for it.
@param [type] {string} Toll-free or Local, the type of number that is desired for this number pool. Defaults to Toll-free.
@param [country] {string} For Local number pools only, the 2 character country code for the country you would like the numbers to be in. Defaults to US. Possible values: US CA AT BE DK FI FR GB IE IT NL PL SE
@param [afid] {string} The affiliate ID this number pool belongs to. You must set up the affiliate before you create the number pool.
@param [max_pool_size] {integer} The maximum number of numbers you want in this number pool. Defaults to 10.
@param [buffer_seconds] {integer} The number of seconds for a number to retain its assignment after the visitor has left your site. Defaults to 0.
@param [hide_embedded_access] {boolean} When true, will not show the embedded affiliate phone number management interface for this campaign. Defaults to false.
@param [google_analytics] {boolean} Enables our Google Analytics integration. Incompatible with number pools that have an assigned affiliate. Defaults to false.
@param [reserve_size] {integer} The number of numbers to pre-provision and hold in reserve. Allows numbers to be instantly assigned and helps avoid delays in provisioning numbers. Minimum 1, defaults to 1.

**Request**
~~~~
JSON
{
    "number_pool": {
        "cid": "111",
		"max_pool_size": 100,
		"hide_embedded_access": true,
		"google_analytics": true
    }
}
~~~~

**Response**
~~~~
JSON
{
    "number_pool": {
        "id": 49,
        "numbers_count": 0,
        "country": "US",
        "max_pool_size": 100,
        "buffer_seconds": 0,
        "updated_at": "2013-04-25T16:53:52Z",
        "created_at": "2013-04-25T16:53:52Z",
        "hide_embedded_access": true,
        "google_analytics": true,
        "afid": null,
        "cid": "111",
        "type": "Toll-free",
        "reserve_size": 1,
        "google_analytics_paths": [
            {
                "name": "Network timer (always)",
                "path": "/callpixels/8ddc04aa-0020-45eb-89df-ed45b8ed43a7"
            },
            {
                "name": "Network timer (connect)",
                "path": "/callpixels/18135f62-00ff-4818-941d-d88b6a93f27d"
            }
        ]
    }
}
~~~~

# PUT /number_pools/1.format

Updates number pool with the given ID.

**Resource URL**
@note http://callpixels.com/numbers/1.format

# DELETE /number_pools/1.format

Deletes number pool with the given ID.

## WARNING
*This will also delete all numbers associated with this pool!*

**Resource URL**
@note http://callpixels.com/numbers/1.format