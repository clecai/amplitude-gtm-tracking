# amplitude-gtm-tracking

Do you want to implement Amplitude using Google Tag Manager (GTM)? You have knocked at the right door.

This repository contains json files and a few notes detailing the implementation of [Amplitude](https://amplitude.com/)  tracking through [Google Tag Manager (GTM)](https://tagmanager.google.com/) as I initially intended it for the company I work for, [ManoMano](https://www.manomano.fr). Want to know more about our tracking philosophy ? You can read our [tracking principles here](https://github.com/clecai/amplitude-gtm-tracking/blob/master/tracking_principles.md).


## About this repository
*The remainder of this documentation assumes you are somewhat familiar with Google Tag Manager (GTM) and Amplitude. If you are feeling iffy with GTM, I suggest you go ahead and watch the great video trainings of [MeasureSchool on YouTube](https://www.youtube.com/channel/UClgihdkPzNDtuoQy4xDw5mA). 
Before you start your implementation, you should feel comfortable with the dataLayer and the sequences of events happening when a webpage is loaded (pageview, DOM ready, window loaded).*

You can download [*amplitude_gtm_tracking_container.json*](https://github.com/clecai/amplitude-gtm-tracking/blob/master/amplitude_gtm_tracking_container.json) and import it on GTM. It contains, by order of importance :
- `Amplitude — SDK`, a tag that is executed on All Pages (at pageview) and loads the necessary functions for Amplitude to work. 
- `amplitude_project_id`, a lookup variable, which contains the ids of the Amplitude projects we will be sending data to.
- `amplitude_event_properties`, a js object containing all the event-related variables that we will be sending to Amplitude.
- `amplitude_user_properties`, a js object containing all the user-related variables that we will be sending to Amplitude.
work.
- `Amplitude - event - TEMPLATE`, a tag with no trigger, which you should copy each time you want to add a new event. It contains the code that is executed each time you collect an event and send it to Amplitude.
- `Amplitude - Generic event - View page`, this tag enables the collection of all pageviews, it is triggered on window-loaded if no other pageview event has been collected.
- 3 other `Amplitude - event` tags, which collect data at specific stages of a classic e-commerce funnel.
- 4 triggers defining the stages of a classic e-commerce funnel (both server-side and client-side).
- 9 variables starting with `DL_`, which are dataLayer variables.

## Sidenote 1: understanding how Amplitude events work
Amplitude is an event-based tracking solution. Each event is basically an HTTP request that carries 4 informations: 
1. the event name
2. the user id
3. a json object containing event-related variables
4. another json object containing user-related variables

Purely in terms of data it works kind of like events on Google Analytics do, with the notable exception that **you can pass up to 2K variables to an event.** For us, it meant that, at first, we needn't worry about which variables should be sent to which events, we could basically send them all. This was step 1 in simplifying the process of adding new events to the tracking plan.

## Sidenote 2: Leveraging the dataLayer
You will notice that our implementation depends greatly on Google Tag Manager's dataLayer. Our aim is to have the data we collect transition through the dataLayer to GTM and feed Amplitude with as few efforts as possible. This was step 2 in reducing friction in improving our tracking.

Most of the variables we collect — like `product_sku` or `product_price` — are updated at different stages of our funnel — when a product page is loaded or on an add-to-cart event on the category page for instance —, using the dataLayer we only need to connect them once for them to be sent to Amplitude.

## Collecting server-side events (pageviews)
The first thing that you will likely want to do is to collect pageviews (like the example `View category page`), which is a server-side event. Let see what happens when the page loads:

![Amplitude GTM implementation Figure 1](https://github.com/clecai/amplitude-gtm-tracking/blob/master/img/figure1.svg)

On the first available trigger, `Page View`, we load Amplitude's SDK, this enables data collection and loads all necessary functions.

Then on the second available trigger, `DOM ready`, we execute the event tag which sends data to Amplitude in the form of two js objects: amplitude_event_properties (E) and amplitude_user_properties (U). 

As you can see above, these objects (E) and (U) gather data from the dataLayer: **which means that you first have to send data to the dataLayer (that's usually done by a front-end developper)**


## Collecting client-side events (bound to js/ajax callbacks)
Now the second type of events that you will likely want to track are client-side events, generally executed on the user's browser through javascript. Below, I've illustrated what happens the user lands on a category page and they perform an add-to-cart event:

![Amplitude GTM implementation Figure 2](https://github.com/clecai/amplitude-gtm-tracking/blob/master/img/figure2.svg)

The page is loaded and as we have seen previously a pageview event is collected, then the user performs a client-side action and another event is sent to Amplitude along with event and user properties.

Here too, we are relying on the dataLayer to funnel data to Amplitude, **and also** to trigger the event at the appropriate time. Indeed, as is very well documented in [this AnalyticsMania post](https://www.analyticsmania.com/post/google-tag-manager-custom-event-trigger/), one of the main pitfalls of GTM is to trigger events on clicks made by the user (but they can be tricky). 

At ManoMano we use gtm custom events, implemented by front-end developpers in our code like so:

```
 window.dataLayer = window.dataLayer || [];
 window.dataLayer.push({
   'event': 'add_to_cart',
   'page_name': <your_page_name>,
   'page_title': <your_page_title>,
   'product_sku': <your_product_sku>,
   'product_price': <your_product_price>
 });
```
This data push is bound to an ajax callback, so we know we are only sending data when we are 100% sure the event happen. Then GTM sees an `add_to_cart` trigger and in turn sends data to Amplitude.

## Collecting all pageviews without double-counting them
Early-on in our implementation we bumped into a bit of a conundrum: how could we record all pageview events while at the same time being able to tell the main pageview events appart from the rest? The solution was afforded by the sequence of events happening when a the server renders the page.

Here is what happens when we want to collect a specific pageview (complete with its own event name):

![Amplitude GTM implementation Figure 4](https://github.com/clecai/amplitude-gtm-tracking/blob/master/img/figure4.svg)

And here is what happens when we haven't collected any pageview and the server finishes to render the page:

![Amplitude GTM implementation Figure 5](https://github.com/clecai/amplitude-gtm-tracking/blob/master/img/figure5.svg)

## Monitoring and QA-ing your tracking
foobar
