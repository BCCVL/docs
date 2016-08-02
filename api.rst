
=====
 API
=====

.. todo:: Some of the API endpoints described below are not yet implemented / available, and some of the already existing API endpoints are not well documented.


JSON API
========

use <API-Endpoint-URL>/schema to access the json hyperschema for each API endpoint


Error-Response
--------------

In case of an error, the API will respond with an appropriate 4xx or 5xx HTTP Response. The body content may contain a document of type 'application/json' with a top level key 'errors', which holds an array of error objects describing the problem.

   .. json:object:: Error

      An error object returned by the API describing one particular issue. (All fields are optional and may only be sent for error conditions where it makes sense)

      :property int status: standard http status code
      :property string title: human readable title, which should be the same for similar errors
      :property string detail: human readable description, detailing specific error condition
      :property object source: an object pointing to a parameter or related to this error
                               
   .. sourcecode:: http

      HTTP/1.1 4xx/5xx OK
      Content-type: application/json

      {
        "errors": [
          {
            "status": 400,
            "title": '...',
            "detail": '...',
            "source": {
              "parameter": "..."
            }
          }
        ]
      }
       
      
.. http:get:: /API

   The main entry point to BCCVL API

   **Example Request**:

   .. sourcecode:: http

      GET /API HTTP/1.1
      Host: example.com

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json; profile=https://demo.bccvl.org.au/API/schema
       
      {
        "description": "BCCVL API endpoint",
        "id": "API",
        "title": "BCCVL APIs"
      }


Experiment API
--------------
      
.. http:get:: /API/em

   The main entry point to BCCVL Experiment API

   **Example Request**:

   .. sourcecode:: http

      GET /API/em HTTP/1.1
      Host: example.com

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json; profile=https://demo.bccvl.org.au/API/em/schema
       
      {
        "description": "Access experiments",
        "id": "em",
        "title": "Experiment API"
      }    


.. http:get:: /API/em/v1

   The main entry point to BCCVL Experiment API version 1

   **Example Request**:

   .. sourcecode:: http

      GET /API/em/v1 HTTP/1.1
      Host: example.com

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json; profile=https://demo.bccvl.org.au/API/em/v1/schema

      {
        "description": "Experiment API v1",
        "id": "v1",
        "title": "Experiment API v1"
      }


.. http:post:: /API/em/v1/demosdm
   :noindex:
   :deprecated:

   Start demo SDM. (Deprecated)

   :form string lsid: Species LSID

                      
.. http:post:: /API/em/v1/submitsdm

   Start a new SDM with given parameters and return a status message and a job id to track progress.

   :<json string title: The title for the experiment
   :<json string description: A short description for the experiment
   :<json string occurrence_data: an lsid or a dataset uuid to be used as occurrence data
   :<json string occurrence_data.source: "bccvl", "ala" or "gbif"
   :<json string occurrence_data.id: an identifier suitable for source (uuid, lsid, gbifid)
   :<json string absence_data: a dataset uuid to be used as absence data or skip/null to use random pseudo absence points
   :<json boolean scale_down: if true, rescale environmental datasets to highest resolution otherwise rescale to lowest resolution
   :<json object environmental_data: dictionary where keys refer to dataset uuid and values are a list of layer identifiers from this dataset.
   :<json string modelling_region: an optional GeoJSON string to constrain modelling area
   :<json object algorithms: a dictionary where the key identifies the algorithm and the value dictionary contains customisable parameter values.
                             
   :>json string jobid: an identifier to use with job api endpoint

   **Example request**

   .. sourcecode:: http

      POST /API/em/v1/submitsdm HTTP/1.1
      Host: app.bccvl.org.au
      Content-Type: application/json

      {
        "title": "Example SDM",
        "description": "Example SDM using maxent",
        "occurrence_data": {
          "source": "bccvl",
          "id": "303ed3750a9248698e7736377d9d789f"
        },
        "absence_data": null,
        "scale_down": false,
        "environmental_data": {
          "72ca2a1d5d91440bbb583427a5624dc8": ["B01", "B02", "B03"]
        },
        "modelling_region": null,
        "algorithms": {
          "maxent": null,
        },
      }

   **Example response**

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-type: application/json

      {
        "experiment": {
            "uuid": "....",
            "url": "...."
        }
        "jobs": ["72ca2a1d5d91440bbb583427a5624dc8"]
      }


Job API
-------

.. http:get:: /API/job

   The main entry point to BCCVL Job API

   **Example Request**:

   .. sourcecode:: http

      GET /API/em HTTP/1.1
      Host: example.com

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json; profile=https://demo.bccvl.org.au/API/job/schema
       
      {
        "description": "Access jobs",
        "id": "job",
        "title": "Job API"
      }    


.. http:get:: /API/job/v1

   The main entry point to BCCVL Job API version 1

   **Example Request**:

   .. sourcecode:: http

      GET /API/job/v1 HTTP/1.1
      Host: example.com

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json; profile=https://demo.bccvl.org.au/API/job/v1/schema

      {
        "description": "Job API v1",
        "id": "v1",
        "title": "Job API v1"
      }

      
.. http:get:: /API/job/v1/state

   Query job by bccvl content uuid or jobid

   :query string uuid: Content object UUID to find job for
   :query string jobid: Job ID to query

   :>json string state: The Job state and other stuff

   **Example request**

   .. sourcecode:: http

      GET /API/job/v1/state?jobid=ajobid HTTP/1.1
      Host: app.bccvl.org.au

   **Example response**

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-type: application/json

      {
        "jobid": "ajobid",
        "state": "RUNNING"
      }
                        

