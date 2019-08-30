Facebook Marketing API
======================

This document introduces and explains the use of Facebook API for creating and managing campaigns on Facebook using the ICN platform.

Introduction
^^^^^^^^^^^^

Facebook Marketing API for `ICN <http://icrowdnewswire.com>`_ provides an array of endpoints to create and manage campaigns on Facebook. Every endpoint requires a `JSON Web Token <https://jwt.io/>`_ for authentication. This token can be generated via ``/token`` endpoint and requires ``username`` and ``password hash`` of a user. All ``GET`` requests require ``token`` parameter to be passed as part of the ``url`` whereas all ``POST`` requests require ``token`` to be passed as a ``form parameter``. Overtime, more endpoints will be added to offer more functionality. With that in mind, let's have a look at the endpoints the API provides at this point.


.. warning::

    Please make sure you use a HTTP client that can differentiate between successful responses and exceptions based on the status
    codes. **At this point, the API does not provide any customized failure messages**. It will only return ``JSON responses`` when a call is successful.
    The only exception to this is the token validation failure.

    The recommended HTTP client is `Axios <https://github.com/axios/axios>`_.


API Endpoints
^^^^^^^^^^^^^

API Endpoints are sorted by dependencies in the ascending order. This means that the last section (i.e. Ads) have the most dependencies.

.. contents:: :local:

Tokens
######

This following endpoints deal with using `JSON Web Token <https://jwt.io/>`_ for authentication.

.. contents:: :local:

Generate
--------

.. http:post:: /token

    Generates a new `JSON Web Token <https://jwt.io/>`_ that is used for authentication when making calls to the other endpoints of the API. This token expires after ``1 hour``. Use the `/token/verify <#verify>`_ endpoint to verify the validity of a token.

    Example Request
        .. sourcecode:: http

            POST /token HTTP/1.1
            Host: icrowdnewswire.com/api/
            User-Agent: curl/7.65.3
            Accept: */*

    :reqheader Content-Type: application/x-www-form-urlencoded
    :form username: ``Username`` of the account to authenticate against.
    :form password: ``Password Hash`` of the account to authenticate against.

    Example Response
        .. sourcecode:: http

            HTTP/1.0 201 Created
            Content-Type: application/json

            {"token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE1NjcwNjk2M..."}

    :>json string token: JSON Web Token that can be used to make calls to the API endpoints.


Verify
------

.. http:get:: /token/verify?token=<JWT>

    Checks to see if a token is valid or expired.

    Example Request
        .. sourcecode:: http

            GET /token/verify?token=eyJ0eXAiOiJKV1QiLCJhbG... HTTP/1.1
            Host: icrowdnewswire.com/api/
            User-Agent: curl/7.65.3
            Accept: */*

    :query token: ``JWT`` to validate.

    Example Response
        .. sourcecode:: http

            HTTP/1.1 200 OK
            Content-Type: application/json


.. warning::

    Every request to the API (except the token endpoints) is checked for a valid token. If the token is invalid or
    has expired, ``Invalid token!`` and ``The token has expired!`` responses will be returned respectively. Both
    responses will have the status code of ``401`` indicating that the API call did not succeed.
    Make sure your HTTP client is configured to handle these exceptions.


Campaigns
#########

This following endpoints deal with creating and managing Facebook Ad Campaigns.

.. contents:: :local:

Create
------

.. http:post:: /campaign

    Creates a new campaign.

    Example Request
        .. sourcecode:: http

            POST /campaign HTTP/1.1
            Host: icrowdnewswire.com/api/
            User-Agent: curl/7.65.3
            Accept: */*

    :reqheader Content-Type: application/x-www-form-urlencoded
    :form token: ``JSON Web Token`` to be used for authentication.
    :form name: Name of the campaign.
    :form status: Status of the campaign at the time of creation.
    :form objective: ``optional`` Campaign objective. Defaults to ``POST_ENGAGEMENT``

    .. note::
          By default, the campaign objective (passed in as an optional form parameter) defaults to **POST_ENGAGEMENT**. Depending on your requirements, you can use of one the following:

            ``CONVERSIONS`` ``EVENT_RESPONSES`` ``LEAD_GENERATION`` ``LINK_CLICKS`` ``REACH`` ``APP_INSTALLS`` ``OFFER_CLAIMS`` ``PAGE_LIKES`` ``STORE_VISITS`` ``VIDEO_VIEWS``

    Example Response
        .. sourcecode:: http

            HTTP/1.0 201 Created
            Content-Type: application/json

            {
                "id": "120330000039963113"
            }

    :>json string id: Id of the created campaign.


Fetch
-----

.. http:get:: /campaigns?token=<JWT>

    Returns all campaigns associated with the account.

    Example Request
        .. sourcecode:: http

            GET /campaigns?token=eyJ0eXAiOiJKV1QiLCJhbG... HTTP/1.1
            Host: icrowdnewswire.com/api/
            User-Agent: curl/7.65.3
            Accept: */*

    :query token: ``JWT`` used for authentication.

    Example Response:
        .. sourcecode:: http

            HTTP/1.1 200 OK
            Content-Type: application/json

            {
                "campaigns": [
                    {
                        "id": "120330000039963113",
                        "name": "My Campaign 777",
                        "objective": "POST_ENGAGEMENT"
                    },
                    {
                        "id": "120330000039839913",
                        "name": "My Campaign",
                        "objective": "POST_ENGAGEMENT"
                    },
                    {
                        "id": "120330000039716513",
                        "name": "My Campaign",
                        "objective": "PAGE_LIKES"
                    },
                ]
            }


