# Action-Network-SDK

simple php SDK for Action Network API v. 2
Version 1, December 2015

author: [Jonathan Kissam](http://jonathankissam.com)

This is not a full SDK for the Action Network API, it is merely intended to allow php-driven websites to push new supporters into a client's Action Network database, to pull lists of __petitions, forms, events and tags__, and to tag supporters and add them to petitions, forms, and events.

This SDK contains an ActionNetwork class, which creates an Action Network API connection controller.  It has the following methods:

* __call__: Makes an API call. Pass an endpoint, an optional HTTP method (defaults to "GET"), and a php object which will be converted into JSON. Returns a php object version of the JSON returned from Action Network's API
* __getAllPetitions__, __getAllForms__, __getAllEvents__ and __getAllTags__: fetch simple lists of resources (ids and titles/names)
* __signupPerson__, __recordAttendance__, __recordSignature__ and __recordSubmission__: use Action Network's helper API endpoints to add supporters, and add them to petitions, forms and events.  All of these methods take an Action Network Person (see below), the event/petition/form helpers take the id of the relevant event, petition or form as a second argument, recordSignature takes an optional comment as a third argument, and all of these functions can take an array of tags (which must already exist in the system) as a final argument
* __getCollection__, __getSimpleCollection__ and __getFullSimpleCollection__: helper functions for dealing with paging
* __getResourceId__, __getResourceTitle__ and __simplifyCollection__: helper functions for simplifying collections into lists of ids and titles

The SDK also contains an ActionNetworkPerson class, which creates a well-formed [person object](https://actionnetwork.org/docs/v2/people/) which can be passed to Action Network's API.  There are three methods to instantiate the class:

1. Pass a php object which follows the [schema](https://actionnetwork.org/docs/v2/people/)
2. Pass a php associative array which follows the schema.
3. Pass a simple php object or associative array with some or all of the following properties/keys:
  * email
  * status ('subscribed', 'unsubscribed', 'bouncing', or 'spam complaint')
  * given_name
  * family_name
  * address_lines
  * locality
  * region
  * postal_code
  * country

In addition, any properties or (first-level) array values which are simple strings will be treated as custom fields

The class will enforce receiving a valid email address, but in order to work with the API, it also must receive a postal code.

The class also has the following methods, which should be fairly self-explanatory:
* __setSubscriptionStatus__: takes a string ('subscribed', 'unsubscribed', 'bouncing', or 'spam complaint')
* __addFamilyName__: takes a string
* __addGivenName__: takes a string
* __addPostalAddress__: takes a well-formed [postal address](https://actionnetwork.org/docs/v2/people/#postal-addresses)
* __addCustomField__: takes two strings (key, value), or an associative array
