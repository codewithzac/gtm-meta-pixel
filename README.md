# GTM Meta Pixel
An unofficial Meta Pixel template for Google Tag Manager, with built-in support for GTM Ecommerce events.

This GTM template is based on Simo Ahava's excellent [Facebook Pixel template](https://www.simoahava.com/custom-templates/facebook-pixel/), with the following updates:
* Add: support for the GA4 / GTM Ecommerce event model
* Remove: support for the GUA Enhanced Ecommerce event model
* Remove: support for Facebook Consent API (use GTM Consent Mode instead!)

## Prerequisites
I recommend that you have a robust data layer configured on your website before adding the Meta Pixel, especially if you want to take advantage of the built-in support for GA4 / GTM Ecommerce events. Please review the [documentation for Ecommerce measurement](https://developers.google.com/analytics/devguides/collection/ga4/ecommerce?client_type=gtm) for more information.

As many jurisdictions now require or recommend obtaining consent from users, I also recommend configuring GTM Consent Mode - and ensuring tags such as the Meta Pixel require consent before firing. Please review the [support article for Tag Manager Consent Mode](https://support.google.com/tagmanager/answer/10718549) for more information.

## Installation
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

## Additional considerations
If you're planning to add server side tracking via the Conversions API in a GTM Server container, you should add a variable template to generate an Event ID. I quite like the [Unique Event ID variable template](https://tagmanager.google.com/gallery/#/owners/stape-io/templates/unique-event-id-variable) by stape-io for this - once you've added this template and created the variable, add it into your Meta Pixel tag(s) in the More Settings section.