Fetch **Ids**
-------------

.. http:get:: /campaigns/ids?token=<JWT>

    Returns ids of all campaigns associated with the account.
    These ids can be used to query individual campaigns.

    Example Request
        .. sourcecode:: http

            GET /campaigns/ids?token=eyJ0eXAiOiJKV1QiLCJhbG... HTTP/1.1
            Host: icrowdnewswire.com/api/
            User-Agent: curl/7.65.3
            Accept: */*

    :query token: ``JWT`` used for authentication.

    Example Response
        .. sourcecode:: http

            HTTP/1.1 200 OK
            Content-Type: application/json

            [
                "120330000039839913",
                "120330000039837013",
                "120330000039834113",
                "120330000039831213",
                "120330000039828313",
                "120330000039825413"
            ]

    Response JSON Array of Strings
        * List of ids. Every id points to a ``Campaign``.


Delete
------

.. http:delete:: /campaign

    Deletes a campaign.

    Example Request
        .. sourcecode:: http

            DELETE /campaign HTTP/1.1
            Host: icrowdnewswire.com/api/
            User-Agent: curl/7.65.3
            Accept: */*

    :reqheader Content-Type: application/x-www-form-urlencoded
    :form token: ``JSON Web Token`` to be used for authentication.
    :form campaign_id: Id of the Campaign to be deleted.

    Example Response
        .. sourcecode:: http

            HTTP/1.0 204 No Content

Ad Sets
#######

The following endpoints deal with creating and managing AdSets.

.. contents:: :local:

Create
------

.. http:post:: /ad/set

    Creates a new AdSet.

    Example Request
        .. sourcecode:: http

            POST /ad/set HTTP/1.1
            Host: icrowdnewswire.com/api/
            User-Agent: curl/7.65.3
            Accept: */*

    :reqheader Content-Type: application/x-www-form-urlencoded
    :form token: ``JSON Web Token`` to be used for authentication.
    :form name: Name of the AdSet.
    :form status: Status of the AdSet at the time of creation.
    :form countries: An ``array`` of countries to be targeted.
    :form daily_budget: Daily Budget.
    :form bid_amount: Bid Amount.
    :form campaign_id: ``Campaign Id`` under which to create this AdSet.
    :form promoted_page_id: ``Page Id`` under which to create ads.

    Example Response
        .. sourcecode:: http

            HTTP/1.0 201 Created
            Content-Type: application/json

            {
                "id": "120330000039963813"
            }

    :>json string id: Id of the created AdSet.


Fetch
-----

.. http:get:: /ad/sets?token=<JWT>&campaign_id=<Campaign Id>

    Fetches all AdSets for a given Campaign id.

    Example Request
        .. sourcecode:: http

            GET /ad/sets?token=eyJ0eXAiOiJKV1QiLCiJ9...&campaign_id=120330000... HTTP/1.1
            Host: icrowdnewswire.com/api/
            User-Agent: curl/7.65.3
            Accept: */*

    :query token: ``JWT`` used for authentication.
    :query campaign_id: ``Campaign Id`` for the required ``AdSets``.


    Example Response
        .. sourcecode:: http

            HTTP/1.0 201 Created
            Content-Type: application/json

            [
                {
                    "id": "120330000039837313",
                    "name": "My AdSet",
                    "status": "PAUSED",
                    "campaign": {
                        "id": "120330000039837013"
                    },
                    "configured_status": "PAUSED",
                    "created_time": "2019-08-28T15:08:31+0500",
                    "daily_budget": "200000",
                    "lifetime_budget": "0",
                    "optimization_goal": "IMPRESSIONS",
                    "promoted_object": {
                        "page_id": "447378825409527"
                    },
                    "targeting": {
                        "age_max": 65,
                        "age_min": 18,
                        "geo_locations": {
                            "countries": [
                                "US"
                            ],
                            "location_types": [
                                "home"
                            ]
                        }
                    },
                    "start_time": "2019-08-28T15:08:32+0500"
                }
            ]

    :>jsonarr object ad_set: Single AdSet.