.. http:get:: /API/job/v1/query

   Queries Job Catalog directly using job catalog index names.

   :query string kwargs: query params for job catalog


Data API
--------
                         
.. http:get:: /API/dm

   The main entry point to BCCVL Datasets API

   **Example Request**:

   .. sourcecode:: http

      GET /API/dm HTTP/1.1
      Host: example.com

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json; profile=https://demo.bccvl.org.au/API/dm/schema
       
      {
        "description": "Access datasets",
        "id": "dm",
        "title": "Dataset API"
      }    


.. http:get:: /API/dm/v1

   The main entry point to BCCVL Dataset API v1

   **Example Request**:

   .. sourcecode:: http

      GET /API/em HTTP/1.1
      Host: example.com

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json; profile=https://demo.bccvl.org.au/API/dm/v1/schema
       
      {
        "description": "Dataset API v1",
        "id": "v1",
        "title": "Dataset API v1"
      }    


.. http:get:: /API/dm/v1/metadata

   Retrieve metadata for Dataset

   :query string uuid: UUID for dataset object

   :>json objct metadata: Metadata about requested dataset

                          
.. http:get:: /API/dm/v1/update_metadata

   Extract and update metadata for Dataset

   :query string uuid: UUID for dataset to update


                       
Site API
--------
                       
.. http:get:: /API/site

   The main entry point to BCCVL Site API

   **Example Request**:

   .. sourcecode:: http

      GET /API/site HTTP/1.1
      Host: example.com

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json; profile=https://demo.bccvl.org.au/API/site/schema
       
      {
        "description": "Access site information",
        "id": "site",
        "title": "Site API"
      }    


.. http:get:: /API/site/v1

   The main entry point to BCCVL Site API v1

   **Example Request**:

   .. sourcecode:: http

      GET /API/site/v1 HTTP/1.1
      Host: example.com

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json; profile=https://demo.bccvl.org.au/API/site/v1/schema
       
      {
        "description": "Access site wide information",
        "id": "v1",
        "title": "Site API v1"
      }    


.. http:get:: /API/site/v1/send_support_email

   Send quick email to ask for help.

   :query string uuid: Experiment / Result UUID for which help is requested.

.. http:get:: /API/site/v1/can_access

   Check if current user has access to content object

   :query string uuid: Content Object UUID

.. http:get:: /API/site/v1/vocabulary

   Fetch named vorabulary

   :query string name: name of vocabulary

   :>json object vocab: list of entries within vocabulary

   +--------------------------------------------------------------------------------+
   |**Valid values for name**                                                       |
   +-----------------------+--------------------------------------------------------+
   |emsc_source            |list all known emission scenarios                       |
   +-----------------------+--------------------------------------------------------+
   |gcm_source             |list all global cirucaltion models                      |
   +-----------------------+--------------------------------------------------------+
   |layers_source          |all known layers within BCCVL                           |
   +-----------------------+--------------------------------------------------------+
   |sdm_functions_source   |all functions available to be used with SDM experiments |
   +-----------------------+--------------------------------------------------------+


Deprecated API
==============

The following API endpoints can return XMLRPC or JSON

ALL API endponits can work directly on a content object or a parameter **datasetid** can be supplied.

.. http:get:: <object>/@uuid
   :noindex:

    return uuid for context object

.. http:get:: /dm/metadata
   :noindex:

    return metadata for object

    :query string datasetid: Dataset UUID

.. http:get:: /dm/getMetadata
   :noindex:

    same as above

.. http:get:: /dm/getRAT
   :noindex:

    return RAT for given dataset and optional layer

    :query string datasetid: Dataset UUID
    :query string layer: Layer identifier within dataset

.. http:get:: /dm/query
   :noindex:

    query catalog with current context

.. http:get:: /dm/queryDataset
   :noindex:

    :query string genre: Dataset Genre
    :query string layers: List of Layers to search for

.. http:get:: /dm/getVocabulary
   :noindex:

    :query string name: return vocabulary by name

.. http:get:: <dataset>/dm/getMetadata
   :noindex:

    see above

.. http:get:: <dataset>/dm/share
   :noindex:

    publishes current dataset to allow access to anyone

.. http:get:: <dataset>/dm/unshare
   :noindex:

    un-publish dataset

.. http:get:: <object>/jm/getJobStatus
   :noindex:

    return job status for current object

.. http:get:: <object>jm/getJobStates
   :noindex:

    return job states in case this object has multiple sub jobs (e.g. experiment vs. result)

.. http:get:: .../ala/autojson
   :noindex:

    proxy request to ala autjson web service

.. http:get:: .../ala/searchjson
   :noindex:

    proxy request to ala searchjson web service

.. http:get:: .../gbif/autojson
   :noindex:

    proxy request to gbif auto complete web service

.. http:get:: ../gbif/searchjson
   :noindex:

    proxy request to gbif search web service

.. http:get:: ../gbif/speciesjson
   :noindex:

    proxy request to gbif species web service

.. http:get:: ../dv/pullOccurrenceFromALA
   :noindex:

    trigger ALA import job

.. http:get:: <result>/export_result
   :noindex:

    export experiment results to cloud storage
