Shutterfly Coding Challenge
===========================

## [Problem Description](https://github.com/harikamireddi/Shutterfly_challenge/blob/master/PROBLEM_DESCRIPTION.md)

Installation Instructions
-------------------------

* Create and activate a python 3 environment
  * Using conda, that would be
    * `$ conda create --name shutterfly_challenge python=3`
    * `$ source activate shutterfly_challenge`
  * Virtualenv can also be used
    * `$ cd src/shutterfly_challenge`
    * `$ virtualenv -p python3 .venv`
    * `$ source .venv/bin/activate`

* Inside the environment

  * Navigate to source directory
    * `(shutterfly_challenge) $ cd src/shutterfly_challenge`

  * Install dependencies
    * `(shutterfly_challenge) $ pip install -r requirements.txt`

  * Run program
    * `(shutterfly_challenge) $ python app.py`

Libraries installed
--------------------

* [dateparser](https://pypi.python.org/pypi/dateparser) is used to parse the event_time.


Reading The Code
----------------

* Start with [src/shutterfly_code_challenge/app.py](https://github.com/harikamireddi/Shutterfly_challenge/blob/master/src/shutterfly_code_challenge/app.py).

* Then look into [src/shutterfly_code_challenge/events](https://github.com/harikamireddi/Shutterfly_challenge/tree/master/src/shutterfly_code_challenge/events) (start with base_event.py) and [src/shutterfly_code_challenge/lib](https://github.com/harikamireddi/Shutterfly_challenge/tree/master/src/shutterfly_code_challenge/lib) to see how events are implemented.

* Then look into [src/shutterfly_code_challenge/main/main.py](https://github.com/harikamireddi/Shutterfly_challenge/blob/master/src/shutterfly_code_challenge/main/main.py) to see how Ingest(e, D) and TopXSimpleLTVCustomers(x, D) are implemented.


Design 
------

### Events

* Invalid events are ignored completely.
  * Validation is performed in the Event classes (events folder) to
    * make sure event_time is correct
    * enforce required attributes
    * enforce a proper verb is given
    * check some other event specific attributes


* No keys are duplicated. As new events come in, existing keys are merged in.

* Each attribute (such as Order.total_amount) is stored with the event_time associated with it.
  * See lib/value_at_time.py
  * This way, when out of order updates come in, only attributes that are supposed
    to be updated will be updated.

* A new attribute called created_time is created for each record.
  * This is the event_time on the NEW or UPLOAD event.

Assumptions and Considerations
------------------------------

* All Order.total_amount values are in USD

* x in TopXSimpleLTVCustomerEvents(x, D) will be small

* If an Order event UPDATE comes in for a key, but the NEW event has not come in
  yet, that key is ignored for this calculation.
  * This is because this method uses the Order.created_time attribute, which is
    created when the NEW method comes in.
  * This could be addressed by setting Order.created_time to the event_time of
    the UPDATE if Order.created_time is empty.

Performance characteristic
--------------------------
*Keeping Performance in mind tried to use dictionary for the in-memory Data Structure instead of lists,
 wherever frequent searching is required, so that we can reduce the lookup time to O(1).