Ad Creatives
############

The following endpoints deal with creating and managing AdCreatives.

.. contents:: :local:

Create
------

.. http:post:: /ad/creative

    Creates a new AdCreative.

    Example Request:
        .. sourcecode:: http

            POST /ad/set HTTP/1.1
            Host: icrowdnewswire.com/api/
            User-Agent: curl/7.65.3
            Accept: */*

    :reqheader Content-Type: application/x-www-form-urlencoded
    :form token: ``JSON Web Token`` to be used for authentication.
    :form image: Image to be used for the creative. Must be an ``Image File``.
    :form name: Creative name.
    :form message: Message of the creative. This appears right above the image.
    :form link: Creative link.

    Example Response:
        .. sourcecode:: http

            HTTP/1.0 201 Created
            Content-Type: application/json

            {
                "id": "120330000039964613"
            }

    :>json string id: Id of the created AdCreative.

Ads
###

The following endpoints deal with creating and managing Ads.

.. contents:: :local:

Create
------

.. http:post:: /ad

    Creates a new ad.

    Example Request
        .. sourcecode:: http

            POST /ad HTTP/1.1
            Host: icrowdnewswire.com/api/
            User-Agent: curl/7.65.3
            Accept: */*

    :reqheader Content-Type: application/x-www-form-urlencoded
    :form token: ``JSON Web Token`` to be used for authentication.
    :form ad_set_id: The id of the AdSet to associate with this ad.
    :form creative_id: The id of the AdCreative to associate with this ad.
    :form name: Name of the ad.
    :form message: Status of this ad at the time of creation.

    Example Response
        .. sourcecode:: http

            HTTP/1.0 201 Created
            Content-Type: application/json

            {
                "id": "120330000039965013",
                "preview_link": "https://www.facebook.com/ads/api/preview_iframe.php?d=AQJ_J6R.."
            }

    :>json string id: Id of the created Ad.
    :>json string preview_link: Preview link of the created Ad. This link is viewable in all browsers.


Insights
--------

.. http:get:: /ad/insights?token=<JWT>&ad_set_id=839239498234...

    Returns insights for an ad.

    Example Request
        .. sourcecode:: http

            GET /ad/insights?token=eyJ0eXAiOiJKV1QiLCJhbG...&ad_set_id=123023101... HTTP/1.1
            Host: icrowdnewswire.com/api/
            User-Agent: curl/7.65.3
            Accept: */*

    :query token: ``JWT`` used for authentication.
    :query ad_set_id: ``AdSet`` id that was used to create the ad.

    Example Response
        .. sourcecode:: http

            HTTP/1.1 200 OK
            Content-Type: application/json

            {
                "insights": [
                    {
                        "adset_id": "120330000039968513",
                        "adset_name": "My Ad Set 7773",
                        "campaign_id": "120330000039814513",
                        "campaign_name": "My Campaign 777",
                        "impressions": "200",
                        "clicks": "20",
                        "cpc": "1",
                        "cpm": "100",
                        "ctr": "10",
                        "reach": "0",
                        "frequency": "0",
                        "spend": "20",
                        "objective": "POST_ENGAGEMENT",
                        "date_start": "2019-07-30",
                        "date_stop": "2019-08-28"
                    }
                ]
            }

    :>json string adset_id: AdSet id associated with this Ad.
    :>json string adset_name: Name of the AdSet associated with this Ad.
    :>json string campaign_id: Id of the Campaign associated with this Ad.
    :>json string campaign_name: Name of the Campaign associated with this Ad.
    :>json string impressions: Estimated impressions for this Ad.
    :>json string clicks: Estimated clicks for this Ad.
    :>json string cpc: Estimated cost per click for this Ad.
    :>json string cpm: Estimated cost per 1000 impressions for this Ad.
    :>json string ctr: Estimated click through rate for this Ad.
    :>json string reach: Estimated reach for this Ad.
    :>json string frequency: Estimated frequency for this Ad.
    :>json string spend: How much is spent so far for this Ad.
    :>json string objective: Objective for this Ad.
    :>json string date_start: Date this Ad was started.
    :>json string date_stop: Date this Ad will stop.


.. toctree::
   :maxdepth: 2

