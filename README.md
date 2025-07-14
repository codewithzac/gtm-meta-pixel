# GTM Meta Pixel
An unofficial Meta Pixel template for Google Tag Manager, with built-in support for GTM Ecommerce events.

This GTM template is based on Simo Ahava's excellent [Facebook Pixel template](https://tagmanager.google.com/gallery/#/owners/facebookarchive/templates/GoogleTagManager-WebTemplate-For-FacebookPixel), with the following updates:
* Add: support for the GA4 / GTM Ecommerce event model
* Remove: support for the GUA Enhanced Ecommerce event model
* Remove: support for Facebook Consent API (use GTM Consent Mode instead!)

## Prerequisites
I recommend that you have a robust data layer configured on your website before adding the Meta Pixel, especially if you want to take advantage of the built-in support for GTM Ecommerce events.

Some light reading:
* [Introduction to the Data Layer](https://developers.google.com/tag-platform/tag-manager/datalayer)
* [Documentation for Ecommerce measurement](https://developers.google.com/analytics/devguides/collection/ga4/ecommerce?client_type=gtm)\
  (your Ecommerce data layer _must_ be in this format for automatic event handling to work)
* [Analytics mania guide to the Data Layer](https://www.analyticsmania.com/post/what-is-data-layer-in-google-tag-manager/)\
  (a longer read, but worthwhile if you're not familiar with how the GTM dataLayer works)

As many jurisdictions now require or recommend obtaining consent from users, I also recommend configuring GTM Consent Mode - and ensuring tags such as the Meta Pixel have appropriate consent from the end user before firing. Many leading CMPs have configuration guides for integrating with GTM Consent Mode, and it's possible to use free consent banners with GTM Consent Mode as well.

Please review the [support article for Tag Manager Consent Mode](https://support.google.com/tagmanager/answer/10718549) for more information.

## Quick Start Guide
Add the Meta Pixel tag template from the Community Gallery in the usual manner first..

### Page Views
Add an instance of the tag for page views as follows:
* Meta Pixel ID(s): enter your Meta Pixel ID
* GTM Ecommerce dataLayer Integration: leave unchecked
* Event Name: leave set to Standard, with PageView selected in the dropdown

All remaining tag settings can either be left at defaults, or configured to taste.

To configure the Meta Pixel to respect user consent preferences (requires GTM Consent Mode): in the Advanced Settings, open Consent Settings and select Require additional consent for tag to fire. Choose the appropriate consent category for your consent requirements (likely ad_storage).

In the Triggering section, choose the built-in All Pages trigger.

### Ecommerce Events
Add another instance of the tag for ecommerce events as follows:
* Meta Pixel ID(s): enter your Meta Pixel ID
* GTM Ecommerce dataLayer Integration: check the checkbox

All remaining tag settings can either be left at defaults, or configured to taste.

To configure the Meta Pixel to respect user consent preferences (requires GTM Consent Mode): in the Advanced Settings, open Consent Settings and select Require additional consent for tag to fire. Choose the appropriate consent category for your consent requirements (likely ad_storage).

In the Triggering section, create a new trigger:
* Trigger Type: Custom Event
* Event name: `^view_item$|^add_to_cart$|^add_to_wishlist$|^begin_checkout$|^add_payment_info$|^purchase$`
* Use regex matching: check the checkbox
* This trigger fires on: select All Custom Events

Give the trigger a name, then save the trigger.

## Conversions API Requirements
If you're planning to add server side tracking via the Conversions API in a GTM Server container, you should add a variable template to generate an Event ID. I quite like the [Unique Event ID variable template](https://tagmanager.google.com/gallery/#/owners/stape-io/templates/unique-event-id-variable) by stape-io for this - once you've added this template and created the variable, add it into your Meta Pixel tag(s) in the More Settings section.

You will also need to add this Event ID to whichever tag you are using to send events to your GTM Server container.

## Configuration Reference
Additional information for configuring the Meta Pixel tag.

### Multiple Meta Pixels
To trigger multiple Meta Pixels, put a comma-separated list of pixel IDs (without spaces) in the Meta Pixel ID(s) box. Note that this assumes the exact same events and data need to be sent to all pixels - if different pixels have different data requirements, you will need separate tags.

### GTM Ecommerce dataLayer Integration
Check this box to automatically trigger Ecommerce events; this should be a separate tag configuration to page views.

#### Ecommerce Event Types
The following events are triggered when GTM Ecommerce events are pushed into the dataLayer:

| GTM Ecommerce event | Meta Pixel event |
| ------------------- | ---------------- |
| view_item           | ViewContent      |
| add_to_cart         | AddToCart        |
| add_to_wishlist     | AddToWishlist    |
| begin_checkout      | InitiateCheckout |
| add_payment_info    | AddPaymentInfo   |
| purchase            | Purchase         |

Other events supported by the GTM Ecommerce event model (add_shipping_into, refund, remove_from_cart, etc.) don't have equivalent Meta Pixel standard events, and thus aren't supported.

#### Ecommerce Event Data
GTM Ecommerce event data is mapped to the Meta Pixel as follows. Note that the data format of the GTM Ecommerce `ecommerce.items` variables and the Meta Pixel `contents` parameters are both arrays of objects:

| GTM Ecommerce event data    | Meta Pixel event data  |
| --------------------------- | ---------------------- |
| "product" (hardcoded)       | content_type           |
| ecommerce.items[x].item_id  | contents[x].id         |
| ecommerce.items[x].price    | contents[x].item_price |
| ecommerce.items[x].quantity | contents[x].quantity   |
| ecommerce.value             | value                  |
| ecommerce.currency          | currency               |

Specifically for the InitiateCheckout and Purchase events, all of the values in `ecommerce.items[x].quantity` are added up and mapped as `num_items`.

### Event Name
For when you aren't using the GTM Ecommerce dataLayer Integration, you can choose from the following options:

#### Standard
Select a standard event supported by the Meta Pixel. Note you will need to map parameters separately when using this option.

#### Custom
For custom requirements; you should only use this if you have custom events being tracked by the Meta Pixel. Intended for using plain text as the event name.

#### Variable
Same as Custom above, but is intended for mapping a variable as the event name.

### Enable Advanced Matching
Enables the *Customer Information Data Parameters* section - see below.

### Data Processing Options
Intended for configuring the [Limited Data Use](https://www.facebook.com/business/help/1151133471911882) processing option for the Meta Pixel, for certain jurisdictions in the United States of America.

### Customer Information Data Parameters
For mapping supported PII parameters from the Data Layer, to improve match rates for Meta Pixel events.

### Object Properties
For mapping parameters from Data Layer variables. Allows you to send a pre-built object, plus any individual variables. An example use case for this would be for Search events: map the Meta Pixel parameter `search_string` from a variable in your Data Layer (or perhaps a query string, if you don't have a Data Layer variable to use)

### More Settings
#### Disable Automatic Configuration
Turns off automatic data collection and button click tracking. Disabling this gives you more control over what's tracked, at the expense of additional configuration potentially being required.

#### Disable History Event Tracking
Turns off automatic tracking for Browser API `pushState()` and `replaceState()` functions. Disabling this may be required for certain Single Page Application configurations, to prevent double tracking.

#### Event ID
As discussed in the Conversions API Requirements above.
