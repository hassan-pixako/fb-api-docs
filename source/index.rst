Facebook Marketing API
======================

This document introduces and explains the use of Facebook API for creating and managing campaigns on Facebook using the ICN platform.

Introduction
^^^^^^^^^^^^

Facebook Marketing API for **ICN** provides an array of endpoints to create and manage campaigns on Facebook. Every endpoint requires a ``JSON Web Token`` for authentication. This token can be generated via ``/token`` endpoint and requires ``username`` and ``password hash`` of a user. All ``GET`` requests require ``token`` parameter to be passed as part of the ``url`` whereas all ``POST`` requests require ``token`` to be passed as a ``form parameter``. Overtime, more endpoints will be added to offer more functionality. With that in mind, let's have a look at the endpoints the API provides at this point.


API Endpoints
^^^^^^^^^^^^^

Tokens
######

.. http:post:: /token

    Generates a ``JSON Web Token`` that is used for authentication when making calls to the other endpoints of the API. This token expires after ``1 hour``. Use the ``/token/verify`` to verify the validity of a token.

    :reqheader Content-Type: application/x-www-form-urlencoded
    :form username: ``Username`` of the account to authenticate against.
    :form password: ``Password Hash`` of the account to authenticate against.

    **Example Request**:

    .. sourcecode:: http

        POST /token HTTP/1.1
        Host: icrowdnewswire.com/api/
        User-Agent: curl/7.65.3
        Accept: */*

    **Example Response**

    .. sourcecode:: http

        HTTP/1.0 201 Created
        Content-Type: application/json

        {"token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE1NjcwNjk2M..."}


.. http:get:: /token/verify?token=<JWT>

    Checks to see if a token is valid or expired.

    **Example Request**:

    .. sourcecode:: http

        GET /token/verify?token=eyJ0eXAiOiJKV1QiLCJhbG... HTTP/1.1
        Host: icrowdnewswire.com/api/
        User-Agent: curl/7.65.3
        Accept: */*

    **Example Response**

    .. sourcecode:: http

        HTTP/1.1 200 OK
        Content-Type: application/json

    :query token: ``JWT string`` used for authentication.


Campaigns
#########

.. http:post:: /campaign

    Creates a new campaign.

    :reqheader Content-Type: application/x-www-form-urlencoded
    :form token: ``JSON Web Token`` to be used for authentication.
    :form name: Name of the campaign.
    :form status: Status of the campaign at the time of creation.

    **Example Request**:

    .. sourcecode:: http

        POST /campaign HTTP/1.1
        Host: icrowdnewswire.com/api/
        User-Agent: curl/7.65.3
        Accept: */*

    **Example Response**

    Returns the id of the Campaign.

    .. sourcecode:: http

        HTTP/1.0 201 Created
        Content-Type: application/json

        {
            "id": "120330000039963113"
        }


.. http:get:: /campaigns?token=<JWT>

    Returns ids of all campaigns associated with the account.
    These ids can be used to query individual campaigns.

    **Example Request**:

    .. sourcecode:: http

        GET /campaigns/ids?token=eyJ0eXAiOiJKV1QiLCJhbG... HTTP/1.1
        Host: icrowdnewswire.com/api/
        User-Agent: curl/7.65.3
        Accept: */*

    **Example Response**

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

    :query token: ``JWT string`` used for authentication.


Ad Sets
#########

.. http:post:: /ad/set

    Creates a new AdSet.

    :reqheader Content-Type: application/x-www-form-urlencoded
    :form token: ``JSON Web Token`` to be used for authentication.
    :form name: Name of the AdSet.
    :form status: Status of the AdSet at the time of creation.
    :form countries: An ``array`` of countries to be targeted.
    :form daily_budget: Daily Budget.
    :form bid_amount: Bid Amount.
    :form campaign_id: ``Campaign Id`` under which to create this AdSet.
    :form promoted_page_id: ``Page Id`` under which to create ads.

    **Example Request**:

    .. sourcecode:: http

        POST /ad/set HTTP/1.1
        Host: icrowdnewswire.com/api/
        User-Agent: curl/7.65.3
        Accept: */*

    **Example Response**

    Returns the id of the AdSet.

    .. sourcecode:: http

        HTTP/1.0 201 Created
        Content-Type: application/json

        {
            "id": "120330000039963813"
        }

Ad Creatives
############

.. http:post:: /ad/creative

    Creates a new AdCreative.

    :reqheader Content-Type: application/x-www-form-urlencoded
    :form token: ``JSON Web Token`` to be used for authentication.
    :form image: Image to be used for the creative.
    :form name: Name of the creative.
    :form message: Message of the creative. This appears right above the image.
    :form link: Creative link.

    **Example Request**:

    .. sourcecode:: http

        POST /ad/set HTTP/1.1
        Host: icrowdnewswire.com/api/
        User-Agent: curl/7.65.3
        Accept: */*

    **Example Response**

    Returns the id of the AdCreative.

    .. sourcecode:: http

        HTTP/1.0 201 Created
        Content-Type: application/json

        {
            "id": "120330000039964613"
        }

Ad
##

.. http:post:: /ad

    Creates a new ad.

    :reqheader Content-Type: application/x-www-form-urlencoded
    :form token: ``JSON Web Token`` to be used for authentication.
    :form ad_set_id: The id of the AdSet to associate with this ad.
    :form creative_id: The id of the AdCreative to associate with this ad.
    :form name: Name of the ad.
    :form message: Status of this ad at the time of creation.

    **Example Request**:

    .. sourcecode:: http

        POST /ad HTTP/1.1
        Host: icrowdnewswire.com/api/
        User-Agent: curl/7.65.3
        Accept: */*

    **Example Response**

    Returns the id of the ad and a preview link.

    .. sourcecode:: http

        HTTP/1.0 201 Created
        Content-Type: application/json

        {
            "id": "120330000039965013",
            "preview_link": "https://www.facebook.com/ads/api/preview_iframe.php?d=AQJ_J6R.."
        }


.. toctree::
   :maxdepth: 2

