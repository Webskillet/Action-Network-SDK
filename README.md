# Action Network SDK

simple php SDK for Action Network API v. 2  
Version 1.2, October 2016

author: [Jonathan Kissam](http://jonathankissam.com)

This is not a full SDK for the Action Network API. It provides a method to call an arbitrary endpoint, and also supports:
* traversing collections of Action Network resources with a callback function
* pushing new supporters into a client's Action Network database
* pulling lists of __petitions, forms, events and tags__
* tagging supporters and adding them to petitions, forms, and events.

It was primarily developed for use in my [Action Network Wordpress plugin](https://github.com/jkissam/actionnetwork) and [webform_external Drupal module](https://github.com/webskillet/webform_external)

[Sign up for my email list](http://eepurl.com/cabLYT) to receive updates about new versions and applications.

If you find this SDK useful, please consider supporting further development by [hiring me or making a donation](http://jonathankissam.com/support).

Please [contact me](http://jonathankissam.com/about#contact) with bug reports, feedback, thoughts or feature requests.

## Updates

_October 6, 2016:_

1. Updated to address the fact that Action Network's API removed the *total_pages* field from the "people" collection on 9/2/2016. __getFullSimpleCollection__ and __traverseFullCollection__ should now work with the "people" endpoint, although that is probably not a good practice, for the same reason that Action Network removed the field from the collection (i.e., for any large collection, it is probably better to use __traverseCollection__ with the new __getNextpage__ method in a batching function, to prevent php timeouts).

2. Added __getNextPage__ method, to return the _links->next->href property from a collection (to aid in traversing collections which return multiple pages).

3. Modified the __getResourceTitle__ method to return the email address for the "people" collection, since a "person" resource has neither "title" nor "name," and the email address is unique, required, and more important than the actual name.

## ActionNetwork class

This SDK contains an ActionNetwork class, which creates an Action Network API connection controller.  It has the following methods:

* __call__: Makes an API call. Pass an endpoint, an optional HTTP method (defaults to "GET"), and a php object which will be converted into JSON. Returns a php object version of the JSON returned from Action Network's API
* __traverseCollection__ and __traverseFullCollection__: Pass a collection endpoint (such as "events") and a callback function, and the method will iterate through the embedded resources in the collection, passing each one to the callback function, along with the endpoint, a numerical index, and the total number of records in that collection. __traverseFullCollection__ will iterate through all pages in the collection, while __traverseCollection__ will only iterate through a single page. The callback function can either be a method of a class that extends ActionNetwork, or a standalone function. If the latter, the callback function will also be passed the ActionNetwork object.
* __getEmbedCodes__: fetch embed codes for a particular resource
* __getAllPetitions__, __getAllForms__, __getAllEvents__ and __getAllTags__: fetch simple lists of resources (ids and titles/names)
* __signupPerson__, __recordAttendance__, __recordSignature__ and __recordSubmission__: use Action Network's helper API endpoints to add supporters, and add them to petitions, forms and events.  All of these methods take an Action Network Person (see below), the event/petition/form helpers take the id of the relevant event, petition or form as a second argument, recordSignature takes an optional comment as a third argument, and all of these functions can take an array of tags (which must already exist in the system) as a final argument
* __getCollection__, __getSimpleCollection__ and __getFullSimpleCollection__: helper functions for dealing with paging
* __getResourceId__, __getResourceTitle__ and __simplifyCollection__: helper functions for simplifying collections into lists of ids and titles

## ActionNetworkPerson class

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
